PERFORMING DATABASE MAINTENANCE - ANSWER
========================================

This guide covers database integrity checking with DBCC CHECKDB,
corruption repair, and index fragmentation management with
REORGANIZE and REBUILD operations.

================================================================================
                    UNDERSTANDING DATABASE CORRUPTION
================================================================================

WHAT IS DATABASE CORRUPTION?
---------------------------

Database corruption occurs when data pages become damaged or inconsistent.

Causes of corruption:
- Hardware failures (disk, memory, controller)
- Software bugs (SQL Server, OS, drivers)
- Power failures during writes
- Improper shutdowns
- Malware or unauthorized modifications

TYPES OF CORRUPTION:
-------------------

+----------------------+------------------------------------------------+
| Type                 | Description                                    |
+----------------------+------------------------------------------------+
| Page checksum error  | Page data doesn't match checksum               |
| Torn page            | Incomplete page write (partial sectors)        |
| Logical corruption   | Data inconsistencies (orphaned rows, etc.)     |
| Structural damage    | Damaged system pages, allocation errors        |
+----------------------+------------------------------------------------+

PAGE_VERIFY OPTIONS:
-------------------

+----------------------+------------------------------------------------+
| Option               | Description                                    |
+----------------------+------------------------------------------------+
| CHECKSUM             | Full page checksum (recommended)               |
| TORN_PAGE_DETECTION  | Checks for torn writes (legacy)                |
| NONE                 | No verification (not recommended)              |
+----------------------+------------------------------------------------+

================================================================================
                    PART 1: CREATE TEST DATABASE
================================================================================

STEP 1.1: CREATE CORRUPTIONTEST DATABASE
----------------------------------------

    USE master;
    GO

    -- Drop database if exists
    DROP DATABASE IF EXISTS [CorruptionTest];
    GO

    -- Create the database
    CREATE DATABASE [CorruptionTest];
    GO

    -- Set data file size to 2GB
    ALTER DATABASE [CorruptionTest]
    MODIFY FILE (NAME = N'CorruptionTest', SIZE = 2GB);
    GO

    -- Set log file size to 2GB
    ALTER DATABASE [CorruptionTest]
    MODIFY FILE (NAME = N'CorruptionTest_log', SIZE = 2GB);
    GO

    -- Set recovery model to FULL
    ALTER DATABASE [CorruptionTest] SET RECOVERY FULL;
    GO

    -- Enable page checksum verification
    ALTER DATABASE [CorruptionTest] SET PAGE_VERIFY CHECKSUM;
    GO

    -- Verify database settings
    SELECT
        name,
        recovery_model_desc,
        page_verify_option_desc,
        state_desc
    FROM sys.databases
    WHERE name = 'CorruptionTest';
    GO

STEP 1.2: CREATE TEST TABLE WITH DATA
-------------------------------------

    -- Create table with clustered index
    USE CorruptionTest;
    GO

    CREATE TABLE dbo.mssqltips (
        increment INT,
        randomGUID UNIQUEIDENTIFIER,
        randomValue INT,
        BigCol CHAR(2000) DEFAULT 'a',
        INDEX CIX_SQLShack_increment1 UNIQUE CLUSTERED (increment)
    );
    GO

    -- Insert 250,000 rows (this will take several minutes)
    SET NOCOUNT ON;
    DECLARE @counter INT = 1;

    PRINT 'Starting data insert at: ' + CONVERT(VARCHAR, GETDATE(), 120);

    BEGIN TRAN
        WHILE @counter <= 250000
        BEGIN
            INSERT INTO CorruptionTest.dbo.mssqltips
                (increment, randomGUID, randomValue)
            VALUES
                (@counter, NEWID(), ABS(CHECKSUM(NEWID())) % 140000000);

            -- Progress indicator every 50,000 rows
            IF @counter % 50000 = 0
                PRINT 'Inserted ' + CAST(@counter AS VARCHAR) + ' rows...';

            SET @counter += 1;
        END;
    COMMIT TRAN;

    PRINT 'Completed data insert at: ' + CONVERT(VARCHAR, GETDATE(), 120);
    GO

    -- Verify row count
    SELECT COUNT(*) AS TotalRows FROM dbo.mssqltips;
    GO
    -- Should return: 250000

