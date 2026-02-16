# AUTOMATING SQL SERVER MANAGEMENT - ANSWER

This guide covers SQL Server Agent job automation including credentials,
proxy accounts, and multi-step job creation with PowerShell integration.

## UNDERSTANDING SQL SERVER AGENT SECURITY

### Security Hierarchy

```
+------------------+
| Windows Account  |  <-- Operating system level
+--------+---------+
         |
+--------v---------+
| SQL Credential   |  <-- Maps Windows account to SQL Server
+--------+---------+
         |
+--------v---------+
|  Proxy Account   |  <-- Allows non-sysadmin to run specific subsystems
+--------+---------+
         |
+--------v---------+
|   Job Step       |  <-- Runs under proxy context
+------------------+
```

### Why Use Proxy Accounts?

- SQL Server Agent service account often has elevated privileges
- Proxy accounts allow running job steps with specific, limited permissions
- Different steps can run under different security contexts
- Better security isolation and audit trail

### Job Step Subsystems

| Subsystem | Description |
|-----------|-------------|
| TSQL | T-SQL scripts (default) |
| PowerShell | PowerShell scripts |
| CmdExec | Operating system commands |
| SSIS | Integration Services packages |
| SSAS | Analysis Services commands |
| SSRS | Reporting Services |
| ActiveScripting | ActiveX scripts (legacy) |

## PART 1: RESTORE ADVENTUREWORKS DATABASE

### STEP 1.1: RESTORE DATABASE

```sql
USE master;
GO

-- Restore AdventureWorks database
RESTORE DATABASE AdventureWorks2019
FROM DISK = 'C:\Backup\AdventureWorks2019.bak'
WITH
    MOVE 'AdventureWorks2019' TO 'C:\SQLData\AdventureWorks2019.mdf',
    MOVE 'AdventureWorks2019_log' TO 'C:\SQLLog\AdventureWorks2019_log.ldf',
    REPLACE,
    STATS = 10;
GO

-- Verify database is online
SELECT name, state_desc FROM sys.databases WHERE name = 'AdventureWorks2019';
GO
```

## PART 2: CREATE LOCAL WINDOWS ACCOUNT

### STEP 2.1: CREATE WINDOWS ACCOUNT USING COMPUTER MANAGEMENT

1. Open Computer Management (compmgmt.msc)
2. Navigate to: Local Users and Groups -> Users
3. Right-click Users -> New User...
4. Configure:
   - User name: SQLJobProxy
   - Full name: SQL Job Proxy Account
   - Description: Account for SQL Server Agent job proxy
   - Password: ProxyP@ssw0rd123!
   - Uncheck: User must change password at next logon
   - Check: Password never expires
   - Check: User cannot change password
5. Click Create, then Close

### STEP 2.2: CREATE WINDOWS ACCOUNT USING POWERSHELL

```powershell
# Create the proxy Windows account
$Password = ConvertTo-SecureString "ProxyP@ssw0rd123!" -AsPlainText -Force
New-LocalUser -Name "SQLJobProxy" `
    -Password $Password `
    -FullName "SQL Job Proxy Account" `
    -Description "Account for SQL Server Agent job proxy" `
    -PasswordNeverExpires `
    -UserMayNotChangePassword

# Verify account created
Get-LocalUser -Name "SQLJobProxy"
```

### STEP 2.3: CREATE WINDOWS ACCOUNT USING NET USER

```cmd
net user SQLJobProxy ProxyP@ssw0rd123! /add /fullname:"SQL Job Proxy Account"
net user SQLJobProxy /passwordchg:no
wmic useraccount where "name='SQLJobProxy'" set PasswordExpires=false
```

### STEP 2.4: GRANT FOLDER PERMISSIONS

The proxy account needs permission to create folders:

```powershell
# Grant permissions on parent backup directory
$BackupPath = "C:\SQLBackups"
if (-not (Test-Path $BackupPath)) {
    New-Item -ItemType Directory -Path $BackupPath -Force
}

