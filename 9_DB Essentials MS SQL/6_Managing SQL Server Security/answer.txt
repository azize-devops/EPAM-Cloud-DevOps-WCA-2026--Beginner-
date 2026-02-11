MANAGING SQL SERVER SECURITY - ANSWER
=====================================

This guide covers SQL Server security configuration including creating
logins, users, roles, and permissions, as well as contained database
users for portable database authentication.

================================================================================
                    UNDERSTANDING SQL SERVER SECURITY
================================================================================

SECURITY HIERARCHY:
------------------

    +------------------+
    | Windows/SQL Login| <-- Server Level (Instance)
    +--------+---------+
             |
             v
    +--------+---------+
    | Database User    | <-- Database Level
    +--------+---------+
             |
             v
    +--------+---------+
    | Permissions/Roles| <-- Object Level
    +------------------+

AUTHENTICATION TYPES:
--------------------

+----------------------+-------------------------------------------------------+
| Type                 | Description                                           |
+----------------------+-------------------------------------------------------+
| Windows Auth         | Uses Windows/AD accounts. More secure, SSO support.   |
| SQL Server Auth      | Username/password stored in SQL Server. Cross-platform|
| Contained DB User    | Auth at database level. Portable with database.       |
+----------------------+-------------------------------------------------------+

================================================================================
                    PART 1: CREATE WINDOWS LOCAL ACCOUNTS
================================================================================

For service accounts, we need Windows local accounts first.

STEP 1.1: CREATE WINDOWS ACCOUNTS USING COMPUTER MANAGEMENT
-----------------------------------------------------------

1. Open Computer Management:
   - Right-click "This PC" -> Manage
   - Or run: compmgmt.msc

2. Navigate to: Local Users and Groups -> Users

3. Create Application Service Account:
   - Right-click Users -> New User...
   - User name: svc_sqlapp
   - Full name: SQL Application Service
   - Password: P@ssw0rd123! (set strong password)
   - Uncheck: User must change password at next logon
   - Check: Password never expires
   - Check: User cannot change password
   - Click Create

4. Create Maintenance Service Account:
   - User name: svc_sqlmaint
   - Full name: SQL Maintenance Service
   - Password: P@ssw0rd123!
   - Same options as above
   - Click Create

5. Click Close

STEP 1.2: CREATE WINDOWS ACCOUNTS USING POWERSHELL
--------------------------------------------------

    # Create Application Service Account
    $appPassword = ConvertTo-SecureString "P@ssw0rd123!" -AsPlainText -Force
    New-LocalUser -Name "svc_sqlapp" `
        -Password $appPassword `
        -FullName "SQL Application Service" `
        -Description "Service account for SQL application" `
        -PasswordNeverExpires `
        -UserMayNotChangePassword

    # Create Maintenance Service Account
    $maintPassword = ConvertTo-SecureString "P@ssw0rd123!" -AsPlainText -Force
    New-LocalUser -Name "svc_sqlmaint" `
        -Password $maintPassword `
        -FullName "SQL Maintenance Service" `
        -Description "Service account for SQL maintenance" `
        -PasswordNeverExpires `
        -UserMayNotChangePassword

    # Verify accounts created
    Get-LocalUser | Where-Object { $_.Name -like "svc_*" }

STEP 1.3: CREATE WINDOWS ACCOUNTS USING NET USER
------------------------------------------------

    net user svc_sqlapp P@ssw0rd123! /add /fullname:"SQL Application Service" /passwordchg:no
    net user svc_sqlmaint P@ssw0rd123! /add /fullname:"SQL Maintenance Service" /passwordchg:no

    :: Set password to never expire
    wmic useraccount where "name='svc_sqlapp'" set PasswordExpires=false
    wmic useraccount where "name='svc_sqlmaint'" set PasswordExpires=false

================================================================================
                    PART 2: CREATE SQL SERVER LOGINS
================================================================================

