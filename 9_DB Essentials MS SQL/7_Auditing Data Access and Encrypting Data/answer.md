# AUDITING DATA ACCESS AND ENCRYPTING DATA - ANSWER

This guide covers SQL Server Audit configuration for security monitoring
and Transparent Data Encryption (TDE) for protecting data at rest.

================================================================================
                    UNDERSTANDING SQL SERVER AUDIT
================================================================================

AUDIT ARCHITECTURE:
------------------

    +------------------+
    |   SQL Server     |
    |     Audit        |  <-- Defines WHERE to write (File/Event Log)
    +--------+---------+
             |
    +--------+---------+
    |                  |
    v                  v
+--------+       +----------+
| Server |       | Database |
| Audit  |       |  Audit   |
|  Spec  |       |   Spec   |
+--------+       +----------+
    |                  |
    v                  v
Server-level      Database-level
actions           actions
(logins,          (SELECT, INSERT,
backups, etc.)    UPDATE, DELETE)

AUDIT DESTINATIONS:
------------------

+----------------------+--------------------------------------------------+
| Destination          | Description                                      |
+----------------------+--------------------------------------------------+
| File                 | Binary audit files (.sqlaudit). Most common.     |
| Windows Application  | Windows Event Log - Application section          |
| Windows Security     | Windows Event Log - Security section (requires   |
|                      | special permissions)                             |
+----------------------+--------------------------------------------------+

================================================================================
                    PART 1: CREATE SQL SERVER AUDIT
================================================================================

STEP 1.1: CREATE AUDIT DIRECTORY
--------------------------------

    -- Create directory for audit files using xp_cmdshell
    EXEC xp_cmdshell 'mkdir C:\SQLAudit';
    GO

    -- Or via PowerShell/Command Prompt before running SQL:
    -- mkdir C:\SQLAudit

    -- Grant SQL Server service account access
    -- icacls "C:\SQLAudit" /grant "NT SERVICE\MSSQLSERVER":(OI)(CI)F

STEP 1.2: CREATE SQL SERVER AUDIT (T-SQL)
-----------------------------------------

    USE master;
    GO

    -- Create the audit object
    CREATE SERVER AUDIT ServerSecurityAudit
    TO FILE (
        FILEPATH = 'C:\SQLAudit\',
        MAXSIZE = 100 MB,
        MAX_ROLLOVER_FILES = 10,
        RESERVE_DISK_SPACE = OFF
    )
    WITH (
        QUEUE_DELAY = 1000,  -- milliseconds before writing to file
        ON_FAILURE = CONTINUE  -- continue if audit fails
    );
    GO

    -- Enable the audit
    ALTER SERVER AUDIT ServerSecurityAudit WITH (STATE = ON);
    GO

    -- Verify audit is created and enabled
    SELECT
        name,
        type_desc,
        is_state_enabled,
        create_date
    FROM sys.server_audits;
    GO

STEP 1.3: CREATE SQL SERVER AUDIT USING SSMS (ALTERNATIVE)
----------------------------------------------------------

1. In Object Explorer, expand Security folder
2. Right-click "Audits" -> "New Audit..."
3. Configure:
   - Audit name: ServerSecurityAudit
   - Audit destination: File
   - File path: C:\SQLAudit\
   - Maximum file size: 100 MB
   - Maximum rollover files: 10
4. Click OK
5. Right-click the audit -> Enable

================================================================================
                    PART 2: CREATE SERVER AUDIT SPECIFICATION
================================================================================

STEP 2.1: CREATE SERVER AUDIT SPECIFICATION (T-SQL)
---------------------------------------------------

    USE master;
    GO

    -- Create server audit specification with at least 3 action groups
    CREATE SERVER AUDIT SPECIFICATION ServerAuditSpec
    FOR SERVER AUDIT ServerSecurityAudit
    ADD (BACKUP_RESTORE_GROUP),           -- Tracks backup/restore operations
    ADD (FAILED_LOGIN_GROUP),             -- Tracks failed login attempts
    ADD (LOGIN_CHANGE_PASSWORD_GROUP),    -- Tracks password changes
    ADD (SERVER_ROLE_MEMBER_CHANGE_GROUP),-- Tracks server role membership changes
    ADD (DATABASE_CHANGE_GROUP)           -- Tracks database CREATE/ALTER/DROP
    WITH (STATE = ON);
    GO

    -- Verify server audit specification
    SELECT
        a.name AS AuditName,
        s.name AS SpecificationName,
        s.is_state_enabled,
        d.audit_action_name
    FROM sys.server_audits a
    JOIN sys.server_audit_specifications s ON a.audit_guid = s.audit_guid
    JOIN sys.server_audit_specification_details d ON s.server_specification_id = d.server_specification_id
    WHERE a.name = 'ServerSecurityAudit';
    GO

