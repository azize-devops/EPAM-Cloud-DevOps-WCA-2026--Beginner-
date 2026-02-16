# RESTORING SQL SERVER DATABASES - ANSWER

This guide covers SQL Server backup and restore operations including
Full, Differential, and Transaction Log backups, along with point-in-time
recovery and tail-log backup procedures.

## UNDERSTANDING BACKUP TYPES

### Backup Type Overview

| Backup Type | Description |
|-------------|-------------|
| FULL | Complete copy of database. Base for all other backups. |
| DIFFERENTIAL | Only changes since last FULL backup. Requires FULL. |
| TRANSACTION LOG | Only transaction log records since last log backup. Enables point-in-time recovery. Requires FULL recovery. |

### Recovery Models

| Recovery Model | Description |
|----------------|-------------|
| SIMPLE | No transaction log backups. Minimal log management. Cannot do point-in-time recovery. |
| FULL | Full transaction log maintained. Supports all backup types including point-in-time recovery. |
| BULK_LOGGED | Like FULL but minimally logs bulk operations. Limited point-in-time recovery for bulk operations. |

## PART 1: SETUP AND RESTORE ADVENTUREWORKS

All commands below are executed from the HOST machine via remote connection.

### STEP 1.1: CONNECT TO SQL SERVER FROM HOST

Open SSMS on host machine and connect:
- Server: [VM_IP_ADDRESS] (e.g., 192.168.1.100)
- Authentication: SQL Server Authentication
- Login: sa
- Password: [your_password]

Or connect using PowerShell from host:

```powershell
$serverInstance = "192.168.1.100"
$credential = Get-Credential  # Enter sa credentials
```

### STEP 1.2: CREATE BACKUP DIRECTORY ON VM

```sql
-- Create backup directory (run via xp_cmdshell or manually on VM)
EXEC xp_cmdshell 'mkdir C:\SQLBackups';
GO

-- If xp_cmdshell is disabled, enable it first:
EXEC sp_configure 'show advanced options', 1;
RECONFIGURE;
EXEC sp_configure 'xp_cmdshell', 1;
RECONFIGURE;
GO
```

### STEP 1.3: RESTORE ADVENTUREWORKS DATABASE

```sql
-- Check backup file contents
RESTORE FILELISTONLY
FROM DISK = 'C:\Backup\AdventureWorks2019.bak';
GO

-- Restore the database
USE master;
GO

RESTORE DATABASE AdventureWorks2019
FROM DISK = 'C:\Backup\AdventureWorks2019.bak'
WITH
    MOVE 'AdventureWorks2019' TO 'C:\SQLData\AdventureWorks2019.mdf',
    MOVE 'AdventureWorks2019_log' TO 'C:\SQLLog\AdventureWorks2019_log.ldf',
    REPLACE,
    STATS = 10;
GO

-- Verify database is online
SELECT name, state_desc, recovery_model_desc
FROM sys.databases
WHERE name = 'AdventureWorks2019';
GO
```

## PART 2: FULL BACKUP (WITHOUT COMPRESSION)

### STEP 2.1: CHECK CURRENT COMPRESSION SETTING

```sql
-- Check if backup compression is enabled by default
EXEC sp_configure 'backup compression default';
GO

-- Result: run_value = 0 means compression is OFF by default
```

### STEP 2.2: CREATE FULL BACKUP WITHOUT COMPRESSION

```sql
-- Full backup without compression
BACKUP DATABASE AdventureWorks2019
TO DISK = 'C:\SQLBackups\AdventureWorks2019_Full_NoCompression.bak'
WITH
    FORMAT,
    INIT,
    NAME = 'AdventureWorks2019-Full-NoCompression',
    STATS = 10,
    NO_COMPRESSION;  -- Explicitly disable compression
GO
```

### STEP 2.3: CHECK BACKUP SIZE

```sql
-- Get backup file size
RESTORE HEADERONLY
FROM DISK = 'C:\SQLBackups\AdventureWorks2019_Full_NoCompression.bak';
GO

-- Look at BackupSize and CompressedBackupSize columns
-- When no compression: BackupSize = CompressedBackupSize

-- Or check file size via xp_cmdshell
EXEC xp_cmdshell 'dir C:\SQLBackups\AdventureWorks2019_Full_NoCompression.bak';
GO
```

## PART 3: CHANGE RECOVERY MODEL TO FULL

### STEP 3.1: CHECK CURRENT RECOVERY MODEL

```sql
SELECT name, recovery_model_desc
FROM sys.databases
WHERE name = 'AdventureWorks2019';
GO

-- AdventureWorks is typically in SIMPLE recovery model by default
```

### STEP 3.2: CHANGE TO FULL RECOVERY MODEL