STEP 2.1: CREATE LOGINS FOR ALL USERS
-------------------------------------

Connect to SQL Server as administrator and run:

    USE master;
    GO

    -- ============================================================
    -- 1. CREATE DEVELOPER LOGINS (SQL Authentication) - 5 users
    -- ============================================================

    CREATE LOGIN Dev1 WITH PASSWORD = 'DevP@ss001!', CHECK_POLICY = ON;
    CREATE LOGIN Dev2 WITH PASSWORD = 'DevP@ss002!', CHECK_POLICY = ON;
    CREATE LOGIN Dev3 WITH PASSWORD = 'DevP@ss003!', CHECK_POLICY = ON;
    CREATE LOGIN Dev4 WITH PASSWORD = 'DevP@ss004!', CHECK_POLICY = ON;
    CREATE LOGIN Dev5 WITH PASSWORD = 'DevP@ss005!', CHECK_POLICY = ON;
    GO

    -- ============================================================
    -- 2. CREATE APPLICATION SERVICE LOGIN (Windows Authentication)
    -- ============================================================

    -- Replace YOURSERVER with your actual computer name
    CREATE LOGIN [YOURSERVER\svc_sqlapp] FROM WINDOWS;
    GO

    -- ============================================================
    -- 3. CREATE MAINTENANCE SERVICE LOGIN (Windows Authentication)
    -- ============================================================

    CREATE LOGIN [YOURSERVER\svc_sqlmaint] FROM WINDOWS;
    GO

    -- ============================================================
    -- 4. CREATE BACKUP USER LOGIN (SQL Authentication)
    -- ============================================================

    CREATE LOGIN BackupUser WITH PASSWORD = 'BackupP@ss123!', CHECK_POLICY = ON;
    GO

    -- ============================================================
    -- 5. CREATE QA USER LOGINS (SQL Authentication) - 2 users
    -- ============================================================

    CREATE LOGIN QA1 WITH PASSWORD = 'QAP@ss001!', CHECK_POLICY = ON;
    CREATE LOGIN QA2 WITH PASSWORD = 'QAP@ss002!', CHECK_POLICY = ON;
    GO

    -- Verify all logins created
    SELECT name, type_desc, create_date, is_disabled
    FROM sys.server_principals
    WHERE type IN ('S', 'U')  -- S=SQL Login, U=Windows Login
    AND name NOT LIKE '##%'   -- Exclude system accounts
    ORDER BY create_date DESC;
    GO

NOTE: Replace YOURSERVER with your actual computer name.
Find it with: SELECT SERVERPROPERTY('MachineName');

================================================================================
                    PART 3: CREATE DATABASE USERS AND ROLES
================================================================================

STEP 3.1: CREATE CUSTOM TABLE FOR APPLICATION SERVICE
-----------------------------------------------------

First, create a table that the application service will use:

    USE AdventureWorks2019;  -- Or your AdventureWorks database name
    GO

    -- Create a custom table for application service
    CREATE TABLE dbo.ApplicationData (
        DataID INT IDENTITY(1,1) PRIMARY KEY,
        DataKey NVARCHAR(50) NOT NULL,
        DataValue NVARCHAR(MAX),
        CreatedDate DATETIME DEFAULT GETDATE(),
        ModifiedDate DATETIME DEFAULT GETDATE()
    );
    GO

    -- Insert sample data
    INSERT INTO dbo.ApplicationData (DataKey, DataValue)
    VALUES
        ('Config1', 'Value1'),
        ('Config2', 'Value2'),
        ('Setting1', 'SettingValue1');
    GO