AVAILABLE SERVER AUDIT ACTION GROUPS:
-------------------------------------

+------------------------------------+------------------------------------------+
| Action Group                       | Description                              |
+------------------------------------+------------------------------------------+
| BACKUP_RESTORE_GROUP               | Backup or restore commands               |
| FAILED_LOGIN_GROUP                 | Failed login attempts                    |
| SUCCESSFUL_LOGIN_GROUP             | Successful logins                        |
| LOGIN_CHANGE_PASSWORD_GROUP        | Password changes                         |
| SERVER_ROLE_MEMBER_CHANGE_GROUP    | Server role membership changes           |
| DATABASE_CHANGE_GROUP              | CREATE/ALTER/DROP database               |
| SERVER_PERMISSION_CHANGE_GROUP     | Server permission changes                |
| SERVER_PRINCIPAL_CHANGE_GROUP      | Server principal (login) changes         |
| AUDIT_CHANGE_GROUP                 | Audit configuration changes              |
| DATABASE_PERMISSION_CHANGE_GROUP   | Database permission changes              |
+------------------------------------+------------------------------------------+

================================================================================
                    PART 3: SIMULATE SERVER AUDIT EVENTS
================================================================================

STEP 3.1: SIMULATE BACKUP_RESTORE_GROUP
---------------------------------------

    -- Perform a backup to trigger audit event
    BACKUP DATABASE AdventureWorks2019
    TO DISK = 'C:\SQLBackups\AW_AuditTest.bak'
    WITH FORMAT, INIT, NAME = 'Audit Test Backup';
    GO

    PRINT 'Backup performed - should be logged in audit';
    GO

STEP 3.2: SIMULATE FAILED_LOGIN_GROUP
-------------------------------------

Method 1: Using SSMS
1. Open a new connection window
2. Enter:
   - Server: localhost
   - Authentication: SQL Server Authentication
   - Login: FakeUser
   - Password: WrongPassword
3. Click Connect
4. Login will fail - this triggers the audit event

Method 2: Using sqlcmd (Command Prompt)

    sqlcmd -S localhost -U FakeUser -P WrongPassword -Q "SELECT 1"
    :: This will fail and be logged

Method 3: Using PowerShell

    # This will fail and trigger the audit
    try {
        Invoke-Sqlcmd -ServerInstance "localhost" -Username "FakeUser" -Password "WrongPassword" -Query "SELECT 1" -ErrorAction Stop
    } catch {
        Write-Host "Login failed as expected - audit event triggered"
    }

STEP 3.3: SIMULATE LOGIN_CHANGE_PASSWORD_GROUP
----------------------------------------------

    USE master;
    GO

    -- First create a test login (if not exists)
    IF NOT EXISTS (SELECT 1 FROM sys.server_principals WHERE name = 'TestLoginForAudit')
    BEGIN
        CREATE LOGIN TestLoginForAudit WITH PASSWORD = 'OldP@ssword123!';
    END
    GO

    -- Change the password (triggers audit event)
    ALTER LOGIN TestLoginForAudit WITH PASSWORD = 'NewP@ssword456!';
    GO

    PRINT 'Password changed - should be logged in audit';
    GO

STEP 3.4: SIMULATE OTHER ACTION GROUPS (OPTIONAL)
-------------------------------------------------

    -- SERVER_ROLE_MEMBER_CHANGE_GROUP
    ALTER SERVER ROLE [bulkadmin] ADD MEMBER TestLoginForAudit;
    ALTER SERVER ROLE [bulkadmin] DROP MEMBER TestLoginForAudit;
    GO

    -- DATABASE_CHANGE_GROUP
    CREATE DATABASE AuditTestDB;
    DROP DATABASE AuditTestDB;
    GO

================================================================================
                    PART 4: CREATE DATABASE AUDIT SPECIFICATION (SSMS)
================================================================================

STEP 4.1: CREATE DATABASE AUDIT SPECIFICATION USING SSMS GUI
------------------------------------------------------------

1. In Object Explorer, connect to your SQL Server
2. Expand Databases -> AdventureWorks2019
3. Expand Security folder
4. Right-click "Database Audit Specifications"
5. Select "New Database Audit Specification..."

6. Configure the specification:
   - Name: AdventureWorksAuditSpec
   - Audit: ServerSecurityAudit (select from dropdown)

7. Add audit actions (click in the grid):

   Row 1:
   - Audit Action Type: DELETE
   - Object Class: SCHEMA
   - Object Schema: dbo
   - Object Name: (leave blank for all)
   - Principal Name: public

   Row 2:
   - Audit Action Type: UPDATE
   - Object Class: SCHEMA
   - Object Schema: Person
   - Object Name: (leave blank for all)
   - Principal Name: public

   Row 3:
   - Audit Action Type: SELECT
   - Object Class: OBJECT
   - Object Schema: HumanResources
   - Object Name: Employee
   - Principal Name: public

   Row 4:
   - Audit Action Type: INSERT
   - Object Class: SCHEMA
   - Object Schema: dbo
   - Object Name: (leave blank for all)
   - Principal Name: public

8. Click OK to create
9. Right-click the specification -> Enable

STEP 4.2: CREATE DATABASE AUDIT SPECIFICATION USING T-SQL (ALTERNATIVE)
-----------------------------------------------------------------------

    USE AdventureWorks2019;
    GO

    -- Create database audit specification
    CREATE DATABASE AUDIT SPECIFICATION AdventureWorksAuditSpec
    FOR SERVER AUDIT ServerSecurityAudit
    ADD (DELETE ON SCHEMA::dbo BY public),
    ADD (UPDATE ON SCHEMA::Person BY public),
    ADD (SELECT ON OBJECT::HumanResources.Employee BY public),
    ADD (INSERT ON SCHEMA::dbo BY public),
    ADD (UPDATE ON SCHEMA::Sales BY public),
    ADD (DELETE ON SCHEMA::Production BY public)
    WITH (STATE = ON);
    GO

    -- Verify database audit specification
    SELECT
        a.name AS AuditName,
        s.name AS SpecificationName,
        s.is_state_enabled,
        d.audit_action_name,
        d.class_desc,
        SCHEMA_NAME(d.major_id) AS SchemaName
    FROM sys.server_audits a
    JOIN sys.database_audit_specifications s ON a.audit_guid = s.audit_guid
    JOIN sys.database_audit_specification_details d ON s.database_specification_id = d.database_specification_id
    WHERE DB_NAME() = 'AdventureWorks2019';
    GO

DATABASE AUDIT ACTIONS:
----------------------

+------------------+------------------------------------------------+
| Action           | Description                                    |
+------------------+------------------------------------------------+
| SELECT           | SELECT statements                              |
| INSERT           | INSERT statements                              |
| UPDATE           | UPDATE statements                              |
| DELETE           | DELETE statements                              |
| EXECUTE          | EXECUTE statements (stored procedures)         |
| RECEIVE          | RECEIVE statements (Service Broker)            |
| REFERENCES       | REFERENCES permission checks                   |
+------------------+------------------------------------------------+

================================================================================
                    PART 5: PERFORM DATABASE ACTIONS TO TRIGGER AUDIT
================================================================================

STEP 5.1: PERFORM DELETE OPERATIONS
-----------------------------------

    USE AdventureWorks2019;
    GO

    -- Create a test table for audit testing
    CREATE TABLE dbo.AuditTestTable (
        ID INT IDENTITY PRIMARY KEY,
        Name NVARCHAR(50),
        Value NVARCHAR(100)
    );
    GO

    -- Insert test data
    INSERT INTO dbo.AuditTestTable (Name, Value)
    VALUES ('Test1', 'Value1'), ('Test2', 'Value2'), ('Test3', 'Value3');
    GO

    -- DELETE operation (should be audited)
    DELETE FROM dbo.AuditTestTable WHERE ID = 1;
    GO

    PRINT 'DELETE performed - should be logged in audit';
    GO

STEP 5.2: PERFORM UPDATE OPERATIONS
-----------------------------------

    USE AdventureWorks2019;
    GO

    -- UPDATE on dbo schema (should be audited)
    UPDATE dbo.AuditTestTable
    SET Value = 'UpdatedValue'
    WHERE ID = 2;
    GO

    -- UPDATE on Person schema (should be audited)
    UPDATE Person.Person
    SET Title = 'Mr.'
    WHERE BusinessEntityID = 1 AND Title IS NULL;
    GO

    PRINT 'UPDATE performed - should be logged in audit';
    GO

STEP 5.3: PERFORM SELECT OPERATIONS
-----------------------------------

    USE AdventureWorks2019;
    GO

    -- SELECT on HumanResources.Employee (should be audited)
    SELECT TOP 10
        BusinessEntityID,
        LoginID,
        JobTitle
    FROM HumanResources.Employee;
    GO

    PRINT 'SELECT performed - should be logged in audit';
    GO

STEP 5.4: PERFORM INSERT OPERATIONS
-----------------------------------

    USE AdventureWorks2019;
    GO

    -- INSERT on dbo schema (should be audited)
    INSERT INTO dbo.AuditTestTable (Name, Value)
    VALUES ('AuditTest', 'AuditValue');
    GO

    PRINT 'INSERT performed - should be logged in audit';
    GO

================================================================================
                    PART 6: VIEW AND ANALYZE AUDIT LOGS
================================================================================

STEP 6.1: VIEW AUDIT LOG USING SSMS GUI
---------------------------------------

Method 1: From Audit Object
1. In Object Explorer, expand Security -> Audits
2. Right-click "ServerSecurityAudit"
3. Select "View Audit Logs"
4. Log File Viewer opens with audit events

Method 2: From File
1. File -> Open -> File
2. Navigate to C:\SQLAudit\
3. Select .sqlaudit file
4. View in Log File Viewer

STEP 6.2: VIEW AUDIT LOG USING T-SQL
------------------------------------

    -- Read audit log using fn_get_audit_file function
    SELECT
        event_time,
        action_id,
        succeeded,
        session_server_principal_name AS LoginName,
        server_principal_name,
        database_name,
        schema_name,
        object_name,
        statement,
        additional_information
    FROM sys.fn_get_audit_file('C:\SQLAudit\ServerSecurityAudit*.sqlaudit', DEFAULT, DEFAULT)
    ORDER BY event_time DESC;
    GO

STEP 6.3: FILTER SPECIFIC AUDIT EVENTS
--------------------------------------

    -- View only backup/restore events
    SELECT
        event_time,
        action_id,
        statement,
        database_name
    FROM sys.fn_get_audit_file('C:\SQLAudit\*.sqlaudit', DEFAULT, DEFAULT)
    WHERE action_id IN ('BCK', 'RS')  -- BCK=Backup, RS=Restore
    ORDER BY event_time DESC;
    GO

    -- View only failed login events
    SELECT
        event_time,
        action_id,
        succeeded,
        server_principal_name,
        additional_information
    FROM sys.fn_get_audit_file('C:\SQLAudit\*.sqlaudit', DEFAULT, DEFAULT)
    WHERE action_id = 'LGFL'  -- LGFL = Login Failed
    ORDER BY event_time DESC;
    GO

    -- View only password change events
    SELECT
        event_time,
        action_id,
        server_principal_name,
        object_name,
        statement
    FROM sys.fn_get_audit_file('C:\SQLAudit\*.sqlaudit', DEFAULT, DEFAULT)
    WHERE action_id = 'PWC'  -- PWC = Password Change
    ORDER BY event_time DESC;
    GO

    -- View only DELETE/UPDATE operations
    SELECT
        event_time,
        action_id,
        database_name,
        schema_name,
        object_name,
        session_server_principal_name AS UserName,
        statement
    FROM sys.fn_get_audit_file('C:\SQLAudit\*.sqlaudit', DEFAULT, DEFAULT)
    WHERE action_id IN ('DL', 'UP')  -- DL=Delete, UP=Update
    ORDER BY event_time DESC;
    GO

COMMON ACTION_ID VALUES:
-----------------------

+----------+--------------------------------+
| ActionID | Description                    |
+----------+--------------------------------+
| BCK      | Backup                         |
| RS       | Restore                        |
| LGFL     | Login Failed                   |
| LGSD     | Login Succeeded                |
| PWC      | Password Change                |
| SRVR     | Server Role Member Change      |
| DL       | Delete                         |
| UP       | Update                         |
| SL       | Select                         |
| IN       | Insert                         |
| EX       | Execute                        |
+----------+--------------------------------+

