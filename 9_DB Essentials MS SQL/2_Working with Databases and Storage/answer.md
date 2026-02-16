# WORKING WITH DATABASES AND STORAGE - ANSWER

This guide covers moving SQL Server databases between disks using various
methods: Detach/Attach, ALTER DATABASE MODIFY FILE, and special procedures
for system databases.

## PART 1: ADD NEW DISKS TO VM

### STEP 1.1: ADD VIRTUAL DISKS IN VIRTUALBOX

1. Shut down the VM
2. Open VirtualBox Manager
3. Select your VM -> Settings -> Storage
4. Click on "Controller: SATA"
5. Click the "Add hard disk" icon (disk with plus sign)

Create Data Disk:
- Click "Create"
- VDI (VirtualBox Disk Image)
- Dynamically allocated
- Size: 20 GB
- Name: SQLData.vdi

Create Log Disk:
- Click "Create" again
- VDI (VirtualBox Disk Image)
- Dynamically allocated
- Size: 10 GB
- Name: SQLLog.vdi

6. Click OK and start the VM

### STEP 1.2: INITIALIZE AND FORMAT DISKS IN WINDOWS

**Method 1: Using Disk Management GUI**

1. Open Server Manager
2. Click Tools -> Computer Management
3. Click Disk Management (or run: diskmgmt.msc)

4. You'll see two new disks as "Unknown" and "Not Initialized"

5. Initialize Data Disk:
   - Right-click on Disk 1 -> "Initialize Disk"
   - Select GPT (GUID Partition Table)
   - Click OK

6. Create Volume on Data Disk:
   - Right-click on "Unallocated" space -> "New Simple Volume"
   - Volume size: Use maximum
   - Drive letter: D:
   - File system: NTFS
   - Volume label: SQLData
   - Perform quick format
   - Click Finish

7. Initialize and Create Volume on Log Disk:
   - Right-click on Disk 2 -> "Initialize Disk" -> GPT
   - Right-click "Unallocated" -> "New Simple Volume"
   - Drive letter: E:
   - Volume label: SQLLog
   - NTFS, quick format
   - Click Finish

**Method 2: Using PowerShell**

```powershell
# List all disks
Get-Disk

# Initialize new disks (replace disk numbers as needed)
Initialize-Disk -Number 1 -PartitionStyle GPT
Initialize-Disk -Number 2 -PartitionStyle GPT

# Create partitions and format
# Data Disk (D:)
New-Partition -DiskNumber 1 -UseMaximumSize -DriveLetter D
Format-Volume -DriveLetter D -FileSystem NTFS -NewFileSystemLabel "SQLData" -Confirm:$false

# Log Disk (E:)
New-Partition -DiskNumber 2 -UseMaximumSize -DriveLetter E
Format-Volume -DriveLetter E -FileSystem NTFS -NewFileSystemLabel "SQLLog" -Confirm:$false
```

### STEP 1.3: CREATE FOLDER STRUCTURE

```powershell
# Create folders for SQL Server files
New-Item -ItemType Directory -Path "D:\SQLData" -Force
New-Item -ItemType Directory -Path "E:\SQLLog" -Force

# Set permissions for SQL Server service account
$acl = Get-Acl "D:\SQLData"
$permission = "NT SERVICE\MSSQLSERVER","FullControl","ContainerInherit,ObjectInherit","None","Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule $permission
$acl.SetAccessRule($accessRule)
Set-Acl "D:\SQLData" $acl

$acl = Get-Acl "E:\SQLLog"
$acl.SetAccessRule($accessRule)
Set-Acl "E:\SQLLog" $acl
```

Or using icacls:

```cmd
icacls "D:\SQLData" /grant "NT SERVICE\MSSQLSERVER":(OI)(CI)F
icacls "E:\SQLLog" /grant "NT SERVICE\MSSQLSERVER":(OI)(CI)F
```

## PART 2: RESTORE ADVENTUREWORKS DATABASE

### STEP 2.1: DOWNLOAD ADVENTUREWORKS BACKUP

**Option 1: Download from Microsoft GitHub**

1. Open browser and go to:
   https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks

2. Download: AdventureWorks2019.bak
   (or AdventureWorks2017.bak for SQL Server 2017)

3. Save to: C:\Backup\AdventureWorks2019.bak

**Option 2: Using PowerShell**

```powershell
# Create backup directory
New-Item -ItemType Directory -Path "C:\Backup" -Force

# Download AdventureWorks backup
$url = "https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2019.bak"
$output = "C:\Backup\AdventureWorks2019.bak"

Invoke-WebRequest -Uri $url -OutFile $output

# Verify download
Get-Item $output
```

### STEP 2.2: RESTORE DATABASE USING SSMS

1. Open SQL Server Management Studio
2. Connect to your SQL Server instance
3. Right-click "Databases" -> "Restore Database..."

4. Restore Database dialog:
   - Source: Device
   - Click "..." button -> Add -> Browse to C:\Backup\AdventureWorks2019.bak
   - Click OK

5. Select the backup set to restore

6. Options page (left panel):
   - Check "Overwrite the existing database (WITH REPLACE)" if needed
   - Verify file paths:
     * AdventureWorks2019 -> C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\MSSQL\DATA\AdventureWorks2019.mdf
     * AdventureWorks2019_log -> C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\MSSQL\DATA\AdventureWorks2019_log.ldf

7. Click OK to restore

### STEP 2.3: RESTORE DATABASE USING T-SQL

```sql
-- First, check what's in the backup file
RESTORE FILELISTONLY
FROM DISK = 'C:\Backup\AdventureWorks2019.bak';
GO

-- Results show logical names:
-- LogicalName              PhysicalName                                        Type
-- AdventureWorks2019       ...\AdventureWorks2019.mdf                          D
-- AdventureWorks2019_log   ...\AdventureWorks2019_log.ldf                      L

-- Restore the database
RESTORE DATABASE AdventureWorks2019
FROM DISK = 'C:\Backup\AdventureWorks2019.bak'
WITH
    MOVE 'AdventureWorks2019' TO 'C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\MSSQL\DATA\AdventureWorks2019.mdf',
    MOVE 'AdventureWorks2019_log' TO 'C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\MSSQL\DATA\AdventureWorks2019_log.ldf',
    REPLACE,
    STATS = 10;
GO
```

### STEP 2.4: VERIFY RESTORE

```sql
-- Check database exists
SELECT name, state_desc, recovery_model_desc
FROM sys.databases
WHERE name = 'AdventureWorks2019';

-- Check file locations
SELECT
    name AS LogicalName,
    physical_name AS PhysicalPath,
    type_desc AS FileType,
    size * 8 / 1024 AS SizeMB
FROM sys.master_files
WHERE database_id = DB_ID('AdventureWorks2019');
```

## PART 3: CHANGE DEFAULT DATABASE LOCATIONS

### STEP 3.1: USING SSMS GUI

1. In Object Explorer, right-click on server name
2. Select "Properties"
3. Click "Database Settings" page (left panel)

4. Database default locations:
   - Data: D:\SQLData
   - Log: E:\SQLLog
   - Backup: (optionally change to D:\SQLBackup)

5. Click OK

NOTE: This only affects NEW databases. Existing databases keep their locations.

### STEP 3.2: USING T-SQL (Registry)

```sql
-- View current default locations
EXEC xp_instance_regread
    N'HKEY_LOCAL_MACHINE',
    N'Software\Microsoft\MSSQLServer\MSSQLServer',
    N'DefaultData';

EXEC xp_instance_regread
    N'HKEY_LOCAL_MACHINE',
    N'Software\Microsoft\MSSQLServer\MSSQLServer',
    N'DefaultLog';

-- Change default data location
EXEC xp_instance_regwrite
    N'HKEY_LOCAL_MACHINE',
    N'Software\Microsoft\MSSQLServer\MSSQLServer',
    N'DefaultData',
    REG_SZ,
    N'D:\SQLData';

-- Change default log location
EXEC xp_instance_regwrite
    N'HKEY_LOCAL_MACHINE',
    N'Software\Microsoft\MSSQLServer\MSSQLServer',
    N'DefaultLog',
    REG_SZ,
    N'E:\SQLLog';

-- Restart SQL Server for changes to take effect
-- Or use SQL Server Configuration Manager
```

### STEP 3.3: VERIFY DEFAULT LOCATIONS

Create a test database to verify:

```sql
CREATE DATABASE TestDefaultLocation;
GO

-- Check where files were created
SELECT
    name,
    physical_name
FROM sys.master_files
WHERE database_id = DB_ID('TestDefaultLocation');

-- Expected:
-- TestDefaultLocation     D:\SQLData\TestDefaultLocation.mdf
-- TestDefaultLocation_log E:\SQLLog\TestDefaultLocation_log.ldf

-- Clean up
DROP DATABASE TestDefaultLocation;
GO
```

## PART 4: MOVE DATABASE USING DETACH/ATTACH

This method physically moves the files while the database is offline.

### STEP 4.1: CHECK CURRENT FILE LOCATIONS

```sql
USE master;
GO

SELECT
    name AS LogicalName,
    physical_name AS CurrentLocation,
    type_desc AS FileType
FROM sys.master_files
WHERE database_id = DB_ID('AdventureWorks2019');

-- Results:
-- LogicalName              CurrentLocation                                           FileType
-- AdventureWorks2019       C:\...\DATA\AdventureWorks2019.mdf                        ROWS
-- AdventureWorks2019_log   C:\...\DATA\AdventureWorks2019_log.ldf                    LOG
```

### STEP 4.2: DETACH THE DATABASE

**Method 1: Using SSMS**

1. Right-click on "AdventureWorks2019"
2. Select Tasks -> Detach...
3. Check "Drop Connections" if needed
4. Click OK

**Method 2: Using T-SQL**

```sql
USE master;
GO

-- Set database to single user mode to disconnect all users
ALTER DATABASE AdventureWorks2019
SET SINGLE_USER WITH ROLLBACK IMMEDIATE;
GO

-- Detach the database
EXEC sp_detach_db
    @dbname = N'AdventureWorks2019',
    @skipchecks = 'false';
GO
```

### STEP 4.3: PHYSICALLY MOVE THE FILES

Using File Explorer:
1. Navigate to: C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\MSSQL\DATA\
2. Cut AdventureWorks2019.mdf
3. Paste to D:\SQLData\
4. Cut AdventureWorks2019_log.ldf
5. Paste to E:\SQLLog\

Using PowerShell:

```powershell
# Define source and destination paths
$sourceData = "C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\MSSQL\DATA\AdventureWorks2019.mdf"
$sourceLog = "C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\MSSQL\DATA\AdventureWorks2019_log.ldf"
$destData = "D:\SQLData\AdventureWorks2019.mdf"
$destLog = "E:\SQLLog\AdventureWorks2019_log.ldf"

# Move files
Move-Item -Path $sourceData -Destination $destData
Move-Item -Path $sourceLog -Destination $destLog

# Verify files exist in new location
Get-Item $destData
Get-Item $destLog
```

Using Command Prompt:

```cmd
move "C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\MSSQL\DATA\AdventureWorks2019.mdf" "D:\SQLData\"
move "C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\MSSQL\DATA\AdventureWorks2019_log.ldf" "E:\SQLLog\"
```

### STEP 4.4: ATTACH THE DATABASE

**Method 1: Using SSMS**

1. Right-click on "Databases"
2. Select "Attach..."
3. Click "Add..."
4. Browse to D:\SQLData\AdventureWorks2019.mdf
5. Click OK

6. In the dialog, verify both files are listed:
   - D:\SQLData\AdventureWorks2019.mdf
   - E:\SQLLog\AdventureWorks2019_log.ldf

   Note: If log file shows wrong path, click on it and use "..." to browse to E:\SQLLog\

7. Click OK

**Method 2: Using T-SQL**

```sql
USE master;
GO

CREATE DATABASE AdventureWorks2019
ON (FILENAME = 'D:\SQLData\AdventureWorks2019.mdf'),
   (FILENAME = 'E:\SQLLog\AdventureWorks2019_log.ldf')
FOR ATTACH;
GO

-- Or using sp_attach_db (deprecated but still works)
EXEC sp_attach_db
    @dbname = N'AdventureWorks2019',
    @filename1 = N'D:\SQLData\AdventureWorks2019.mdf',
    @filename2 = N'E:\SQLLog\AdventureWorks2019_log.ldf';
GO
```

### STEP 4.5: VERIFY NEW LOCATIONS

```sql
SELECT
    name AS LogicalName,
    physical_name AS NewLocation,
    type_desc AS FileType
FROM sys.master_files
WHERE database_id = DB_ID('AdventureWorks2019');

-- Results should show:
-- AdventureWorks2019       D:\SQLData\AdventureWorks2019.mdf            ROWS
-- AdventureWorks2019_log   E:\SQLLog\AdventureWorks2019_log.ldf         LOG
```

## PART 5: MOVE DATABASE USING ALTER DATABASE MODIFY FILE

This method uses T-SQL to change file paths, then requires database offline
to physically move files.

### STEP 5.1: MODIFY FILE LOCATION IN SYSTEM CATALOG

```sql
USE master;
GO

-- Change the recorded path for data file
ALTER DATABASE AdventureWorks2019
MODIFY FILE (
    NAME = 'AdventureWorks2019',
    FILENAME = 'C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\MSSQL\DATA\AdventureWorks2019.mdf'
);
GO

-- Change the recorded path for log file
ALTER DATABASE AdventureWorks2019
MODIFY FILE (
    NAME = 'AdventureWorks2019_log',
    FILENAME = 'C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\MSSQL\DATA\AdventureWorks2019_log.ldf'
);
GO

-- Note: This only updates the system catalog!
-- The files are NOT physically moved yet.
```

### STEP 5.2: SET DATABASE OFFLINE

```sql
ALTER DATABASE AdventureWorks2019 SET OFFLINE;
GO

-- Or with immediate rollback
ALTER DATABASE AdventureWorks2019
SET OFFLINE WITH ROLLBACK IMMEDIATE;
GO
```

### STEP 5.3: PHYSICALLY MOVE FILES TO NEW LOCATION

PowerShell:

```powershell
# Move data file back to C: drive
Move-Item -Path "D:\SQLData\AdventureWorks2019.mdf" `
          -Destination "C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\MSSQL\DATA\"

# Move log file back to C: drive
Move-Item -Path "E:\SQLLog\AdventureWorks2019_log.ldf" `
          -Destination "C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\MSSQL\DATA\"
```

Command Prompt:

```cmd
move "D:\SQLData\AdventureWorks2019.mdf" "C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\MSSQL\DATA\"
move "E:\SQLLog\AdventureWorks2019_log.ldf" "C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\MSSQL\DATA\"
```

### STEP 5.4: BRING DATABASE ONLINE

```sql
ALTER DATABASE AdventureWorks2019 SET ONLINE;
GO
```

### STEP 5.5: VERIFY THE MOVE

```sql
SELECT
    name AS LogicalName,
    physical_name AS CurrentLocation,
    type_desc AS FileType,
    state_desc AS State
FROM sys.master_files
WHERE database_id = DB_ID('AdventureWorks2019');

-- Should show files back on C: drive
```