```sql
-- Change recovery model to FULL
ALTER DATABASE AdventureWorks2019
SET RECOVERY FULL;
GO

-- Verify the change
SELECT name, recovery_model_desc
FROM sys.databases
WHERE name = 'AdventureWorks2019';
GO

-- Result should show: FULL
```

IMPORTANT: After changing to FULL recovery model, you must take a FULL backup
to start a new backup chain. Transaction log backups won't work properly
until you have a base FULL backup.

## PART 4: ENABLE BACKUP COMPRESSION

### STEP 4.1: ENABLE BACKUP COMPRESSION SERVER-WIDE

```sql
-- Enable backup compression as default
EXEC sp_configure 'backup compression default', 1;
RECONFIGURE;
GO

-- Verify setting
EXEC sp_configure 'backup compression default';
GO

-- Result: run_value = 1 means compression is ON by default
```

### STEP 4.2: ALTERNATIVE - ENABLE VIA SSMS

1. Right-click server in Object Explorer
2. Select Properties
3. Go to "Database Settings" page
4. Check "Compress backup"
5. Click OK

## PART 5: FULL BACKUP (WITH COMPRESSION)

### STEP 5.1: CREATE FULL BACKUP WITH COMPRESSION

    -- Full backup with compression (now default)
    BACKUP DATABASE AdventureWorks2019
    TO DISK = 'C:\SQLBackups\AdventureWorks2019_Full_Compressed.bak'
    WITH
        FORMAT,
        INIT,
        NAME = 'AdventureWorks2019-Full-Compressed',
        STATS = 10,
        COMPRESSION;  -- Explicitly enable (or rely on default)
    GO

## PART 6: COMPARE BACKUP SIZES

### STEP 6.1: GET BACKUP INFORMATION

    -- Compare both backups
    DECLARE @NoCompression TABLE (
        BackupName nvarchar(128),
        BackupDescription nvarchar(255),
        BackupType smallint,
        ExpirationDate datetime,
        Compressed bit,
        Position smallint,
        DeviceType tinyint,
        UserName nvarchar(128),
        ServerName nvarchar(128),
        DatabaseName nvarchar(128),
        DatabaseVersion int,
        DatabaseCreationDate datetime,
        BackupSize numeric(20,0),
        FirstLSN numeric(25,0),
        LastLSN numeric(25,0),
        CheckpointLSN numeric(25,0),
        DatabaseBackupLSN numeric(25,0),
        BackupStartDate datetime,
        BackupFinishDate datetime,
        SortOrder smallint,
        CodePage smallint,
        UnicodeLocaleId int,
        UnicodeComparisonStyle int,
        CompatibilityLevel tinyint,
        SoftwareVendorId int,
        SoftwareVersionMajor int,
        SoftwareVersionMinor int,
        SoftwareVersionBuild int,
        MachineName nvarchar(128),
        Flags int,
        BindingID uniqueidentifier,
        RecoveryForkID uniqueidentifier,
        Collation nvarchar(128),
        FamilyGUID uniqueidentifier,
        HasBulkLoggedData bit,
        IsSnapshot bit,
        IsReadOnly bit,
        IsSingleUser bit,
        HasBackupChecksums bit,
        IsDamaged bit,
        BeginsLogChain bit,
        HasIncompleteMetaData bit,
        IsForceOffline bit,
        IsCopyOnly bit,
        FirstRecoveryForkID uniqueidentifier,
        ForkPointLSN numeric(25,0),
        RecoveryModel nvarchar(60),
        DifferentialBaseLSN numeric(25,0),
        DifferentialBaseGUID uniqueidentifier,
        BackupTypeDescription nvarchar(60),
        BackupSetGUID uniqueidentifier,
        CompressedBackupSize numeric(20,0),
        Containment tinyint,
        KeyAlgorithm nvarchar(32),
        EncryptorThumbprint varbinary(20),
        EncryptorType nvarchar(32)
    );

    INSERT INTO @NoCompression
    EXEC('RESTORE HEADERONLY FROM DISK = ''C:\SQLBackups\AdventureWorks2019_Full_NoCompression.bak''');

    DECLARE @Compressed TABLE (
        BackupName nvarchar(128), BackupDescription nvarchar(255), BackupType smallint,
        ExpirationDate datetime, Compressed bit, Position smallint, DeviceType tinyint,
        UserName nvarchar(128), ServerName nvarchar(128), DatabaseName nvarchar(128),
        DatabaseVersion int, DatabaseCreationDate datetime, BackupSize numeric(20,0),
        FirstLSN numeric(25,0), LastLSN numeric(25,0), CheckpointLSN numeric(25,0),
        DatabaseBackupLSN numeric(25,0), BackupStartDate datetime, BackupFinishDate datetime,
        SortOrder smallint, CodePage smallint, UnicodeLocaleId int, UnicodeComparisonStyle int,
        CompatibilityLevel tinyint, SoftwareVendorId int, SoftwareVersionMajor int,
        SoftwareVersionMinor int, SoftwareVersionBuild int, MachineName nvarchar(128),
        Flags int, BindingID uniqueidentifier, RecoveryForkID uniqueidentifier,
        Collation nvarchar(128), FamilyGUID uniqueidentifier, HasBulkLoggedData bit,
        IsSnapshot bit, IsReadOnly bit, IsSingleUser bit, HasBackupChecksums bit,
        IsDamaged bit, BeginsLogChain bit, HasIncompleteMetaData bit, IsForceOffline bit,
        IsCopyOnly bit, FirstRecoveryForkID uniqueidentifier, ForkPointLSN numeric(25,0),
        RecoveryModel nvarchar(60), DifferentialBaseLSN numeric(25,0),
        DifferentialBaseGUID uniqueidentifier, BackupTypeDescription nvarchar(60),
        BackupSetGUID uniqueidentifier, CompressedBackupSize numeric(20,0),
        Containment tinyint, KeyAlgorithm nvarchar(32), EncryptorThumbprint varbinary(20),
        EncryptorType nvarchar(32)
    );

    INSERT INTO @Compressed
    EXEC('RESTORE HEADERONLY FROM DISK = ''C:\SQLBackups\AdventureWorks2019_Full_Compressed.bak''');

    -- Compare sizes
    SELECT
        'No Compression' AS BackupType,
        BackupSize / 1024 / 1024 AS BackupSizeMB,
        CompressedBackupSize / 1024 / 1024 AS ActualSizeMB,
        100.0 AS CompressionRatio
    FROM @NoCompression
    UNION ALL
    SELECT
        'With Compression' AS BackupType,
        BackupSize / 1024 / 1024 AS BackupSizeMB,
        CompressedBackupSize / 1024 / 1024 AS ActualSizeMB,
        CAST(CompressedBackupSize AS FLOAT) / CAST(BackupSize AS FLOAT) * 100 AS CompressionRatio
    FROM @Compressed;
    GO

STEP 6.2: SIMPLE SIZE COMPARISON
--------------------------------

    -- Simpler approach using backup history
    SELECT
        bs.backup_start_date,
        bs.backup_finish_date,
        bs.type AS BackupType,
        CASE bs.type
            WHEN 'D' THEN 'Full'
            WHEN 'I' THEN 'Differential'
            WHEN 'L' THEN 'Log'
        END AS BackupTypeDesc,
        bs.backup_size / 1024 / 1024 AS BackupSizeMB,
        bs.compressed_backup_size / 1024 / 1024 AS CompressedSizeMB,
        CAST(100.0 - (bs.compressed_backup_size * 100.0 / bs.backup_size) AS DECIMAL(5,2)) AS CompressionSavingsPercent,
        bmf.physical_device_name AS BackupFile
    FROM msdb.dbo.backupset bs
    JOIN msdb.dbo.backupmediafamily bmf ON bs.media_set_id = bmf.media_set_id
    WHERE bs.database_name = 'AdventureWorks2019'
    ORDER BY bs.backup_start_date DESC;
    GO

EXPECTED RESULTS:
-----------------
Typical compression ratios for AdventureWorks:

| Backup Type | Backup Size | Compressed Size | Compression Savings |
|-------------|-------------|-----------------|---------------------|
| No Compression | ~200 MB | ~200 MB | 0% |
| With Compression | ~200 MB | ~50 MB | ~75% |

---
                    PART 7: DATA CHANGES AND DIFFERENTIAL BACKUP
---

STEP 7.1: VIEW CURRENT DATA IN PERSON.PERSONPHONE
-------------------------------------------------

    USE AdventureWorks2019;
    GO

    -- View some records
    SELECT TOP 10
        BusinessEntityID,
        PhoneNumber,
        PhoneNumberTypeID,
        ModifiedDate
    FROM Person.PersonPhone
    ORDER BY BusinessEntityID;
    GO

STEP 7.2: MAKE DATA CHANGES
---------------------------

    -- Update some phone numbers
    UPDATE Person.PersonPhone
    SET PhoneNumber = '555-0100',
        ModifiedDate = GETDATE()
    WHERE BusinessEntityID BETWEEN 1 AND 10;
    GO

    -- Verify changes
    SELECT TOP 10
        BusinessEntityID,
        PhoneNumber,
        PhoneNumberTypeID,
        ModifiedDate
    FROM Person.PersonPhone
    ORDER BY BusinessEntityID;
    GO