STEP 6.4: EXPORT AUDIT LOG TO TABLE
-----------------------------------

    -- Create table to store audit logs
    USE master;
    GO

    CREATE TABLE dbo.AuditLogArchive (
        event_time DATETIME2,
        action_id VARCHAR(10),
        succeeded BIT,
        session_server_principal_name NVARCHAR(256),
        database_name NVARCHAR(256),
        schema_name NVARCHAR(256),
        object_name NVARCHAR(256),
        statement NVARCHAR(MAX)
    );
    GO

    -- Insert audit log data
    INSERT INTO dbo.AuditLogArchive
    SELECT
        event_time,
        action_id,
        succeeded,
        session_server_principal_name,
        database_name,
        schema_name,
        object_name,
        statement
    FROM sys.fn_get_audit_file('C:\SQLAudit\*.sqlaudit', DEFAULT, DEFAULT);
    GO

================================================================================
                    PART 7: TRANSPARENT DATA ENCRYPTION (TDE)
================================================================================

TDE encrypts data at rest (data files, log files, backups) using a
Database Encryption Key (DEK) protected by a certificate.

TDE HIERARCHY:
-------------

    +---------------------------+
    | Service Master Key (SMK)  |  <-- Created automatically
    | (Instance level)          |
    +-------------+-------------+
                  |
    +-------------v-------------+
    | Database Master Key (DMK) |  <-- Created in master database
    | (master database)         |
    +-------------+-------------+
                  |
    +-------------v-------------+
    |     TDE Certificate       |  <-- Protects DEK
    | (master database)         |
    +-------------+-------------+
                  |
    +-------------v-------------+
    | Database Encryption Key   |  <-- Encrypts database
    | (user database)           |
    +---------------------------+

STEP 7.1: CREATE MASTER KEY IN MASTER DATABASE
----------------------------------------------

    USE master;
    GO

    -- Check if master key exists
    SELECT * FROM sys.symmetric_keys WHERE name = '##MS_DatabaseMasterKey##';
    GO

    -- Create master key if not exists
    IF NOT EXISTS (SELECT 1 FROM sys.symmetric_keys WHERE name = '##MS_DatabaseMasterKey##')
    BEGIN
        CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'MasterKeyP@ssw0rd123!';
        PRINT 'Master key created';
    END
    ELSE
        PRINT 'Master key already exists';
    GO

STEP 7.2: CREATE CERTIFICATE FOR TDE
------------------------------------

    USE master;
    GO

    -- Create certificate for TDE
    CREATE CERTIFICATE TDECertificate
    WITH SUBJECT = 'TDE Certificate for AdventureWorks',
    EXPIRY_DATE = '2030-12-31';
    GO

    -- Verify certificate created
    SELECT
        name,
        subject,
        start_date,
        expiry_date,
        pvt_key_encryption_type_desc
    FROM sys.certificates
    WHERE name = 'TDECertificate';
    GO

STEP 7.3: BACKUP THE CERTIFICATE (CRITICAL!)
--------------------------------------------

    -- IMPORTANT: Backup certificate and private key!
    -- Without this, you cannot restore database on another server!

    USE master;
    GO

    -- Create backup directory
    EXEC xp_cmdshell 'mkdir C:\SQLCerts';
    GO

    -- Backup certificate with private key
    BACKUP CERTIFICATE TDECertificate
    TO FILE = 'C:\SQLCerts\TDECertificate.cer'
    WITH PRIVATE KEY (
        FILE = 'C:\SQLCerts\TDECertificate_PrivateKey.pvk',
        ENCRYPTION BY PASSWORD = 'CertBackupP@ssw0rd123!'
    );
    GO

    PRINT 'Certificate backed up to C:\SQLCerts\';
    PRINT 'STORE THESE FILES SECURELY!';
    GO

STEP 7.4: CREATE DATABASE ENCRYPTION KEY
----------------------------------------

    USE AdventureWorks2019;
    GO

    -- Create database encryption key
    CREATE DATABASE ENCRYPTION KEY
    WITH ALGORITHM = AES_256
    ENCRYPTION BY SERVER CERTIFICATE TDECertificate;
    GO

    -- Verify DEK created
    SELECT
        DB_NAME(database_id) AS DatabaseName,
        encryption_state,
        encryption_state_desc =
            CASE encryption_state
                WHEN 0 THEN 'No encryption key'
                WHEN 1 THEN 'Unencrypted'
                WHEN 2 THEN 'Encryption in progress'
                WHEN 3 THEN 'Encrypted'
                WHEN 4 THEN 'Key change in progress'
                WHEN 5 THEN 'Decryption in progress'
                WHEN 6 THEN 'Protection change in progress'
            END,
        percent_complete,
        key_algorithm,
        key_length
    FROM sys.dm_database_encryption_keys
    WHERE database_id = DB_ID('AdventureWorks2019');
    GO