# Grant SQLJobProxy full control
$acl = Get-Acl $BackupPath
$permission = "$(hostname)\SQLJobProxy","FullControl","ContainerInherit,ObjectInherit","None","Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule $permission
$acl.SetAccessRule($accessRule)
Set-Acl $BackupPath $acl
```

Or using icacls:

```cmd
icacls "C:\SQLBackups" /grant "SQLJobProxy":(OI)(CI)F
```

## PART 3: CREATE SQL SERVER CREDENTIAL

### STEP 3.1: CREATE CREDENTIAL USING T-SQL

```sql
USE master;
GO

-- Create credential for the Windows account
-- Replace YOURSERVER with your actual computer name
CREATE CREDENTIAL [SQLJobProxyCredential]
WITH IDENTITY = 'YOURSERVER\SQLJobProxy',
SECRET = 'ProxyP@ssw0rd123!';
GO

-- Verify credential created
SELECT
    credential_id,
    name,
    credential_identity,
    create_date
FROM sys.credentials
WHERE name = 'SQLJobProxyCredential';
GO
```

NOTE: Replace YOURSERVER with your actual computer name.
Find it with: `SELECT SERVERPROPERTY('MachineName');`

### STEP 3.2: CREATE CREDENTIAL USING SSMS GUI

1. In Object Explorer, expand Security
2. Right-click Credentials -> New Credential...
3. Configure:
   - Credential name: SQLJobProxyCredential
   - Identity: YOURSERVER\SQLJobProxy
   - Password: ProxyP@ssw0rd123!
   - Confirm password: ProxyP@ssw0rd123!
4. Click OK

## PART 4: CREATE PROXY ACCOUNT

### STEP 4.1: CREATE PROXY USING T-SQL

```sql
USE msdb;
GO

-- Create proxy for PowerShell subsystem
EXEC sp_add_proxy
    @proxy_name = N'SQLJobProxy_PowerShell',
    @credential_name = N'SQLJobProxyCredential',
    @enabled = 1,
    @description = N'Proxy account for PowerShell job steps';
GO

-- Grant proxy access to PowerShell subsystem
-- Subsystem ID 12 = PowerShell
EXEC sp_grant_proxy_to_subsystem
    @proxy_name = N'SQLJobProxy_PowerShell',
    @subsystem_id = 12;  -- PowerShell
GO

-- Verify proxy created
SELECT
    p.proxy_id,
    p.name AS ProxyName,
    c.name AS CredentialName,
    c.credential_identity,
    p.enabled
FROM msdb.dbo.sysproxies p
JOIN sys.credentials c ON p.credential_id = c.credential_id
WHERE p.name = 'SQLJobProxy_PowerShell';
GO

-- Verify subsystem association
SELECT
    p.name AS ProxyName,
    s.subsystem_id,
    s.subsystem AS SubsystemName
FROM msdb.dbo.sysproxies p
JOIN msdb.dbo.sysproxysubsystem ps ON p.proxy_id = ps.proxy_id
JOIN msdb.dbo.syssubsystems s ON ps.subsystem_id = s.subsystem_id
WHERE p.name = 'SQLJobProxy_PowerShell';
GO
```

### Subsystem IDs

| ID | Subsystem |
|----|-----------|
| 1 | ActiveScripting |
| 2 | CmdExec |
| 3 | TSQL |
| 4 | Snapshot |
| 5 | LogReader |
| 6 | Distribution |
| 7 | Merge |
| 8 | QueueReader |
| 9 | ANALYSISQUERY |
| 10 | ANALYSISCOMMAND |
| 11 | SSIS |
| 12 | PowerShell |

### STEP 4.2: GRANT PROXY TO LOGIN (OPTIONAL)

By default, only sysadmin can use proxies. To allow other logins:

```sql
-- Grant proxy to specific login
EXEC msdb.dbo.sp_grant_login_to_proxy
    @login_name = N'YourLogin',
    @proxy_name = N'SQLJobProxy_PowerShell';
GO
```

### STEP 4.3: CREATE PROXY USING SSMS GUI

1. In Object Explorer, expand SQL Server Agent
2. Expand Proxies
3. Right-click PowerShell -> New Proxy...
4. General page:
   - Proxy name: SQLJobProxy_PowerShell
   - Credential name: SQLJobProxyCredential (select from dropdown)
   - Description: Proxy account for PowerShell job steps
   - Active to the following subsystems: [X] PowerShell
5. Principals page (optional):
   - Add logins that can use this proxy
6. Click OK

## PART 5: CREATE SQL SERVER AGENT JOB

### STEP 5.1: VERIFY SQL SERVER AGENT IS RUNNING

```sql
-- Check SQL Server Agent status
EXEC xp_servicecontrol 'querystate', 'SQLServerAgent';
GO

-- Or using PowerShell
-- Get-Service -Name "SQLSERVERAGENT"
```

If not running, start it:

```sql
-- Start SQL Server Agent
EXEC xp_servicecontrol 'start', 'SQLServerAgent';
GO
```

### STEP 5.2: CREATE JOB USING T-SQL

```sql
USE msdb;
GO

-- ================================================================
-- STEP 1: Create the Job
-- ================================================================

EXEC sp_add_job
    @job_name = N'AdventureWorks_Maintenance_Job',
    @enabled = 1,
    @description = N'Database integrity check, folder creation, and backup',
    @category_name = N'Database Maintenance',
    @owner_login_name = N'sa';
GO

-- ================================================================
-- STEP 2: Add Job Step 1 - DBCC CHECKDB
-- ================================================================

EXEC sp_add_jobstep
    @job_name = N'AdventureWorks_Maintenance_Job',
    @step_name = N'Step 1 - Database Integrity Check',
    @step_id = 1,
    @subsystem = N'TSQL',
    @command = N'DBCC CHECKDB (''AdventureWorks2019'') WITH NO_INFOMSGS;',
    @database_name = N'master',
    @on_success_action = 3,  -- Go to next step
    @on_fail_action = 2,     -- Quit with failure
    @retry_attempts = 0,
    @retry_interval = 0;
GO

-- ================================================================
-- STEP 3: Add Job Step 2 - PowerShell (Create Backup Folder)
-- THIS STEP RUNS AS PROXY ACCOUNT
-- ================================================================

DECLARE @PowerShellScript NVARCHAR(MAX);
SET @PowerShellScript = N'
$FolderPath = "C:\SQLBackups\AdventureWorks"

# Check if folder exists and remove it
If (Test-Path $FolderPath) {
    Remove-Item $FolderPath -Force -Recurse -ErrorAction SilentlyContinue
}

# Create new folder
New-Item -ItemType Directory -Path $FolderPath -Force

# Verify folder created
If (Test-Path $FolderPath) {
    Write-Output "Folder created successfully: $FolderPath"
} Else {
    Write-Error "Failed to create folder: $FolderPath"
    Exit 1
}
';

EXEC sp_add_jobstep
    @job_name = N'AdventureWorks_Maintenance_Job',
    @step_name = N'Step 2 - Create Backup Folder (PowerShell)',
    @step_id = 2,
    @subsystem = N'PowerShell',
    @command = @PowerShellScript,
    @database_name = N'master',
    @proxy_name = N'SQLJobProxy_PowerShell',  -- RUN AS PROXY!
    @on_success_action = 3,  -- Go to next step
    @on_fail_action = 2,     -- Quit with failure
    @retry_attempts = 0,
    @retry_interval = 0;
GO

-- ================================================================
-- STEP 4: Add Job Step 3 - Backup Database
-- ================================================================

DECLARE @BackupScript NVARCHAR(MAX);
SET @BackupScript = N'
DECLARE @BackupPath NVARCHAR(500);
DECLARE @BackupFile NVARCHAR(500);
DECLARE @Timestamp NVARCHAR(50);