## PART 6: MOVE SYSTEM DATABASES

Moving system databases requires special procedures because SQL Server needs
them to start. We'll move: master, msdb, model, and tempdb.

IMPORTANT: Create a backup before moving system databases!

### STEP 6.1: PREPARE NEW LOCATIONS

```powershell
-- Create folders for system databases
New-Item -ItemType Directory -Path "D:\SQLData\System" -Force
New-Item -ItemType Directory -Path "E:\SQLLog\System" -Force

-- Grant permissions
icacls "D:\SQLData\System" /grant "NT SERVICE\MSSQLSERVER":(OI)(CI)F
icacls "E:\SQLLog\System" /grant "NT SERVICE\MSSQLSERVER":(OI)(CI)F
```

### STEP 6.2: CHECK CURRENT SYSTEM DATABASE LOCATIONS

```sql
SELECT
    db.name AS DatabaseName,
    mf.name AS LogicalName,
    mf.physical_name AS CurrentPath,
    mf.type_desc AS FileType
FROM sys.databases db
JOIN sys.master_files mf ON db.database_id = mf.database_id
WHERE db.database_id <= 4  -- System databases: master(1), tempdb(2), model(3), msdb(4)
ORDER BY db.database_id, mf.type;
```

### STEP 6.3: MOVE TEMPDB DATABASE

TempDB is recreated on every SQL Server startup, so we only need to change
the path - no file copying required!

```sql
USE master;
GO

-- Move tempdb data file
ALTER DATABASE tempdb
MODIFY FILE (
    NAME = 'tempdev',
    FILENAME = 'D:\SQLData\System\tempdb.mdf'
);
GO

-- Move tempdb log file
ALTER DATABASE tempdb
MODIFY FILE (
    NAME = 'templog',
    FILENAME = 'E:\SQLLog\System\templog.ldf'
);
GO

-- If you have multiple tempdb files, move them all:
-- ALTER DATABASE tempdb MODIFY FILE (NAME = 'temp2', FILENAME = 'D:\SQLData\System\tempdb_mssql_2.ndf');
-- ALTER DATABASE tempdb MODIFY FILE (NAME = 'temp3', FILENAME = 'D:\SQLData\System\tempdb_mssql_3.ndf');
-- ALTER DATABASE tempdb MODIFY FILE (NAME = 'temp4', FILENAME = 'D:\SQLData\System\tempdb_mssql_4.ndf');

-- Restart SQL Server for changes to take effect
-- Old tempdb files can be deleted after restart
```

### STEP 6.4: MOVE MODEL DATABASE

```sql
USE master;
GO

-- Update system catalog with new paths
ALTER DATABASE model
MODIFY FILE (
    NAME = 'modeldev',
    FILENAME = 'D:\SQLData\System\model.mdf'
);
GO

ALTER DATABASE model
MODIFY FILE (
    NAME = 'modellog',
    FILENAME = 'E:\SQLLog\System\modellog.ldf'
);
GO

-- SQL Server must be stopped to physically move model files
```

### STEP 6.5: MOVE MSDB DATABASE

```sql
USE master;
GO

-- Update system catalog with new paths
ALTER DATABASE msdb
MODIFY FILE (
    NAME = 'MSDBData',
    FILENAME = 'D:\SQLData\System\MSDBData.mdf'
);
GO

ALTER DATABASE msdb
MODIFY FILE (
    NAME = 'MSDBLog',
    FILENAME = 'E:\SQLLog\System\MSDBLog.ldf'
);
GO

-- SQL Server must be stopped to physically move msdb files
```

### STEP 6.6: MOVE MASTER DATABASE (Special Procedure)

Master database requires modifying SQL Server startup parameters.

1. Open SQL Server Configuration Manager

2. Navigate to: SQL Server Services
3. Right-click "SQL Server (MSSQLSERVER)" -> Properties

4. Click "Startup Parameters" tab

