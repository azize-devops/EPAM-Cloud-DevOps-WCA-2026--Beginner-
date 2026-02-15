# IMPORTING AND EXPORTING DATA - ANSWER

This guide covers various methods of importing and exporting data in
SQL Server, including CSV import, bcp utility, BULK INSERT, OPENROWSET,
and Data Migration Assistant.

================================================================================
                    OVERVIEW: DATA IMPORT/EXPORT METHODS
================================================================================

| Method | Description |
|--------|-------------|
| Import Flat File | SSMS wizard for importing CSV/TXT files. Creates table. |
| bcp | Command-line bulk copy utility. Fast, flexible. |
| BULK INSERT | T-SQL statement. Table must exist. Fast for large data. |
| OPENROWSET | T-SQL function. Can SELECT INTO to create table. |
| DMA | GUI tool for database migration and assessment. |

================================================================================
                    PART 1: CREATE DATABASE ON INSTANCE1
================================================================================

STEP 1.1: CONNECT TO DEFAULT INSTANCE (INSTANCE1)
-------------------------------------------------

Open SSMS and connect to:
- Server name: localhost (or SERVERNAME for default instance)
- Authentication: SQL Server Authentication or Windows Authentication

STEP 1.2: CREATE LAB6 DATABASE
------------------------------

    -- Connect to Instance1 (default instance)
    USE master;
    GO

    -- Create Lab6 database
    CREATE DATABASE Lab6;
    GO

    -- Verify creation
    SELECT name, state_desc, recovery_model_desc
    FROM sys.databases
    WHERE name = 'Lab6';
    GO

================================================================================
                    PART 2: GENERATE CSV FILE WITH POWERSHELL
================================================================================

STEP 2.1: POWERSHELL SCRIPT TO GENERATE CSV
-------------------------------------------

Create a PowerShell script (Generate-SampleData.ps1):

    # ============================================================================
    # Generate-SampleData.ps1
    # Creates a CSV file with 1000+ rows of sample employee data
    # ============================================================================

    # Output file path
    $outputPath = "C:\SQLData\SampleEmployees.csv"

    # Ensure directory exists
    $directory = Split-Path $outputPath -Parent
    if (-not (Test-Path $directory)) {
        New-Item -ItemType Directory -Path $directory -Force
    }

    # Sample data arrays for random selection
    $firstNames = @('John', 'Jane', 'Michael', 'Emily', 'David', 'Sarah', 'Robert',
                    'Lisa', 'William', 'Jennifer', 'James', 'Maria', 'Charles',
                    'Patricia', 'Thomas', 'Elizabeth', 'Daniel', 'Linda', 'Matthew',
                    'Barbara', 'Anthony', 'Susan', 'Mark', 'Jessica', 'Donald',
                    'Karen', 'Steven', 'Nancy', 'Paul', 'Betty')

    $lastNames = @('Smith', 'Johnson', 'Williams', 'Brown', 'Jones', 'Garcia',
                   'Miller', 'Davis', 'Rodriguez', 'Martinez', 'Hernandez', 'Lopez',
                   'Gonzalez', 'Wilson', 'Anderson', 'Thomas', 'Taylor', 'Moore',
                   'Jackson', 'Martin', 'Lee', 'Perez', 'Thompson', 'White',
                   'Harris', 'Sanchez', 'Clark', 'Ramirez', 'Lewis', 'Robinson')

    $departments = @('IT', 'HR', 'Finance', 'Marketing', 'Sales', 'Operations',
                     'Engineering', 'Support', 'Legal', 'Research')

    $cities = @('New York', 'Los Angeles', 'Chicago', 'Houston', 'Phoenix',
                'Philadelphia', 'San Antonio', 'San Diego', 'Dallas', 'San Jose')

    # Generate random phone number
    function Get-RandomPhone {
        $area = Get-Random -Minimum 200 -Maximum 999
        $prefix = Get-Random -Minimum 200 -Maximum 999
        $line = Get-Random -Minimum 1000 -Maximum 9999
        return "$area-$prefix-$line"
    }

    # Generate random date within range
    function Get-RandomDate {
        param (
            [DateTime]$StartDate = (Get-Date).AddYears(-10),
            [DateTime]$EndDate = (Get-Date)
        )
        $range = ($EndDate - $StartDate).Days
        return $StartDate.AddDays((Get-Random -Maximum $range))
    }

    # Generate employee data
    $employees = @()

    for ($i = 1; $i -le 1000; $i++) {
        $firstName = $firstNames | Get-Random
        $lastName = $lastNames | Get-Random
        $email = "$($firstName.ToLower()).$($lastName.ToLower())$i@company.com"

        $employee = [PSCustomObject]@{
            EmployeeID    = $i
            FirstName     = $firstName
            LastName      = $lastName
            Email         = $email
            Phone         = Get-RandomPhone
            Department    = $departments | Get-Random
            City          = $cities | Get-Random
            HireDate      = (Get-RandomDate -StartDate (Get-Date).AddYears(-15) -EndDate (Get-Date)).ToString('yyyy-MM-dd')
            Salary        = [Math]::Round((Get-Random -Minimum 35000 -Maximum 150000), 2)
            IsActive      = (Get-Random -InputObject @('True', 'False'))
        }

        $employees += $employee

        # Progress indicator
        if ($i % 100 -eq 0) {
            Write-Host "Generated $i records..."
        }
    }

    # Export to CSV
    $employees | Export-Csv -Path $outputPath -NoTypeInformation -Encoding UTF8

    Write-Host "`nCSV file created successfully!"
    Write-Host "Location: $outputPath"
    Write-Host "Total rows: $($employees.Count)"

    # Display first few rows
    Write-Host "`nFirst 5 rows:"
    $employees | Select-Object -First 5 | Format-Table

STEP 2.2: RUN THE POWERSHELL SCRIPT
-----------------------------------

    # Run the script
    .\Generate-SampleData.ps1

    # Or run directly in PowerShell console:
    # Copy and paste the entire script above

STEP 2.3: VERIFY CSV FILE
-------------------------

    # Check file exists and count lines
    Get-Content "C:\SQLData\SampleEmployees.csv" | Measure-Object -Line

    # View first few lines
    Get-Content "C:\SQLData\SampleEmployees.csv" -First 10

Expected CSV content:
    "EmployeeID","FirstName","LastName","Email","Phone","Department","City","HireDate","Salary","IsActive"
    "1","John","Smith","john.smith1@company.com","555-123-4567","IT","New York","2020-05-15","75000.00","True"
    "2","Jane","Johnson","jane.johnson2@company.com","555-234-5678","HR","Chicago","2019-08-20","65000.00","True"
    ...

================================================================================
                    PART 3: IMPORT CSV USING IMPORT FLAT FILE WIZARD
================================================================================

STEP 3.1: OPEN IMPORT FLAT FILE WIZARD
--------------------------------------

1. In SSMS, connect to Instance1 (default instance)
2. Expand Databases -> Lab6
3. Right-click on Lab6 -> Tasks -> Import Flat File...

STEP 3.2: SPECIFY INPUT FILE
----------------------------

1. Introduction page: Click Next
2. Specify Input File:
   - Input file to be imported: C:\SQLData\SampleEmployees.csv
   - New table name: Employees
   - Table schema: dbo
   - Click Next

STEP 3.3: PREVIEW DATA
----------------------

1. Review the preview of data
2. Verify columns are detected correctly
3. Click Next

STEP 3.4: MODIFY COLUMNS
------------------------

Review and modify column definitions if needed:

    Column Name     | Data Type       | Primary Key | Allow Nulls
    ----------------+-----------------+-------------+-------------
    EmployeeID      | int             | Yes         | No
    FirstName       | nvarchar(50)    | No          | Yes
    LastName        | nvarchar(50)    | No          | Yes
    Email           | nvarchar(100)   | No          | Yes
    Phone           | nvarchar(20)    | No          | Yes
    Department      | nvarchar(50)    | No          | Yes
    City            | nvarchar(50)    | No          | Yes
    HireDate        | date            | No          | Yes
    Salary          | decimal(10,2)   | No          | Yes
    IsActive        | bit             | No          | Yes

Click Next

STEP 3.5: COMPLETE IMPORT
-------------------------

1. Review summary
2. Click Finish
3. Wait for import to complete
4. Verify success message

STEP 3.6: VERIFY IMPORTED DATA
------------------------------

    USE Lab6;
    GO

    -- Check row count
    SELECT COUNT(*) AS TotalRows FROM dbo.Employees;
    GO

    -- View sample data
    SELECT TOP 10 * FROM dbo.Employees;
    GO

    -- Check table structure
    EXEC sp_help 'dbo.Employees';
    GO

================================================================================
                    PART 4: CREATE DATABASE ON INSTANCE2
================================================================================

STEP 4.1: CONNECT TO NAMED INSTANCE (INSTANCE2)
-----------------------------------------------

Open new SSMS connection:
- Server name: localhost\YOURINSTANCE (or SERVERNAME\YOURINSTANCE)
- Authentication: SQL Server Authentication
- Login: sa
- Password: [your_password]

STEP 4.2: CREATE LAB6 DATABASE ON INSTANCE2
-------------------------------------------

    -- Connect to Instance2 (named instance)
    USE master;
    GO

    -- Create Lab6 database
    CREATE DATABASE Lab6;
    GO

    -- Verify creation
    SELECT name, state_desc
    FROM sys.databases
    WHERE name = 'Lab6';
    GO

================================================================================
                    PART 5: COPY TABLE USING BCP UTILITY
================================================================================

BCP (Bulk Copy Program) is a command-line utility for bulk data transfer.

STEP 5.1: BCP SYNTAX AND HELP
-----------------------------

Open Command Prompt and view help:

    bcp /?

Key parameters:
    bcp {table | query} {in | out | queryout | format} datafile
        -S server\instance     Server and instance name
        -d database            Database name
        -U username            SQL login (use -T for Windows auth)
        -P password            Password for SQL login
        -c                     Character data type (text mode)
        -t delimiter           Field terminator (default: tab)
        -r row_terminator      Row terminator (default: newline)
        -T                     Trusted connection (Windows auth)
        -F first_row           First row to copy (skip header)
        -b batch_size          Batch size
        -e error_file          Error file path

STEP 5.2: EXPORT DATA FROM INSTANCE1 USING BCP
----------------------------------------------

Open Command Prompt as Administrator:

    -- Export to native format (faster for SQL-to-SQL transfer)
    bcp Lab6.dbo.Employees out "C:\SQLData\Employees_bcp.dat" -S localhost -d Lab6 -T -n

    -- OR export to character format (human-readable)
    bcp Lab6.dbo.Employees out "C:\SQLData\Employees_bcp.csv" -S localhost -d Lab6 -T -c -t "," -r "\n"

    -- Using SQL Authentication:
    bcp Lab6.dbo.Employees out "C:\SQLData\Employees_bcp.dat" -S localhost -d Lab6 -U sa -P "YourPassword" -n

Parameters explained:
- out: Export data
- -S localhost: Connect to default instance
- -d Lab6: Database name
- -T: Windows Authentication (trusted connection)
- -n: Native format (binary, faster)
- -c: Character format (text)
- -t ",": Field delimiter (comma for CSV)

STEP 5.3: CREATE TABLE ON INSTANCE2
-----------------------------------

Before importing with bcp, create the destination table:

    -- Connect to Instance2
    USE Lab6;
    GO

    -- Create table structure (same as source)
    CREATE TABLE dbo.Employees (
        EmployeeID INT PRIMARY KEY,
        FirstName NVARCHAR(50),
        LastName NVARCHAR(50),
        Email NVARCHAR(100),
        Phone NVARCHAR(20),
        Department NVARCHAR(50),
        City NVARCHAR(50),
        HireDate DATE,
        Salary DECIMAL(10,2),
        IsActive BIT
    );
    GO

STEP 5.4: IMPORT DATA TO INSTANCE2 USING BCP
--------------------------------------------

    -- Import from native format file
    bcp Lab6.dbo.Employees in "C:\SQLData\Employees_bcp.dat" -S localhost\YOURINSTANCE -d Lab6 -T -n

    -- OR using SQL Authentication for named instance:
    bcp Lab6.dbo.Employees in "C:\SQLData\Employees_bcp.dat" -S localhost\YOURINSTANCE -d Lab6 -U sa -P "YourPassword" -n

    -- If using character format:
    bcp Lab6.dbo.Employees in "C:\SQLData\Employees_bcp.csv" -S localhost\YOURINSTANCE -d Lab6 -T -c -t "," -r "\n"

STEP 5.5: ALTERNATIVE - DIRECT TRANSFER WITH BCP (QUERYOUT + IN)
----------------------------------------------------------------

Export using query (for custom columns or filtering):

    bcp "SELECT * FROM Lab6.dbo.Employees" queryout "C:\SQLData\Employees_query.dat" -S localhost -T -n

STEP 5.6: VERIFY DATA ON INSTANCE2
----------------------------------

    -- Connect to Instance2
    USE Lab6;
    GO

    SELECT COUNT(*) AS TotalRows FROM dbo.Employees;
    GO

    SELECT TOP 10 * FROM dbo.Employees;
    GO

================================================================================
                    PART 6: IMPORT USING BULK INSERT
================================================================================

BULK INSERT is a T-SQL statement for importing data from a file.
The destination table MUST exist before running BULK INSERT.

STEP 6.1: EXPORT DATA TO CSV FOR BULK INSERT
--------------------------------------------

First, export data to a clean CSV format:

    bcp Lab6.dbo.Employees out "C:\SQLData\Employees_bulk.csv" -S localhost -d Lab6 -T -c -t "," -r "\n"

Or create format file for more control:

    bcp Lab6.dbo.Employees format nul -S localhost -d Lab6 -T -n -f "C:\SQLData\Employees.fmt"

STEP 6.2: CREATE DESTINATION TABLE ON INSTANCE2
-----------------------------------------------

    -- Connect to Instance2
    USE Lab6;
    GO

    -- Create table with different name for BULK INSERT
    CREATE TABLE dbo.Employees_BulkInsert (
        EmployeeID INT PRIMARY KEY,
        FirstName NVARCHAR(50),
        LastName NVARCHAR(50),
        Email NVARCHAR(100),
        Phone NVARCHAR(20),
        Department NVARCHAR(50),
        City NVARCHAR(50),
        HireDate DATE,
        Salary DECIMAL(10,2),
        IsActive BIT
    );
    GO

STEP 6.3: ENABLE AD HOC DISTRIBUTED QUERIES (If needed)
-------------------------------------------------------

    -- On Instance2
    EXEC sp_configure 'show advanced options', 1;
    RECONFIGURE;
    EXEC sp_configure 'Ad Hoc Distributed Queries', 1;
    RECONFIGURE;
    GO

STEP 6.4: BULK INSERT DATA
--------------------------

Method 1: Basic BULK INSERT

    USE Lab6;
    GO

    BULK INSERT dbo.Employees_BulkInsert
    FROM 'C:\SQLData\Employees_bulk.csv'
    WITH (
        FIELDTERMINATOR = ',',
        ROWTERMINATOR = '\n',
        FIRSTROW = 1,           -- Use 2 if CSV has header row
        TABLOCK
    );
    GO

Method 2: BULK INSERT with format file

    BULK INSERT dbo.Employees_BulkInsert
    FROM 'C:\SQLData\Employees_bcp.dat'
    WITH (
        FORMATFILE = 'C:\SQLData\Employees.fmt',
        TABLOCK
    );
    GO

Method 3: BULK INSERT with more options

    BULK INSERT dbo.Employees_BulkInsert
    FROM 'C:\SQLData\Employees_bulk.csv'
    WITH (
        FIELDTERMINATOR = ',',
        ROWTERMINATOR = '\n',
        FIRSTROW = 1,
        BATCHSIZE = 500,        -- Commit every 500 rows
        MAXERRORS = 10,         -- Allow up to 10 errors
        ERRORFILE = 'C:\SQLData\BulkInsert_Errors.log',
        TABLOCK,                -- Table-level lock for performance
        CHECK_CONSTRAINTS,      -- Check constraints during insert
        FIRE_TRIGGERS           -- Fire insert triggers
    );
    GO

STEP 6.5: VERIFY BULK INSERT DATA
---------------------------------

    USE Lab6;
    GO

    SELECT COUNT(*) AS TotalRows FROM dbo.Employees_BulkInsert;
    GO

    SELECT TOP 10 * FROM dbo.Employees_BulkInsert;
    GO

================================================================================
                    PART 7: IMPORT USING OPENROWSET
================================================================================

OPENROWSET provides ad-hoc remote data access. Unlike BULK INSERT,
OPENROWSET can be used with SELECT INTO to CREATE and INSERT in one statement.

STEP 7.1: ENABLE REQUIRED CONFIGURATIONS
----------------------------------------

    -- On Instance2
    EXEC sp_configure 'show advanced options', 1;
    RECONFIGURE;

    -- Enable Ad Hoc Distributed Queries
    EXEC sp_configure 'Ad Hoc Distributed Queries', 1;
    RECONFIGURE;
    GO

STEP 7.2: GRANT BULK OPERATIONS PERMISSION (If needed)
------------------------------------------------------

    -- Grant ADMINISTER BULK OPERATIONS to your login
    USE master;
    GO
    GRANT ADMINISTER BULK OPERATIONS TO [sa];  -- Or your login name
    GO

STEP 7.3: OPENROWSET WITH SELECT INTO (Creates Table Automatically!)
--------------------------------------------------------------------

This is the KEY DIFFERENCE from BULK INSERT - OPENROWSET with SELECT INTO
creates the destination table automatically!

    USE Lab6;
    GO

    -- Create table AND insert data in one statement
    SELECT *
    INTO dbo.Employees_OpenRowset
    FROM OPENROWSET(
        BULK 'C:\SQLData\Employees_bulk.csv',
        FORMATFILE = 'C:\SQLData\Employees.fmt'
    ) AS DataFile;
    GO

STEP 7.4: OPENROWSET WITHOUT FORMAT FILE
----------------------------------------

Using field/row terminators directly:

    -- First, drop table if testing multiple times
    -- DROP TABLE IF EXISTS dbo.Employees_OpenRowset;

    SELECT *
    INTO dbo.Employees_OpenRowset
    FROM OPENROWSET(
        BULK 'C:\SQLData\Employees_bulk.csv',
        SINGLE_CLOB
    ) AS DataFile;
    GO

Better approach with format specification:

    -- Using OPENROWSET with inline format (SQL Server 2017+)
    SELECT *
    INTO dbo.Employees_OpenRowset
    FROM OPENROWSET(
        BULK 'C:\SQLData\Employees_bulk.csv',
        FORMAT = 'CSV',
        FIELDTERMINATOR = ',',
        ROWTERMINATOR = '\n',
        FIRSTROW = 1
    ) AS DataFile;
    GO

STEP 7.5: ALTERNATIVE - CREATE FORMAT FILE FIRST
------------------------------------------------

Create a format file using bcp:

    bcp Lab6.dbo.Employees format nul -S localhost -d Lab6 -T -c -t "," -r "\n" -f "C:\SQLData\Employees_csv.fmt"

Then use with OPENROWSET:

    USE Lab6;
    GO

    SELECT
        EmployeeID,
        FirstName,
        LastName,
        Email,
        Phone,
        Department,
        City,
        HireDate,
        Salary,
        IsActive
    INTO dbo.Employees_OpenRowset
    FROM OPENROWSET(
        BULK 'C:\SQLData\Employees_bulk.csv',
        FORMATFILE = 'C:\SQLData\Employees_csv.fmt'
    ) AS DataFile;
    GO

STEP 7.6: OPENROWSET TO EXISTING TABLE (INSERT INTO)
----------------------------------------------------

If table already exists:

    -- Create table first
    CREATE TABLE dbo.Employees_OpenRowset2 (
        EmployeeID INT,
        FirstName NVARCHAR(50),
        LastName NVARCHAR(50),
        Email NVARCHAR(100),
        Phone NVARCHAR(20),
        Department NVARCHAR(50),
        City NVARCHAR(50),
        HireDate DATE,
        Salary DECIMAL(10,2),
        IsActive BIT
    );
    GO

    -- Insert using OPENROWSET
    INSERT INTO dbo.Employees_OpenRowset2
    SELECT *
    FROM OPENROWSET(
        BULK 'C:\SQLData\Employees_bulk.csv',
        FORMATFILE = 'C:\SQLData\Employees_csv.fmt'
    ) AS DataFile;
    GO

STEP 7.7: VERIFY OPENROWSET DATA
--------------------------------

    USE Lab6;
    GO

    SELECT COUNT(*) AS TotalRows FROM dbo.Employees_OpenRowset;
    GO

    SELECT TOP 10 * FROM dbo.Employees_OpenRowset;
    GO

    -- Verify all 3 tables exist
    SELECT
        t.name AS TableName,
        p.rows AS RowCount
    FROM sys.tables t
    JOIN sys.partitions p ON t.object_id = p.object_id
    WHERE p.index_id IN (0, 1)  -- Heap or clustered index
    ORDER BY t.name;
    GO

================================================================================
                    KEY DIFFERENCES: BULK INSERT vs OPENROWSET
================================================================================

| Feature | BULK INSERT | OPENROWSET |
|---------|-------------|------------|
| Table Creation | Table MUST exist before import | Can create with SELECT INTO (auto-create) |
| Syntax | Statement | Function (used in SELECT) |
| Usage | INSERT only | SELECT, INSERT, UPDATE |
| Data Source | Local files only | Local files + remote |
| Performance | Slightly faster | More flexible |
| Transactions | Can be in transaction | Each is its own query |
| Triggers | FIRE_TRIGGERS option | Triggers always fire |

================================================================================
                    PART 8: MIGRATE DATABASE USING DATA MIGRATION ASSISTANT
================================================================================

Data Migration Assistant (DMA) is a GUI tool for assessing and migrating
SQL Server databases.

STEP 8.1: DOWNLOAD AND INSTALL DMA
----------------------------------

1. Go to: https://www.microsoft.com/en-us/download/details.aspx?id=53595
2. Download DataMigrationAssistant.msi
3. Run installer and follow prompts
4. Launch "Microsoft Data Migration Assistant"

STEP 8.2: CREATE NEW MIGRATION PROJECT
--------------------------------------

1. Click "New" (+) button
2. Configure project:
   - Project type: Migration
   - Project name: Lab6Migration
   - Source server type: SQL Server
   - Target server type: SQL Server
   - Migration scope: Schema and data
3. Click "Create"

STEP 8.3: CONFIGURE SOURCE (INSTANCE2)
--------------------------------------

1. Select Source tab
2. Server name: localhost\YOURINSTANCE (or SERVERNAME\YOURINSTANCE)
3. Authentication type: SQL Server Authentication
   - Username: sa
   - Password: [your_password]
   OR
   - Windows Authentication
4. Connection properties:
   - Encrypt connection: Optional
   - Trust server certificate: Check if using self-signed cert
5. Click "Connect"

6. Select source database:
   - Check [X] Lab6
7. Click "Next"

STEP 8.4: CONFIGURE TARGET (INSTANCE1)
--------------------------------------

1. Select Target tab
2. Server name: localhost (default instance)
3. Authentication type: SQL Server Authentication or Windows
4. Click "Connect"

5. Target database name: Lab6Copy
   (DMA will create this database)

6. Click "Next"

STEP 8.5: SELECT OBJECTS TO MIGRATE
-----------------------------------

1. Review schema objects:
   - Tables:
     [X] dbo.Employees (from bcp)
     [X] dbo.Employees_BulkInsert
     [X] dbo.Employees_OpenRowset
   - Other objects (if any)

2. Click "Generate SQL script" to review

3. Click "Deploy schema"
   - Wait for schema deployment to complete

4. Click "Migrate data"
   - Select all tables
   - Click "Start data migration"

STEP 8.6: MONITOR MIGRATION PROGRESS
------------------------------------

1. Watch progress bars for each table
2. Review any warnings or errors
3. Wait for "Migration completed" message

STEP 8.7: VERIFY MIGRATION ON INSTANCE1
---------------------------------------

