CPU DIAGNOSTICS - ANSWERS
=========================

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
TASK 2: Inspect CPU usage using top, htop, and uptime
================================================================================

METHOD 1: Using top command (interactive)
-----------------------------------------

$ top

Output:
top - 10:30:45 up 2 days,  5:15,  2 users,  load average: 0.52, 0.48, 0.45
Tasks: 245 total,   2 running, 243 sleeping,   0 stopped,   0 zombie
%Cpu(s):  5.2 us,  2.1 sy,  0.0 ni, 91.5 id,  0.8 wa,  0.2 hi,  0.2 si,  0.0 st
MiB Mem :   7936.0 total,   3500.0 free,   2100.0 used,   2336.0 buff/cache
MiB Swap:   2048.0 total,   2048.0 free,      0.0 used.   5500.0 avail Mem

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
 1234 user1     20   0  456000 120000  45000 R  25.0   1.5   5:30.25 firefox
 2345 user1     20   0  890000  85000  35000 S  12.5   1.1   2:15.10 code
 ...

CPU Line Explanation (%Cpu(s)):
- us (user):     CPU time in user mode (applications)
- sy (system):   CPU time in kernel mode (system calls)
- ni (nice):     CPU time for low-priority processes
- id (idle):     CPU time idle
- wa (wait):     CPU time waiting for I/O
- hi (hardware): CPU time for hardware interrupts
- si (software): CPU time for software interrupts
- st (steal):    CPU time stolen by hypervisor (VMs)

Useful top shortcuts:
- 1 = Show individual CPU cores
- P = Sort by CPU usage
- M = Sort by memory usage
- k = Kill a process
- q = Quit

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 2a <<<                         |
|                                                                  |
|  top command showing:                                            |
|  - System uptime and load average at top                         |
|  - Tasks summary (running, sleeping, stopped, zombie)            |
|  - %Cpu(s) line with us, sy, ni, id, wa, hi, si, st values       |
|  - Memory summary                                                |
|  - Process list with PID, USER, %CPU, %MEM, COMMAND              |
|                                                                  |
+------------------------------------------------------------------+


Show individual CPU cores in top:
---------------------------------
While in top, press '1' (number one):

%Cpu0  :  8.5 us,  3.2 sy,  0.0 ni, 87.1 id,  0.8 wa,  0.2 hi,  0.2 si,  0.0 st
%Cpu1  :  4.2 us,  1.8 sy,  0.0 ni, 93.2 id,  0.5 wa,  0.1 hi,  0.2 si,  0.0 st
%Cpu2  :  6.1 us,  2.5 sy,  0.0 ni, 90.5 id,  0.6 wa,  0.2 hi,  0.1 si,  0.0 st
%Cpu3  :  3.8 us,  1.5 sy,  0.0 ni, 94.0 id,  0.4 wa,  0.1 hi,  0.2 si,  0.0 st

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 2b <<<                         |
|                                                                  |
|  top command with individual CPU cores:                          |
|  - Press '1' in top to show per-CPU statistics                   |
|  - Each CPU core shown separately (Cpu0, Cpu1, Cpu2, Cpu3, ...)  |
|  - Usage percentages for each core                               |
|                                                                  |
+------------------------------------------------------------------+


METHOD 2: Using htop command (enhanced interactive)
---------------------------------------------------

$ htop

Note: htop may need installation first:
$ sudo yum install htop -y    # CentOS/RHEL
$ sudo dnf install htop -y    # CentOS 8+/Fedora
$ sudo apt install htop -y    # Ubuntu/Debian

htop Features:
- Colorful CPU bars showing usage per core
- Mouse support
- Easier process navigation
- Tree view of processes (F5)

htop shortcuts:
- F1 = Help
- F2 = Setup
- F3 = Search
- F4 = Filter
- F5 = Tree view
- F6 = Sort by
- F9 = Kill
- F10 = Quit

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 2c <<<                         |
|                                                                  |
|  htop command showing:                                           |
|  - CPU usage bars for each core (colored)                        |
|  - Memory and Swap usage bars                                    |
|  - Tasks, load average, uptime                                   |
|  - Process list with tree view option                            |
|  - Function key shortcuts at bottom                              |
|                                                                  |
+------------------------------------------------------------------+


METHOD 3: Using uptime command
------------------------------

$ uptime

Output:
 10:30:45 up 2 days,  5:15,  2 users,  load average: 0.52, 0.48, 0.45

Explanation:
- 10:30:45 = Current time
- up 2 days, 5:15 = System uptime
- 2 users = Number of logged-in users
- load average: 0.52, 0.48, 0.45 = CPU load averages for 1, 5, 15 minutes

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 2d <<<                         |
|                                                                  |
|  Terminal showing:                                               |
|  - $ uptime                                                      |
|  - Output with time, uptime duration, users, load average        |
|  - Load average values: X.XX, X.XX, X.XX                         |
|                                                                  |
+------------------------------------------------------------------+


METHOD 4: Using mpstat (CPU statistics)
---------------------------------------

$ mpstat

Note: May need installation:
$ sudo yum install sysstat -y

Output:
Linux 5.14.0-162.6.1.el9_1.x86_64    01/15/2024    _x86_64_    (4 CPU)

10:30:45 AM  CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
10:30:45 AM  all    5.20    0.00    2.10    0.80    0.20    0.20    0.00    0.00    0.00   91.50

Per-CPU statistics:
$ mpstat -P ALL

Output:
10:30:45 AM  CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
10:30:45 AM  all    5.20    0.00    2.10    0.80    0.20    0.20    0.00    0.00    0.00   91.50
10:30:45 AM    0    8.50    0.00    3.20    0.80    0.20    0.20    0.00    0.00    0.00   87.10
10:30:45 AM    1    4.20    0.00    1.80    0.50    0.10    0.20    0.00    0.00    0.00   93.20
10:30:45 AM    2    6.10    0.00    2.50    0.60    0.20    0.10    0.00    0.00    0.00   90.50
10:30:45 AM    3    3.80    0.00    1.50    0.40    0.10    0.20    0.00    0.00    0.00   94.00

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 2e <<<                         |
|                                                                  |
|  Terminal showing:                                               |
|  - $ mpstat -P ALL                                               |
|  - CPU statistics for all cores                                  |
|  - %usr, %sys, %iowait, %idle columns visible                    |
|                                                                  |
+------------------------------------------------------------------+


METHOD 5: Using vmstat
----------------------

$ vmstat 1 5

Output (5 samples, 1 second interval):
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 1  0      0 3500000 120000 2200000    0    0    10    25  500  800  5  2 92  1  0
 0  0      0 3498000 120000 2200000    0    0     0    15  480  750  4  2 93  1  0
 ...

CPU columns:
- us = User time
- sy = System time
- id = Idle time
- wa = Wait I/O
- st = Stolen time

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 2f <<<                         |
|                                                                  |
|  Terminal showing:                                               |
|  - $ vmstat 1 5                                                  |
|  - Multiple lines showing CPU usage over time                    |
|  - CPU columns: us, sy, id, wa, st                               |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 3: Find the number of CPU cores in your system
================================================================================

METHOD 1: Using lscpu command (Recommended)
-------------------------------------------

$ lscpu

Output:
Architecture:            x86_64
CPU op-mode(s):          32-bit, 64-bit
Address sizes:           39 bits physical, 48 bits virtual
Byte Order:              Little Endian
CPU(s):                  4
On-line CPU(s) list:     0-3
Vendor ID:               GenuineIntel
Model name:              Intel(R) Core(TM) i5-8250U CPU @ 1.60GHz
CPU family:              6
Model:                   142
Thread(s) per core:      2
Core(s) per socket:      2
Socket(s):               1
...

Key Information:
- CPU(s): 4 = Total logical processors
- Core(s) per socket: 2 = Physical cores per CPU
- Thread(s) per core: 2 = Threads per core (hyperthreading)
- Socket(s): 1 = Number of physical CPUs

Formula: CPU(s) = Socket(s) × Core(s) per socket × Thread(s) per core
Example: 4 = 1 × 2 × 2

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 3a <<<                         |
|                                                                  |
|  Terminal showing:                                               |
|  - $ lscpu                                                       |
|  - Output with CPU information:                                  |
|    CPU(s):              4                                        |
|    Core(s) per socket:  2                                        |
|    Thread(s) per core:  2                                        |
|    Socket(s):           1                                        |
|    Model name:          Intel...                                 |
|                                                                  |
+------------------------------------------------------------------+


METHOD 2: Using /proc/cpuinfo
-----------------------------

Count physical cores:
$ grep -c "^processor" /proc/cpuinfo

Output:
4

OR get detailed info:
$ cat /proc/cpuinfo | grep -E "processor|model name|cpu cores"

Output:
processor       : 0
model name      : Intel(R) Core(TM) i5-8250U CPU @ 1.60GHz
cpu cores       : 2
processor       : 1
model name      : Intel(R) Core(TM) i5-8250U CPU @ 1.60GHz
cpu cores       : 2
...

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 3b <<<                         |
|                                                                  |
|  Terminal showing:                                               |
|  - $ grep -c "^processor" /proc/cpuinfo                          |
|  - Output: 4 (or your number of CPU cores)                       |
|  - $ cat /proc/cpuinfo | grep -E "processor|model name"          |
|  - Processor entries and model names listed                      |
|                                                                  |
+------------------------------------------------------------------+


METHOD 3: Using nproc command
-----------------------------

$ nproc

Output:
4

This shows the number of processing units available.

For all installed processors:
$ nproc --all

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 3c <<<                         |
|                                                                  |
|  Terminal showing:                                               |
|  - $ nproc                                                       |
|  - Output: 4 (or your number of CPUs)                            |
|  - Simple, single number output                                  |
|                                                                  |
+------------------------------------------------------------------+


METHOD 4: Using getconf
-----------------------

$ getconf _NPROCESSORS_ONLN

Output:
4

This shows online (active) processors.

For configured processors:
$ getconf _NPROCESSORS_CONF


METHOD 5: CPU Summary Script
----------------------------

$ echo "=== CPU Summary ===" && \
  echo "Total CPUs: $(nproc)" && \
  echo "Physical Cores: $(lscpu | grep 'Core(s) per socket' | awk '{print $4}')" && \
  echo "Sockets: $(lscpu | grep 'Socket(s)' | awk '{print $2}')" && \
  echo "Threads per Core: $(lscpu | grep 'Thread(s) per core' | awk '{print $4}')" && \
  echo "CPU Model: $(lscpu | grep 'Model name' | cut -d: -f2 | xargs)"

Output:
=== CPU Summary ===
Total CPUs: 4
Physical Cores: 2
Sockets: 1
Threads per Core: 2
CPU Model: Intel(R) Core(TM) i5-8250U CPU @ 1.60GHz

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 3d <<<                         |
|                                                                  |
|  Terminal showing CPU summary:                                   |
|  - Total CPUs, Physical Cores, Sockets                           |
|  - Threads per Core                                              |
|  - CPU Model name                                                |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 4: Find current CPU load
================================================================================

UNDERSTANDING LOAD AVERAGE
--------------------------

Load Average = Average number of processes waiting for CPU time

- 1-minute load: Most recent, can spike quickly
- 5-minute load: Short-term trend
- 15-minute load: Long-term trend

Interpreting Load Average:
- Load = 1.0 on single-core = 100% utilization
- Load = 4.0 on 4-core = 100% utilization
- Load < Number of cores = System is not overloaded
- Load > Number of cores = Processes are waiting for CPU

Example (4 CPU cores):
- Load 0.50 = 12.5% utilization (healthy)
- Load 2.00 = 50% utilization (moderate)
- Load 4.00 = 100% utilization (fully loaded)
- Load 8.00 = 200% - processes waiting (overloaded)


METHOD 1: Using uptime
----------------------

$ uptime

Output:
 10:30:45 up 2 days,  5:15,  2 users,  load average: 0.52, 0.48, 0.45
                                                     ^^^^  ^^^^  ^^^^
                                                     1min  5min  15min

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 4a <<<                         |
|                                                                  |
|  Terminal showing:                                               |
|  - $ uptime                                                      |
|  - Load average values clearly visible                           |
|  - Format: load average: X.XX, X.XX, X.XX                        |
|                                                                  |
+------------------------------------------------------------------+


