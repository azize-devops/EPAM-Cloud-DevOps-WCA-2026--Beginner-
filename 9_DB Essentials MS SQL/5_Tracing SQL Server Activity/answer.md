# TRACING SQL SERVER ACTIVITY - ANSWER

This guide covers SQL Server Profiler usage, creating deadlock scenarios,
and analyzing deadlock graphs to understand how SQL Server handles
concurrent transactions and lock conflicts.

---
                    UNDERSTANDING DEADLOCKS
---

WHAT IS A DEADLOCK?
-------------------

A deadlock occurs when two or more transactions permanently block each other
by each holding a lock that the other needs.

Visual representation:

    Session 1                     Session 2
    +--------+                    +--------+
    | HOLDS  |                    | HOLDS  |
    | Lock A |<---- WANTS --------|  Lock B|
    |        |                    |        |
    |  WANTS |----- WANTS ------->|        |
    | Lock B |                    | Lock A |
    +--------+                    +--------+

Neither can proceed - this is a deadlock!

SQL Server automatically detects deadlocks and terminates one transaction
(the "victim") to allow the other to complete.

LOCK TYPES:
-----------
| Lock Type | Description |
|-----------|-------------|
| Shared (S) | Read operations. Multiple shared locks allowed |
| Exclusive (X) | Write operations. No other locks allowed |
| Update (U) | Prevents deadlock during update reads |
| Intent (IS/IX) | Hierarchy locks indicating lower-level locks |

---
                    PART 1: START SQL SERVER PROFILER
---

STEP 1.1: OPEN SQL SERVER PROFILER
----------------------------------

Method 1: From SSMS
1. Open SQL Server Management Studio
2. Go to Tools menu
3. Click "SQL Server Profiler"

Method 2: Direct Launch
1. Open Start menu
2. Search for "SQL Server Profiler"
3. Click to launch

Method 3: Command Line
    profiler.exe

STEP 1.2: CONNECT TO SQL SERVER
-------------------------------

1. In Profiler, File -> New Trace (or Ctrl+N)
2. Connect to SQL Server dialog appears:
   - Server name: localhost (or your server name)
   - Authentication: Windows Authentication or SQL Server Authentication
   - Click "Connect"

STEP 1.3: SELECT TSQL_LOCKS TEMPLATE
------------------------------------

1. Trace Properties dialog opens
2. General tab:
   - Trace name: DeadlockDemo (or any name)
   - Use the template: TSQL_Locks
   - Save to file: (optional) C:\Traces\DeadlockDemo.trc

3. Events Selection tab (review included events):
   The TSQL_Locks template includes:
   - Blocked process report
   - Deadlock graph
   - Lock:Acquired
   - Lock:Cancel
   - Lock:Deadlock
   - Lock:Deadlock Chain
   - Lock:Escalation
   - Lock:Released
   - Lock:Timeout
   - RPC:Completed
   - SQL:BatchCompleted
   - SQL:BatchStarting

4. Click "Run" to start the trace

STEP 1.4: ADD DEADLOCK GRAPH EVENT (IF NOT INCLUDED)
----------------------------------------------------

If Deadlock Graph is not in the template:

1. Click "Events Selection" tab
2. Check "Show all events"
3. Expand "Locks" category
4. Check:
   [X] Deadlock graph
   [X] Lock:Deadlock
   [X] Lock:Deadlock Chain
5. Click "Run"

---
                    PART 2: CREATE TEST TABLES
---

STEP 2.1: OPEN SSMS AND CONNECT
-------------------------------

1. Open SQL Server Management Studio
2. Connect to your SQL Server instance
3. Open a new query window (Ctrl+N)

STEP 2.2: CREATE GLOBAL TEMPORARY TABLES
----------------------------------------

