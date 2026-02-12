# MONITORING SQL SERVER USING ALERTS AND NOTIFICATIONS - ANSWER

This guide covers configuring Database Mail, creating operators and alerts,
and setting up email notifications for SQL Server events like deadlocks.

================================================================================
                    UNDERSTANDING SQL SERVER ALERTING
================================================================================

ALERT SYSTEM ARCHITECTURE:
-------------------------

    +------------------+
    | SQL Server Error |
    | or Performance   |
    | Condition        |
    +--------+---------+
             |
             v
    +--------+---------+
    | Windows Event    |  <-- Error logged to event log (WITH_LOG)
    |      Log         |
    +--------+---------+
             |
             v
    +--------+---------+
    | SQL Server Agent |  <-- Monitors event log
    |    Alert         |
    +--------+---------+
             |
             v
    +--------+---------+
    | Operator         |  <-- Email notification
    | (Email/Pager)    |
    +--------+---------+
             |
             v
    +--------+---------+
    |  Database Mail   |  <-- Sends email via SMTP
    +------------------+

ALERT TYPES:
-----------

+----------------------+------------------------------------------------+
| Alert Type           | Description                                    |
+----------------------+------------------------------------------------+
| SQL Server Event     | Triggered by specific error numbers or         |
|                      | severity levels                                |
+----------------------+------------------------------------------------+
| Performance Condition| Triggered when performance counter exceeds     |
|                      | threshold                                      |
+----------------------+------------------------------------------------+
| WMI Event            | Triggered by WMI events                        |
+----------------------+------------------------------------------------+

================================================================================
                    PART 1: CONFIGURE DATABASE MAIL
================================================================================

STEP 1.1: ENABLE DATABASE MAIL FEATURE
--------------------------------------

    USE master;
    GO

    -- Enable Database Mail XPs
    EXEC sp_configure 'show advanced options', 1;
    RECONFIGURE;
    GO

    EXEC sp_configure 'Database Mail XPs', 1;
    RECONFIGURE;
    GO

    -- Verify it's enabled
    EXEC sp_configure 'Database Mail XPs';
    GO

STEP 1.2: CREATE DATABASE MAIL PROFILE USING T-SQL
--------------------------------------------------

    USE msdb;
    GO

    -- Create Database Mail account
    EXEC msdb.dbo.sysmail_add_account_sp
        @account_name = 'SQLMailAccount',
        @description = 'Mail account for SQL Server notifications',
        @email_address = 'your-email@domain.com',        -- Your email address
        @display_name = 'SQL Server Alerts',
        @replyto_address = 'your-email@domain.com',
        @mailserver_name = 'smtp.office365.com',          -- SMTP server
        @mailserver_type = 'SMTP',
        @port = 587,                                       -- Port number
        @enable_ssl = 1,                                   -- Enable SSL
        @username = 'your-email@domain.com',              -- SMTP username
        @password = 'YourEmailPassword';                   -- SMTP password
    GO

    -- Create Database Mail profile
    EXEC msdb.dbo.sysmail_add_profile_sp
        @profile_name = 'SQLAlertProfile',
        @description = 'Profile for SQL Server alert notifications';
    GO

    -- Add account to profile
    EXEC msdb.dbo.sysmail_add_profileaccount_sp
        @profile_name = 'SQLAlertProfile',
        @account_name = 'SQLMailAccount',
        @sequence_number = 1;
    GO

    -- Grant permission to use profile (for public or specific user)
    EXEC msdb.dbo.sysmail_add_principalprofile_sp
        @profile_name = 'SQLAlertProfile',
        @principal_name = 'public',
        @is_default = 1;
    GO

    PRINT 'Database Mail configured successfully!';
    GO

SMTP SERVER SETTINGS:
--------------------

+----------------------+------------------+------+-----+
| Provider             | SMTP Server      | Port | SSL |
+----------------------+------------------+------+-----+
| Office 365           | smtp.office365.com| 587 | Yes |
| Gmail                | smtp.gmail.com   | 587  | Yes |
| Outlook.com          | smtp-mail.outlook.com| 587| Yes |
| Yahoo                | smtp.mail.yahoo.com| 587| Yes |
+----------------------+------------------+------+-----+