Connect to Instance1 and verify:

    USE Lab6Copy;
    GO

    -- List all tables
    SELECT
        t.name AS TableName,
        p.rows AS RowCount
    FROM sys.tables t
    JOIN sys.partitions p ON t.object_id = p.object_id
    WHERE p.index_id IN (0, 1)
    ORDER BY t.name;
    GO

    -- Expected output:
    -- TableName               RowCount
    -- Employees               1000
    -- Employees_BulkInsert    1000
    -- Employees_OpenRowset    1000

    -- Verify data in each table
    SELECT TOP 5 * FROM dbo.Employees;
    SELECT TOP 5 * FROM dbo.Employees_BulkInsert;
    SELECT TOP 5 * FROM dbo.Employees_OpenRowset;
    GO

================================================================================
                    ALTERNATIVE: SCRIPT-BASED MIGRATION
================================================================================

If DMA is not available, use these T-SQL alternatives:

OPTION 1: LINKED SERVER
-----------------------

    -- On Instance1, create linked server to Instance2
    EXEC sp_addlinkedserver
        @server = 'YOURINSTANCE',
        @srvproduct = '',
        @provider = 'SQLNCLI',
        @datasrc = 'localhost\YOURINSTANCE';
    GO

    -- Set login mapping
    EXEC sp_addlinkedsrvlogin
        @rmtsrvname = 'YOURINSTANCE',
        @useself = 'false',
        @rmtuser = 'sa',
        @rmtpassword = 'YourPassword';
    GO

    -- Create database
    CREATE DATABASE Lab6Copy;
    GO

    USE Lab6Copy;
    GO

    -- Copy tables
    SELECT * INTO dbo.Employees
    FROM [YOURINSTANCE].Lab6.dbo.Employees;

    SELECT * INTO dbo.Employees_BulkInsert
    FROM [YOURINSTANCE].Lab6.dbo.Employees_BulkInsert;

    SELECT * INTO dbo.Employees_OpenRowset
    FROM [YOURINSTANCE].Lab6.dbo.Employees_OpenRowset;
    GO

OPTION 2: BACKUP AND RESTORE
----------------------------

    -- On Instance2: Backup Lab6
    BACKUP DATABASE Lab6
    TO DISK = 'C:\SQLBackups\Lab6_Migration.bak'
    WITH FORMAT, INIT;
    GO

    -- On Instance1: Restore as Lab6Copy
    RESTORE DATABASE Lab6Copy
    FROM DISK = 'C:\SQLBackups\Lab6_Migration.bak'
    WITH
        MOVE 'Lab6' TO 'C:\SQLData\Lab6Copy.mdf',
        MOVE 'Lab6_log' TO 'C:\SQLLog\Lab6Copy_log.ldf',
        REPLACE;
    GO

================================================================================
                    SUMMARY
================================================================================

Final Database Structure:
------------------------

INSTANCE1 (Default):
+------------------+
| Lab6             |
|  - Employees     | (from Import Flat File wizard)
+------------------+
| Lab6Copy         | (migrated from Instance2 using DMA)
|  - Employees     | (copied via bcp)
|  - Employees_BulkInsert |
|  - Employees_OpenRowset |
+------------------+

INSTANCE2 (Named):
+------------------+
| Lab6             |
|  - Employees     | (copied via bcp)
|  - Employees_BulkInsert | (created via BULK INSERT)
|  - Employees_OpenRowset | (created via OPENROWSET SELECT INTO)
+------------------+

Files Created:
--------------
C:\SQLData\
├── SampleEmployees.csv         (Generated by PowerShell)
├── Employees_bcp.dat           (bcp native format export)
├── Employees_bcp.csv           (bcp character format export)
├── Employees_bulk.csv          (for BULK INSERT)
├── Employees.fmt               (Format file)
└── Employees_csv.fmt           (CSV format file)

Key Commands Reference:
----------------------
# BCP Export
bcp Lab6.dbo.Employees out "file.dat" -S server -d database -T -n

# BCP Import
bcp Lab6.dbo.Employees in "file.dat" -S server\instance -d database -T -n

# BULK INSERT
BULK INSERT table FROM 'file' WITH (FIELDTERMINATOR=',', ROWTERMINATOR='\n')

# OPENROWSET (creates table)
SELECT * INTO table FROM OPENROWSET(BULK 'file', FORMATFILE='fmt') AS f

