# PROCESSES HIERARCHY - ANSWERS

================================================================================
TASK 1: Open terminal of your choosing
================================================================================

Methods:
- Press Ctrl + Alt + T
- Activities > Search "Terminal"
- Right-click on Desktop > Open Terminal

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                          |
|                                                                  |
|  Terminal window showing:                                        |
|  - Empty terminal with command prompt                            |
|  - Username and hostname visible (user@hostname:~$)              |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 2: Create script.sh with sleep 5m content
================================================================================

Method 1: Using cat with heredoc
$ cat > script.sh << 'EOF'
#!/bin/bash

sleep 5m
EOF

Method 2: Using echo
$ echo -e '#!/bin/bash\n\nsleep 5m' > script.sh

Verify:
$ cat script.sh

Expected Output:
#!/bin/bash

sleep 5m

Explanation:
- #!/bin/bash = shebang line, specifies interpreter
- sleep 5m = sleep for 5 minutes (keeps script running)

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                          |
|                                                                  |
|  Terminal showing:                                               |
|  - cat > script.sh << 'EOF' command                              |
|  - Script content typed                                          |
|  - EOF to close                                                  |
|  - cat script.sh command                                         |
|  - Output showing:                                               |
|    #!/bin/bash                                                   |
|                                                                  |
|    sleep 5m                                                      |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 3: Make executable and run in background
================================================================================

Make executable:
$ chmod +x script.sh

Run in background:
$ ./script.sh &

Expected Output:
[1] 12345

Explanation:
- & at the end runs the command in background
- [1] = job number
- 12345 = Process ID (PID) - yours will be different

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                          |
|                                                                  |
|  Terminal showing:                                               |
|  - $ chmod +x script.sh                                          |
|  - $ ./script.sh &                                               |
|  - [1] 12345    <-- job number and PID visible                   |
|  - $            <-- prompt returns immediately                   |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 4: Verify script running with jobs command
================================================================================

Command:
$ jobs

Expected Output:
[1]+  Running                 ./script.sh &

Detailed output:
$ jobs -l

Expected Output:
[1]+ 12345 Running                 ./script.sh &

Explanation:
- [1] = job number
- + = current job (default for fg/bg commands)
- 12345 = PID
- Running = job status
- ./script.sh & = command

Job statuses:
- Running = currently executing
- Stopped = suspended (Ctrl+Z)
- Done = completed

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                          |
|                                                                  |
|  Terminal showing:                                               |
|  - $ jobs                                                        |
|  - [1]+  Running                 ./script.sh &                   |
|  - $ jobs -l                                                     |
|  - [1]+ 12345 Running            ./script.sh &                   |
|        ^^^^^ PID visible here                                    |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 5: Inspect processes with top and ps commands
================================================================================

METHOD 1: Using top (interactive)
---------------------------------
$ top

Expected Output (interactive screen):
top - 10:15:32 up 2 days,  3:22,  1 user,  load average: 0.00, 0.01, 0.05
Tasks: 125 total,   1 running, 124 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.3 us,  0.2 sy,  0.0 ni, 99.5 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :   3936.0 total,   2500.0 free,    800.0 used,    636.0 buff/cache
MiB Swap:   2048.0 total,   2048.0 free,      0.0 used.   2900.0 avail Mem

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
12345 user1     20   0    5500   1200    1100 S   0.0   0.0   0:00.01 script.sh
12346 user1     20   0    5500   1000    900  S   0.0   0.0   0:00.00 sleep

Press 'q' to quit top.

Useful top commands:
- q = quit
- k = kill process (enter PID)
- u = filter by user
- P = sort by CPU
- M = sort by memory
- h = help

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 5a <<<                         |
|                                                                  |
|  top command showing:                                            |
|  - System summary at top (uptime, tasks, CPU, memory)            |
|  - Process list below                                            |
|  - script.sh process visible in the list                         |
|  - sleep process visible (child of script.sh)                    |
|  - PID, USER, COMMAND columns visible                            |
|                                                                  |
+------------------------------------------------------------------+


METHOD 2: Using ps (snapshot)
-----------------------------

Basic ps:
$ ps

Expected Output:
  PID TTY          TIME CMD
11111 pts/0    00:00:00 bash
12345 pts/0    00:00:00 script.sh
12346 pts/0    00:00:00 sleep
12347 pts/0    00:00:00 ps

Full listing:
$ ps -f

Expected Output:
UID        PID  PPID  C STIME TTY          TIME CMD
user1    11111 11110  0 10:00 pts/0    00:00:00 bash
user1    12345 11111  0 10:15 pts/0    00:00:00 /bin/bash ./script.sh
user1    12346 12345  0 10:15 pts/0    00:00:00 sleep 5m
user1    12347 11111  0 10:16 pts/0    00:00:00 ps -f

