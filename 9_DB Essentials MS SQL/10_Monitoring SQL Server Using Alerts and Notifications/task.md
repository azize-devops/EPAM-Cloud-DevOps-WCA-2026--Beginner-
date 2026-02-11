MONITORING SQL SERVER USING ALERTS AND NOTIFICATIONS - TASK
===========================================================

GOALS:
------
- Learn to configure Database Mail for email notifications
- Create SQL Server Agent operators for alert recipients
- Set up alerts for specific SQL Server errors
- Enable deadlock tracing and logging
- Test alert notifications by simulating a deadlock

PREREQUISITES:
--------------
- 1 VM with installed SQL Server 2019
- VM should have access to the internet
- Valid email account (Office 365, Gmail, or other SMTP server)
- AdventureWorks database (or any user database)

TASKS:
------

1. Enable and Configure Database Mail:
   - Enable Database Mail feature
   - Create a Database Mail profile
   - Configure SMTP settings:
     * Server: smtp.office365.com (or other valid mail server)
     * Port: 587
     * Enable SSL/TLS secure connection
   - Test email configuration

2. Configure SQL Server Agent Mail Profile:
   - Open SQL Server Agent properties
   - Enable mail profile
   - Select the Database Mail profile created in step 1

3. Enable Deadlock Error Logging:
   Execute the following code to enable tracing of deadlock errors:

   EXEC master.sys.sp_altermessage 1205, 'WITH_LOG', TRUE;
   GO
   EXEC master.sys.sp_altermessage 3928, 'WITH_LOG', TRUE;
   GO

4. Enable Extended Deadlock Reporting:
   Enable trace flags for Extended Events deadlock reporting:

   DBCC TRACEON (1204, -1)
   DBCC TRACEON (1222, -1)

5. Create SQL Server Agent Operator:
   - Create an operator as the recipient of alert emails
   - Configure email address for notifications

6. Create Alerts for Deadlock Errors:
   - Create alert for error 1205 (Deadlock victim)
   - Create alert for error 3928 (Marked transaction failure)
   - Configure alerts for target database
   - Associate alerts with the operator

7. Simulate Deadlock:
   Create test tables:

   USE AdventureWorks
   CREATE TABLE Employees (
       EmpId INT IDENTITY,
       EmpName VARCHAR(16),
       Phone VARCHAR(16)
   )
   GO
   INSERT INTO Employees (EmpName, Phone)
   VALUES ('Kate', '800-666-3434'), ('John', '619-444-0808')
   GO

   CREATE TABLE Suppliers(
       SupplierId INT IDENTITY,
       SupName VARCHAR(64),
       Fax VARCHAR(16)
   )
   GO
   INSERT INTO Suppliers (SupName, Fax)
   VALUES ('Amway', '877-333-0404'), ('Ariel', '800-935-6518')
   GO

   Execute deadlock scenario in two query windows:

        Session 1                  |        Session 2
   ================================|================================
   USE AdventureWorks              | USE AdventureWorks
   BEGIN TRAN;                     | BEGIN TRAN;
   ================================|================================
   UPDATE Employees                |
   SET EmpName = 'Christie'        |
   WHERE EmpId = 1                 |
   ================================|================================
                                   | UPDATE Suppliers
                                   | SET Fax = N'444-9898'
                                   | WHERE SupplierId = 1
   ================================|================================
   UPDATE Suppliers                |
   SET Fax = N'888-4343'           |
   WHERE SupplierId = 1            |
   ================================|================================
   <blocked>                       | UPDATE Employees
                                   | SET Phone = N'777-8888'
                                   | WHERE EmpId = 1
   ================================|================================
                                   | <blocked>
   ================================|================================
   DEADLOCK OCCURS!

8. Verify Email Notification:
   - Check your mailbox for deadlock notification email
   - Verify email contains deadlock error information

REQUIREMENTS:
-------------
1. Database Mail must be enabled and configured with SMTP
2. SQL Server Agent must use the Database Mail profile
3. Deadlock errors 1205 and 3928 must be set to log to event log
4. Trace flags 1204 and 1222 must be enabled
5. SQL Server Agent operator must be created
6. Alerts must be created for errors 1205 and 3928
7. Deadlock must be simulated successfully
8. Email notification must be received in mailbox

SELF-REVIEW:
------------
- Database Mail is configured and test email works
- SQL Server Agent is configured to use Database Mail
- sp_altermessage executed for errors 1205 and 3928
- Trace flags 1204 and 1222 are enabled
- Operator is created with valid email address
- Alert for error 1205 is created and enabled
- Alert for error 3928 is created and enabled
- Test tables (Employees, Suppliers) are created
- Deadlock is triggered by executing both sessions
- Email notification received about the deadlock
- Email contains deadlock error details

HELPFUL MATERIALS:
------------------
https://docs.microsoft.com/en-us/sql/relational-databases/database-mail/database-mail
https://docs.microsoft.com/en-us/sql/relational-databases/database-mail/configure-database-mail
https://docs.microsoft.com/en-us/sql/ssms/agent/create-an-operator
https://docs.microsoft.com/en-us/sql/ssms/agent/create-an-alert-using-an-error-number
https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sp-altermessage-transact-sql
https://docs.microsoft.com/en-us/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql
