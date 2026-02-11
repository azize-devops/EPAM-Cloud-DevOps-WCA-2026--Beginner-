IMPORTING AND EXPORTING DATA - TASK
===================================

GOALS:
------
- Learn to generate test data using PowerShell
- Master importing data from flat files (CSV) into SQL Server
- Understand different data transfer methods between SQL Server instances
- Compare bcp utility, BULK INSERT, and OPENROWSET approaches
- Use Data Migration Assistant for database migration

PREREQUISITES:
--------------
- 1 VM with at least 2 instances of SQL Server 2019:
  * Default Instance (MSSQLSERVER)
  * Named Instance (e.g., YOURINSTANCE)
- SQL Server Management Studio (SSMS)
- Data Migration Assistant (DMA) installed
- PowerShell access

TASKS:
------
1. Create Database on Instance1:
   - Create database named "Lab6" on the default instance (Instance1)

2. Generate CSV File with PowerShell:
   - Create a CSV file containing at least 1000 rows
   - Format: First row = column headers
   - First column = IDs (1, 2, 3, ...)
   - Include multiple data columns (e.g., Name, Email, Phone, Date, Amount)

3. Import CSV Using 'Import Flat File' Wizard:
   - Use SSMS "Import Flat File" wizard
   - Import CSV data into a new table in Lab6 database
   - Table name: e.g., "Employees" or "SampleData"

4. Create Database on Instance2:
   - Create database named "Lab6" on the named instance (Instance2)

5. Copy Table Using BCP Utility:
   - Export data from Instance1 using bcp
   - Import data to Instance2 using bcp
   - Destination table: Same name as source (e.g., "Employees")
   - Use bcp --help to learn connection parameters for named instances

6. Import Using BULK INSERT:
   - Export data to a file from Instance1
   - Use BULK INSERT statement on Instance2
   - Destination table: Different name (e.g., "Employees_BulkInsert")
   - Note: Table must exist before BULK INSERT

7. Import Using OPENROWSET:
   - Use OPENROWSET function to import data on Instance2
   - Destination table: Different name (e.g., "Employees_OpenRowset")
   - Note: OPENROWSET can create and insert in one statement (SELECT INTO)

8. Migrate Database Using Data Migration Assistant:
   - Use DMA to copy Lab6 database from Instance2 to Instance1
   - Destination database name: "Lab6Copy"
   - Verify Lab6Copy contains all 3 tables:
     * Table from step 5 (bcp)
     * Table from step 6 (BULK INSERT)
     * Table from step 7 (OPENROWSET)

REQUIREMENTS:
-------------
1. Lab6 database must be created on both instances
2. CSV file must contain at least 1000 rows with proper formatting
3. Import Flat File wizard must be used for initial import
4. bcp utility must be used for cross-instance data transfer
5. BULK INSERT statement must be demonstrated
6. OPENROWSET function must be demonstrated
7. Data Migration Assistant must successfully migrate database
8. Lab6Copy must contain all 3 tables with data

SELF-REVIEW:
------------
- Lab6 database exists on Instance1
- CSV file generated with 1000+ rows and proper format
- Table created using Import Flat File wizard
- Lab6 database exists on Instance2
- Table copied using bcp utility
- Table created using BULK INSERT (Employees_BulkInsert)
- Table created using OPENROWSET (Employees_OpenRowset)
- Data Migration Assistant installed and working
- Lab6Copy database exists on Instance1
- Lab6Copy contains all 3 tables with correct data
- Understand differences between bcp, BULK INSERT, and OPENROWSET

HELPFUL MATERIALS:
------------------
https://docs.microsoft.com/en-us/sql/tools/bcp-utility
https://docs.microsoft.com/en-us/sql/t-sql/statements/bulk-insert-transact-sql
https://docs.microsoft.com/en-us/sql/t-sql/functions/openrowset-transact-sql
https://docs.microsoft.com/en-us/sql/dma/dma-overview
https://docs.microsoft.com/en-us/sql/relational-databases/import-export/import-flat-file-wizard
https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/export-csv