5. Modify the -d parameter (master data file):
   - Select: -dC:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\MSSQL\DATA\master.mdf
   - Click "Update"
   - Change to: -dD:\SQLData\System\master.mdf

6. Modify the -l parameter (master log file):
   - Select: -lC:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\MSSQL\DATA\mastlog.ldf
   - Click "Update"
   - Change to: -lE:\SQLLog\System\mastlog.ldf

7. Click OK

NOTE: Do NOT restart SQL Server yet!

### STEP 6.7: STOP SQL SERVER AND MOVE FILES

1. Stop SQL Server Service:

**Method 1: SQL Server Configuration Manager**
- Right-click "SQL Server (MSSQLSERVER)" -> Stop

**Method 2: PowerShell**

```powershell
Stop-Service -Name "MSSQLSERVER" -Force
```

**Method 3: Command Prompt**

```cmd
net stop MSSQLSERVER
```

2. Move the system database files:

PowerShell script:

```powershell
# Define paths
$oldDataPath = "C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\MSSQL\DATA"
$newDataPath = "D:\SQLData\System"
$newLogPath = "E:\SQLLog\System"

# Move master database files
Move-Item "$oldDataPath\master.mdf" "$newDataPath\master.mdf"
Move-Item "$oldDataPath\mastlog.ldf" "$newLogPath\mastlog.ldf"

# Move model database files
Move-Item "$oldDataPath\model.mdf" "$newDataPath\model.mdf"
Move-Item "$oldDataPath\modellog.ldf" "$newLogPath\modellog.ldf"

# Move msdb database files
Move-Item "$oldDataPath\MSDBData.mdf" "$newDataPath\MSDBData.mdf"
Move-Item "$oldDataPath\MSDBLog.ldf" "$newLogPath\MSDBLog.ldf"

# Note: tempdb files will be recreated, no need to move
# But you can delete old tempdb files to free space:
# Remove-Item "$oldDataPath\tempdb.mdf" -ErrorAction SilentlyContinue
# Remove-Item "$oldDataPath\templog.ldf" -ErrorAction SilentlyContinue
```

Command Prompt:

```cmd
move "C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\MSSQL\DATA\master.mdf" "D:\SQLData\System\"
move "C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\MSSQL\DATA\mastlog.ldf" "E:\SQLLog\System\"
move "C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\MSSQL\DATA\model.mdf" "D:\SQLData\System\"
move "C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\MSSQL\DATA\modellog.ldf" "E:\SQLLog\System\"
move "C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\MSSQL\DATA\MSDBData.mdf" "D:\SQLData\System\"
move "C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\MSSQL\DATA\MSDBLog.ldf" "E:\SQLLog\System\"
```

### STEP 6.8: START SQL SERVER AND VERIFY

1. Start SQL Server:

PowerShell:

```powershell
Start-Service -Name "MSSQLSERVER"
```

Command Prompt:

```cmd
net start MSSQLSERVER
```

SQL Server Configuration Manager:
    Right-click "SQL Server (MSSQLSERVER)" -> Start

2. Verify system databases:

```sql
SELECT
    db.name AS DatabaseName,
    mf.name AS LogicalName,
    mf.physical_name AS NewPath,
    mf.type_desc AS FileType
FROM sys.databases db
JOIN sys.master_files mf ON db.database_id = mf.database_id
WHERE db.database_id <= 4
ORDER BY db.database_id, mf.type;
```

Expected output:

| DatabaseName | LogicalName | NewPath | FileType |
|--------------|-------------|---------|----------|
| master | master | D:\SQLData\System\master.mdf | ROWS |
| master | mastlog | E:\SQLLog\System\mastlog.ldf | LOG |
| tempdb | tempdev | D:\SQLData\System\tempdb.mdf | ROWS |
| tempdb | templog | E:\SQLLog\System\templog.ldf | LOG |
| model | modeldev | D:\SQLData\System\model.mdf | ROWS |
| model | modellog | E:\SQLLog\System\modellog.ldf | LOG |
| msdb | MSDBData | D:\SQLData\System\MSDBData.mdf | ROWS |
| msdb | MSDBLog | E:\SQLLog\System\MSDBLog.ldf | LOG |