STEP 3.2: CREATE DATABASE USERS
-------------------------------

    USE AdventureWorks2019;
    GO

    -- ============================================================
    -- 1. CREATE DEVELOPER USERS
    -- ============================================================

    CREATE USER Dev1User FOR LOGIN Dev1;
    CREATE USER Dev2User FOR LOGIN Dev2;
    CREATE USER Dev3User FOR LOGIN Dev3;
    CREATE USER Dev4User FOR LOGIN Dev4;
    CREATE USER Dev5User FOR LOGIN Dev5;
    GO

    -- ============================================================
    -- 2. CREATE APPLICATION SERVICE USER
    -- ============================================================

    CREATE USER AppServiceUser FOR LOGIN [YOURSERVER\svc_sqlapp];
    GO

    -- ============================================================
    -- 3. CREATE MAINTENANCE SERVICE USER
    -- ============================================================

    CREATE USER MaintServiceUser FOR LOGIN [YOURSERVER\svc_sqlmaint];
    GO

    -- ============================================================
    -- 4. CREATE BACKUP USER (needs user in database for DENY)
    -- ============================================================

    CREATE USER BackupUserDB FOR LOGIN BackupUser;
    GO

    -- ============================================================
    -- 5. CREATE QA USERS
    -- ============================================================

    CREATE USER QA1User FOR LOGIN QA1;
    CREATE USER QA2User FOR LOGIN QA2;
    GO

    -- Verify all users created
    SELECT name, type_desc, create_date
    FROM sys.database_principals
    WHERE type IN ('S', 'U')
    AND name NOT IN ('dbo', 'guest', 'INFORMATION_SCHEMA', 'sys')
    ORDER BY create_date DESC;
    GO

STEP 3.3: CREATE CUSTOM DATABASE ROLES
--------------------------------------

    USE AdventureWorks2019;
    GO

    -- ============================================================
    -- CREATE CUSTOM ROLES FOR BETTER PERMISSION MANAGEMENT
    -- ============================================================

    -- Role for Developers (read/write all data)
    CREATE ROLE DeveloperRole;
    GO

    -- Role for Application Service (specific table only)
    CREATE ROLE AppServiceRole;
    GO

    -- Role for Maintenance (modify DB, backup, no delete)
    CREATE ROLE MaintenanceRole;
    GO

    -- Role for QA (read only)
    CREATE ROLE QARole;
    GO

    -- Verify roles created
    SELECT name, type_desc
    FROM sys.database_principals
    WHERE type = 'R'
    AND is_fixed_role = 0
    ORDER BY name;
    GO

================================================================================
                    PART 4: ASSIGN PERMISSIONS
================================================================================

STEP 4.1: DEVELOPER PERMISSIONS (Read/Write All Data)
-----------------------------------------------------

    USE AdventureWorks2019;
    GO

    -- Grant read and write permissions to DeveloperRole
    GRANT SELECT, INSERT, UPDATE, DELETE ON SCHEMA::dbo TO DeveloperRole;
    GRANT SELECT, INSERT, UPDATE, DELETE ON SCHEMA::Person TO DeveloperRole;
    GRANT SELECT, INSERT, UPDATE, DELETE ON SCHEMA::Production TO DeveloperRole;
    GRANT SELECT, INSERT, UPDATE, DELETE ON SCHEMA::Purchasing TO DeveloperRole;
    GRANT SELECT, INSERT, UPDATE, DELETE ON SCHEMA::Sales TO DeveloperRole;
    GRANT SELECT, INSERT, UPDATE, DELETE ON SCHEMA::HumanResources TO DeveloperRole;
    GO

    -- Add all developer users to the role
    ALTER ROLE DeveloperRole ADD MEMBER Dev1User;
    ALTER ROLE DeveloperRole ADD MEMBER Dev2User;
    ALTER ROLE DeveloperRole ADD MEMBER Dev3User;
    ALTER ROLE DeveloperRole ADD MEMBER Dev4User;
    ALTER ROLE DeveloperRole ADD MEMBER Dev5User;
    GO

STEP 4.2: APPLICATION SERVICE PERMISSIONS (Specific Table Only)
---------------------------------------------------------------

    USE AdventureWorks2019;
    GO

    -- Grant permissions ONLY on ApplicationData table
    GRANT SELECT, INSERT, UPDATE, DELETE ON dbo.ApplicationData TO AppServiceRole;
    GO

    -- DENY access to all other tables (explicit deny for security)
    DENY SELECT, INSERT, UPDATE, DELETE ON SCHEMA::Person TO AppServiceRole;
    DENY SELECT, INSERT, UPDATE, DELETE ON SCHEMA::Production TO AppServiceRole;
    DENY SELECT, INSERT, UPDATE, DELETE ON SCHEMA::Purchasing TO AppServiceRole;
    DENY SELECT, INSERT, UPDATE, DELETE ON SCHEMA::Sales TO AppServiceRole;
    DENY SELECT, INSERT, UPDATE, DELETE ON SCHEMA::HumanResources TO AppServiceRole;
    GO

    -- Add application service user to the role
    ALTER ROLE AppServiceRole ADD MEMBER AppServiceUser;
    GO

STEP 4.3: MAINTENANCE SERVICE PERMISSIONS (Modify DB, Backup, No Delete)
------------------------------------------------------------------------

    USE AdventureWorks2019;
    GO

    -- Grant ALTER on database (for modifications)
    GRANT ALTER ON DATABASE::AdventureWorks2019 TO MaintServiceUser;
    GO

    -- Grant backup permissions
    GRANT BACKUP DATABASE TO MaintServiceUser;
    GRANT BACKUP LOG TO MaintServiceUser;
    GO

    -- Grant ability to create/alter objects
    GRANT CREATE TABLE TO MaintServiceUser;
    GRANT CREATE VIEW TO MaintServiceUser;
    GRANT CREATE PROCEDURE TO MaintServiceUser;
    GRANT CREATE FUNCTION TO MaintServiceUser;
    GO

    -- Grant data modification (for maintenance tasks)
    ALTER ROLE db_datareader ADD MEMBER MaintServiceUser;
    ALTER ROLE db_datawriter ADD MEMBER MaintServiceUser;
    GO

    -- DENY dropping the database (at server level)
    USE master;
    GO
    DENY ALTER ANY DATABASE TO [YOURSERVER\svc_sqlmaint];
    GO

    -- Note: The maintenance user CAN modify the database structure
    -- but CANNOT drop the entire database