STEP 1.3: CREATE BACKUP BEFORE CORRUPTION (RECOMMENDED)
-------------------------------------------------------

    -- Backup database before corrupting (for comparison/recovery)
    BACKUP DATABASE CorruptionTest
    TO DISK = 'C:\SQLBackups\CorruptionTest_Clean.bak'
    WITH FORMAT, INIT, COMPRESSION;
    GO

================================================================================
                    PART 2: CORRUPT THE DATABASE
================================================================================

WARNING: THE FOLLOWING COMMAND CAUSES IRREPARABLE DAMAGE!
        DO NOT RUN ANYWHERE NEAR A PRODUCTION ENVIRONMENT!

STEP 2.1: UNDERSTAND DBCC WRITEPAGE
-----------------------------------

DBCC WRITEPAGE writes directly to a database page, bypassing normal
SQL Server data protection mechanisms.

Syntax:
    DBCC WRITEPAGE (dbname, filenum, pagenum, offset, length, data, directORbuffer)

Parameters:
- dbname: Database name
- filenum: File number (1 = primary data file)
- pagenum: Page number to corrupt (8330 in our case)
- offset: Byte offset within page (4000)
- length: Number of bytes to write (1)
- data: Data to write (0x0 = null byte)
- directORbuffer: 1 = direct to disk

STEP 2.2: CORRUPT A DATABASE PAGE
---------------------------------

    USE master;
    GO

    -- Enable trace flag to allow DBCC WRITEPAGE
    DBCC TRACEON (2588);
    GO

    -- Set database to single user mode
    ALTER DATABASE [CorruptionTest] SET SINGLE_USER WITH ROLLBACK IMMEDIATE;
    GO

    -- CORRUPT THE PAGE!
    -- This writes a null byte at offset 4000 on page 8330
    DBCC WRITEPAGE ('CorruptionTest', 1, 8330, 4000, 1, 0x0, 1);
    GO

    -- Return to multi-user mode
    ALTER DATABASE [CorruptionTest] SET MULTI_USER;
    GO

    PRINT 'Database page has been corrupted!';
    GO

STEP 2.3: VERIFY CORRUPTION BY READING DATA
-------------------------------------------

    -- Try to read data (this should fail with an error)
    USE CorruptionTest;
    GO

    SELECT *
    FROM [CorruptionTest].[dbo].mssqltips
    ORDER BY increment DESC;
    GO

Expected Error:
    Msg 824, Level 24, State 2, Line X
    SQL Server detected a logical consistency-based I/O error: incorrect checksum
    (expected: 0xXXXXXXXX; actual: 0xXXXXXXXX). It occurred during a read of page
    (1:8330) in database ID XX at offset 0xXXXXXXX in file 'C:\...\CorruptionTest.mdf'.

This error confirms the page is corrupted!

================================================================================
                    PART 3: DETECT CORRUPTION WITH DBCC CHECKDB
================================================================================

STEP 3.1: RUN DBCC CHECKDB
--------------------------

    USE master;
    GO

    -- Run CHECKDB to detect corruption
    DBCC CHECKDB ('CorruptionTest') WITH NO_INFOMSGS;
    GO

Expected output shows corruption:

    Msg 8928, Level 16, State 1, Line X
    Object ID XXXXX, index ID 1, partition ID XXXXX, alloc unit ID XXXXX (type In-row data):
    Page (1:8330) could not be processed. See other errors for details.

    Msg 8939, Level 16, State 98, Line X
    Table error: Object ID XXXXX, index ID 1, partition ID XXXXX, alloc unit ID XXXXX (type In-row data),
    page (1:8330). Test (IS_OFF (BUF_IOERR, pBUF->bstat)) failed.

    CHECKDB found 0 allocation errors and 2 consistency errors in table 'mssqltips'
    (object ID XXXXX).
    CHECKDB found 0 allocation errors and 2 consistency errors in database 'CorruptionTest'.

STEP 3.2: CHECKDB OPTIONS
-------------------------

    -- Check database with detailed information
    DBCC CHECKDB ('CorruptionTest') WITH ALL_ERRORMSGS, EXTENDED_LOGICAL_CHECKS;
    GO

    -- Check only specific table
    DBCC CHECKTABLE ('dbo.mssqltips') WITH ALL_ERRORMSGS;
    GO

    -- Check allocation consistency only (faster)
    DBCC CHECKALLOC ('CorruptionTest');
    GO

    -- Check catalog consistency
    DBCC CHECKCATALOG ('CorruptionTest');
    GO

================================================================================
                    PART 4: REPAIR THE CORRUPTED DATABASE
================================================================================

REPAIR OPTIONS:
--------------

+---------------------------+------------------------------------------------+
| Repair Option             | Description                                    |
+---------------------------+------------------------------------------------+
| REPAIR_REBUILD            | Attempts to repair without data loss.          |
|                           | Rebuilds indexes, etc.                         |
+---------------------------+------------------------------------------------+
| REPAIR_ALLOW_DATA_LOSS    | Repairs by deallocating corrupted pages.       |
|                           | MAY RESULT IN DATA LOSS!                       |
+---------------------------+------------------------------------------------+

IMPORTANT: Always try REPAIR_REBUILD first. Only use REPAIR_ALLOW_DATA_LOSS
as a last resort when you don't have a good backup!

STEP 4.1: ATTEMPT REPAIR_REBUILD FIRST
--------------------------------------

    USE master;
    GO

    -- Set database to single user mode (required for repair)
    ALTER DATABASE [CorruptionTest] SET SINGLE_USER WITH ROLLBACK IMMEDIATE;
    GO

    -- Try repair without data loss
    DBCC CHECKDB ('CorruptionTest', REPAIR_REBUILD) WITH NO_INFOMSGS;
    GO

    -- Return to multi-user mode
    ALTER DATABASE [CorruptionTest] SET MULTI_USER;
    GO

Result: REPAIR_REBUILD likely won't fix this corruption because
the page data itself is damaged, not just index structures.

STEP 4.2: USE REPAIR_ALLOW_DATA_LOSS
------------------------------------

WARNING: This WILL result in data loss!

    USE master;
    GO

    -- Set database to single user mode
    ALTER DATABASE [CorruptionTest] SET SINGLE_USER WITH ROLLBACK IMMEDIATE;
    GO

    -- Repair with potential data loss
    DBCC CHECKDB ('CorruptionTest', REPAIR_ALLOW_DATA_LOSS);
    GO

    -- Return to multi-user mode
    ALTER DATABASE [CorruptionTest] SET MULTI_USER;
    GO

Expected output:

    Repair: The page (1:8330) has been deallocated from object ID XXXXX,
    index ID 1, partition ID XXXXX, alloc unit ID XXXXX (type In-row data).

    CHECKDB found 0 allocation errors and 2 consistency errors in table 'mssqltips'.
    repair_allow_data_loss is the minimum repair level for the errors found.

STEP 4.3: VERIFY REPAIR
-----------------------

    -- Run CHECKDB again to verify repair
    DBCC CHECKDB ('CorruptionTest') WITH NO_INFOMSGS;
    GO

    -- Should return:
    -- CHECKDB found 0 allocation errors and 0 consistency errors in database 'CorruptionTest'.

STEP 4.4: CHECK DATA LOSS
-------------------------

    USE CorruptionTest;
    GO

    -- Check row count (will be less than 250,000)
    SELECT COUNT(*) AS RemainingRows FROM dbo.mssqltips;
    GO

    -- Find gaps in increment column (indicates deleted rows)
    SELECT
        increment,
        LEAD(increment) OVER (ORDER BY increment) AS NextIncrement,
        LEAD(increment) OVER (ORDER BY increment) - increment - 1 AS MissingRows
    FROM dbo.mssqltips
    WHERE LEAD(increment) OVER (ORDER BY increment) - increment > 1
    ORDER BY increment;
    GO

    -- Try reading all data now
    SELECT TOP 100 * FROM dbo.mssqltips ORDER BY increment DESC;
    GO
    -- Should work now without errors

================================================================================
                    ALTERNATIVE: RESTORE FROM BACKUP
================================================================================