STEP 7.3: CREATE DIFFERENTIAL BACKUP
------------------------------------

    -- Differential backup (only changes since last FULL backup)
    BACKUP DATABASE AdventureWorks2019
    TO DISK = 'C:\SQLBackups\AdventureWorks2019_Diff1.bak'
    WITH
        DIFFERENTIAL,
        FORMAT,
        INIT,
        NAME = 'AdventureWorks2019-Differential-1',
        STATS = 10;
    GO

---
                    PART 8: ADDITIONAL CHANGES AND LOG BACKUP 1
---

STEP 8.1: MAKE MORE CHANGES
---------------------------

    USE AdventureWorks2019;
    GO

    -- Make more changes
    UPDATE Person.PersonPhone
    SET PhoneNumber = '555-0200',
        ModifiedDate = GETDATE()
    WHERE BusinessEntityID BETWEEN 11 AND 20;
    GO

    -- Insert new record
    INSERT INTO Person.PersonPhone (BusinessEntityID, PhoneNumber, PhoneNumberTypeID, ModifiedDate)
    SELECT
        (SELECT MAX(BusinessEntityID) FROM Person.Person),
        '555-NEW-001',
        1,
        GETDATE();
    GO

STEP 8.2: TRANSACTION LOG BACKUP 1
----------------------------------

    -- Transaction log backup
    BACKUP LOG AdventureWorks2019
    TO DISK = 'C:\SQLBackups\AdventureWorks2019_Log1.trn'
    WITH
        FORMAT,
        INIT,
        NAME = 'AdventureWorks2019-Log-1',
        STATS = 10;
    GO

---
                    PART 9: REPEAT LOG BACKUPS (2-3 MORE TIMES)
---

STEP 9.1: LOG BACKUP 2 - MORE CHANGES
-------------------------------------

    USE AdventureWorks2019;
    GO

    -- Changes for Log Backup 2
    UPDATE Person.PersonPhone
    SET PhoneNumber = '555-0300',
        ModifiedDate = GETDATE()
    WHERE BusinessEntityID BETWEEN 21 AND 30;
    GO

    -- Transaction log backup 2
    BACKUP LOG AdventureWorks2019
    TO DISK = 'C:\SQLBackups\AdventureWorks2019_Log2.trn'
    WITH
        FORMAT,
        INIT,
        NAME = 'AdventureWorks2019-Log-2',
        STATS = 10;
    GO

STEP 9.2: LOG BACKUP 3 - MORE CHANGES
-------------------------------------

    USE AdventureWorks2019;
    GO

    -- Changes for Log Backup 3
    UPDATE Person.PersonPhone
    SET PhoneNumber = '555-0400',
        ModifiedDate = GETDATE()
    WHERE BusinessEntityID BETWEEN 31 AND 40;
    GO

    -- Transaction log backup 3
    BACKUP LOG AdventureWorks2019
    TO DISK = 'C:\SQLBackups\AdventureWorks2019_Log3.trn'
    WITH
        FORMAT,
        INIT,
        NAME = 'AdventureWorks2019-Log-3',
        STATS = 10;
    GO

STEP 9.3: FINAL CHANGES (BEFORE NOTING TIME)
--------------------------------------------

    USE AdventureWorks2019;
    GO

    -- Final changes
    UPDATE Person.PersonPhone
    SET PhoneNumber = '555-FINAL',
        ModifiedDate = GETDATE()
    WHERE BusinessEntityID BETWEEN 41 AND 50;
    GO

    -- Take final log backup
    BACKUP LOG AdventureWorks2019
    TO DISK = 'C:\SQLBackups\AdventureWorks2019_Log4.trn'
    WITH
        FORMAT,
        INIT,
        NAME = 'AdventureWorks2019-Log-4',
        STATS = 10;
    GO

---
                    PART 10: NOTE THE TIME
---

STEP 10.1: RECORD THE EXACT TIME
--------------------------------

    -- Get and record the current time
    -- THIS IS THE TIME YOU WILL RESTORE TO!
    SELECT GETDATE() AS 'RESTORE_POINT_TIME';
    GO

    -- Example output: 2024-01-15 14:30:45.123
    -- WRITE THIS DOWN! You will need it for point-in-time restore

    -- Also verify the data one more time
    SELECT TOP 50
        BusinessEntityID,
        PhoneNumber,
        ModifiedDate
    FROM Person.PersonPhone
    ORDER BY BusinessEntityID;
    GO

---
                    PART 11: WAIT AND DELETE TABLE
---

STEP 11.1: WAIT 2-3 MINUTES
---------------------------

    -- Wait 2-3 minutes (you can use WAITFOR)
    WAITFOR DELAY '00:03:00';  -- Wait 3 minutes
    GO

    -- Or just wait manually for 2-3 minutes