METHOD 2: Using /proc/loadavg
-----------------------------

$ cat /proc/loadavg

Output:
0.52 0.48 0.45 2/245 12345

Explanation:
- 0.52 = 1-minute load average
- 0.48 = 5-minute load average
- 0.45 = 15-minute load average
- 2/245 = Running processes / Total processes
- 12345 = Last process ID used

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 4b <<<                         |
|                                                                  |
|  Terminal showing:                                               |
|  - $ cat /proc/loadavg                                           |
|  - Output: X.XX X.XX X.XX X/XXX XXXXX                            |
|  - Load averages and process counts                              |
|                                                                  |
+------------------------------------------------------------------+


METHOD 3: Using top (first line)
--------------------------------

$ top -bn1 | head -1

Output:
top - 10:30:45 up 2 days,  5:15,  2 users,  load average: 0.52, 0.48, 0.45

Explanation:
- -b = Batch mode (non-interactive)
- -n1 = Run once
- head -1 = Show only first line


METHOD 4: Using w command
-------------------------

$ w

Output:
 10:30:45 up 2 days,  5:15,  2 users,  load average: 0.52, 0.48, 0.45
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
user1    pts/0    192.168.1.100    09:00    0.00s  0.05s  0.00s w
user1    pts/1    192.168.1.100    09:30    1:00   0.10s  0.05s bash

First line shows same info as uptime.

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 4c <<<                         |
|                                                                  |
|  Terminal showing:                                               |
|  - $ w                                                           |
|  - First line with load average                                  |
|  - User session information below                                |
|                                                                  |
+------------------------------------------------------------------+


METHOD 5: Load with interpretation
----------------------------------

$ echo "CPU Cores: $(nproc)" && \
  echo "Load Average: $(cat /proc/loadavg | awk '{print $1, $2, $3}')" && \
  awk -v cores=$(nproc) '{
    load1=$1; load5=$2; load15=$3
    printf "1-min Load: %.2f (%.0f%% of %d cores)\n", load1, (load1/cores)*100, cores
    printf "5-min Load: %.2f (%.0f%% of %d cores)\n", load5, (load5/cores)*100, cores
    printf "15-min Load: %.2f (%.0f%% of %d cores)\n", load15, (load15/cores)*100, cores
  }' /proc/loadavg

Output:
CPU Cores: 4
Load Average: 0.52 0.48 0.45
1-min Load: 0.52 (13% of 4 cores)
5-min Load: 0.48 (12% of 4 cores)
15-min Load: 0.45 (11% of 4 cores)

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 4d <<<                         |
|                                                                  |
|  Terminal showing load interpretation:                           |
|  - CPU Cores count                                               |
|  - Load averages with percentages                                |
|  - Clear indication of CPU utilization                           |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 5: Find the process that utilizes the most CPU
================================================================================

METHOD 1: Using ps command (sorted by CPU)
------------------------------------------

$ ps aux --sort=-%cpu | head -11

Output:
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
user1     1234 25.0  1.5 456000 120000 ?       R    10:00   5:30 firefox
user1     2345 12.5  1.1 890000  85000 ?       S    10:05   2:15 code
root      3456  5.2  0.5 234000  40000 ?       Ssl  09:00   1:00 gnome-shell
...

Explanation:
- --sort=-%cpu = Sort by CPU percentage (descending)
- head -11 = Show header + top 10 processes
- %CPU = CPU utilization percentage

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 5a <<<                         |
|                                                                  |
|  Terminal showing:                                               |
|  - $ ps aux --sort=-%cpu | head -11                              |
|  - Header row with columns                                       |
|  - Top processes sorted by CPU usage                             |
|  - First process after header is highest CPU consumer            |
|  - %CPU column clearly visible                                   |
|                                                                  |
+------------------------------------------------------------------+


METHOD 2: Show only top CPU process
-----------------------------------

$ ps aux --sort=-%cpu | awk 'NR==2 {print "Process:", $11, "- CPU:", $3"%", "- PID:", $2}'