The BEST solution for corruption is restoring from a known good backup:

    USE master;
    GO

    -- Drop corrupted database
    DROP DATABASE CorruptionTest;
    GO

    -- Restore from clean backup
    RESTORE DATABASE CorruptionTest
    FROM DISK = 'C:\SQLBackups\CorruptionTest_Clean.bak'
    WITH REPLACE;
    GO

This restores ALL data without any loss!

================================================================================
                    PART 5: INDEX FRAGMENTATION SETUP
================================================================================

For this part, we'll recreate the database or use the repaired one.

STEP 5.1: RECREATE DATABASE (OPTIONAL)
--------------------------------------

    USE master;
    GO

    DROP DATABASE IF EXISTS [CorruptionTest];
    GO

    CREATE DATABASE [CorruptionTest];
    GO

STEP 5.2: CREATE FILLER TABLE
-----------------------------

    -- Create 10MB filler table at the 'front' of the data file
    USE [CorruptionTest];
    GO

    CREATE TABLE [FillerTable] (
        [c1] INT IDENTITY,
        [c2] CHAR(8000) DEFAULT 'filler'
    );
    GO

    -- Fill up the filler table with 1280 rows
    -- (Each row is ~8KB, so 1280 rows = ~10MB)
    INSERT INTO [FillerTable] DEFAULT VALUES;
    GO 1280

    -- Verify row count
    SELECT COUNT(*) AS FillerRows FROM FillerTable;
    -- Should return: 1280

    PRINT 'FillerTable created with 1280 rows (~10MB)';
    GO

STEP 5.3: CREATE PRODUCTION TABLE
---------------------------------

    USE [CorruptionTest];
    GO

    -- Create production table (will be placed 'after' FillerTable in data file)
    CREATE TABLE [ProdTable] (
        [c1] INT IDENTITY,
        [c2] CHAR(8000) DEFAULT 'production'
    );
    GO

    -- Create clustered index
    CREATE CLUSTERED INDEX [prod_cl] ON [ProdTable] ([c1]);
    GO

    -- Insert 1280 rows
    INSERT INTO [ProdTable] DEFAULT VALUES;
    GO 1280

    -- Verify row count
    SELECT COUNT(*) AS ProdRows FROM ProdTable;
    -- Should return: 1280

    PRINT 'ProdTable created with 1280 rows and clustered index';
    GO

================================================================================
                    PART 6: CHECK INDEX FRAGMENTATION
================================================================================

UNDERSTANDING FRAGMENTATION:
---------------------------

+----------------------+------------------------------------------------+
| Fragmentation Type   | Description                                    |
+----------------------+------------------------------------------------+
| Logical Fragmentation| Pages not in physical order. Affects scans.   |
| (External)           | Measured as % of out-of-order pages.           |
+----------------------+------------------------------------------------+
| Page Density         | How full pages are. Low = wasted space.        |
| (Internal)           | Measured as avg_page_space_used_in_percent.    |
+----------------------+------------------------------------------------+

STEP 6.1: CHECK INITIAL FRAGMENTATION
-------------------------------------

    USE CorruptionTest;
    GO

    -- Check index fragmentation using DMV
    SELECT
        OBJECT_NAME(ips.object_id) AS TableName,
        i.name AS IndexName,
        i.type_desc AS IndexType,
        ips.index_type_desc,
        ips.alloc_unit_type_desc,
        ips.index_depth,
        ips.index_level,
        ips.avg_fragmentation_in_percent AS FragmentationPct,
        ips.fragment_count,
        ips.avg_fragment_size_in_pages,
        ips.page_count,
        ips.avg_page_space_used_in_percent AS PageDensityPct,
        ips.record_count
    FROM sys.dm_db_index_physical_stats(
        DB_ID('CorruptionTest'),  -- Database ID
        OBJECT_ID('ProdTable'),   -- Object ID (table)
        NULL,                      -- Index ID (NULL = all indexes)
        NULL,                      -- Partition number
        'DETAILED'                 -- Mode: LIMITED, SAMPLED, or DETAILED
    ) ips
    JOIN sys.indexes i ON ips.object_id = i.object_id AND ips.index_id = i.index_id
    WHERE ips.page_count > 0;
    GO

Expected Initial Results:
    TableName  IndexName  FragmentationPct  PageDensityPct  page_count
    ProdTable  prod_cl    ~0-5%             ~99%            1280+

