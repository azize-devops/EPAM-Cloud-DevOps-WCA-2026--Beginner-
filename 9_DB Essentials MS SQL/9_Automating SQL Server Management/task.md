# Automating SQL Server Management - Task

## Goals

- Learn to create and manage SQL Server Agent jobs
- Understand credentials and proxy accounts for secure job execution
- Create multi-step jobs with different execution contexts
- Execute PowerShell scripts from SQL Server Agent
- Generate T-SQL scripts for job deployment

## Prerequisites

- 1 VM with installed SQL Server 2019
- AdventureWorks database backup file
- SQL Server Agent service running
- Administrative access to Windows and SQL Server

## Tasks

### 1. Restore AdventureWorks Database

- Restore the AdventureWorks DB on your SQL Server instance

### 2. Create Local Windows Account

- Create a local Windows user account for the proxy
- Grant necessary permissions for file system access

### 3. Create SQL Server Credential

- Create a credential in SQL Server
- Set the Windows account created above as the identity
- Store the Windows account password securely

### 4. Create Proxy Account

- Create a proxy account in the PowerShell subsystem
- Associate the proxy with the previously created credential
- Grant necessary permissions to use the proxy

### 5. Create SQL Job with Three Steps

**Step 1: Database Integrity Check**
- Type: T-SQL
- Check integrity of AdventureWorks DB using `DBCC CHECKDB`
- Run as SQL Server Agent service account

**Step 2: Create Backup Folder (PowerShell)**
- Type: PowerShell
- Run as the PROXY ACCOUNT (not service account)
- Execute the following script:

```powershell
$FolderPath = "C:\SQLBackups\AdventureWorks"
If (Test-Path $FolderPath) {
    Remove-Item $FolderPath -Force -Recurse -ErrorAction SilentlyContinue
}
New-Item -ItemType directory -Path $FolderPath
```

**Step 3: Backup Database**
- Type: T-SQL
- Create a backup of AdventureWorks DB
- Save backup to the folder created in Step 2

### 6. Verify Proxy Account Ownership

- After running the job, verify that the folder created in Step 2 is owned by the proxy account (Windows user)
- Check folder properties to confirm owner

### 7. Generate T-SQL Scripts for Job

- Generate a T-SQL script to DROP the SQL job
- Generate a T-SQL script to CREATE the SQL job
- Execute both scripts to verify they work correctly

## Requirements

1. AdventureWorks database must be restored
2. Local Windows account must be created
3. SQL Server credential must be created with Windows account identity
4. Proxy account must be created for PowerShell subsystem
5. SQL job must have 3 steps (DBCC CHECKDB, PowerShell, Backup)
6. PowerShell step MUST run under proxy account
7. Backup folder must be owned by the proxy Windows account
8. T-SQL scripts must be generated to drop and recreate the job
9. Both scripts must execute successfully

## Self-Review

- [ ] AdventureWorks database is restored and accessible
- [ ] Local Windows account exists (e.g., SQLJobProxy)
- [ ] SQL Server credential is created with Windows account
- [ ] Proxy account is created and associated with PowerShell subsystem
- [ ] SQL job is created with 3 steps
- [ ] Step 1 runs `DBCC CHECKDB` successfully
- [ ] Step 2 runs PowerShell as proxy account
- [ ] Step 3 creates backup in new folder
- [ ] Backup folder owner is the proxy Windows account
- [ ] DROP job script generated and executes successfully
- [ ] CREATE job script generated and executes successfully
- [ ] Job runs end-to-end without errors

## Helpful Materials

- https://docs.microsoft.com/en-us/sql/ssms/agent/create-a-job
- https://docs.microsoft.com/en-us/sql/ssms/agent/create-a-credential
- https://docs.microsoft.com/en-us/sql/ssms/agent/create-a-sql-server-agent-proxy
- https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sp-add-job-transact-sql
- https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sp-add-jobstep-transact-sql
- https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sp-add-proxy-transact-sql