Output:
Process: firefox - CPU: 25.0% - PID: 1234

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 5b <<<                         |
|                                                                  |
|  Terminal showing:                                               |
|  - Command to find single top CPU process                        |
|  - Output: Process: [name] - CPU: [%] - PID: [number]            |
|  - Clear identification of highest CPU consumer                  |
|                                                                  |
+------------------------------------------------------------------+


METHOD 3: Using top (sorted by CPU - default)
---------------------------------------------

$ top

Top displays processes sorted by CPU by default.
Press 'P' to re-sort by CPU if needed.

The first process in the list uses most CPU.

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 5c <<<                         |
|                                                                  |
|  top command showing:                                            |
|  - Process list sorted by CPU (default)                          |
|  - First process is highest CPU consumer                         |
|  - %CPU column visible                                           |
|  - PID and COMMAND columns visible                               |
|                                                                  |
+------------------------------------------------------------------+


METHOD 4: Using top in batch mode
---------------------------------

$ top -bn1 -o %CPU | head -17

Output:
top - 10:30:45 up 2 days,  5:15,  2 users,  load average: 0.52, 0.48, 0.45
...
  PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
 1234 user1     20   0  456000 120000  45000 R  25.0   1.5   5:30.25 firefox
 2345 user1     20   0  890000  85000  35000 S  12.5   1.1   2:15.10 code
...

Explanation:
- -b = Batch mode
- -n1 = Run once
- -o %CPU = Order by CPU


METHOD 5: Using htop (sorted by CPU)
------------------------------------

$ htop

Then press F6 and select PERCENT_CPU, or start with:
$ htop --sort-key PERCENT_CPU

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 5d <<<                         |
|                                                                  |
|  htop command showing:                                           |
|  - Processes sorted by CPU usage                                 |
|  - CPU% column highlighted                                       |
|  - First process is highest CPU consumer                         |
|  - Colored bars showing CPU usage                                |
|                                                                  |
+------------------------------------------------------------------+


METHOD 6: Using pidstat (per-process CPU)
-----------------------------------------

$ pidstat 1 3

Output (3 samples, 1 second interval):
Linux 5.14.0-162.6.1.el9_1.x86_64    01/15/2024    _x86_64_    (4 CPU)

10:30:45 AM   UID       PID    %usr %system  %guest   %wait    %CPU   CPU  Command
10:30:46 AM  1000      1234   20.00    5.00    0.00    0.00   25.00     0  firefox
10:30:46 AM  1000      2345   10.00    2.50    0.00    0.00   12.50     1  code
...

Note: Requires sysstat package:
$ sudo yum install sysstat -y


METHOD 7: Complete CPU Analysis
-------------------------------

$ echo "=== TOP 5 CPU-CONSUMING PROCESSES ===" && \
  ps aux --sort=-%cpu | awk 'NR==1 || NR<=6 {
    if(NR==1) printf "%-8s %-6s %-6s %-8s %s\n", "USER", "PID", "%CPU", "TIME", "COMMAND"
    else printf "%-8s %-6s %-6s %-8s %s\n", $1, $2, $3, $10, $11
  }'

Output:
=== TOP 5 CPU-CONSUMING PROCESSES ===
USER     PID    %CPU   TIME     COMMAND
user1    1234   25.0   5:30     firefox
user1    2345   12.5   2:15     code
root     3456   5.2    1:00     gnome-shell
user1    4567   3.8    0:45     slack
root     789    2.1    0:30     Xorg

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 5e <<<                         |
|                                                                  |
|  Terminal showing complete analysis:                             |
|  - Top 5 CPU-consuming processes                                 |
|  - Formatted table with USER, PID, %CPU, TIME, COMMAND           |
|  - Clear ranking of CPU usage                                    |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
COMPLETE COMMAND SEQUENCE
================================================================================

# Task 1: Open terminal
# (Use Ctrl+Alt+T)

# Task 2: Inspect CPU usage
top              # Interactive (press 1 for per-core, q to quit)
htop             # Enhanced interactive (if installed)
uptime           # Quick load average
mpstat           # CPU statistics
vmstat 1 5       # Virtual memory and CPU stats

# Task 3: Find number of CPU cores
lscpu
nproc
grep -c "^processor" /proc/cpuinfo

# Task 4: Find current CPU load
uptime
cat /proc/loadavg
w

# Task 5: Find top CPU process
ps aux --sort=-%cpu | head -11
ps aux --sort=-%cpu | awk 'NR==2 {print $11, $3"%"}'
top              # Press P to sort by CPU


================================================================================
CPU COMMANDS REFERENCE
================================================================================

VIEWING CPU INFO:
+---------------------------+------------------------------------------+
| Command                   | Description                              |
+---------------------------+------------------------------------------+
| lscpu                     | Display CPU architecture information     |
| nproc                     | Print number of processing units         |
| cat /proc/cpuinfo         | Detailed CPU information                 |
| getconf _NPROCESSORS_ONLN | Number of online processors              |
+---------------------------+------------------------------------------+

MONITORING CPU USAGE:
+---------------------------+------------------------------------------+
| Command                   | Description                              |
+---------------------------+------------------------------------------+
| top                       | Interactive process viewer               |
| htop                      | Enhanced interactive viewer              |
| uptime                    | System uptime and load averages          |
| w                         | Who is logged in and load averages       |
| mpstat                    | CPU statistics (sysstat package)         |
| vmstat                    | Virtual memory and CPU statistics        |
| pidstat                   | Per-process CPU usage (sysstat)          |
| sar                       | System activity reporter (sysstat)       |
+---------------------------+------------------------------------------+

PROCESS SORTING:
+---------------------------+------------------------------------------+
| Command                   | Description                              |
+---------------------------+------------------------------------------+
| ps aux --sort=-%cpu       | Sort by CPU descending                   |
| ps aux --sort=%cpu        | Sort by CPU ascending                    |
| top + P key               | Sort by CPU in top                       |
| htop + F6                 | Choose sort column in htop               |
+---------------------------+------------------------------------------+


================================================================================
LOAD AVERAGE INTERPRETATION
================================================================================

For a system with N CPU cores:

+------------------+------------------------------------------+
| Load Average     | Interpretation                           |
+------------------+------------------------------------------+
| < N × 0.7        | Healthy, plenty of capacity              |
| N × 0.7 to N     | Moderate load, acceptable                |
| N to N × 1.5     | High load, may need attention            |
| > N × 1.5        | Overloaded, processes are waiting        |
+------------------+------------------------------------------+

Example with 4 CPU cores:
+------------------+------------------------------------------+
| Load Average     | Status                                   |
+------------------+------------------------------------------+
| < 2.8            | Healthy                                  |
| 2.8 - 4.0        | Moderate                                 |
| 4.0 - 6.0        | High                                     |
| > 6.0            | Overloaded                               |
+------------------+------------------------------------------+


================================================================================
SCREENSHOTS CHECKLIST
================================================================================

Print this checklist and mark each screenshot as you take it:

[ ] SCREENSHOT 1  - Terminal window opened
[ ] SCREENSHOT 2a - top command with CPU line visible
[ ] SCREENSHOT 2b - top with individual CPU cores (press 1)
[ ] SCREENSHOT 2c - htop with colored CPU bars
[ ] SCREENSHOT 2d - uptime command output
[ ] SCREENSHOT 2e - mpstat -P ALL showing per-CPU stats
[ ] SCREENSHOT 2f - vmstat showing CPU columns
[ ] SCREENSHOT 3a - lscpu showing CPU information
[ ] SCREENSHOT 3b - /proc/cpuinfo with processor count
[ ] SCREENSHOT 3c - nproc showing number of CPUs
[ ] SCREENSHOT 3d - CPU summary script output
[ ] SCREENSHOT 4a - uptime with load average
[ ] SCREENSHOT 4b - /proc/loadavg output
[ ] SCREENSHOT 4c - w command showing load average
[ ] SCREENSHOT 4d - Load interpretation with percentages
[ ] SCREENSHOT 5a - ps aux sorted by CPU
[ ] SCREENSHOT 5b - Single top CPU process identification
[ ] SCREENSHOT 5c - top sorted by CPU
[ ] SCREENSHOT 5d - htop sorted by CPU
[ ] SCREENSHOT 5e - Top 5 CPU processes analysis

TOTAL: 20 Screenshots Required