STEP 11.2: DELETE THE TABLE
---------------------------

    USE AdventureWorks2019;
    GO

    -- Record time of deletion
    SELECT GETDATE() AS 'TABLE_DELETION_TIME';
    GO

    -- Delete (drop) the table
    DROP TABLE Person.PersonPhone;
    GO

    -- Verify table is gone
    SELECT * FROM Person.PersonPhone;
    GO
    -- Error: Invalid object name 'Person.PersonPhone'

---
                    PART 12: POINT-IN-TIME RESTORE WITH TAIL-LOG BACKUP
---

STEP 12.1: TAKE TAIL-LOG BACKUP
-------------------------------

A tail-log backup captures all log records that haven't been backed up yet.
This is critical for recovering up to the point of failure!

    USE master;
    GO

    -- Take tail-log backup (captures everything since last log backup)
    BACKUP LOG AdventureWorks2019
    TO DISK = 'C:\SQLBackups\AdventureWorks2019_TailLog.trn'
    WITH
        FORMAT,
        INIT,
        NAME = 'AdventureWorks2019-TailLog',
        NO_TRUNCATE,  -- Don't truncate log (important for recovery)
        NORECOVERY,   -- Leave database in restoring state
        STATS = 10;
    GO

NOTE: NO_TRUNCATE captures the log tail even if the database is damaged.
      NORECOVERY leaves the database ready for more restores.

STEP 12.2: RESTORE FULL BACKUP (WITH NORECOVERY)
------------------------------------------------

    -- Start the restore chain with FULL backup
    RESTORE DATABASE AdventureWorks2019
    FROM DISK = 'C:\SQLBackups\AdventureWorks2019_Full_Compressed.bak'
    WITH
        REPLACE,
        NORECOVERY,  -- More restores to come
        STATS = 10;
    GO

STEP 12.3: RESTORE DIFFERENTIAL BACKUP (WITH NORECOVERY)
--------------------------------------------------------

    -- Apply differential backup
    RESTORE DATABASE AdventureWorks2019
    FROM DISK = 'C:\SQLBackups\AdventureWorks2019_Diff1.bak'
    WITH
        NORECOVERY,
        STATS = 10;
    GO

STEP 12.4: RESTORE TRANSACTION LOG BACKUPS (WITH NORECOVERY)
------------------------------------------------------------

    -- Apply log backup 1
    RESTORE LOG AdventureWorks2019
    FROM DISK = 'C:\SQLBackups\AdventureWorks2019_Log1.trn'
    WITH
        NORECOVERY,
        STATS = 10;
    GO

    -- Apply log backup 2
    RESTORE LOG AdventureWorks2019
    FROM DISK = 'C:\SQLBackups\AdventureWorks2019_Log2.trn'
    WITH
        NORECOVERY,
        STATS = 10;
    GO

    -- Apply log backup 3
    RESTORE LOG AdventureWorks2019
    FROM DISK = 'C:\SQLBackups\AdventureWorks2019_Log3.trn'
    WITH
        NORECOVERY,
        STATS = 10;
    GO

    -- Apply log backup 4
    RESTORE LOG AdventureWorks2019
    FROM DISK = 'C:\SQLBackups\AdventureWorks2019_Log4.trn'
    WITH
        NORECOVERY,
        STATS = 10;
    GO

STEP 12.5: RESTORE TAIL-LOG WITH POINT-IN-TIME (STOPAT)
-------------------------------------------------------

    -- Apply tail-log backup with STOPAT (point-in-time recovery)
    -- Replace the datetime with the time you noted in Step 10!
    RESTORE LOG AdventureWorks2019
    FROM DISK = 'C:\SQLBackups\AdventureWorks2019_TailLog.trn'
    WITH
        RECOVERY,  -- This is the last restore, bring database online
        STOPAT = '2024-01-15 14:30:45',  -- YOUR NOTED TIME HERE!
        STATS = 10;
    GO

NOTE: STOPAT = 'YYYY-MM-DD HH:MM:SS' recovers to that exact point in time.
      All transactions after that time are rolled back.

---
                    PART 13: VERIFY RESTORATION
---

STEP 13.1: CHECK DATABASE STATUS
--------------------------------

    SELECT
        name,
        state_desc,
        recovery_model_desc
    FROM sys.databases
    WHERE name = 'AdventureWorks2019';
    GO

    -- Should show: ONLINE

STEP 13.2: VERIFY TABLE EXISTS
------------------------------

    USE AdventureWorks2019;
    GO

    -- Check if Person.PersonPhone table exists
    SELECT
        SCHEMA_NAME(schema_id) AS SchemaName,
        name AS TableName
    FROM sys.tables
    WHERE name = 'PersonPhone';
    GO

    -- Should return the PersonPhone table