The initial fragmentation should be very LOW because pages were
allocated sequentially during insert.

STEP 6.2: SIMPLIFIED FRAGMENTATION QUERY
----------------------------------------

    -- Quick fragmentation check
    SELECT
        OBJECT_NAME(object_id) AS TableName,
        index_id,
        index_type_desc,
        ROUND(avg_fragmentation_in_percent, 2) AS FragPct,
        page_count
    FROM sys.dm_db_index_physical_stats(
        DB_ID('CorruptionTest'),
        NULL, NULL, NULL, 'LIMITED')
    WHERE page_count > 100
    ORDER BY avg_fragmentation_in_percent DESC;
    GO

================================================================================
                    PART 7: CREATE FRAGMENTATION BY SHRINKING
================================================================================

STEP 7.1: DROP FILLER TABLE
---------------------------

    USE [CorruptionTest];
    GO

    -- Drop the filler table
    -- This creates 10MB of free space at the 'front' of the data file
    DROP TABLE [FillerTable];
    GO

    PRINT 'FillerTable dropped - 10MB free space created at front of file';
    GO

STEP 7.2: SHRINK THE DATABASE
-----------------------------

    -- Shrink the database
    -- This moves pages from the end to fill gaps at the beginning
    -- Causing ProdTable's pages to become out of order (fragmented!)
    DBCC SHRINKDATABASE ([CorruptionTest]);
    GO

    PRINT 'Database shrunk - pages relocated';
    GO

STEP 7.3: CHECK FRAGMENTATION AFTER SHRINK
------------------------------------------

    USE CorruptionTest;
    GO

    -- Check fragmentation after shrink
    SELECT
        OBJECT_NAME(ips.object_id) AS TableName,
        i.name AS IndexName,
        ROUND(ips.avg_fragmentation_in_percent, 2) AS FragmentationPct,
        ROUND(ips.avg_page_space_used_in_percent, 2) AS PageDensityPct,
        ips.page_count,
        ips.fragment_count
    FROM sys.dm_db_index_physical_stats(
        DB_ID('CorruptionTest'),
        OBJECT_ID('ProdTable'),
        NULL, NULL, 'DETAILED') ips
    JOIN sys.indexes i ON ips.object_id = i.object_id AND ips.index_id = i.index_id
    WHERE ips.page_count > 0 AND ips.index_level = 0;  -- Leaf level only
    GO

Expected Results After Shrink:
    TableName  IndexName  FragmentationPct  PageDensityPct  fragment_count
    ProdTable  prod_cl    ~90-99%           ~99%            1000+

The fragmentation should now be VERY HIGH (90%+) because SHRINKDATABASE
moved pages from the end of the file to fill the gap at the beginning,
causing the physical order to not match the logical order.

================================================================================
                    PART 8: INDEX MAINTENANCE OPERATIONS
================================================================================

FRAGMENTATION GUIDELINES:
------------------------

+-----------------------+--------------------------------------------+
| Fragmentation Level   | Recommended Action                         |
+-----------------------+--------------------------------------------+
| < 5%                  | No action needed                           |
| 5% - 30%              | REORGANIZE (online, less resource)         |
| > 30%                 | REBUILD (offline by default, more thorough)|
+-----------------------+--------------------------------------------+

STEP 8.1: REORGANIZE INDEX
--------------------------

ALTER INDEX REORGANIZE:
- Online operation (table remains available)
- Uses minimal resources
- Physically reorders leaf-level pages
- Compacts pages to increase density
- Does NOT update statistics

    USE CorruptionTest;
    GO

    -- Record fragmentation before REORGANIZE
    SELECT 'BEFORE REORGANIZE' AS Status,
        ROUND(avg_fragmentation_in_percent, 2) AS FragPct,
        page_count
    FROM sys.dm_db_index_physical_stats(
        DB_ID(), OBJECT_ID('ProdTable'), 1, NULL, 'LIMITED');
    GO

    -- Reorganize the clustered index
    ALTER INDEX [prod_cl] ON [ProdTable] REORGANIZE;
    GO

    -- Check fragmentation after REORGANIZE
    SELECT 'AFTER REORGANIZE' AS Status,
        ROUND(avg_fragmentation_in_percent, 2) AS FragPct,
        page_count
    FROM sys.dm_db_index_physical_stats(
        DB_ID(), OBJECT_ID('ProdTable'), 1, NULL, 'LIMITED');
    GO

Expected Results:
    BEFORE REORGANIZE: FragPct = ~90%
    AFTER REORGANIZE:  FragPct = ~10-30% (reduced but not eliminated)

REORGANIZE reduces fragmentation but may not eliminate it completely
for highly fragmented indexes.

STEP 8.2: REBUILD INDEX
-----------------------

ALTER INDEX REBUILD:
- Drops and recreates the index
- Completely defragments the index
- Updates statistics automatically
- OFFLINE by default (table locked) - can use ONLINE option
- More resource-intensive than REORGANIZE

    USE CorruptionTest;
    GO

    -- Record fragmentation before REBUILD
    SELECT 'BEFORE REBUILD' AS Status,
        ROUND(avg_fragmentation_in_percent, 2) AS FragPct,
        page_count
    FROM sys.dm_db_index_physical_stats(
        DB_ID(), OBJECT_ID('ProdTable'), 1, NULL, 'LIMITED');
    GO

    -- Rebuild the clustered index (OFFLINE)
    ALTER INDEX [prod_cl] ON [ProdTable] REBUILD;
    GO

    -- Or rebuild ONLINE (Enterprise Edition only)
    -- ALTER INDEX [prod_cl] ON [ProdTable] REBUILD WITH (ONLINE = ON);
    -- GO

    -- Check fragmentation after REBUILD
    SELECT 'AFTER REBUILD' AS Status,
        ROUND(avg_fragmentation_in_percent, 2) AS FragPct,
        page_count
    FROM sys.dm_db_index_physical_stats(
        DB_ID(), OBJECT_ID('ProdTable'), 1, NULL, 'LIMITED');
    GO

Expected Results:
    BEFORE REBUILD: FragPct = ~10-30% (after reorganize) or ~90% (if skipped reorganize)
    AFTER REBUILD:  FragPct = ~0% (completely defragmented)

REBUILD completely eliminates fragmentation by recreating the index.

STEP 8.3: REBUILD ALL INDEXES ON TABLE
--------------------------------------

    -- Rebuild all indexes on a table
    ALTER INDEX ALL ON [ProdTable] REBUILD;
    GO

    -- Rebuild with fill factor
    ALTER INDEX ALL ON [ProdTable] REBUILD WITH (FILLFACTOR = 80);
    GO

STEP 8.4: COMPARISON SCRIPT
---------------------------

    -- Complete comparison of REORGANIZE vs REBUILD
    USE CorruptionTest;
    GO

    PRINT '=== INITIAL STATE ===';
    SELECT ROUND(avg_fragmentation_in_percent, 2) AS FragPct
    FROM sys.dm_db_index_physical_stats(DB_ID(), OBJECT_ID('ProdTable'), 1, NULL, 'LIMITED');

    PRINT '=== AFTER REORGANIZE ===';
    ALTER INDEX [prod_cl] ON [ProdTable] REORGANIZE;
    SELECT ROUND(avg_fragmentation_in_percent, 2) AS FragPct
    FROM sys.dm_db_index_physical_stats(DB_ID(), OBJECT_ID('ProdTable'), 1, NULL, 'LIMITED');

    -- Re-create fragmentation (need to repeat steps 5-7 for accurate test)
    -- Or continue with current state

    PRINT '=== AFTER REBUILD ===';
    ALTER INDEX [prod_cl] ON [ProdTable] REBUILD;
    SELECT ROUND(avg_fragmentation_in_percent, 2) AS FragPct
    FROM sys.dm_db_index_physical_stats(DB_ID(), OBJECT_ID('ProdTable'), 1, NULL, 'LIMITED');
    GO

================================================================================
                    AUTOMATED INDEX MAINTENANCE SCRIPT
================================================================================

    -- Automated index maintenance based on fragmentation level
    USE CorruptionTest;
    GO

    DECLARE @TableName NVARCHAR(128);
    DECLARE @IndexName NVARCHAR(128);
    DECLARE @FragPct FLOAT;
    DECLARE @SQL NVARCHAR(MAX);

    -- Cursor to loop through indexes
    DECLARE IndexCursor CURSOR FOR
        SELECT
            OBJECT_NAME(ips.object_id) AS TableName,
            i.name AS IndexName,
            ips.avg_fragmentation_in_percent AS FragPct
        FROM sys.dm_db_index_physical_stats(DB_ID(), NULL, NULL, NULL, 'LIMITED') ips
        JOIN sys.indexes i ON ips.object_id = i.object_id AND ips.index_id = i.index_id
        WHERE ips.page_count > 1000  -- Only indexes with 1000+ pages
        AND i.name IS NOT NULL;       -- Skip heaps

    OPEN IndexCursor;
    FETCH NEXT FROM IndexCursor INTO @TableName, @IndexName, @FragPct;

    WHILE @@FETCH_STATUS = 0
    BEGIN
        IF @FragPct < 5
        BEGIN
            PRINT 'Skipping ' + @IndexName + ' (Frag: ' + CAST(@FragPct AS VARCHAR) + '%)';
        END
        ELSE IF @FragPct BETWEEN 5 AND 30
        BEGIN
            SET @SQL = 'ALTER INDEX [' + @IndexName + '] ON [' + @TableName + '] REORGANIZE';
            PRINT 'REORGANIZE: ' + @IndexName + ' (Frag: ' + CAST(@FragPct AS VARCHAR) + '%)';
            EXEC sp_executesql @SQL;
        END
        ELSE -- > 30%
        BEGIN
            SET @SQL = 'ALTER INDEX [' + @IndexName + '] ON [' + @TableName + '] REBUILD';
            PRINT 'REBUILD: ' + @IndexName + ' (Frag: ' + CAST(@FragPct AS VARCHAR) + '%)';
            EXEC sp_executesql @SQL;
        END

        FETCH NEXT FROM IndexCursor INTO @TableName, @IndexName, @FragPct;
    END

    CLOSE IndexCursor;
    DEALLOCATE IndexCursor;
    GO

================================================================================
                    SUMMARY
================================================================================

DBCC CHECKDB COMMANDS:
---------------------

+----------------------------------+----------------------------------------+
| Command                          | Purpose                                |
+----------------------------------+----------------------------------------+
| DBCC CHECKDB (dbname)            | Full integrity check                   |
| DBCC CHECKDB (db, REPAIR_REBUILD)| Repair without data loss               |
| DBCC CHECKDB (db, REPAIR_ALLOW_  | Repair with potential data loss        |
|              DATA_LOSS)          |                                        |
| DBCC CHECKTABLE (table)          | Check specific table                   |
| DBCC CHECKALLOC (dbname)         | Check allocation consistency           |
| DBCC CHECKCATALOG (dbname)       | Check catalog consistency              |
+----------------------------------+----------------------------------------+

INDEX MAINTENANCE COMPARISON:
----------------------------

+------------------+-------------------+-------------------+
| Feature          | REORGANIZE        | REBUILD           |
+------------------+-------------------+-------------------+
| Online           | Always            | Offline (default) |
| Resource Usage   | Low               | High              |
| Defrag Result    | Partial           | Complete          |
| Statistics       | Not updated       | Updated           |
| Space Required   | Minimal           | 2x index size     |
| Duration         | Shorter           | Longer            |
| Best For         | 5-30% frag        | >30% frag         |
+------------------+-------------------+-------------------+

KEY TAKEAWAYS:
-------------

1. CORRUPTION:
   - Always have backups before corruption occurs
   - REPAIR_ALLOW_DATA_LOSS is last resort
   - Restore from backup is preferred solution

2. FRAGMENTATION:
   - Check regularly using sys.dm_db_index_physical_stats
   - REORGANIZE for moderate fragmentation (5-30%)
   - REBUILD for severe fragmentation (>30%)
   - Don't defragment small indexes (<1000 pages)

3. SHRINKDATABASE:
   - Avoid using SHRINKDATABASE in production
   - It causes severe fragmentation
   - Use only after major data deletion

4. MAINTENANCE SCHEDULE:
   - Run DBCC CHECKDB weekly (at minimum)
   - Run index maintenance weekly
   - Update statistics after REORGANIZE
