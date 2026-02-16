# Working with Databases and Storage - Task

## Goals

- Learn to restore databases from backup files
- Understand how to manage database file locations
- Master database detach and attach operations
- Learn to move databases using T-SQL `ALTER DATABASE` command
- Understand system database relocation procedures

## Prerequisites

- VM with installed SQL Server 2019
- AdventureWorks database backup file (.bak)
- SQL Server Management Studio (SSMS)
- Administrator access to the VM

## Tasks

### 1. Add New Disks to VM

- Add a separate disk for database data files (e.g., D: drive)
- Add a separate disk for database log files (e.g., E: drive)
- Initialize and format the new disks
- Create appropriate folder structures

### 2. Restore AdventureWorks Database

- Download AdventureWorks backup file
- Restore the database to your SQL Server instance

### 3. Change Default Database Locations

- Open Server Properties in SSMS
- Navigate to Database Settings
- Change default data file location to new disk
- Change default log file location to new disk

### 4. Move AdventureWorks Using Detach/Attach Method

- Detach the AdventureWorks database
- Physically move data file (.mdf) to new data disk
- Physically move log file (.ldf) to new log disk
- Attach the database with new file locations

### 5. Move AdventureWorks Using ALTER DATABASE

- Use `ALTER DATABASE ... MODIFY FILE` command
- Move database back to original location on C: drive
- Verify the database files are in the correct location

### 6. Move System Databases

- Move master database (data and log files)
- Move msdb database (data and log files)
- Move model database (data and log files)
- Move tempdb database (data and log files)
- Update SQL Server Configuration Manager startup parameters
- Verify all system databases are working correctly

## Requirements

1. AdventureWorks DB must be restored successfully
2. Separate disks must be added for data and log files
3. Default database locations must be changed in Server Properties
4. AdventureWorks must be moved using Detach/Attach method
5. AdventureWorks must be moved back using `ALTER DATABASE ... MODIFY FILE`
6. ALL system databases (master, msdb, model, tempdb) must be moved using T-SQL and Configuration Manager

## Self-Review

- [ ] New disks are added, initialized, and formatted
- [ ] AdventureWorks database is restored and accessible
- [ ] Default database locations point to new disks
- [ ] Detach/Attach method successfully moves AdventureWorks
- [ ] `ALTER DATABASE MODIFY FILE` successfully moves AdventureWorks back
- [ ] master database files are on new disk
- [ ] msdb database files are on new disk
- [ ] model database files are on new disk
- [ ] tempdb database files are on new disk
- [ ] SQL Server starts successfully after moving system databases
- [ ] All databases are accessible and functional

## Helpful Materials

- https://docs.microsoft.com/en-us/sql/relational-databases/databases/move-user-databases
- https://docs.microsoft.com/en-us/sql/relational-databases/databases/move-system-databases
- https://docs.microsoft.com/en-us/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options
- https://docs.microsoft.com/en-us/sql/relational-databases/databases/database-detach-and-attach-sql-server
- https://docs.microsoft.com/en-us/sql/samples/adventureworks-install-configure
- https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/move-the-tempdb-database