3. Verify SQL Server is functioning:

```sql
-- Test basic operations
SELECT @@VERSION;
SELECT GETDATE();

-- Test tempdb
CREATE TABLE #TestTemp (ID INT);
INSERT INTO #TestTemp VALUES (1);
SELECT * FROM #TestTemp;
DROP TABLE #TestTemp;

-- Test msdb (SQL Agent)
SELECT * FROM msdb.dbo.sysjobs;
```

## PART 7: TROUBLESHOOTING

### PROBLEM: SQL Server won't start after moving master

1. Check Windows Event Viewer for SQL Server errors
2. Verify startup parameters in Configuration Manager are correct
3. Verify files exist in new locations
4. Verify permissions on new files/folders
5. Start SQL Server in single-user mode for troubleshooting:

```cmd
net start MSSQLSERVER /f /m
```

### PROBLEM: "Operating system error 5 (Access denied)"

Grant permissions to SQL Server service account:

```cmd
icacls "D:\SQLData\System" /grant "NT SERVICE\MSSQLSERVER":(OI)(CI)F
icacls "E:\SQLLog\System" /grant "NT SERVICE\MSSQLSERVER":(OI)(CI)F
```

### PROBLEM: Cannot attach database - file not found

1. Verify file paths are correct
2. Check if files were actually moved
3. Use full paths in ATTACH statement
4. Verify files aren't locked by another process

### PROBLEM: tempdb doesn't move

TempDB is recreated on restart. After ALTER DATABASE:
1. Restart SQL Server
2. Delete old tempdb files from original location

### PROBLEM: Database in "Recovery Pending" state

```sql
ALTER DATABASE [DatabaseName] SET EMERGENCY;
DBCC CHECKDB ([DatabaseName], REPAIR_ALLOW_DATA_LOSS);
ALTER DATABASE [DatabaseName] SET ONLINE;
```

## SUMMARY

### Methods for Moving Databases

| Method | Use Case | Steps Required |
|--------|----------|----------------|
| Detach/Attach | User databases | 1. Detach 2. Move files 3. Attach |
| ALTER DATABASE MODIFY FILE | User databases | 1. ALTER DATABASE MODIFY FILE 2. Set OFFLINE 3. Move files 4. Set ONLINE |
| System DB Move | master, model, msdb | 1. ALTER DATABASE MODIFY FILE 2. Update startup params (master) 3. Stop SQL Server 4. Move files 5. Start SQL Server |
| TempDB Move | tempdb only | 1. ALTER DATABASE MODIFY FILE 2. Restart SQL Server (Files recreated automatically) |

### Final File Locations

**System Databases:**
- D:\SQLData\System\master.mdf
- E:\SQLLog\System\mastlog.ldf
- D:\SQLData\System\model.mdf
- E:\SQLLog\System\modellog.ldf
- D:\SQLData\System\MSDBData.mdf
- E:\SQLLog\System\MSDBLog.ldf
- D:\SQLData\System\tempdb.mdf
- E:\SQLLog\System\templog.ldf

**User Databases (Default):**
- D:\SQLData\[DatabaseName].mdf
- E:\SQLLog\[DatabaseName]_log.ldf

### Key Commands

- Detach: `EXEC sp_detach_db @dbname = 'DBName'`
- Attach: `CREATE DATABASE DBName ON (FILENAME='path') FOR ATTACH`
- Modify: `ALTER DATABASE DBName MODIFY FILE (NAME='LogicalName', FILENAME='NewPath')`
- Offline: `ALTER DATABASE DBName SET OFFLINE`
- Online: `ALTER DATABASE DBName SET ONLINE`