Execute the following script:

    -- ========================================
    -- CREATE TEST TABLES FOR DEADLOCK DEMO
    -- ========================================

    -- Drop tables if they exist (for re-running demo)
    IF OBJECT_ID('tempdb..##Employees') IS NOT NULL
        DROP TABLE ##Employees;
    IF OBJECT_ID('tempdb..##Suppliers') IS NOT NULL
        DROP TABLE ##Suppliers;
    GO

    -- Create ##Employees table
    CREATE TABLE ##Employees (
        EmpId INT IDENTITY,
        EmpName VARCHAR(16),
        Phone VARCHAR(16)
    );
    GO

    -- Insert sample data
    INSERT INTO ##Employees (EmpName, Phone)
    VALUES ('Kate', '800-666-3434'), ('John', '619-444-0808');
    GO

    -- Verify data
    SELECT * FROM ##Employees;
    GO

    -- Create ##Suppliers table
    CREATE TABLE ##Suppliers(
        SupplierId INT IDENTITY,
        SupName VARCHAR(64),
        Fax VARCHAR(16)
    );
    GO

    -- Insert sample data
    INSERT INTO ##Suppliers (SupName, Fax)
    VALUES ('Amway', '877-333-0404'), ('Ariel', '800-935-6518');
    GO

    -- Verify data
    SELECT * FROM ##Suppliers;
    GO

Expected output:

    ##Employees:
    EmpId   EmpName   Phone
    1       Kate      800-666-3434
    2       John      619-444-0808

    ##Suppliers:
    SupplierId  SupName   Fax
    1           Amway     877-333-0404
    2           Ariel     800-935-6518

