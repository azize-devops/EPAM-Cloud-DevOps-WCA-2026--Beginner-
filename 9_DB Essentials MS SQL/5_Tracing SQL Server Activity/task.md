# TRACING SQL SERVER ACTIVITY - TASK

GOALS:
------
- Learn to use SQL Server Profiler for tracing database activity
- Understand SQL Server locking mechanisms
- Create and observe a deadlock scenario
- Analyze deadlock graphs to understand victim selection
- Learn how SQL Server resolves deadlocks

PREREQUISITES:
--------------
- 1 VM with installed SQL Server 2019
- SQL Server Management Studio (SSMS)
- SQL Server Profiler (included with SSMS)

TASKS:
------
1. Start SQL Server Profiler:
   - Open SQL Server Profiler
   - Connect to your SQL Server instance
   - Select the "TSQL_Locks" template
   - Start the trace

2. Create Test Tables:
   - Open SSMS and connect to your instance
   - Create two global temporary tables (##Employees, ##Suppliers)
   - Insert sample data into both tables

   -- Create ##Employees table
   CREATE TABLE ##Employees (
       EmpId INT IDENTITY,
       EmpName VARCHAR(16),
       Phone VARCHAR(16)
   )
   GO
   INSERT INTO ##Employees (EmpName, Phone)
   VALUES ('Kate', '800-666-3434'), ('John', '619-444-0808')
   GO

   -- Create ##Suppliers table
   CREATE TABLE ##Suppliers(
       SupplierId INT IDENTITY,
       SupName VARCHAR(64),
       Fax VARCHAR(16)
   )
   GO
   INSERT INTO ##Suppliers (SupName, Fax)
   VALUES ('Amway', '877-333-0404'), ('Ariel', '800-935-6518')
   GO

3. Create Deadlock Scenario:
   - Open TWO separate query windows in SSMS
   - Execute commands step-by-step, alternating between sessions
   - Observe the deadlock occur

   Session 1 (Query Window 1)    |    Session 2 (Query Window 2)
   =============================|================================
   Step 1: BEGIN TRAN;          |    Step 2: BEGIN TRAN;
   =============================|================================
   Step 3: UPDATE ##Employees   |
           SET EmpName='Christie'|
           WHERE EmpId = 1      |
   =============================|================================
                                |    Step 4: UPDATE ##Suppliers
                                |            SET Fax = N'444-9898'
                                |            WHERE SupplierId = 1
   =============================|================================
   Step 5: UPDATE ##Suppliers   |
           SET Fax = N'888-4343'|
           WHERE SupplierId = 1 |
   <Session 1 is BLOCKED>       |
   =============================|================================
                                |    Step 6: UPDATE ##Employees
                                |            SET Phone = N'777-8888'
                                |            WHERE EmpId = 1
                                |    <Session 2 is BLOCKED>
   =============================|================================
   DEADLOCK OCCURS!             |    DEADLOCK OCCURS!
   One session becomes victim   |    One session becomes victim

4. Analyze Deadlock in SQL Profiler:
   - Find the Deadlock Graph event in the trace
   - Examine which process was chosen as the victim
   - Understand WHY that process was selected as the victim

REQUIREMENTS:
-------------
1. SQL Server Profiler must be running with TSQL_Locks template
2. Both global temp tables must be created with sample data
3. Deadlock must be created by executing sessions step-by-step
4. Deadlock graph must be captured and analyzed
5. Explanation of victim selection criteria must be provided

SELF-REVIEW:
------------
- SQL Server Profiler is running with TSQL_Locks template
- Global temp tables ##Employees and ##Suppliers are created
- Sample data is inserted into both tables
- Two query windows are open with separate sessions
- Commands executed step-by-step in correct order
- Deadlock occurred and was captured by Profiler
- Deadlock graph is visible in Profiler trace
- Victim process identified and reason understood
- Understand deadlock victim selection criteria

HELPFUL MATERIALS:
------------------
https://docs.microsoft.com/en-us/sql/tools/sql-server-profiler/sql-server-profiler
https://docs.microsoft.com/en-us/sql/relational-databases/sql-trace/sql-server-profiler-templates
https://docs.microsoft.com/en-us/sql/relational-databases/performance/deadlocks
https://docs.microsoft.com/en-us/sql/relational-databases/sql-trace/analyze-deadlocks-with-sql-server-profiler
https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql

