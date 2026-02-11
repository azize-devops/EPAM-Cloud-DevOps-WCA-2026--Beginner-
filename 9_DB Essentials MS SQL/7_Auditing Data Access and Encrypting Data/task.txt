AUDITING DATA ACCESS AND ENCRYPTING DATA - TASK
================================================

GOALS:
------
- Learn to configure SQL Server Audit for security monitoring
- Understand server-level vs database-level audit specifications
- Create and test audit action groups
- Monitor and review audit logs
- Implement Transparent Data Encryption (TDE) for data-at-rest protection

PREREQUISITES:
--------------
- 1 VM with SQL Server 2019 instance
- AdventureWorks database restored
- SQL Server Management Studio (SSMS)
- Administrator access to SQL Server

TASKS:
------

PART 1: SQL SERVER AUDIT SETUP
-----------------------------

1. Create a SQL Server Audit:
   - Define audit destination (file, Windows Application Log, or Security Log)
   - Configure audit settings (max file size, rollover, etc.)
   - Enable the audit

2. Create Server Audit Specification (using T-SQL):
   - Add at least 3 action groups, for example:
     * BACKUP_RESTORE_GROUP - Tracks backup/restore operations
     * FAILED_LOGIN_GROUP - Tracks failed login attempts
     * LOGIN_CHANGE_PASSWORD_GROUP - Tracks password changes
   - Enable the server audit specification

3. Simulate Actions to Trigger Audit Events:
   - Perform actions that match the defined action groups
   - For BACKUP_RESTORE_GROUP: Create a backup
   - For FAILED_LOGIN_GROUP: Attempt login with wrong password
   - For LOGIN_CHANGE_PASSWORD_GROUP: Change a login's password

PART 2: DATABASE AUDIT SPECIFICATION
-----------------------------------

4. Create Database Audit Specification (using SSMS):
   - Open AdventureWorks database
   - Create database audit specification
   - Configure auditing for database actions:
     * DELETE operations
     * UPDATE operations
     * SELECT operations (optional)
     * INSERT operations (optional)

5. Perform Database Actions:
   - Execute DELETE statements on AdventureWorks tables
   - Execute UPDATE statements on AdventureWorks tables
   - Verify actions are being captured

PART 3: REVIEW AUDIT LOGS
------------------------

6. Open and Review Audit Logs:
   - Locate the audit log files
   - View audit records in SSMS
   - Verify all performed actions are logged
   - Note: Enable audit before testing if not already enabled

PART 4: TRANSPARENT DATA ENCRYPTION
----------------------------------

7. Enable TDE for AdventureWorks Database:
   - Create a master key in master database
   - Create a certificate for TDE
   - Create a database encryption key
   - Enable encryption on the database
   - Verify encryption status
   - Backup the certificate (important for recovery!)

REQUIREMENTS:
-------------
1. SQL Server Audit must be created and enabled
2. Server Audit Specification must include at least 3 action groups
3. Actions must be simulated for each defined action group
4. Database Audit Specification must be created using SSMS GUI
5. Database actions (DELETE, UPDATE) must be audited
6. All actions must appear in audit logs
7. TDE must be enabled for AdventureWorks database
8. TDE certificate must be backed up

SELF-REVIEW:
------------
- SQL Server Audit is created and enabled
- Server Audit Specification with 3+ action groups is active
- Backup operation is logged (BACKUP_RESTORE_GROUP)
- Failed login attempt is logged (FAILED_LOGIN_GROUP)
- Password change is logged (LOGIN_CHANGE_PASSWORD_GROUP)
- Database Audit Specification is created via SSMS
- DELETE operations are logged
- UPDATE operations are logged
- Audit log file can be opened and viewed
- All test actions appear in the log
- TDE master key is created
- TDE certificate is created and backed up
- Database encryption key is created
- AdventureWorks shows encryption_state = 3 (Encrypted)

HELPFUL MATERIALS:
------------------
https://docs.microsoft.com/en-us/sql/relational-databases/security/auditing/sql-server-audit-database-engine
https://docs.microsoft.com/en-us/sql/relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification
https://docs.microsoft.com/en-us/sql/relational-databases/security/auditing/sql-server-audit-action-groups-and-actions
https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/transparent-data-encryption
https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server