All processes:
$ ps aux

Expected Output:
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.1 171520 10240 ?        Ss   Jan14   0:05 /usr/lib/systemd
...
user1    12345  0.0  0.0   5500  1200 pts/0    S    10:15   0:00 /bin/bash ./script.sh
user1    12346  0.0  0.0   5500  1000 pts/0    S    10:15   0:00 sleep 5m
...

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 5b <<<                         |
|                                                                  |
|  ps aux command showing:                                         |
|  - $ ps aux | grep script                                        |
|  - user1  12345  0.0  0.0  ... /bin/bash ./script.sh             |
|  - user1  12346  0.0  0.0  ... sleep 5m                          |
|  - Both script.sh and sleep processes visible                    |
|  - USER, PID, COMMAND columns clearly showing                    |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 6: Find the PID of your script
================================================================================

Method 1: From the background output
When you ran ./script.sh &, the PID was shown:
[1] 12345
       ^
       PID

Method 2: Using $! variable (most recent background process)
$ echo $!

Expected Output:
12345

Method 3: Using jobs -l
$ jobs -l

Expected Output:
[1]+ 12345 Running                 ./script.sh &

Method 4: Using pgrep
$ pgrep -f script.sh

Expected Output:
12345

Method 5: Using ps with grep
$ ps aux | grep script.sh

Expected Output:
user1    12345  0.0  0.0   5500  1200 pts/0    S    10:15   0:00 /bin/bash ./script.sh
user1    12400  0.0  0.0   6400   800 pts/0    S+   10:20   0:00 grep script.sh

Method 6: Using pidof
$ pidof -x script.sh

Expected Output:
12345

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                          |
|                                                                  |
|  Terminal showing multiple PID finding methods:                  |
|  - $ echo $!                                                     |
|  - 12345                                                         |
|  - $ pgrep -f script.sh                                          |
|  - 12345                                                         |
|  - $ ps aux | grep script.sh                                     |
|  - user1 12345 ... /bin/bash ./script.sh                         |
|  - All methods showing SAME PID number                           |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 7: Find command and user information about your script
================================================================================

Method 1: Using ps -p with PID
$ ps -p 12345 -o pid,user,cmd

Expected Output:
  PID USER     CMD
12345 user1    /bin/bash ./script.sh

Method 2: Using ps -f with grep
$ ps -f | grep script.sh

Expected Output:
user1    12345 11111  0 10:15 pts/0    00:00:00 /bin/bash ./script.sh

Explanation of columns:
- USER (user1) = user running the process
- PID (12345) = Process ID
- PPID (11111) = Parent Process ID (bash shell)
- C (0) = CPU utilization
- STIME (10:15) = Start time
- TTY (pts/0) = Terminal
- TIME (00:00:00) = CPU time used
- CMD = Command

Method 3: Using ps aux with grep
$ ps aux | grep script.sh

Expected Output:
user1    12345  0.0  0.0   5500  1200 pts/0    S    10:15   0:00 /bin/bash ./script.sh

Explanation of columns:
- USER = user running the process
- PID = Process ID
- %CPU = CPU percentage
- %MEM = Memory percentage
- VSZ = Virtual memory size
- RSS = Resident memory size
- TTY = Terminal
- STAT = Process state (S=sleeping)
- START = Start time
- TIME = CPU time
- COMMAND = Full command

Method 4: Detailed info from /proc
$ cat /proc/12345/status | grep -E "Name|Pid|PPid|Uid"

Expected Output:
Name:   script.sh
Pid:    12345
PPid:   11111
Uid:    1001    1001    1001    1001

$ cat /proc/12345/cmdline | tr '\0' ' '

Expected Output:
/bin/bash ./script.sh

Method 5: Using ps with custom format
$ ps -p 12345 -o pid,ppid,user,uid,gid,stat,start,time,cmd

Expected Output:
  PID  PPID USER      UID   GID STAT  STARTED     TIME CMD
12345 11111 user1    1001  1001 S    10:15:00 00:00:00 /bin/bash ./script.sh

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                          |
|                                                                  |
|  Terminal showing user and command information:                  |
|  - $ ps -p 12345 -o pid,user,cmd                                 |
|  -   PID USER     CMD                                            |
|  - 12345 user1    /bin/bash ./script.sh                          |
|                                                                  |
|  OR                                                              |
|                                                                  |
|  - $ ps -f | grep script.sh                                      |
|  - user1 12345 11111 0 10:15 pts/0 00:00:00 /bin/bash ./script.sh|
|    ^^^^^ ^^^^^ ^^^^^                        ^^^^^^^^^^^^^^^^^^^^  |
|    USER  PID   PPID                         COMMAND               |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
BONUS: Managing background processes
================================================================================

