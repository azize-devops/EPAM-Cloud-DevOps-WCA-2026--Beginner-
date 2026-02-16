# Performing Database Maintenance - Task

## Goals

- Learn to verify database integrity using DBCC CHECKDB
- Understand database corruption detection and repair options
- Master index fragmentation analysis using DMVs
- Practice index maintenance operations (REORGANIZE vs REBUILD)
- Understand the impact of database shrink on index fragmentation

## Prerequisites

- 1 VM with installed SQL Server 2019
- SQL Server Management Studio (SSMS)
- Sufficient disk space (at least 5GB free)

## Tasks

### Part 1: Database Corruption and Repair

#### 1. Create Test Database

Execute the following script to create a test database with sample data:

```sql
USE master
GO
DROP DATABASE IF EXISTS [CorruptionTest]
GO
CREATE DATABASE [CorruptionTest]
GO
ALTER DATABASE [CorruptionTest] MODIFY FILE ( NAME = N'CorruptionTest', SIZE = 2GB )
GO
ALTER DATABASE [CorruptionTest] MODIFY FILE ( NAME = N'CorruptionTest_log', SIZE = 2GB )
GO
ALTER DATABASE [CorruptionTest] SET RECOVERY FULL;
GO
ALTER DATABASE [CorruptionTest] SET PAGE_VERIFY CHECKSUM
GO
CREATE TABLE CorruptionTest.dbo.mssqltips
(increment INT, randomGUID uniqueidentifier, randomValue INT, BigCol CHAR(2000) DEFAULT 'a',
INDEX CIX_SQLShack_increment1 UNIQUE CLUSTERED (increment))
GO
SET NOCOUNT ON;
DECLARE @counter INT = 1;
BEGIN TRAN
    WHILE @counter <= 250000
    BEGIN
        INSERT INTO CorruptionTest.dbo.mssqltips (increment, randomGUID, randomValue)
        VALUES (@counter, NEWID(), ABS(CHECKSUM(NewId())) % 140000000)
        SET @counter += 1
    END;
COMMIT TRAN;
GO
```

#### 2. Corrupt a Database Page

**WARNING: THIS CAUSES IRREPARABLE DAMAGE - DO NOT RUN IN PRODUCTION!**

```sql
USE master;
GO
ALTER DATABASE [CorruptionTest] SET SINGLE_USER WITH ROLLBACK IMMEDIATE;
GO
DBCC WRITEPAGE ('CorruptionTest', 1, 8330, 4000, 1, 0x0, 1)
GO
ALTER DATABASE [CorruptionTest] SET MULTI_USER;
GO
```

#### 3. Verify Database is Corrupted

Run this query to confirm corruption:

```sql
SELECT *
FROM [CorruptionTest].[dbo].mssqltips
ORDER BY increment DESC
GO
```

(Should produce an error due to corruption)

#### 4. Use DBCC CHECKDB to Detect and Repair Corruption

- Run `DBCC CHECKDB` to observe the error
- Find a way to repair the corrupted database
- Document the repair process and any data loss

### Part 2: Index Fragmentation and Maintenance

#### 5. Create Tables for Fragmentation Testing

Use the same CorruptionTest database (or recreate it):

```sql
-- Create 10MB filler table at the 'front' of the data file
USE [CorruptionTest]
CREATE TABLE [FillerTable] (
    [c1] INT IDENTITY,
    [c2] CHAR (8000) DEFAULT 'filler');
GO
-- Fill up the filler table (1280 rows)
INSERT INTO [FillerTable] DEFAULT VALUES;
GO 1280

-- Create production table 'after' the filler table
USE [CorruptionTest]
CREATE TABLE [ProdTable] (
    [c1] INT IDENTITY,
    [c2] CHAR (8000) DEFAULT 'production');
CREATE CLUSTERED INDEX [prod_cl] ON [ProdTable] ([c1]);
GO
INSERT INTO [ProdTable] DEFAULT VALUES;
GO 1280
```

#### 6. Check Initial Index Fragmentation

- Use `sys.dm_db_index_physical_stats` DMV
- Note the fragmentation percentage of ProdTable

#### 7. Create Fragmentation by Shrinking

```sql
-- Drop filler table, creating 10MB free space at 'front'
USE [CorruptionTest]
DROP TABLE [FillerTable];
GO
-- Shrink the database
DBCC SHRINKDATABASE ([CorruptionTest]);
GO
```

#### 8. Check Fragmentation After Shrink

- Use same DMV to check fragmentation
- Note the significant increase in fragmentation

#### 9. Test REORGANIZE Operation

- Run `ALTER INDEX REORGANIZE`
- Check fragmentation after reorganize

#### 10. Test REBUILD Operation

- Run `ALTER INDEX REBUILD`
- Check fragmentation after rebuild
- Compare results with REORGANIZE

**NOTE:** The database can be recreated to test REORGANIZE and REBUILD separately.

## Requirements

1. CorruptionTest database must be created with 250,000 rows
2. Database page must be corrupted using `DBCC WRITEPAGE`
3. Corruption must be verified by attempting to read data
4. `DBCC CHECKDB` must be used to detect corruption
5. Repair method must be found and executed
6. FillerTable and ProdTable must be created
7. Initial fragmentation must be checked using DMV
8. Fragmentation after `SHRINKDATABASE` must be documented
9. REORGANIZE operation must be tested
10. REBUILD operation must be tested
11. Fragmentation levels must be compared before and after each operation

## Self-Review

- [ ] CorruptionTest database created with 250K records
- [ ] Database page corrupted successfully
- [ ] Error occurs when reading corrupted data
- [ ] `DBCC CHECKDB` detects the corruption
- [ ] Repair method identified and executed
- [ ] Data loss (if any) documented
- [ ] FillerTable created with 1280 rows
- [ ] ProdTable created with clustered index
- [ ] Initial fragmentation percentage noted
- [ ] FillerTable dropped and database shrunk
- [ ] Post-shrink fragmentation significantly higher
- [ ] REORGANIZE reduces fragmentation partially
- [ ] REBUILD reduces fragmentation completely
- [ ] Understand difference between REORGANIZE and REBUILD

## Helpful Materials

- https://docs.microsoft.com/en-us/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql
- https://docs.microsoft.com/en-us/sql/relational-databases/indexes/reorganize-and-rebuild-indexes
- https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-db-index-physical-stats-transact-sql
- https://www.mssqltips.com/sqlservertip/5645/sql-server-database-corruption-and-impact-of-running-checkdb-repair-with-allow-data-loss/
- https://dba.stackexchange.com/questions/196567/test-data-to-generate-high-index-fragmentation-level