STEP 7.5: ENABLE TDE ON DATABASE
--------------------------------

    USE master;
    GO

    -- Enable TDE
    ALTER DATABASE AdventureWorks2019
    SET ENCRYPTION ON;
    GO

    PRINT 'TDE enabled - encryption starting...';
    GO

STEP 7.6: MONITOR ENCRYPTION PROGRESS
-------------------------------------

    -- Check encryption status
    SELECT
        DB_NAME(database_id) AS DatabaseName,
        encryption_state,
        CASE encryption_state
            WHEN 0 THEN 'No encryption key'
            WHEN 1 THEN 'Unencrypted'
            WHEN 2 THEN 'Encryption in progress'
            WHEN 3 THEN 'Encrypted'
            WHEN 4 THEN 'Key change in progress'
            WHEN 5 THEN 'Decryption in progress'
            WHEN 6 THEN 'Protection change in progress'
        END AS encryption_state_desc,
        percent_complete,
        encryptor_type
    FROM sys.dm_database_encryption_keys;
    GO

    -- Wait for encryption to complete (for larger databases)
    WHILE EXISTS (
        SELECT 1 FROM sys.dm_database_encryption_keys
        WHERE database_id = DB_ID('AdventureWorks2019')
        AND encryption_state = 2
    )
    BEGIN
        WAITFOR DELAY '00:00:05';
        SELECT
            DB_NAME(database_id) AS DatabaseName,
            encryption_state,
            percent_complete
        FROM sys.dm_database_encryption_keys
        WHERE database_id = DB_ID('AdventureWorks2019');
    END
    GO

STEP 7.7: VERIFY TDE IS ACTIVE
------------------------------

    -- Check database encryption status
    SELECT
        d.name AS DatabaseName,
        d.is_encrypted,
        dek.encryption_state,
        CASE dek.encryption_state
            WHEN 3 THEN 'Encrypted'
            ELSE 'Not Encrypted'
        END AS Status,
        c.name AS CertificateName
    FROM sys.databases d
    LEFT JOIN sys.dm_database_encryption_keys dek ON d.database_id = dek.database_id
    LEFT JOIN sys.certificates c ON dek.encryptor_thumbprint = c.thumbprint
    WHERE d.name = 'AdventureWorks2019';
    GO

    -- Should show:
    -- DatabaseName         is_encrypted  encryption_state  Status     CertificateName
    -- AdventureWorks2019   1             3                 Encrypted  TDECertificate

STEP 7.8: VERIFY TEMPDB IS ALSO ENCRYPTED
-----------------------------------------

    -- When TDE is enabled on any database, tempdb is also encrypted
    SELECT
        DB_NAME(database_id) AS DatabaseName,
        encryption_state,
        CASE encryption_state WHEN 3 THEN 'Encrypted' ELSE 'Not Encrypted' END AS Status
    FROM sys.dm_database_encryption_keys
    WHERE database_id = 2;  -- tempdb
    GO

================================================================================
                    PART 8: RESTORING TDE DATABASE ON ANOTHER SERVER
================================================================================

To restore a TDE-encrypted database on another server:

STEP 8.1: ON SOURCE SERVER - Export Certificate (already done)
--------------------------------------------------------------

    -- Already done in Step 7.3
    -- Files: TDECertificate.cer, TDECertificate_PrivateKey.pvk

STEP 8.2: ON TARGET SERVER - Create Master Key
----------------------------------------------

    USE master;
    GO

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'TargetMasterKeyP@ss123!';
    GO

STEP 8.3: ON TARGET SERVER - Restore Certificate
------------------------------------------------

    USE master;
    GO

    CREATE CERTIFICATE TDECertificate
    FROM FILE = 'C:\SQLCerts\TDECertificate.cer'
    WITH PRIVATE KEY (
        FILE = 'C:\SQLCerts\TDECertificate_PrivateKey.pvk',
        DECRYPTION BY PASSWORD = 'CertBackupP@ssw0rd123!'
    );
    GO