STEP 13.3: VERIFY DATA CHANGES ARE INTACT
-----------------------------------------

    -- Check the data - all changes made BEFORE the noted time should be there
    SELECT TOP 50
        BusinessEntityID,
        PhoneNumber,
        ModifiedDate
    FROM Person.PersonPhone
    ORDER BY BusinessEntityID;
    GO

    -- You should see:
    -- BusinessEntityID 1-10:  PhoneNumber = '555-0100'
    -- BusinessEntityID 11-20: PhoneNumber = '555-0200'
    -- BusinessEntityID 21-30: PhoneNumber = '555-0300'
    -- BusinessEntityID 31-40: PhoneNumber = '555-0400'
    -- BusinessEntityID 41-50: PhoneNumber = '555-FINAL'

---
                    PART 14: SIMULATE DISASTER (OFFLINE + DELETE MDF)
---

STEP 14.1: PUT DATABASE OFFLINE
-------------------------------

    USE master;
    GO

    ALTER DATABASE AdventureWorks2019 SET OFFLINE;
    GO

    -- Verify offline state
    SELECT name, state_desc
    FROM sys.databases
    WHERE name = 'AdventureWorks2019';
    GO
    -- Should show: OFFLINE

STEP 14.2: DELETE THE MDF FILE
------------------------------

    -- Delete the data file using xp_cmdshell
    EXEC xp_cmdshell 'del "C:\SQLData\AdventureWorks2019.mdf"';
    GO

    -- Verify file is deleted
    EXEC xp_cmdshell 'dir "C:\SQLData\AdventureWorks2019.mdf"';
    GO
    -- Should show: File Not Found

NOTE: You can also delete the log file if you want complete disaster simulation:
    EXEC xp_cmdshell 'del "C:\SQLLog\AdventureWorks2019_log.ldf"';

STEP 14.3: VERIFY DATABASE IS INACCESSIBLE
------------------------------------------

    -- Try to bring database online (will fail)
    ALTER DATABASE AdventureWorks2019 SET ONLINE;
    GO
    -- Error: Operating system error 2 (The system cannot find the file specified)

---
                    PART 15: RESTORE FROM BACKUP CHAIN
---

STEP 15.1: DROP THE DAMAGED DATABASE ENTRY
------------------------------------------

    USE master;
    GO

    -- Drop the database entry (since files are gone)
    DROP DATABASE AdventureWorks2019;
    GO

STEP 15.2: RESTORE FULL BACKUP
------------------------------

    -- Restore from Full backup
    RESTORE DATABASE AdventureWorks2019
    FROM DISK = 'C:\SQLBackups\AdventureWorks2019_Full_Compressed.bak'
    WITH
        MOVE 'AdventureWorks2019' TO 'C:\SQLData\AdventureWorks2019.mdf',
        MOVE 'AdventureWorks2019_log' TO 'C:\SQLLog\AdventureWorks2019_log.ldf',
        NORECOVERY,
        REPLACE,
        STATS = 10;
    GO

STEP 15.3: RESTORE DIFFERENTIAL BACKUP
--------------------------------------

    RESTORE DATABASE AdventureWorks2019
    FROM DISK = 'C:\SQLBackups\AdventureWorks2019_Diff1.bak'
    WITH
        NORECOVERY,
        STATS = 10;
    GO

STEP 15.4: RESTORE ALL TRANSACTION LOG BACKUPS
----------------------------------------------

    -- Log backup 1
    RESTORE LOG AdventureWorks2019
    FROM DISK = 'C:\SQLBackups\AdventureWorks2019_Log1.trn'
    WITH NORECOVERY, STATS = 10;
    GO

    -- Log backup 2
    RESTORE LOG AdventureWorks2019
    FROM DISK = 'C:\SQLBackups\AdventureWorks2019_Log2.trn'
    WITH NORECOVERY, STATS = 10;
    GO

    -- Log backup 3
    RESTORE LOG AdventureWorks2019
    FROM DISK = 'C:\SQLBackups\AdventureWorks2019_Log3.trn'
    WITH NORECOVERY, STATS = 10;
    GO

    -- Log backup 4 (last one before deletion)
    RESTORE LOG AdventureWorks2019
    FROM DISK = 'C:\SQLBackups\AdventureWorks2019_Log4.trn'
    WITH RECOVERY, STATS = 10;  -- RECOVERY brings database online
    GO

NOTE: We use RECOVERY on the LAST restore to bring the database online.
      We skip the tail-log backup because it contains the DROP TABLE command.