STEP 4.4: BACKUP USER PERMISSIONS (Backup All, No Read User DB)
---------------------------------------------------------------

    USE master;
    GO

    -- Grant backup permissions on ALL databases at server level
    -- Add to server role
    ALTER SERVER ROLE [##MS_DatabaseConnector##] ADD MEMBER BackupUser;
    GO

    -- Or grant specific permissions
    GRANT BACKUP ANY DATABASE TO BackupUser;
    GO

    -- In User DB (AdventureWorks), deny data reading
    USE AdventureWorks2019;
    GO

    -- Deny all data access
    DENY SELECT ON SCHEMA::dbo TO BackupUserDB;
    DENY SELECT ON SCHEMA::Person TO BackupUserDB;
    DENY SELECT ON SCHEMA::Production TO BackupUserDB;
    DENY SELECT ON SCHEMA::Purchasing TO BackupUserDB;
    DENY SELECT ON SCHEMA::Sales TO BackupUserDB;
    DENY SELECT ON SCHEMA::HumanResources TO BackupUserDB;
    GO

    -- But allow backup
    GRANT BACKUP DATABASE TO BackupUserDB;
    GRANT BACKUP LOG TO BackupUserDB;
    GO

STEP 4.5: QA USER PERMISSIONS (Read Only)
-----------------------------------------

    USE AdventureWorks2019;
    GO

    -- Add to built-in db_datareader role (read-only)
    ALTER ROLE db_datareader ADD MEMBER QA1User;
    ALTER ROLE db_datareader ADD MEMBER QA2User;
    GO

    -- Or use custom role
    GRANT SELECT ON SCHEMA::dbo TO QARole;
    GRANT SELECT ON SCHEMA::Person TO QARole;
    GRANT SELECT ON SCHEMA::Production TO QARole;
    GRANT SELECT ON SCHEMA::Purchasing TO QARole;
    GRANT SELECT ON SCHEMA::Sales TO QARole;
    GRANT SELECT ON SCHEMA::HumanResources TO QARole;
    GO

    ALTER ROLE QARole ADD MEMBER QA1User;
    ALTER ROLE QARole ADD MEMBER QA2User;
    GO

STEP 4.6: SUMMARY OF ALL PERMISSIONS
------------------------------------

    -- View all permissions in the database
    USE AdventureWorks2019;
    GO

    SELECT
        dp.name AS UserName,
        dp.type_desc AS UserType,
        o.name AS ObjectName,
        p.permission_name,
        p.state_desc AS PermissionState
    FROM sys.database_permissions p
    JOIN sys.database_principals dp ON p.grantee_principal_id = dp.principal_id
    LEFT JOIN sys.objects o ON p.major_id = o.object_id
    WHERE dp.name NOT IN ('public', 'guest', 'dbo')
    ORDER BY dp.name, o.name;
    GO

    -- View role memberships
    SELECT
        r.name AS RoleName,
        m.name AS MemberName
    FROM sys.database_role_members rm
    JOIN sys.database_principals r ON rm.role_principal_id = r.principal_id
    JOIN sys.database_principals m ON rm.member_principal_id = m.principal_id
    WHERE r.is_fixed_role = 0
    ORDER BY r.name, m.name;
    GO

================================================================================
                    PART 5: VERIFY ACCESS FOR EACH USER
================================================================================

STEP 5.1: TEST DEVELOPER ACCESS (Dev1)
--------------------------------------

Connect to SQL Server as Dev1:
- Server: localhost
- Authentication: SQL Server Authentication
- Login: Dev1
- Password: DevP@ss001!

    -- Test read access
    USE AdventureWorks2019;
    SELECT TOP 5 * FROM Person.Person;
    -- Should succeed

    -- Test write access
    INSERT INTO Person.PersonPhone (BusinessEntityID, PhoneNumber, PhoneNumberTypeID, ModifiedDate)
    VALUES (1, '555-TEST-DEV', 1, GETDATE());
    -- Should succeed (or fail due to constraints, but permission granted)

    -- Test delete
    DELETE FROM Person.PersonPhone WHERE PhoneNumber = '555-TEST-DEV';
    -- Should succeed

STEP 5.2: TEST APPLICATION SERVICE ACCESS
-----------------------------------------

Connect using Windows Authentication as svc_sqlapp:
(Use runas or login as that Windows user)

    runas /user:YOURSERVER\svc_sqlapp "ssms.exe"

    -- Test access to ApplicationData table
    USE AdventureWorks2019;
    SELECT * FROM dbo.ApplicationData;
    -- Should succeed

    INSERT INTO dbo.ApplicationData (DataKey, DataValue) VALUES ('Test', 'TestValue');
    -- Should succeed

    -- Test access to other tables (should fail)
    SELECT TOP 5 * FROM Person.Person;
    -- Error: SELECT permission denied

STEP 5.3: TEST MAINTENANCE SERVICE ACCESS
-----------------------------------------

Connect as svc_sqlmaint:

    -- Test backup
    BACKUP DATABASE AdventureWorks2019
    TO DISK = 'C:\SQLBackups\AW_MaintTest.bak'
    WITH INIT;
    -- Should succeed

    -- Test creating table
    USE AdventureWorks2019;
    CREATE TABLE dbo.MaintTestTable (ID INT);
    -- Should succeed

    DROP TABLE dbo.MaintTestTable;
    -- Should succeed

    -- Test dropping database (should fail)
    DROP DATABASE AdventureWorks2019;
    -- Error: Permission denied

STEP 5.4: TEST BACKUP USER ACCESS
---------------------------------

Connect as BackupUser:

    -- Test backup (should succeed)
    BACKUP DATABASE AdventureWorks2019
    TO DISK = 'C:\SQLBackups\AW_BackupUserTest.bak'
    WITH INIT;
    -- Should succeed

    BACKUP DATABASE master
    TO DISK = 'C:\SQLBackups\Master_BackupUserTest.bak'
    WITH INIT;
    -- Should succeed

    -- Test reading data (should fail)
    USE AdventureWorks2019;
    SELECT TOP 5 * FROM Person.Person;
    -- Error: SELECT permission denied

STEP 5.5: TEST QA USER ACCESS
-----------------------------

Connect as QA1:

    -- Test read access
    USE AdventureWorks2019;
    SELECT TOP 5 * FROM Person.Person;
    -- Should succeed

    SELECT COUNT(*) FROM Sales.SalesOrderHeader;
    -- Should succeed

    -- Test write access (should fail)
    INSERT INTO Person.PersonPhone (BusinessEntityID, PhoneNumber, PhoneNumberTypeID, ModifiedDate)
    VALUES (1, '555-QA-TEST', 1, GETDATE());
    -- Error: INSERT permission denied

    DELETE FROM Person.Person WHERE BusinessEntityID = 1;
    -- Error: DELETE permission denied

STEP 5.6: VERIFICATION SCRIPT (RUN AS EACH USER)
------------------------------------------------

    -- Comprehensive permission test script
    -- Run this when connected as each user

    PRINT 'Current User: ' + SUSER_NAME();
    PRINT 'Database: ' + DB_NAME();
    PRINT '';

    -- Test SELECT
    BEGIN TRY
        EXEC('SELECT TOP 1 * FROM Person.Person');
        PRINT 'SELECT on Person.Person: ALLOWED';
    END TRY
    BEGIN CATCH
        PRINT 'SELECT on Person.Person: DENIED';
    END CATCH

    -- Test INSERT
    BEGIN TRY
        EXEC('INSERT INTO dbo.ApplicationData (DataKey, DataValue) VALUES (''Test'', ''Test'')');
        EXEC('DELETE FROM dbo.ApplicationData WHERE DataKey = ''Test'' AND DataValue = ''Test''');
        PRINT 'INSERT on ApplicationData: ALLOWED';
    END TRY
    BEGIN CATCH
        PRINT 'INSERT on ApplicationData: DENIED';
    END CATCH

    -- Test BACKUP
    BEGIN TRY
        BACKUP DATABASE AdventureWorks2019 TO DISK = 'NUL';  -- NUL discards output
        PRINT 'BACKUP DATABASE: ALLOWED';
    END TRY
    BEGIN CATCH
        PRINT 'BACKUP DATABASE: DENIED';
    END CATCH
    GO

================================================================================
                    PART 6: CREATE CONTAINED DATABASE (LabDB)
================================================================================

STEP 6.1: ENABLE CONTAINED DATABASE AUTHENTICATION
--------------------------------------------------

    USE master;
    GO

    -- Enable contained database authentication at server level
    EXEC sp_configure 'contained database authentication', 1;
    RECONFIGURE;
    GO

    -- Verify setting
    EXEC sp_configure 'contained database authentication';
    -- run_value should be 1

STEP 6.2: CREATE LABDB AS CONTAINED DATABASE
--------------------------------------------

    USE master;
    GO

    -- Create database with CONTAINMENT = PARTIAL
    CREATE DATABASE LabDB
    CONTAINMENT = PARTIAL;
    GO

    -- Verify containment
    SELECT name, containment, containment_desc
    FROM sys.databases
    WHERE name = 'LabDB';
    -- Should show: PARTIAL

STEP 6.3: CREATE CONTAINED DATABASE USER
----------------------------------------

    USE LabDB;
    GO

    -- Create contained user (no server login required!)
    CREATE USER LabUser WITH PASSWORD = 'LabUserP@ss123!';
    GO

    -- Grant permissions to LabUser
    GRANT SELECT, INSERT, UPDATE, DELETE TO LabUser;
    GRANT CREATE TABLE TO LabUser;
    GO

    -- Add to db_owner for full access (optional)
    -- ALTER ROLE db_owner ADD MEMBER LabUser;
    -- GO

    -- Verify user is contained (no login_sid link)
    SELECT
        name,
        type_desc,
        authentication_type,
        authentication_type_desc
    FROM sys.database_principals
    WHERE name = 'LabUser';
    -- authentication_type_desc should be: DATABASE

STEP 6.4: CREATE TEST DATA IN LABDB
-----------------------------------

    USE LabDB;
    GO

    -- Create a test table
    CREATE TABLE dbo.TestData (
        ID INT IDENTITY PRIMARY KEY,
        Name NVARCHAR(100),
        Value NVARCHAR(MAX),
        Created DATETIME DEFAULT GETDATE()
    );
    GO

    -- Insert test data
    INSERT INTO dbo.TestData (Name, Value)
    VALUES
        ('Item1', 'Value1'),
        ('Item2', 'Value2'),
        ('Item3', 'Value3');
    GO

    -- Grant access to LabUser
    GRANT SELECT, INSERT, UPDATE, DELETE ON dbo.TestData TO LabUser;
    GO

STEP 6.5: TEST CONTAINED USER CONNECTION
----------------------------------------

Connect to SQL Server using contained user:
- Server: localhost
- Authentication: SQL Server Authentication
- Login: LabUser
- Password: LabUserP@ss123!
- Options -> Connection Properties -> Connect to database: LabDB

IMPORTANT: You MUST specify the database name when connecting with
a contained user, as they don't have a server-level login!

    -- Once connected as LabUser
    SELECT SUSER_NAME() AS LoginName, USER_NAME() AS UserName;
    SELECT * FROM dbo.TestData;

================================================================================
                    PART 7: MIGRATE DATABASE TO INSTANCE2
================================================================================

STEP 7.1: ENSURE INSTANCE2 SUPPORTS CONTAINED DATABASES
-------------------------------------------------------

On Instance2:

    USE master;
    GO

    -- Enable contained database authentication
    EXEC sp_configure 'contained database authentication', 1;
    RECONFIGURE;
    GO

STEP 7.2: BACKUP LABDB ON INSTANCE1
-----------------------------------

    -- On Instance1
    BACKUP DATABASE LabDB
    TO DISK = 'C:\SQLBackups\LabDB_Migration.bak'
    WITH FORMAT, INIT, COMPRESSION;
    GO

STEP 7.3: RESTORE LABDB ON INSTANCE2
------------------------------------

    -- On Instance2
    USE master;
    GO

    -- Restore the database
    RESTORE DATABASE LabDB
    FROM DISK = 'C:\SQLBackups\LabDB_Migration.bak'
    WITH
        MOVE 'LabDB' TO 'C:\SQLData\LabDB.mdf',
        MOVE 'LabDB_log' TO 'C:\SQLLog\LabDB_log.ldf',
        REPLACE;
    GO

    -- Verify containment is preserved
    SELECT name, containment, containment_desc
    FROM sys.databases
    WHERE name = 'LabDB';
    -- Should show: PARTIAL

STEP 7.4: VERIFY CONTAINED USER ON INSTANCE2
--------------------------------------------

    USE LabDB;
    GO

    -- Check if LabUser exists
    SELECT
        name,
        type_desc,
        authentication_type_desc
    FROM sys.database_principals
    WHERE name = 'LabUser';
    -- Should exist and show DATABASE authentication

STEP 7.5: CONNECT TO INSTANCE2 AS LABUSER
-----------------------------------------

Connect to Instance2 with contained user:
- Server: localhost\YOURINSTANCE (named instance)
- Authentication: SQL Server Authentication
- Login: LabUser
- Password: LabUserP@ss123!
- Options -> Connection Properties -> Connect to database: LabDB

    -- Verify connection
    SELECT
        @@SERVERNAME AS ServerName,
        DB_NAME() AS DatabaseName,
        SUSER_NAME() AS LoginName,
        USER_NAME() AS UserName;

    -- Test data access
    SELECT * FROM dbo.TestData;

    -- Test insert
    INSERT INTO dbo.TestData (Name, Value) VALUES ('Instance2Test', 'Works!');
    SELECT * FROM dbo.TestData;

KEY POINT: No server-level login was created on Instance2!
The LabUser authentication is entirely contained within the database.

================================================================================
                    ALTERNATIVE MIGRATION METHODS
================================================================================

METHOD 1: DETACH/ATTACH
-----------------------

    -- On Instance1
    USE master;
    ALTER DATABASE LabDB SET SINGLE_USER WITH ROLLBACK IMMEDIATE;
    EXEC sp_detach_db 'LabDB';

    -- Copy files to Instance2 data folders
    -- Then on Instance2:
    CREATE DATABASE LabDB ON
        (FILENAME = 'C:\SQLData\LabDB.mdf'),
        (FILENAME = 'C:\SQLLog\LabDB_log.ldf')
    FOR ATTACH;

METHOD 2: USING DATA MIGRATION ASSISTANT
----------------------------------------

1. Open Data Migration Assistant
2. Create new Migration project
3. Source: Instance1, Database: LabDB
4. Target: Instance2
5. Migrate schema and data
6. Containment settings preserved

================================================================================
                    SUMMARY
================================================================================

USERS AND PERMISSIONS CREATED:
-----------------------------

+-------------------+--------------------+----------------------------------+
| User              | Auth Type          | Permissions                      |
+-------------------+--------------------+----------------------------------+
| Dev1-Dev5         | SQL Authentication | Read/Write all data              |
+-------------------+--------------------+----------------------------------+
| svc_sqlapp        | Windows Auth       | R/W only ApplicationData table   |
+-------------------+--------------------+----------------------------------+
| svc_sqlmaint      | Windows Auth       | Modify DB, Backup, No Delete     |
+-------------------+--------------------+----------------------------------+
| BackupUser        | SQL Authentication | Backup all DBs, No read data     |
+-------------------+--------------------+----------------------------------+
| QA1, QA2          | SQL Authentication | Read-only all data               |
+-------------------+--------------------+----------------------------------+
| LabUser           | Contained          | Full access to LabDB (portable)  |
+-------------------+--------------------+----------------------------------+

CONTAINED DATABASE BENEFITS:
---------------------------

1. Portability: Database can be moved without recreating logins
2. Self-contained: All auth info stored within database
3. No orphaned users: Users travel with database
4. Easier migration: No server-level dependencies

SECURITY BEST PRACTICES:
-----------------------

1. Use Windows Authentication when possible
2. Use roles for permission management
3. Follow principle of least privilege
4. Use DENY for explicit restrictions
5. Regular permission audits
6. Use contained databases for portable solutions

KEY COMMANDS REFERENCE:
----------------------

    -- Create SQL Login
    CREATE LOGIN name WITH PASSWORD = 'password';

    -- Create Windows Login
    CREATE LOGIN [DOMAIN\user] FROM WINDOWS;

    -- Create Database User
    CREATE USER username FOR LOGIN loginname;

    -- Create Contained User
    CREATE USER username WITH PASSWORD = 'password';

    -- Grant Permission
    GRANT SELECT ON schema::SchemaName TO username;

    -- Deny Permission
    DENY DELETE ON tablename TO username;

    -- Create Role
    CREATE ROLE rolename;

    -- Add to Role
    ALTER ROLE rolename ADD MEMBER username;

    -- Enable Contained DB
    EXEC sp_configure 'contained database authentication', 1;