SET @Timestamp = REPLACE(REPLACE(REPLACE(CONVERT(NVARCHAR, GETDATE(), 120), ''-'', ''''), '':'', ''''), '' '', ''_'');
SET @BackupPath = ''C:\SQLBackups\AdventureWorks\'';
SET @BackupFile = @BackupPath + ''AdventureWorks2019_'' + @Timestamp + ''.bak'';

BACKUP DATABASE AdventureWorks2019
TO DISK = @BackupFile
WITH
    FORMAT,
    INIT,
    NAME = ''AdventureWorks2019 Full Backup'',
    COMPRESSION,
    STATS = 10;

PRINT ''Backup completed: '' + @BackupFile;
';

EXEC sp_add_jobstep
    @job_name = N'AdventureWorks_Maintenance_Job',
    @step_name = N'Step 3 - Backup Database',
    @step_id = 3,
    @subsystem = N'TSQL',
    @command = @BackupScript,
    @database_name = N'master',
    @on_success_action = 1,  -- Quit with success
    @on_fail_action = 2,     -- Quit with failure
    @retry_attempts = 0,
    @retry_interval = 0;
GO

-- ================================================================
-- STEP 5: Set Job Start Step
-- ================================================================

EXEC sp_update_job
    @job_name = N'AdventureWorks_Maintenance_Job',
    @start_step_id = 1;
GO

-- ================================================================
-- STEP 6: Add Job to Local Server
-- ================================================================

EXEC sp_add_jobserver
    @job_name = N'AdventureWorks_Maintenance_Job',
    @server_name = N'(local)';
GO

PRINT 'Job created successfully!';
GO
```

### STEP 5.3: VERIFY JOB CREATION

```sql
-- View job details
SELECT
    j.job_id,
    j.name AS JobName,
    j.enabled,
    j.description,
    j.date_created,
    l.name AS OwnerLogin
FROM msdb.dbo.sysjobs j
JOIN master.sys.server_principals l ON j.owner_sid = l.sid
WHERE j.name = 'AdventureWorks_Maintenance_Job';
GO

-- View job steps
SELECT
    j.name AS JobName,
    s.step_id,
    s.step_name,
    s.subsystem,
    s.command,
    p.name AS ProxyName,
    CASE s.on_success_action
        WHEN 1 THEN 'Quit with success'
        WHEN 2 THEN 'Quit with failure'
        WHEN 3 THEN 'Go to next step'
        WHEN 4 THEN 'Go to step...'
    END AS OnSuccess
FROM msdb.dbo.sysjobs j
JOIN msdb.dbo.sysjobsteps s ON j.job_id = s.job_id
LEFT JOIN msdb.dbo.sysproxies p ON s.proxy_id = p.proxy_id
WHERE j.name = 'AdventureWorks_Maintenance_Job'
ORDER BY s.step_id;
GO
```

### STEP 5.4: CREATE JOB USING SSMS GUI

1. In Object Explorer, expand SQL Server Agent
2. Right-click Jobs -> New Job...
3. General page:
   - Name: AdventureWorks_Maintenance_Job
   - Owner: sa
   - Category: Database Maintenance
   - Description: Database integrity check, folder creation, and backup
   - Enabled: Checked

4. Steps page -> Click New...

   **Step 1:**
   - Step name: Step 1 - Database Integrity Check
   - Type: Transact-SQL script (T-SQL)
   - Database: master
   - Command: `DBCC CHECKDB ('AdventureWorks2019') WITH NO_INFOMSGS;`
   - Advanced tab -> On success action: Go to the next step
   - Click OK

   **Step 2:**
   - Step name: Step 2 - Create Backup Folder (PowerShell)
   - Type: PowerShell
   - Run as: SQLJobProxy_PowerShell (SELECT PROXY!)
   - Command: (paste PowerShell script)
   - Advanced tab -> On success action: Go to the next step
   - Click OK

   **Step 3:**
   - Step name: Step 3 - Backup Database
   - Type: Transact-SQL script (T-SQL)
   - Database: master
   - Command: (paste backup script)
   - Advanced tab -> On success action: Quit the job reporting success
   - Click OK

5. Click OK to create job

## PART 6: RUN AND TEST THE JOB

### STEP 6.1: RUN JOB MANUALLY

```sql
-- Execute the job
EXEC msdb.dbo.sp_start_job @job_name = N'AdventureWorks_Maintenance_Job';
GO

PRINT 'Job started. Check job history for results.';
GO
```

### STEP 6.2: MONITOR JOB EXECUTION

```sql
-- Check if job is running
EXEC msdb.dbo.sp_help_job
    @job_name = N'AdventureWorks_Maintenance_Job',
    @execution_status = 1;  -- 1 = Executing
GO

-- Wait for job to complete
WAITFOR DELAY '00:00:30';  -- Wait 30 seconds
GO
```

### STEP 6.3: VIEW JOB HISTORY

```sql
-- View job execution history
SELECT
    j.name AS JobName,
    h.step_id,
    h.step_name,
    h.run_status,
    CASE h.run_status
        WHEN 0 THEN 'Failed'
        WHEN 1 THEN 'Succeeded'
        WHEN 2 THEN 'Retry'
        WHEN 3 THEN 'Canceled'
        WHEN 4 THEN 'In Progress'
    END AS Status,
    h.run_date,
    h.run_time,
    h.run_duration,
    h.message
FROM msdb.dbo.sysjobhistory h
JOIN msdb.dbo.sysjobs j ON h.job_id = j.job_id
WHERE j.name = 'AdventureWorks_Maintenance_Job'
ORDER BY h.instance_id DESC;
GO
```

### STEP 6.4: VERIFY BACKUP FILE EXISTS

```sql
-- Check if backup file was created
EXEC xp_cmdshell 'dir "C:\SQLBackups\AdventureWorks"';
GO
```

## PART 7: VERIFY FOLDER OWNERSHIP

### STEP 7.1: CHECK FOLDER OWNER USING POWERSHELL

```powershell
# Check folder owner
$FolderPath = "C:\SQLBackups\AdventureWorks"
$acl = Get-Acl $FolderPath
Write-Host "Folder: $FolderPath"
Write-Host "Owner: $($acl.Owner)"
```

Expected output:

```
Folder: C:\SQLBackups\AdventureWorks
Owner: YOURSERVER\SQLJobProxy
```

### STEP 7.2: CHECK FOLDER OWNER USING GUI

1. Open File Explorer
2. Navigate to C:\SQLBackups\AdventureWorks
3. Right-click folder -> Properties
4. Click Security tab -> Advanced
5. Look at "Owner:" field at the top
   - Should show: YOURSERVER\SQLJobProxy

### STEP 7.3: CHECK FOLDER OWNER USING CMD

```cmd
dir /q "C:\SQLBackups\AdventureWorks"
```

The owner column will show the Windows account that created the folder.

## PART 8: GENERATE T-SQL SCRIPTS FOR JOB

### STEP 8.1: GENERATE SCRIPTS USING SSMS GUI

1. In Object Explorer, expand SQL Server Agent -> Jobs
2. Right-click "AdventureWorks_Maintenance_Job"
3. Select "Script Job as" -> DROP To -> New Query Editor Window
4. Save the DROP script
5. Right-click job again
6. Select "Script Job as" -> CREATE To -> New Query Editor Window
7. Save the CREATE script

### STEP 8.2: DROP JOB SCRIPT (T-SQL)

```sql
-- ================================================================
-- DROP JOB SCRIPT
-- ================================================================

USE [msdb];
GO

-- Check if job exists and delete it
IF EXISTS (SELECT job_id FROM msdb.dbo.sysjobs WHERE name = N'AdventureWorks_Maintenance_Job')
BEGIN
    -- Delete the job
    EXEC msdb.dbo.sp_delete_job
        @job_name = N'AdventureWorks_Maintenance_Job',
        @delete_unused_schedule = 1;

    PRINT 'Job dropped successfully.';
END
ELSE
BEGIN
    PRINT 'Job does not exist.';
END
GO
```

### STEP 8.3: CREATE JOB SCRIPT (T-SQL)

```sql
-- ================================================================
-- CREATE JOB SCRIPT
-- ================================================================

USE [msdb];
GO

BEGIN TRANSACTION;

DECLARE @ReturnCode INT = 0;
DECLARE @jobId BINARY(16);

-- Create Job Category if not exists
IF NOT EXISTS (SELECT name FROM msdb.dbo.syscategories WHERE name = N'Database Maintenance' AND category_class = 1)
BEGIN
    EXEC @ReturnCode = msdb.dbo.sp_add_category
        @class = N'JOB',
        @type = N'LOCAL',
        @name = N'Database Maintenance';
    IF (@@ERROR <> 0 OR @ReturnCode <> 0) GOTO QuitWithRollback;
END

-- Create the Job
EXEC @ReturnCode = msdb.dbo.sp_add_job
    @job_name = N'AdventureWorks_Maintenance_Job',
    @enabled = 1,
    @notify_level_eventlog = 0,
    @notify_level_email = 0,
    @notify_level_netsend = 0,
    @notify_level_page = 0,
    @delete_level = 0,
    @description = N'Database integrity check, folder creation, and backup',
    @category_name = N'Database Maintenance',
    @owner_login_name = N'sa',
    @job_id = @jobId OUTPUT;
IF (@@ERROR <> 0 OR @ReturnCode <> 0) GOTO QuitWithRollback;

-- Add Step 1: DBCC CHECKDB
EXEC @ReturnCode = msdb.dbo.sp_add_jobstep
    @job_id = @jobId,
    @step_name = N'Step 1 - Database Integrity Check',
    @step_id = 1,
    @cmdexec_success_code = 0,
    @on_success_action = 3,
    @on_success_step_id = 0,
    @on_fail_action = 2,
    @on_fail_step_id = 0,
    @retry_attempts = 0,
    @retry_interval = 0,
    @os_run_priority = 0,
    @subsystem = N'TSQL',
    @command = N'DBCC CHECKDB (''AdventureWorks2019'') WITH NO_INFOMSGS;',
    @database_name = N'master',
    @flags = 0;
IF (@@ERROR <> 0 OR @ReturnCode <> 0) GOTO QuitWithRollback;

-- Add Step 2: PowerShell (Create Backup Folder)
EXEC @ReturnCode = msdb.dbo.sp_add_jobstep
    @job_id = @jobId,
    @step_name = N'Step 2 - Create Backup Folder (PowerShell)',
    @step_id = 2,
    @cmdexec_success_code = 0,
    @on_success_action = 3,
    @on_success_step_id = 0,
    @on_fail_action = 2,
    @on_fail_step_id = 0,
    @retry_attempts = 0,
    @retry_interval = 0,
    @os_run_priority = 0,
    @subsystem = N'PowerShell',
    @command = N'
$FolderPath = "C:\SQLBackups\AdventureWorks"
If (Test-Path $FolderPath) {
    Remove-Item $FolderPath -Force -Recurse -ErrorAction SilentlyContinue
}
New-Item -ItemType Directory -Path $FolderPath -Force
If (Test-Path $FolderPath) {
    Write-Output "Folder created successfully: $FolderPath"
} Else {
    Write-Error "Failed to create folder: $FolderPath"
    Exit 1
}
',
    @database_name = N'master',
    @proxy_name = N'SQLJobProxy_PowerShell',
    @flags = 0;
IF (@@ERROR <> 0 OR @ReturnCode <> 0) GOTO QuitWithRollback;

-- Add Step 3: Backup Database
EXEC @ReturnCode = msdb.dbo.sp_add_jobstep
    @job_id = @jobId,
    @step_name = N'Step 3 - Backup Database',
    @step_id = 3,
    @cmdexec_success_code = 0,
    @on_success_action = 1,
    @on_success_step_id = 0,
    @on_fail_action = 2,
    @on_fail_step_id = 0,
    @retry_attempts = 0,
    @retry_interval = 0,
    @os_run_priority = 0,
    @subsystem = N'TSQL',
    @command = N'
DECLARE @BackupPath NVARCHAR(500);
DECLARE @BackupFile NVARCHAR(500);
DECLARE @Timestamp NVARCHAR(50);

SET @Timestamp = REPLACE(REPLACE(REPLACE(CONVERT(NVARCHAR, GETDATE(), 120), ''-'', ''''), '':'', ''''), '' '', ''_'');
SET @BackupPath = ''C:\SQLBackups\AdventureWorks\'';
SET @BackupFile = @BackupPath + ''AdventureWorks2019_'' + @Timestamp + ''.bak'';

BACKUP DATABASE AdventureWorks2019
TO DISK = @BackupFile
WITH FORMAT, INIT, NAME = ''AdventureWorks2019 Full Backup'', COMPRESSION, STATS = 10;

PRINT ''Backup completed: '' + @BackupFile;
',
    @database_name = N'master',
    @flags = 0;
IF (@@ERROR <> 0 OR @ReturnCode <> 0) GOTO QuitWithRollback;

-- Set Start Step
EXEC @ReturnCode = msdb.dbo.sp_update_job
    @job_id = @jobId,
    @start_step_id = 1;
IF (@@ERROR <> 0 OR @ReturnCode <> 0) GOTO QuitWithRollback;

-- Add Job Server
EXEC @ReturnCode = msdb.dbo.sp_add_jobserver
    @job_id = @jobId,
    @server_name = N'(local)';
IF (@@ERROR <> 0 OR @ReturnCode <> 0) GOTO QuitWithRollback;

COMMIT TRANSACTION;
PRINT 'Job created successfully!';
GOTO EndSave;

QuitWithRollback:
    IF (@@TRANCOUNT > 0) ROLLBACK TRANSACTION;
    PRINT 'Job creation failed!';

EndSave:
GO
```

### STEP 8.4: TEST DROP AND CREATE SCRIPTS

```sql
-- First, run DROP script
-- Then, run CREATE script
-- Finally, test the job

-- Run the job after recreation
EXEC msdb.dbo.sp_start_job @job_name = N'AdventureWorks_Maintenance_Job';
GO
```

## OPTIONAL: ADD JOB SCHEDULE

```sql
-- Add a daily schedule at 2:00 AM
USE msdb;
GO

EXEC sp_add_schedule
    @schedule_name = N'Daily_2AM',
    @freq_type = 4,           -- Daily
    @freq_interval = 1,       -- Every 1 day
    @active_start_time = 020000;  -- 02:00:00 AM
GO

-- Attach schedule to job
EXEC sp_attach_schedule
    @job_name = N'AdventureWorks_Maintenance_Job',
    @schedule_name = N'Daily_2AM';
GO
```

## TROUBLESHOOTING

### PROBLEM: PowerShell step fails with access denied

1. Verify Windows account has folder permissions
2. Check credential password is correct
3. Verify proxy is associated with PowerShell subsystem

### PROBLEM: Job step fails to find proxy

1. Verify proxy exists: `SELECT * FROM msdb.dbo.sysproxies;`
2. Verify proxy is enabled
3. Verify proxy is granted to PowerShell subsystem

### PROBLEM: SQL Server Agent not running

1. Check service status: `Get-Service SQLSERVERAGENT`
2. Start service: `Start-Service SQLSERVERAGENT`
3. Set to automatic: `Set-Service SQLSERVERAGENT -StartupType Automatic`

### PROBLEM: DBCC CHECKDB fails

1. Check database is online
2. Check for existing corruption
3. Check available disk space

## SUMMARY

### Components Created

| Component | Name/Details |
|-----------|--------------|
| Windows Account | SQLJobProxy |
| SQL Credential | SQLJobProxyCredential |
| Proxy Account | SQLJobProxy_PowerShell (PowerShell) |
| SQL Agent Job | AdventureWorks_Maintenance_Job |
| Step 1 | DBCC CHECKDB (T-SQL) |
| Step 2 | Create Folder (PowerShell + Proxy) |
| Step 3 | Backup Database (T-SQL) |

### Key Stored Procedures

```
sp_add_job          - Create job
sp_add_jobstep      - Add job step
sp_add_proxy        - Create proxy
sp_add_credential   - Create credential (use CREATE CREDENTIAL instead)
sp_grant_proxy_to_subsystem - Grant proxy to subsystem
sp_start_job        - Execute job
sp_delete_job       - Delete job
```

### Files Created

```
C:\SQLBackups\AdventureWorks\              - Backup folder (owner: SQLJobProxy)
C:\SQLBackups\AdventureWorks\AW_*.bak      - Backup files
```