NOTE: Gmail may require "App Password" if 2FA is enabled.
Office 365 may require "Less secure apps" or App Password.

STEP 1.3: CREATE DATABASE MAIL USING SSMS GUI
---------------------------------------------

1. In Object Explorer, expand Management
2. Right-click "Database Mail" -> "Configure Database Mail"
3. Select "Set up Database Mail..."
4. Click Next

5. Profile Name page:
   - Profile name: SQLAlertProfile
   - Description: Profile for SQL Server alert notifications
   - Click Add to add SMTP account

6. New Database Mail Account:
   - Account name: SQLMailAccount
   - Description: Mail account for SQL Server notifications
   - E-mail address: your-email@domain.com
   - Display name: SQL Server Alerts
   - Reply e-mail: your-email@domain.com
   - Server name: smtp.office365.com
   - Port number: 587
   - Check "This server requires a secure connection (SSL)"
   - SMTP Authentication: Basic authentication
   - User name: your-email@domain.com
   - Password: YourEmailPassword
   - Confirm password: YourEmailPassword
   - Click OK

7. Manage Profile Security:
   - Check "Public" and set "Default Profile" to Yes
   - Click Next

8. Configure System Parameters:
   - Leave defaults or adjust as needed
   - Click Next

9. Complete the Wizard -> Click Finish

STEP 1.4: TEST DATABASE MAIL
----------------------------

    -- Send a test email
    EXEC msdb.dbo.sp_send_dbmail
        @profile_name = 'SQLAlertProfile',
        @recipients = 'your-email@domain.com',
        @subject = 'SQL Server Database Mail Test',
        @body = 'This is a test email from SQL Server Database Mail.',
        @body_format = 'TEXT';
    GO

    -- Check mail status
    SELECT
        sent_status,
        send_request_date,
        recipients,
        subject
    FROM msdb.dbo.sysmail_allitems
    ORDER BY send_request_date DESC;
    GO

    -- Check for errors
    SELECT *
    FROM msdb.dbo.sysmail_event_log
    ORDER BY log_date DESC;
    GO

================================================================================
                    PART 2: CONFIGURE SQL SERVER AGENT MAIL PROFILE
================================================================================

STEP 2.1: CONFIGURE USING SSMS GUI
----------------------------------

1. In Object Explorer, expand SQL Server Agent
2. Right-click "SQL Server Agent" -> Properties
3. Select "Alert System" page
4. Check "Enable mail profile"
5. Mail system: Database Mail
6. Mail profile: SQLAlertProfile (select from dropdown)
7. Click OK
8. Restart SQL Server Agent when prompted

STEP 2.2: CONFIGURE USING T-SQL
-------------------------------

    USE msdb;
    GO

    -- Set Database Mail profile for SQL Server Agent
    EXEC msdb.dbo.sp_set_sqlagent_properties
        @email_save_in_sent_folder = 1,
        @databasemail_profile = N'SQLAlertProfile',
        @use_databasemail = 1;
    GO

    -- Verify settings
    EXEC msdb.dbo.sp_get_sqlagent_properties;
    GO

STEP 2.3: RESTART SQL SERVER AGENT
----------------------------------

    -- Restart SQL Server Agent
    EXEC xp_servicecontrol 'stop', 'SQLServerAgent';
    WAITFOR DELAY '00:00:05';
    EXEC xp_servicecontrol 'start', 'SQLServerAgent';
    GO

Or using PowerShell:

    Restart-Service SQLSERVERAGENT

================================================================================
                    PART 3: ENABLE DEADLOCK ERROR LOGGING
================================================================================

For alerts to work, errors must be logged to the Windows Event Log.