NOTE: Global temporary tables (##) are used because they're visible across
all sessions, which is required for our deadlock demo.

---
                    PART 3: CREATE DEADLOCK SCENARIO
---

STEP 3.1: OPEN TWO QUERY WINDOWS
--------------------------------

In SSMS:
1. Open first query window (Ctrl+N) - This is Session 1
2. Open second query window (Ctrl+N) - This is Session 2

You can arrange them side by side:
- Window -> New Vertical Tab Group

Or view them separately by clicking their tabs.

STEP 3.2: IDENTIFY SESSION IDs (OPTIONAL)
-----------------------------------------

In each query window, run:

    SELECT @@SPID AS SessionID;

Note the session IDs - you'll see them in the deadlock graph later.
Example: Session 1 = SPID 55, Session 2 = SPID 57

STEP 3.3: EXECUTE DEADLOCK SCENARIO STEP BY STEP
------------------------------------------------

Follow these steps EXACTLY in order, alternating between windows:

+------------------------------------------------------------------+
|  STEP 1: Session 1 - Begin Transaction                           |
+------------------------------------------------------------------+

    -- Session 1 (Query Window 1)
    BEGIN TRAN;
    PRINT 'Session 1: Transaction started';

+------------------------------------------------------------------+
|  STEP 2: Session 2 - Begin Transaction                           |
+------------------------------------------------------------------+

    -- Session 2 (Query Window 2)
    BEGIN TRAN;
    PRINT 'Session 2: Transaction started';

+------------------------------------------------------------------+
|  STEP 3: Session 1 - Update Employees (Acquires X lock)          |
+------------------------------------------------------------------+

    -- Session 1 (Query Window 1)
    UPDATE ##Employees
    SET EmpName = 'Christie'
    WHERE EmpId = 1;
    PRINT 'Session 1: Updated Employees - holding X lock on Employees';

    -- This succeeds immediately
    -- Session 1 now holds exclusive lock on ##Employees (EmpId=1)

+------------------------------------------------------------------+
|  STEP 4: Session 2 - Update Suppliers (Acquires X lock)          |
+------------------------------------------------------------------+

    -- Session 2 (Query Window 2)
    UPDATE ##Suppliers
    SET Fax = N'444-9898'
    WHERE SupplierId = 1;
    PRINT 'Session 2: Updated Suppliers - holding X lock on Suppliers';

    -- This succeeds immediately
    -- Session 2 now holds exclusive lock on ##Suppliers (SupplierId=1)

+------------------------------------------------------------------+
|  STEP 5: Session 1 - Try to Update Suppliers (BLOCKED!)          |
+------------------------------------------------------------------+

    -- Session 1 (Query Window 1)
    UPDATE ##Suppliers
    SET Fax = N'888-4343'
    WHERE SupplierId = 1;

    -- This query will WAIT (be blocked)!
    -- Session 1 needs lock on ##Suppliers, but Session 2 holds it
    -- Status bar shows "Executing query..."

    Current state:
    - Session 1: HOLDS lock on ##Employees, WAITING for ##Suppliers
    - Session 2: HOLDS lock on ##Suppliers

+------------------------------------------------------------------+
|  STEP 6: Session 2 - Try to Update Employees (DEADLOCK!)         |
+------------------------------------------------------------------+

    -- Session 2 (Query Window 2)
    UPDATE ##Employees
    SET Phone = N'777-8888'
    WHERE EmpId = 1;

    -- DEADLOCK OCCURS!
    -- Session 2 needs lock on ##Employees, but Session 1 holds it
    -- SQL Server detects deadlock and kills one session

    Final state before deadlock resolution:
    - Session 1: HOLDS ##Employees, WANTS ##Suppliers
    - Session 2: HOLDS ##Suppliers, WANTS ##Employees
    --> Neither can proceed = DEADLOCK

+------------------------------------------------------------------+
|  DEADLOCK RESOLUTION                                             |
+------------------------------------------------------------------+

One session will receive this error:

    Msg 1205, Level 13, State 51, Line X
    Transaction (Process ID XX) was deadlocked on lock resources with
    another process and has been chosen as the deadlock victim.
    Rerun the transaction.

The OTHER session will complete successfully.

STEP 3.4: CLEAN UP AFTER DEADLOCK
---------------------------------

In the session that completed successfully:

    -- Commit or rollback the transaction
    COMMIT TRAN;  -- or ROLLBACK TRAN;
    GO

    -- Verify final state
    SELECT * FROM ##Employees;
    SELECT * FROM ##Suppliers;
    GO

---
                    PART 4: ANALYZE DEADLOCK IN SQL PROFILER
---

STEP 4.1: FIND DEADLOCK EVENTS IN PROFILER
------------------------------------------

1. Look at the SQL Server Profiler trace window
2. Scroll through events to find:
   - Lock:Deadlock Chain
   - Lock:Deadlock
   - Deadlock Graph

The events appear in chronological order. Look for events that occurred
around the time of your deadlock.

STEP 4.2: VIEW DEADLOCK GRAPH
-----------------------------

1. Find the "Deadlock Graph" event in the trace
2. Click on that row
3. In the bottom pane, you'll see the graphical deadlock representation

OR

1. Click on the Deadlock Graph row
2. Right-click -> "Extract Event Data..."
3. Save as .xdl file (XML Deadlock file)
4. Open .xdl file in SSMS or text editor

STEP 4.3: UNDERSTANDING THE DEADLOCK GRAPH
------------------------------------------

The deadlock graph shows:

    +------------------+                    +------------------+
    |   Process Node   |                    |   Process Node   |
    |   (Oval shape)   |                    |   (Oval shape)   |
    |                  |                    |                  |
    |  SPID: 55        |                    |  SPID: 57        |
    |  Status: Victim  |                    |  Status: Active  |
    |  (X mark)        |                    |                  |
    +--------+---------+                    +--------+---------+
             |                                       |
             |     Request Mode: X (Exclusive)       |
             |     Owner Mode: X (Exclusive)         |
             |                                       |
    +--------v---------+                    +--------v---------+
    |  Resource Node   |                    |  Resource Node   |
    |  (Rectangle)     |                    |  (Rectangle)     |
    |                  |                    |                  |
    |  ##Employees     |                    |  ##Suppliers     |
    |  KEY lock        |                    |  KEY lock        |
    +------------------+                    +------------------+

Arrows indicate:
- Solid arrow: Process OWNS the lock
- Dashed arrow: Process REQUESTS (waits for) the lock

STEP 4.4: READING DEADLOCK GRAPH DETAILS
----------------------------------------

Click on each node to see details:

Process Node information:
- Server Process ID (SPID)
- Client application name
- Login name
- Host name
- Transaction ID
- Lock mode requested
- Statement being executed
- Input buffer (SQL text)

Resource Node information:
- Database name
- Object name (table)
- Index name
- Lock type (KEY, PAGE, OBJECT)
- Lock mode (S, X, U, etc.)

STEP 4.5: EXTRACT DEADLOCK XML
------------------------------

1. Right-click on Deadlock Graph event
2. Select "Extract Event Data..."
3. Save as: DeadlockGraph.xdl
4. Open in SSMS or text editor

Sample deadlock XML structure:

    <deadlock>
      <victim-list>
        <victimProcess id="process123abc" />
      </victim-list>
      <process-list>
        <process id="process123abc" ... >
          <executionStack>
            <frame ... >UPDATE ##Suppliers SET Fax = ...</frame>
          </executionStack>
          <inputbuf>UPDATE ##Suppliers SET Fax = N'888-4343' WHERE SupplierId = 1</inputbuf>
        </process>
        <process id="process456def" ... >
          <executionStack>
            <frame ... >UPDATE ##Employees SET Phone = ...</frame>
          </executionStack>
          <inputbuf>UPDATE ##Employees SET Phone = N'777-8888' WHERE EmpId = 1</inputbuf>
        </process>
      </process-list>
      <resource-list>
        <keylock ... objectname="##Employees" ... >
          <owner-list>
            <owner id="process123abc" mode="X"/>
          </owner-list>
          <waiter-list>
            <waiter id="process456def" mode="X" requestType="wait"/>
          </waiter-list>
        </keylock>
        <keylock ... objectname="##Suppliers" ... >
          <owner-list>
            <owner id="process456def" mode="X"/>
          </owner-list>
          <waiter-list>
            <waiter id="process123abc" mode="X" requestType="wait"/>
          </waiter-list>
        </keylock>
      </resource-list>
    </deadlock>

---
                    PART 5: DEADLOCK VICTIM SELECTION CRITERIA
---

SQL Server uses the following criteria to select a deadlock victim:

STEP 5.1: PRIMARY CRITERION - DEADLOCK_PRIORITY
-----------------------------------------------

Each session can set its deadlock priority:

    SET DEADLOCK_PRIORITY LOW;    -- More likely to be victim
    SET DEADLOCK_PRIORITY NORMAL; -- Default
    SET DEADLOCK_PRIORITY HIGH;   -- Less likely to be victim

    -- Or use numeric values: -10 to 10
    SET DEADLOCK_PRIORITY -5;     -- Lower = more likely victim

The session with LOWER priority is chosen as victim.

STEP 5.2: SECONDARY CRITERION - TRANSACTION COST
------------------------------------------------

If priorities are equal, SQL Server estimates the "cost" to rollback:

Cost factors:
- Number of log records written (transaction size)
- Amount of work done in the transaction
- Resources held

The session that is CHEAPER TO ROLLBACK is chosen as victim.

"Cheaper to rollback" means:
- Less data modified
- Fewer log records to undo
- Less work lost

STEP 5.3: WHY WAS YOUR VICTIM CHOSEN?
-------------------------------------

In our demo, both sessions had:
- Same deadlock priority (NORMAL)
- Similar transaction size

The victim was likely chosen because:

1. If both sessions have equal priority and cost:
   SQL Server may choose somewhat arbitrarily (internal timing)

2. The session that completed less work:
   - Session 1: Updated 1 row in ##Employees
   - Session 2: Updated 1 row in ##Suppliers
   - Both similar, so cost is approximately equal

3. The session that requested the lock LAST:
   In a tie, the most recent lock requester might be chosen

To verify, check the deadlock XML:
- Look at <victim-list> to see which process was killed
- Compare log_used values in process nodes

STEP 5.4: CONTROLLING VICTIM SELECTION
--------------------------------------

To control which session becomes victim:

Option 1: Set deadlock priority

    -- In the session you want to PROTECT:
    SET DEADLOCK_PRIORITY HIGH;

    -- In the session that CAN be killed:
    SET DEADLOCK_PRIORITY LOW;

Option 2: Keep transactions short
- Shorter transactions = lower rollback cost = more likely victim
- But also = less work lost!

---
                    PART 6: MONITORING LOCKS WITH T-SQL
---

Alternative to Profiler - use DMVs (Dynamic Management Views):

STEP 6.1: VIEW CURRENT LOCKS
----------------------------

    -- View all current locks
    SELECT
        resource_type,
        resource_database_id,
        resource_associated_entity_id,
        request_mode,
        request_type,
        request_status,
        request_session_id
    FROM sys.dm_tran_locks
    WHERE resource_database_id = DB_ID('tempdb');  -- For global temp tables
    GO

STEP 6.2: VIEW BLOCKING SESSIONS
--------------------------------

    -- View blocking chains
    SELECT
        blocking.session_id AS BlockingSessionID,
        blocked.session_id AS BlockedSessionID,
        waitstats.wait_type,
        waitstats.wait_duration_ms,
        blocked.command,
        blocked.status
    FROM sys.dm_exec_sessions AS blocking
    JOIN sys.dm_exec_requests AS blocked ON blocking.session_id = blocked.blocking_session_id
    LEFT JOIN sys.dm_os_waiting_tasks AS waitstats ON blocked.session_id = waitstats.session_id;
    GO

STEP 6.3: ENABLE DEADLOCK TRACE FLAG
------------------------------------

    -- Enable trace flag to write deadlocks to error log
    DBCC TRACEON (1222, -1);  -- Detailed deadlock info to error log
    DBCC TRACEON (1204, -1);  -- Basic deadlock info to error log

    -- View error log
    EXEC xp_readerrorlog 0, 1, 'deadlock';

STEP 6.4: USE EXTENDED EVENTS (MODERN ALTERNATIVE)
--------------------------------------------------

Extended Events is the modern replacement for SQL Trace/Profiler:

    -- Create deadlock monitoring session
    CREATE EVENT SESSION [DeadlockMonitor] ON SERVER
    ADD EVENT sqlserver.xml_deadlock_report
    ADD TARGET package0.event_file(SET filename=N'C:\Traces\Deadlocks.xel')
    WITH (STARTUP_STATE=ON);
    GO

    -- Start the session
    ALTER EVENT SESSION [DeadlockMonitor] ON SERVER STATE = START;
    GO

    -- Read deadlock events
    SELECT
        event_data.value('(event/@timestamp)[1]', 'datetime2') AS deadlock_time,
        event_data.query('(event/data[@name="xml_report"]/value/deadlock)[1]') AS deadlock_graph
    FROM (
        SELECT CAST(event_data AS XML) AS event_data
        FROM sys.fn_xe_file_target_read_file('C:\Traces\Deadlocks*.xel', NULL, NULL, NULL)
    ) AS data;
    GO

---
                    PART 7: PREVENTING DEADLOCKS
---

BEST PRACTICES:
---------------

1. Access objects in the same order
   - If all transactions access Table A before Table B, no deadlock

2. Keep transactions short
   - Less time holding locks = less chance of deadlock

3. Use appropriate isolation levels
   - READ COMMITTED SNAPSHOT avoids many locking issues

4. Avoid user interaction during transactions
   - Don't COMMIT after user input while holding locks

5. Use lock hints wisely
   - NOLOCK, ROWLOCK, TABLOCK have trade-offs

6. Index properly
   - Good indexes reduce lock time

---
                    SUMMARY
---

What We Learned:
---------------

1. SQL Server Profiler Setup:
   - Tools -> SQL Server Profiler
   - Template: TSQL_Locks
   - Events: Deadlock Graph, Lock:Deadlock

2. Deadlock Scenario:
   - Two sessions each holding a lock the other needs
   - Neither can proceed without the other releasing

3. Deadlock Resolution:
   - SQL Server automatically detects deadlocks
   - One session is chosen as "victim" and killed
   - Error 1205 returned to victim

4. Victim Selection:
   - First: DEADLOCK_PRIORITY setting
   - Second: Cost to rollback (less work = victim)

5. Deadlock Graph Analysis:
   - Process nodes: Sessions involved
   - Resource nodes: Locked objects
   - Arrows: Lock ownership and requests
   - X mark: Victim process

Key Points:
-----------
| Deadlock Component | Description |
|--------------------|-------------|
| Cause | Circular lock dependency |
| Detection | Automatic by SQL Server (background) |
| Resolution | One transaction killed (victim) |
| Victim Selection | Priority, then rollback cost |
| Error Code | 1205 |
| Monitoring | Profiler, Extended Events, DMVs |
| Prevention | Consistent access order, short transactions |

Files Created (if saved traces):
-------------------------------
C:\Traces\DeadlockDemo.trc    - Profiler trace file
C:\Traces\DeadlockGraph.xdl   - Extracted deadlock XML
C:\Traces\Deadlocks.xel       - Extended Events file