STEP 8.4: ON TARGET SERVER - Restore Database
---------------------------------------------

    RESTORE DATABASE AdventureWorks2019
    FROM DISK = 'C:\SQLBackups\AdventureWorks2019_TDE.bak'
    WITH
        MOVE 'AdventureWorks2019' TO 'C:\SQLData\AdventureWorks2019.mdf',
        MOVE 'AdventureWorks2019_log' TO 'C:\SQLLog\AdventureWorks2019_log.ldf',
        REPLACE;
    GO

================================================================================
                    DISABLING TDE (IF NEEDED)
================================================================================

    -- Disable TDE
    ALTER DATABASE AdventureWorks2019
    SET ENCRYPTION OFF;
    GO

    -- Wait for decryption to complete
    -- Monitor with sys.dm_database_encryption_keys

    -- Remove database encryption key
    USE AdventureWorks2019;
    DROP DATABASE ENCRYPTION KEY;
    GO

================================================================================
                    CLEANUP (OPTIONAL)
================================================================================

    -- Disable audit specifications
    ALTER SERVER AUDIT SPECIFICATION ServerAuditSpec WITH (STATE = OFF);
    ALTER DATABASE AUDIT SPECIFICATION AdventureWorksAuditSpec WITH (STATE = OFF);
    GO

    -- Disable audit
    ALTER SERVER AUDIT ServerSecurityAudit WITH (STATE = OFF);
    GO

    -- Drop audit objects (if needed)
    DROP SERVER AUDIT SPECIFICATION ServerAuditSpec;
    DROP DATABASE AUDIT SPECIFICATION AdventureWorksAuditSpec;
    DROP SERVER AUDIT ServerSecurityAudit;
    GO

================================================================================
                    SUMMARY
================================================================================

AUDIT COMPONENTS CREATED:
------------------------

+-----------------------------+--------------------------------------------+
| Component                   | Purpose                                    |
+-----------------------------+--------------------------------------------+
| ServerSecurityAudit         | Server audit (writes to C:\SQLAudit\)      |
| ServerAuditSpec             | Server-level audit specification           |
|   - BACKUP_RESTORE_GROUP    | Logs backup/restore operations             |
|   - FAILED_LOGIN_GROUP      | Logs failed login attempts                 |
|   - LOGIN_CHANGE_PASSWORD   | Logs password changes                      |
+-----------------------------+--------------------------------------------+
| AdventureWorksAuditSpec     | Database-level audit specification         |
|   - DELETE on dbo           | Logs DELETE operations                     |
|   - UPDATE on Person        | Logs UPDATE operations                     |
|   - SELECT on HR.Employee   | Logs SELECT operations                     |
|   - INSERT on dbo           | Logs INSERT operations                     |
+-----------------------------+--------------------------------------------+

TDE COMPONENTS CREATED:
----------------------

+---------------------------+----------------------------------------------+
| Component                 | Location                                     |
+---------------------------+----------------------------------------------+
| Master Key                | master database                              |
| TDECertificate            | master database                              |
| Database Encryption Key   | AdventureWorks2019                           |
| Certificate Backup        | C:\SQLCerts\TDECertificate.cer               |
| Private Key Backup        | C:\SQLCerts\TDECertificate_PrivateKey.pvk    |
+---------------------------+----------------------------------------------+

KEY COMMANDS REFERENCE:
----------------------

    -- Enable Audit
    ALTER SERVER AUDIT AuditName WITH (STATE = ON);

    -- View Audit Log
    SELECT * FROM sys.fn_get_audit_file('path\*.sqlaudit', DEFAULT, DEFAULT);

    -- Enable TDE
    ALTER DATABASE DatabaseName SET ENCRYPTION ON;

    -- Check TDE Status
    SELECT * FROM sys.dm_database_encryption_keys;

    -- Backup Certificate
    BACKUP CERTIFICATE CertName TO FILE = 'path' WITH PRIVATE KEY (...);

IMPORTANT REMINDERS:
-------------------

1. Always backup TDE certificate and private key!
2. Store certificate backups securely (separate from database backups)
3. Test restore process with TDE on a different server
4. Audit logs can grow large - implement retention policy
5. TDE encrypts tempdb when enabled on any database