STEP 3.1: ENABLE LOGGING FOR DEADLOCK ERRORS
--------------------------------------------

    USE master;
    GO

    -- Enable logging for error 1205 (Transaction was deadlocked)
    EXEC master.sys.sp_altermessage 1205, 'WITH_LOG', TRUE;
    GO

    -- Enable logging for error 3928 (Marked transaction failed)
    EXEC master.sys.sp_altermessage 3928, 'WITH_LOG', TRUE;
    GO

    -- Verify messages are set to log
    SELECT
        message_id,
        severity,
        is_event_logged,
        text
    FROM sys.messages
    WHERE message_id IN (1205, 3928)
    AND language_id = 1033;  -- English
    GO

Expected output:
    message_id  severity  is_event_logged  text
    1205        13        1                Transaction (Process ID %d) was deadlocked...
    3928        16        1                The marked transaction failed...

STEP 3.2: ENABLE EXTENDED DEADLOCK REPORTING
--------------------------------------------

Enable trace flags for detailed deadlock information in error log:

    -- Enable trace flag 1204 (Deadlock participants info)
    DBCC TRACEON (1204, -1);
    GO

    -- Enable trace flag 1222 (Detailed deadlock info in XML format)
    DBCC TRACEON (1222, -1);
    GO

    -- Verify trace flags are enabled
    DBCC TRACESTATUS (1204, 1222);
    GO

Expected output:
    TraceFlag  Status  Global  Session
    1204       1       1       0
    1222       1       1       0

NOTE: -1 enables trace flag globally (all sessions).
To make persistent across restarts, add as startup parameter.

STEP 3.3: MAKE TRACE FLAGS PERSISTENT (OPTIONAL)
------------------------------------------------

To enable trace flags at SQL Server startup:

1. Open SQL Server Configuration Manager
2. Right-click SQL Server service -> Properties
3. Startup Parameters tab
4. Add: -T1204
5. Add: -T1222
6. Click OK
7. Restart SQL Server

================================================================================
                    PART 4: CREATE SQL SERVER AGENT OPERATOR
================================================================================

STEP 4.1: CREATE OPERATOR USING T-SQL
-------------------------------------

    USE msdb;
    GO

    -- Create operator for alert notifications
    EXEC msdb.dbo.sp_add_operator
        @name = N'DBA_Alerts',
        @enabled = 1,
        @email_address = N'your-email@domain.com',
        @pager_days = 0;  -- 0 = no pager (email only)
    GO

    -- Verify operator created
    SELECT
        id,
        name,
        enabled,
        email_address,
        last_email_date
    FROM msdb.dbo.sysoperators
    WHERE name = 'DBA_Alerts';
    GO

STEP 4.2: CREATE OPERATOR USING SSMS GUI
----------------------------------------

1. In Object Explorer, expand SQL Server Agent
2. Right-click "Operators" -> "New Operator..."
3. General page:
   - Name: DBA_Alerts
   - Enabled: Checked
   - E-mail name: your-email@domain.com
4. Click OK

================================================================================
                    PART 5: CREATE DEADLOCK ALERTS
================================================================================

STEP 5.1: CREATE ALERT FOR ERROR 1205 (DEADLOCK VICTIM)
-------------------------------------------------------

    USE msdb;
    GO

    -- Create alert for deadlock error 1205
    EXEC msdb.dbo.sp_add_alert
        @name = N'Deadlock Alert - Error 1205',
        @message_id = 1205,
        @severity = 0,
        @enabled = 1,
        @delay_between_responses = 60,  -- seconds between notifications
        @include_event_description_in = 1,  -- Include in email
        @database_name = N'AdventureWorks2019',  -- Or your database
        @notification_message = N'A deadlock has been detected! Transaction was chosen as deadlock victim.';
    GO

    -- Associate alert with operator
    EXEC msdb.dbo.sp_add_notification
        @alert_name = N'Deadlock Alert - Error 1205',
        @operator_name = N'DBA_Alerts',
        @notification_method = 1;  -- 1 = Email
    GO

STEP 5.2: CREATE ALERT FOR ERROR 3928
-------------------------------------

    USE msdb;
    GO

    -- Create alert for error 3928
    EXEC msdb.dbo.sp_add_alert
        @name = N'Deadlock Alert - Error 3928',
        @message_id = 3928,
        @severity = 0,
        @enabled = 1,
        @delay_between_responses = 60,
        @include_event_description_in = 1,
        @database_name = N'AdventureWorks2019',
        @notification_message = N'Marked transaction failed due to deadlock!';
    GO

    -- Associate alert with operator
    EXEC msdb.dbo.sp_add_notification
        @alert_name = N'Deadlock Alert - Error 3928',
        @operator_name = N'DBA_Alerts',
        @notification_method = 1;
    GO

STEP 5.3: CREATE ALERTS USING SSMS GUI
--------------------------------------

1. In Object Explorer, expand SQL Server Agent
2. Right-click "Alerts" -> "New Alert..."

Alert for Error 1205:
3. General page:
   - Name: Deadlock Alert - Error 1205
   - Type: SQL Server event alert
   - Database name: AdventureWorks2019
   - Error number: 1205
   - Enabled: Checked

4. Response page:
   - Check "Notify operators"
   - Select "DBA_Alerts" and check "E-mail"

5. Options page:
   - Include event description in: E-mail
   - Additional notification message: A deadlock has been detected!
   - Delay between responses: 60 seconds

6. Click OK

Repeat for Error 3928 with appropriate settings.

STEP 5.4: VERIFY ALERTS CREATED
-------------------------------

    -- View all alerts
    SELECT
        a.id,
        a.name AS AlertName,
        a.message_id AS ErrorNumber,
        a.enabled,
        a.database_name,
        o.name AS OperatorName
    FROM msdb.dbo.sysalerts a
    LEFT JOIN msdb.dbo.sysnotifications n ON a.id = n.alert_id
    LEFT JOIN msdb.dbo.sysoperators o ON n.operator_id = o.id
    WHERE a.name LIKE '%Deadlock%';
    GO

================================================================================
                    PART 6: CREATE TEST TABLES FOR DEADLOCK
================================================================================

STEP 6.1: CREATE TABLES IN ADVENTUREWORKS
-----------------------------------------

    USE AdventureWorks2019;  -- Or your AdventureWorks database
    GO

    -- Drop tables if exist
    IF OBJECT_ID('dbo.Employees', 'U') IS NOT NULL
        DROP TABLE dbo.Employees;
    IF OBJECT_ID('dbo.Suppliers', 'U') IS NOT NULL
        DROP TABLE dbo.Suppliers;
    GO

    -- Create Employees table
    CREATE TABLE dbo.Employees (
        EmpId INT IDENTITY(1,1) PRIMARY KEY,
        EmpName VARCHAR(16),
        Phone VARCHAR(16)
    );
    GO

    -- Insert sample data
    INSERT INTO dbo.Employees (EmpName, Phone)
    VALUES ('Kate', '800-666-3434'), ('John', '619-444-0808');
    GO

    -- Create Suppliers table
    CREATE TABLE dbo.Suppliers (
        SupplierId INT IDENTITY(1,1) PRIMARY KEY,
        SupName VARCHAR(64),
        Fax VARCHAR(16)
    );
    GO

    -- Insert sample data
    INSERT INTO dbo.Suppliers (SupName, Fax)
    VALUES ('Amway', '877-333-0404'), ('Ariel', '800-935-6518');
    GO

    -- Verify data
    SELECT * FROM dbo.Employees;
    SELECT * FROM dbo.Suppliers;
    GO

================================================================================
                    PART 7: SIMULATE DEADLOCK
================================================================================

Open TWO separate query windows in SSMS and execute commands step by step.

STEP 7.1: SESSION 1 - BEGIN TRANSACTION
---------------------------------------

    -- SESSION 1 (Query Window 1)
    USE AdventureWorks2019;
    GO

    BEGIN TRAN;
    PRINT 'Session 1: Transaction started';
    GO

STEP 7.2: SESSION 2 - BEGIN TRANSACTION
---------------------------------------

    -- SESSION 2 (Query Window 2)
    USE AdventureWorks2019;
    GO

    BEGIN TRAN;
    PRINT 'Session 2: Transaction started';
    GO