Bring to foreground:
$ fg %1

OR (if only one job):
$ fg

Send back to background:
(Press Ctrl+Z to suspend, then)
$ bg %1

Kill the process:
$ kill 12345

OR:
$ kill %1

Force kill:
$ kill -9 12345

Kill by name:
$ pkill -f script.sh

Wait for background job to complete:
$ wait %1


================================================================================
COMPLETE COMMAND SEQUENCE
================================================================================

# Task 1: Open terminal
# (Use Ctrl+Alt+T)

# Task 2: Create script
cat > script.sh << 'EOF'
#!/bin/bash

sleep 5m
EOF

# Verify script content
cat script.sh

# Task 3: Make executable and run in background
chmod +x script.sh
./script.sh &

# Task 4: Verify with jobs
jobs
jobs -l

# Task 5: Inspect with top and ps
top        # (press q to exit)
ps
ps -f
ps aux

# Task 6: Find PID
echo $!
pgrep -f script.sh
ps aux | grep script.sh

# Task 7: Find command and user info
ps -p $(pgrep -f script.sh) -o pid,user,cmd
ps -f | grep script.sh
ps aux | grep script.sh

# Cleanup: Kill the script
kill $(pgrep -f script.sh)
# OR
kill %1


================================================================================
PROCESS COMMANDS REFERENCE
================================================================================

VIEWING PROCESSES:
+---------------------------+------------------------------------------+
| Command                   | Description                              |
+---------------------------+------------------------------------------+
| ps                        | Show processes in current terminal       |
| ps -f                     | Full format listing                      |
| ps aux                    | All processes, all users                 |
| ps -ef                    | All processes, full format               |
| ps -p PID                 | Show specific process by PID             |
| ps -u username            | Show processes for specific user         |
| top                       | Interactive process viewer               |
| htop                      | Enhanced interactive viewer (if installed)|
| pgrep name                | Find PID by process name                 |
| pidof name                | Find PID of running program              |
+---------------------------+------------------------------------------+

JOB CONTROL:
+---------------------------+------------------------------------------+
| Command                   | Description                              |
+---------------------------+------------------------------------------+
| command &                 | Run command in background                |
| jobs                      | List background jobs                     |
| jobs -l                   | List with PIDs                           |
| fg %n                     | Bring job n to foreground                |
| bg %n                     | Send job n to background                 |
| Ctrl+Z                    | Suspend current process                  |
| Ctrl+C                    | Terminate current process                |
| $!                        | PID of last background process           |
+---------------------------+------------------------------------------+

KILLING PROCESSES:
+---------------------------+------------------------------------------+
| Command                   | Description                              |
+---------------------------+------------------------------------------+
| kill PID                  | Send SIGTERM (graceful stop)             |
| kill -9 PID               | Send SIGKILL (force kill)                |
| kill %n                   | Kill job number n                        |
| pkill name                | Kill by process name                     |
| pkill -f pattern          | Kill by command pattern                  |
| killall name              | Kill all processes with name             |
+---------------------------+------------------------------------------+


================================================================================
PROCESS STATES (STAT column in ps)
================================================================================

+------+------------------------------------------+
| Code | Description                              |
+------+------------------------------------------+
|  R   | Running or runnable                      |
|  S   | Sleeping (interruptible)                 |
|  D   | Uninterruptible sleep (usually I/O)      |
|  T   | Stopped (by signal or debugger)          |
|  Z   | Zombie (terminated but not reaped)       |
|  I   | Idle kernel thread                       |
+------+------------------------------------------+

Additional characters:
+------+------------------------------------------+
|  <   | High priority                            |
|  N   | Low priority                             |
|  L   | Pages locked in memory                   |
|  s   | Session leader                           |
|  l   | Multi-threaded                           |
|  +   | In foreground process group              |
+------+------------------------------------------+


================================================================================
SCREENSHOTS CHECKLIST
================================================================================

Print this checklist and mark each screenshot as you take it:

[ ] SCREENSHOT 1  - Terminal window opened
[ ] SCREENSHOT 2  - Script creation (cat command) and cat output showing content
[ ] SCREENSHOT 3  - chmod +x and ./script.sh & with [1] PID output
[ ] SCREENSHOT 4  - jobs and jobs -l showing Running status and PID
[ ] SCREENSHOT 5a - top command showing script.sh and sleep processes
[ ] SCREENSHOT 5b - ps aux | grep script showing script.sh process
[ ] SCREENSHOT 6  - Multiple PID finding methods (echo $!, pgrep, ps grep)
[ ] SCREENSHOT 7  - ps -p or ps -f showing USER, PID, and COMMAND info

TOTAL: 7-8 Screenshots Required