STEP 15.5: VERIFY COMPLETE RESTORATION
--------------------------------------

    USE AdventureWorks2019;
    GO

    -- Verify database is online
    SELECT name, state_desc
    FROM sys.databases
    WHERE name = 'AdventureWorks2019';
    GO

    -- Verify table exists
    SELECT COUNT(*) AS TotalRecords FROM Person.PersonPhone;
    GO

    -- Verify all changes
    SELECT TOP 50
        BusinessEntityID,
        PhoneNumber,
        ModifiedDate
    FROM Person.PersonPhone
    ORDER BY BusinessEntityID;
    GO

---
                    COMPLETE T-SQL SCRIPT
---

Below is a single comprehensive script for the entire exercise:

-------------------------------------------------------------------------------
-- BACKUP AND RESTORE COMPLETE SCRIPT
-- Run this script from HOST machine connected to VM SQL Server
-------------------------------------------------------------------------------

USE master;
GO

-- ============================================================================
-- SECTION 1: SETUP
-- ============================================================================

-- Create backup directory
EXEC xp_cmdshell 'mkdir C:\SQLBackups';
GO

-- Restore AdventureWorks (adjust path as needed)
RESTORE DATABASE AdventureWorks2019
FROM DISK = 'C:\Backup\AdventureWorks2019.bak'
WITH
    MOVE 'AdventureWorks2019' TO 'C:\SQLData\AdventureWorks2019.mdf',
    MOVE 'AdventureWorks2019_log' TO 'C:\SQLLog\AdventureWorks2019_log.ldf',
    REPLACE, STATS = 10;
GO

-- ============================================================================
-- SECTION 2: FULL BACKUP WITHOUT COMPRESSION
-- ============================================================================

BACKUP DATABASE AdventureWorks2019
TO DISK = 'C:\SQLBackups\AdventureWorks2019_Full_NoCompression.bak'
WITH FORMAT, INIT, NO_COMPRESSION, STATS = 10;
GO

-- ============================================================================
-- SECTION 3: CHANGE RECOVERY MODEL TO FULL
-- ============================================================================

ALTER DATABASE AdventureWorks2019 SET RECOVERY FULL;
GO

-- ============================================================================
-- SECTION 4: ENABLE COMPRESSION
-- ============================================================================

EXEC sp_configure 'backup compression default', 1;
RECONFIGURE;
GO

-- ============================================================================
-- SECTION 5: FULL BACKUP WITH COMPRESSION
-- ============================================================================

BACKUP DATABASE AdventureWorks2019
TO DISK = 'C:\SQLBackups\AdventureWorks2019_Full_Compressed.bak'
WITH FORMAT, INIT, COMPRESSION, STATS = 10;
GO

-- ============================================================================
-- SECTION 6: COMPARE SIZES
-- ============================================================================

SELECT
    bs.backup_start_date,
    CASE bs.type WHEN 'D' THEN 'Full' WHEN 'I' THEN 'Diff' WHEN 'L' THEN 'Log' END AS Type,
    bs.backup_size / 1024 / 1024 AS SizeMB,
    bs.compressed_backup_size / 1024 / 1024 AS CompressedMB,
    bmf.physical_device_name
FROM msdb.dbo.backupset bs
JOIN msdb.dbo.backupmediafamily bmf ON bs.media_set_id = bmf.media_set_id
WHERE bs.database_name = 'AdventureWorks2019'
ORDER BY bs.backup_start_date;
GO

-- ============================================================================
-- SECTION 7: DATA CHANGES + DIFFERENTIAL BACKUP
-- ============================================================================

USE AdventureWorks2019;
UPDATE Person.PersonPhone SET PhoneNumber = '555-0100', ModifiedDate = GETDATE()
WHERE BusinessEntityID BETWEEN 1 AND 10;
GO

BACKUP DATABASE AdventureWorks2019
TO DISK = 'C:\SQLBackups\AdventureWorks2019_Diff1.bak'
WITH DIFFERENTIAL, FORMAT, INIT, STATS = 10;
GO

-- ============================================================================
-- SECTION 8-9: LOG BACKUPS WITH CHANGES
-- ============================================================================

-- Log 1
UPDATE Person.PersonPhone SET PhoneNumber = '555-0200', ModifiedDate = GETDATE()
WHERE BusinessEntityID BETWEEN 11 AND 20;
BACKUP LOG AdventureWorks2019 TO DISK = 'C:\SQLBackups\AdventureWorks2019_Log1.trn'
WITH FORMAT, INIT;
GO

-- Log 2
UPDATE Person.PersonPhone SET PhoneNumber = '555-0300', ModifiedDate = GETDATE()
WHERE BusinessEntityID BETWEEN 21 AND 30;
BACKUP LOG AdventureWorks2019 TO DISK = 'C:\SQLBackups\AdventureWorks2019_Log2.trn'
WITH FORMAT, INIT;
GO

-- Log 3
UPDATE Person.PersonPhone SET PhoneNumber = '555-0400', ModifiedDate = GETDATE()
WHERE BusinessEntityID BETWEEN 31 AND 40;
BACKUP LOG AdventureWorks2019 TO DISK = 'C:\SQLBackups\AdventureWorks2019_Log3.trn'
WITH FORMAT, INIT;
GO

-- Log 4
UPDATE Person.PersonPhone SET PhoneNumber = '555-FINAL', ModifiedDate = GETDATE()
WHERE BusinessEntityID BETWEEN 41 AND 50;
BACKUP LOG AdventureWorks2019 TO DISK = 'C:\SQLBackups\AdventureWorks2019_Log4.trn'
WITH FORMAT, INIT;
GO

-- ============================================================================
-- SECTION 10: NOTE THE TIME
-- ============================================================================

SELECT GETDATE() AS 'RESTORE_POINT_TIME - WRITE THIS DOWN!';
SELECT TOP 50 BusinessEntityID, PhoneNumber, ModifiedDate
FROM Person.PersonPhone ORDER BY BusinessEntityID;
GO

-- ============================================================================
-- SECTION 11: WAIT AND DROP TABLE
-- ============================================================================

WAITFOR DELAY '00:03:00';  -- Wait 3 minutes
DROP TABLE Person.PersonPhone;
GO

-- ============================================================================
-- SECTION 12: POINT-IN-TIME RESTORE
-- ============================================================================

USE master;
GO

-- Tail-log backup
BACKUP LOG AdventureWorks2019 TO DISK = 'C:\SQLBackups\AdventureWorks2019_TailLog.trn'
WITH FORMAT, INIT, NO_TRUNCATE, NORECOVERY;
GO

-- Restore chain
RESTORE DATABASE AdventureWorks2019 FROM DISK = 'C:\SQLBackups\AdventureWorks2019_Full_Compressed.bak'
WITH REPLACE, NORECOVERY;
RESTORE DATABASE AdventureWorks2019 FROM DISK = 'C:\SQLBackups\AdventureWorks2019_Diff1.bak'
WITH NORECOVERY;
RESTORE LOG AdventureWorks2019 FROM DISK = 'C:\SQLBackups\AdventureWorks2019_Log1.trn' WITH NORECOVERY;
RESTORE LOG AdventureWorks2019 FROM DISK = 'C:\SQLBackups\AdventureWorks2019_Log2.trn' WITH NORECOVERY;
RESTORE LOG AdventureWorks2019 FROM DISK = 'C:\SQLBackups\AdventureWorks2019_Log3.trn' WITH NORECOVERY;
RESTORE LOG AdventureWorks2019 FROM DISK = 'C:\SQLBackups\AdventureWorks2019_Log4.trn' WITH NORECOVERY;
RESTORE LOG AdventureWorks2019 FROM DISK = 'C:\SQLBackups\AdventureWorks2019_TailLog.trn'
WITH RECOVERY, STOPAT = '2024-01-15 14:30:45';  -- USE YOUR NOTED TIME!
GO

-- ============================================================================
-- SECTION 13: VERIFY
-- ============================================================================

USE AdventureWorks2019;
SELECT TOP 50 BusinessEntityID, PhoneNumber FROM Person.PersonPhone ORDER BY BusinessEntityID;
GO

---
-- END OF SCRIPT
---

---
                    SUMMARY
---

Backup Chain Created:
--------------------
C:\SQLBackups\
├── AdventureWorks2019_Full_NoCompression.bak  (Full, no compression)
├── AdventureWorks2019_Full_Compressed.bak     (Full, compressed)
├── AdventureWorks2019_Diff1.bak               (Differential)
├── AdventureWorks2019_Log1.trn                (Transaction Log 1)
├── AdventureWorks2019_Log2.trn                (Transaction Log 2)
├── AdventureWorks2019_Log3.trn                (Transaction Log 3)
├── AdventureWorks2019_Log4.trn                (Transaction Log 4)
└── AdventureWorks2019_TailLog.trn             (Tail-log backup)

Restore Order:
--------------
1. FULL backup (NORECOVERY)
2. DIFFERENTIAL backup (NORECOVERY)
3. LOG backups in order (NORECOVERY)
4. TAIL-LOG with STOPAT (RECOVERY)

Key Concepts:
-------------
- NORECOVERY: More restores to come, keep database in restoring state
- RECOVERY: Last restore, bring database online
- STOPAT: Point-in-time recovery to specific datetime
- NO_TRUNCATE: Capture log tail even from damaged database
- Tail-log backup: Critical for recovering to point of failure