STEP 7.3: SESSION 1 - UPDATE EMPLOYEES
--------------------------------------

    -- SESSION 1 (Query Window 1)
    UPDATE dbo.Employees
    SET EmpName = 'Christie'
    WHERE EmpId = 1;

    PRINT 'Session 1: Updated Employees - holding X lock';
    GO

STEP 7.4: SESSION 2 - UPDATE SUPPLIERS
--------------------------------------

    -- SESSION 2 (Query Window 2)
    UPDATE dbo.Suppliers
    SET Fax = N'444-9898'
    WHERE SupplierId = 1;

    PRINT 'Session 2: Updated Suppliers - holding X lock';
    GO

STEP 7.5: SESSION 1 - UPDATE SUPPLIERS (BLOCKED!)
-------------------------------------------------

    -- SESSION 1 (Query Window 1)
    UPDATE dbo.Suppliers
    SET Fax = N'888-4343'
    WHERE SupplierId = 1;

    -- This query will WAIT (blocked)!
    -- Session 1 needs Suppliers, but Session 2 holds it
    GO

STEP 7.6: SESSION 2 - UPDATE EMPLOYEES (DEADLOCK!)
--------------------------------------------------

    -- SESSION 2 (Query Window 2)
    UPDATE dbo.Employees
    SET Phone = N'777-8888'
    WHERE EmpId = 1;

    -- DEADLOCK OCCURS!
    -- Session 2 needs Employees, but Session 1 holds it
    -- SQL Server will terminate one session as deadlock victim
    GO

EXPECTED RESULT:
---------------

One session will receive error:
    Msg 1205, Level 13, State 51, Line X
    Transaction (Process ID XX) was deadlocked on lock resources with
    another process and has been chosen as the deadlock victim.
    Rerun the transaction.

The other session will complete successfully.

STEP 7.7: CLEANUP
-----------------

In the session that succeeded:

    COMMIT TRAN;
    -- or ROLLBACK TRAN;
    GO

================================================================================
                    PART 8: VERIFY EMAIL NOTIFICATION
================================================================================

STEP 8.1: CHECK YOUR EMAIL
--------------------------

After the deadlock occurs:
1. Check your email inbox
2. Look for email from "SQL Server Alerts"
3. Subject should contain: "Deadlock Alert - Error 1205"
4. Body should contain deadlock details

Sample email content:
    Subject: SQL Server Alert System: 'Deadlock Alert - Error 1205' occurred on \\YOURSERVER

    Date: 1/15/2024 2:30:45 PM
    Database: AdventureWorks2019
    Error: 1205

    A deadlock has been detected! Transaction was chosen as deadlock victim.

    [Error details from SQL Server]

STEP 8.2: CHECK ALERT HISTORY
-----------------------------

    -- View alert occurrence history
    SELECT
        a.name AS AlertName,
        a.message_id AS ErrorNumber,
        h.alert_id,
        h.occurrence_count,
        h.last_occurrence_date,
        h.last_response_date
    FROM msdb.dbo.sysalerts_performance_counters_view h
    RIGHT JOIN msdb.dbo.sysalerts a ON h.alert_id = a.id
    WHERE a.name LIKE '%Deadlock%';
    GO

    -- Alternative: Check alert history via sysalerts
    SELECT
        name,
        occurrence_count,
        last_occurrence_date
    FROM msdb.dbo.sysalerts
    WHERE name LIKE '%Deadlock%';
    GO

STEP 8.3: CHECK DATABASE MAIL LOG
---------------------------------

    -- Check sent emails
    SELECT
        mailitem_id,
        recipients,
        subject,
        sent_status,
        send_request_date,
        sent_date
    FROM msdb.dbo.sysmail_allitems
    WHERE subject LIKE '%Deadlock%'
    ORDER BY send_request_date DESC;
    GO

    -- Check for mail errors
    SELECT *
    FROM msdb.dbo.sysmail_event_log
    WHERE event_type = 'error'
    ORDER BY log_date DESC;
    GO

STEP 8.4: CHECK SQL SERVER ERROR LOG
------------------------------------

    -- View deadlock entries in error log
    EXEC xp_readerrorlog 0, 1, 'deadlock';
    GO

================================================================================
                    TROUBLESHOOTING
================================================================================

PROBLEM: Email not sent
-----------------------
1. Check Database Mail is enabled
2. Verify SMTP settings (server, port, SSL)
3. Check credentials are correct
4. Test with sp_send_dbmail
5. Check sysmail_event_log for errors

PROBLEM: Alert not triggered
----------------------------
1. Verify error is set to WITH_LOG:
   SELECT is_event_logged FROM sys.messages WHERE message_id = 1205;
2. Verify alert is enabled
3. Check database name in alert matches exactly
4. Ensure SQL Server Agent is running
5. Check Windows Event Log for the error

PROBLEM: Operator not receiving notifications
--------------------------------------------
1. Verify operator email address is correct
2. Verify notification is associated with alert
3. Check operator is enabled
4. Verify Mail profile is configured in Agent

PROBLEM: Authentication failed with SMTP
----------------------------------------
1. For Office 365: Enable SMTP AUTH or use App Password
2. For Gmail: Enable "Less secure apps" or use App Password
3. Verify SSL settings
4. Check firewall isn't blocking port 587

================================================================================
                    ADDITIONAL ALERTS (OPTIONAL)
================================================================================

CREATE ALERT FOR HIGH SEVERITY ERRORS:
-------------------------------------

    -- Alert for severity 17+ errors (resource errors)
    EXEC msdb.dbo.sp_add_alert
        @name = N'Severity 17 Errors',
        @severity = 17,
        @enabled = 1,
        @delay_between_responses = 60,
        @include_event_description_in = 1;

    EXEC msdb.dbo.sp_add_notification
        @alert_name = N'Severity 17 Errors',
        @operator_name = N'DBA_Alerts',
        @notification_method = 1;
    GO

    -- Alert for severity 20+ errors (fatal errors)
    EXEC msdb.dbo.sp_add_alert
        @name = N'Severity 20+ Fatal Errors',
        @severity = 20,
        @enabled = 1,
        @delay_between_responses = 60,
        @include_event_description_in = 1;

    EXEC msdb.dbo.sp_add_notification
        @alert_name = N'Severity 20+ Fatal Errors',
        @operator_name = N'DBA_Alerts',
        @notification_method = 1;
    GO

================================================================================
                    SUMMARY
================================================================================

COMPONENTS CONFIGURED:
---------------------

+------------------------+----------------------------------------+
| Component              | Details                                |
+------------------------+----------------------------------------+
| Database Mail Profile  | SQLAlertProfile                        |
| Database Mail Account  | SQLMailAccount                         |
| SMTP Server            | smtp.office365.com:587 (SSL)           |
| SQL Agent Operator     | DBA_Alerts                             |
| Alert - Error 1205     | Deadlock Alert - Error 1205            |
| Alert - Error 3928     | Deadlock Alert - Error 3928            |
| Trace Flag 1204        | Deadlock participants info             |
| Trace Flag 1222        | Detailed deadlock XML                  |
+------------------------+----------------------------------------+

KEY STORED PROCEDURES:
---------------------

    -- Database Mail
    sysmail_add_account_sp      - Create mail account
    sysmail_add_profile_sp      - Create mail profile
    sp_send_dbmail              - Send test email

    -- Operators and Alerts
    sp_add_operator             - Create operator
    sp_add_alert                - Create alert
    sp_add_notification         - Associate alert with operator
    sp_altermessage             - Enable error logging

    -- Trace Flags
    DBCC TRACEON (flag, -1)     - Enable trace flag globally

FLOW OF DEADLOCK NOTIFICATION:
-----------------------------

1. Deadlock occurs in database
2. Error 1205 is raised
3. Error is written to Windows Event Log (WITH_LOG)
4. SQL Server Agent detects event
5. Alert matches error number
6. Notification triggered
7. Database Mail sends email to operator
8. DBA receives email notification

