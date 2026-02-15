# MEMORY DIAGNOSTICS - ANSWERS

## TASK 1: Open terminal of your choosing

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


## TASK 2: Inspect RAM usage using free command

### METHOD 1: Basic free command

```bash
$ free
```

Output:
```
              total        used        free      shared  buff/cache   available
Mem:        4028436     1245632     1542820       45632     1239984     2489764
Swap:       2097148           0     2097148
```

Explanation:
- Values are in kilobytes (KB) by default
- Mem: Physical RAM
- Swap: Virtual memory on disk


### METHOD 2: Human-readable format (Recommended)

```bash
$ free -h
```

Output:
```
              total        used        free      shared  buff/cache   available
Mem:          3.8Gi       1.2Gi       1.5Gi        44Mi       1.2Gi       2.4Gi
Swap:         2.0Gi          0B       2.0Gi
```

Explanation:
- -h = human-readable (shows GB, MB, KB)
- Gi = Gibibytes (1024-based)
- Mi = Mebibytes

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 2a <<<                         |
|                                                                  |
|  Terminal showing:                                               |
|  - $ free -h                                                     |
|  - Output table with columns:                                    |
|    total, used, free, shared, buff/cache, available              |
|  - Mem row with RAM values                                       |
|  - Swap row with swap values                                     |
|                                                                  |
+------------------------------------------------------------------+


### METHOD 3: Show output in megabytes

```bash
$ free -m
```

Output:
```
              total        used        free      shared  buff/cache   available
Mem:           3934        1216        1506          44        1211        2432
Swap:          2047           0        2047
```

Explanation:
- -m = megabytes
- Easier to read exact values


### METHOD 4: Show output in gigabytes

```bash
$ free -g
```

Output:
```
              total        used        free      shared  buff/cache   available
Mem:              3           1           1           0           1           2
Swap:             1           0           1
```

Explanation:
- -g = gigabytes
- Values are rounded


### METHOD 5: Show totals row

```bash
$ free -h -t
```

Output:
```
              total        used        free      shared  buff/cache   available
Mem:          3.8Gi       1.2Gi       1.5Gi        44Mi       1.2Gi       2.4Gi
Swap:         2.0Gi          0B       2.0Gi
Total:        5.8Gi       1.2Gi       3.5Gi
```

Explanation:
- -t = show total line (RAM + Swap combined)

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 2b <<<                         |
|                                                                  |
|  Terminal showing:                                               |
|  - $ free -h -t                                                  |
|  - Output with Mem, Swap, and Total rows                         |
|  - Total row showing combined RAM + Swap                         |
|                                                                  |
+------------------------------------------------------------------+


### METHOD 6: Continuous monitoring

```bash
$ free -h -s 2
```

Output:
```
(Displays free output every 2 seconds)

              total        used        free      shared  buff/cache   available
Mem:          3.8Gi       1.2Gi       1.5Gi        44Mi       1.2Gi       2.4Gi
Swap:         2.0Gi          0B       2.0Gi

              total        used        free      shared  buff/cache   available
Mem:          3.8Gi       1.3Gi       1.4Gi        44Mi       1.2Gi       2.3Gi
Swap:         2.0Gi          0B       2.0Gi
...
```

Press Ctrl+C to stop.

Explanation:
- -s 2 = repeat every 2 seconds
- Useful for monitoring memory changes over time


### METHOD 7: Show low and high memory (older systems)

```bash
$ free -l
```

(Shows low and high memory statistics if available)


### OTHER USEFUL TOOLS FOR RAM INSPECTION:

**1. Using /proc/meminfo (detailed)**
```bash
$ cat /proc/meminfo
```

**2. Using vmstat**
```bash
$ vmstat
```

**3. Using top (interactive)**
```bash
$ top
```
(Press 'M' to sort by memory usage)

**4. Using htop (if installed)**
```bash
$ htop
```

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 2c <<<                         |
|                                                                  |
|  Terminal showing:                                               |
|  - $ cat /proc/meminfo                                           |
|  - Detailed memory information:                                  |
|    MemTotal, MemFree, MemAvailable, Buffers, Cached, etc.        |
|                                                                  |
+------------------------------------------------------------------+


## TASK 3: Calculate total, free and cached RAM

### UNDERSTANDING FREE COMMAND OUTPUT

```bash
$ free -h
```

```
              total        used        free      shared  buff/cache   available
Mem:          3.8Gi       1.2Gi       1.5Gi        44Mi       1.2Gi       2.4Gi
Swap:         2.0Gi          0B       2.0Gi
```

Column Definitions:

| Column | Description |
|--------|-------------|
| total | Total installed RAM |
| used | Memory currently in use by processes |
| free | Completely unused memory |
| shared | Memory used by tmpfs and shared between processes |
| buff/cache | Memory used for buffers and disk cache |
| available | Memory available for new applications (free + reclaimable) |


### CALCULATING VALUES

From the example output:

**1. TOTAL RAM:**
```bash
$ free -h | grep Mem | awk '{print $2}'
```

Output: 3.8Gi

OR in megabytes:
```bash
$ free -m | grep Mem | awk '{print $2}'
```

Output: 3934

**2. FREE RAM:**
```bash
$ free -h | grep Mem | awk '{print $4}'
```

Output: 1.5Gi

OR in megabytes:
```bash
$ free -m | grep Mem | awk '{print $4}'
```

Output: 1506

**3. CACHED RAM (buff/cache):**
```bash
$ free -h | grep Mem | awk '{print $6}'
```

Output: 1.2Gi

OR in megabytes:
```bash
$ free -m | grep Mem | awk '{print $6}'
```

Output: 1211

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 3a <<<                         |
|                                                                  |
|  Terminal showing extraction of specific values:                 |
|  - $ free -h                                                     |
|  - (full output shown)                                           |
|  - $ free -m | grep Mem | awk '{print "Total:", $2, "MB"}'       |
|  - Total: 3934 MB                                                |
|  - $ free -m | grep Mem | awk '{print "Free:", $4, "MB"}'        |
|  - Free: 1506 MB                                                 |
|  - $ free -m | grep Mem | awk '{print "Cached:", $6, "MB"}'      |
|  - Cached: 1211 MB                                               |
|                                                                  |
+------------------------------------------------------------------+


### ALTERNATIVE: Using /proc/meminfo

```bash
$ grep -E "MemTotal|MemFree|^Cached|Buffers" /proc/meminfo
```

Output:
```
MemTotal:        4028436 kB
MemFree:         1542820 kB
Buffers:          123456 kB
Cached:          1116528 kB
```

Explanation:
- MemTotal = Total RAM
- MemFree = Free RAM
- Cached = Disk cache
- Buffers = Buffer cache

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 3b <<<                         |
|                                                                  |
|  Terminal showing:                                               |
|  - $ grep -E "MemTotal|MemFree|^Cached|Buffers" /proc/meminfo    |
|  - MemTotal:        4028436 kB                                   |
|  - MemFree:         1542820 kB                                   |
|  - Buffers:          123456 kB                                   |
|  - Cached:          1116528 kB                                   |
|                                                                  |
+------------------------------------------------------------------+


### MEMORY SUMMARY SCRIPT

Create a simple script to display memory summary:

```bash
$ echo "=== Memory Summary ===" && \
  free -m | awk 'NR==2 {
    printf "Total RAM:  %d MB (%.2f GB)\n", $2, $2/1024
    printf "Used RAM:   %d MB (%.2f GB)\n", $3, $3/1024
    printf "Free RAM:   %d MB (%.2f GB)\n", $4, $4/1024
    printf "Cached:     %d MB (%.2f GB)\n", $6, $6/1024
    printf "Available:  %d MB (%.2f GB)\n", $7, $7/1024
  }'
```

Output:
```
=== Memory Summary ===
Total RAM:  3934 MB (3.84 GB)
Used RAM:   1216 MB (1.19 GB)
Free RAM:   1506 MB (1.47 GB)
Cached:     1211 MB (1.18 GB)
Available:  2432 MB (2.38 GB)
```

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 3c <<<                         |
|                                                                  |
|  Terminal showing memory summary:                                |
|  - Memory summary output with all values:                        |
|    Total RAM, Used RAM, Free RAM, Cached, Available              |
|  - Values in both MB and GB                                      |
|                                                                  |
+------------------------------------------------------------------+


### UNDERSTANDING AVAILABLE vs FREE

**IMPORTANT CONCEPT:**

- "free" = Memory that is completely unused
- "available" = Memory that can be used by new applications

available = free + (buff/cache that can be reclaimed)

Linux uses "free" memory for disk caching to improve performance.
This cached memory CAN be freed when applications need it.

So "available" is usually a better indicator of usable memory.

Example:
- Free: 1.5 GB
- Buff/cache: 1.2 GB
- Available: 2.4 GB (because most of buff/cache can be freed)


## TASK 4: Find the process that consumes the most RAM

### METHOD 1: Using ps command (sorted by memory)

```bash
$ ps aux --sort=-%mem | head -11
```

Output:
```
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
user1     2345  2.5 15.2 456789 123456 ?       Sl   10:00   1:23 firefox
user1     3456  1.2  8.5 345678  87654 ?       Sl   10:05   0:45 code
root      1234  0.5  3.2 234567  32456 ?       Ssl  09:00   0:30 gnome-shell
...
```

Explanation:
- --sort=-%mem = Sort by memory percentage (descending)
- head -11 = Show header + top 10 processes
- %MEM = Percentage of total RAM
- RSS = Resident Set Size (actual RAM used in KB)

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 4a <<<                         |
|                                                                  |
|  Terminal showing:                                               |
|  - $ ps aux --sort=-%mem | head -11                              |
|  - Header row: USER PID %CPU %MEM VSZ RSS TTY STAT START TIME CMD|
|  - Top processes sorted by memory                                |
|  - First process (after header) is highest memory consumer       |
|  - %MEM and RSS columns clearly visible                          |
|                                                                  |
+------------------------------------------------------------------+


### METHOD 2: Show only process name and memory

```bash
$ ps aux --sort=-%mem | awk 'NR==1 || NR==2 {print $4, $11}'
```

Output:
```
%MEM COMMAND
15.2 firefox
```

This shows ONLY the top memory-consuming process.

Alternative with more details:
```bash
$ ps aux --sort=-%mem | awk 'NR==2 {print "Process:", $11, "- Memory:", $4"%", "- PID:", $2}'
```

Output:
```
Process: firefox - Memory: 15.2% - PID: 2345
```

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 4b <<<                         |
|                                                                  |
|  Terminal showing:                                               |
|  - Command to find top memory process                            |
|  - Output showing:                                               |
|    Process: [name] - Memory: [%] - PID: [number]                 |
|  - Clear identification of the highest RAM consumer              |
|                                                                  |
+------------------------------------------------------------------+


### METHOD 3: Using top command (interactive)

```bash
$ top
```

Then press 'M' (capital M) to sort by memory.

Output (sorted by memory):
```
top - 10:30:00 up 2 days,  5:00,  1 user,  load average: 0.50, 0.45, 0.40
Tasks: 234 total,   1 running, 233 sleeping,   0 stopped,   0 zombie
%Cpu(s):  5.0 us,  2.0 sy,  0.0 ni, 92.0 id,  1.0 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :   3934.0 total,   1506.0 free,   1216.0 used,   1212.0 buff/cache
MiB Swap:   2048.0 total,   2048.0 free,      0.0 used.   2432.0 avail Mem

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
 2345 user1     20   0  456789 123456  45678 S   2.5  15.2   1:23.45 firefox
 3456 user1     20   0  345678  87654  23456 S   1.2   8.5   0:45.67 code
 ...
```

Press 'q' to quit.

Top commands:
- M = Sort by memory
- P = Sort by CPU
- k = Kill process
- q = Quit

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 4c <<<                         |
|                                                                  |
|  top command showing (sorted by memory):                         |
|  - System summary at top (load, tasks, CPU, memory)              |
|  - Process list sorted by %MEM (after pressing 'M')              |
|  - First process in list is highest memory consumer              |
|  - PID, USER, %MEM, COMMAND columns visible                      |
|                                                                  |
+------------------------------------------------------------------+


### METHOD 4: Using top in batch mode (non-interactive)

```bash
$ top -b -o %MEM -n 1 | head -17
```

Output:
```
(Same as interactive but runs once and exits)
```

Explanation:
- -b = Batch mode (for scripting)
- -o %MEM = Order by memory
- -n 1 = Run once
- head -17 = Show header + top 10 processes


### METHOD 5: Using htop (if installed)

```bash
$ htop
```

Then press F6 and select PERCENT_MEM to sort.

Or start sorted by memory:
```bash
$ htop --sort-key PERCENT_MEM
```

Note: htop may need to be installed first:
```bash
$ sudo yum install htop -y   # CentOS/RHEL
$ sudo apt install htop -y    # Ubuntu/Debian
```

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 4d <<<                         |
|                                                                  |
|  htop command showing (if installed):                            |
|  - Colorful process viewer                                       |
|  - Memory and CPU bars at top                                    |
|  - Process list with memory usage                                |
|  - Sorted by memory percentage                                   |
|                                                                  |
+------------------------------------------------------------------+


### METHOD 6: Using smem (detailed memory analysis)

If smem is installed:
```bash
$ smem -rs pss | head -11
```

Shows:
- PSS (Proportional Set Size) - more accurate shared memory calculation
- USS (Unique Set Size) - memory unique to process
- RSS (Resident Set Size) - total physical memory

Install:
```bash
$ sudo yum install smem -y
```


### METHOD 7: One-liner to find top RAM process

```bash
$ ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%mem | head -2
```

Output:
```
  PID  PPID CMD                         %MEM %CPU
 2345  1234 /usr/lib/firefox/firefox    15.2  2.5
```

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 4e <<<                         |
|                                                                  |
|  Terminal showing one-liner result:                              |
|  - $ ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%mem | head -2        |
|  - Header row                                                    |
|  - Single process row showing top memory consumer                |
|  - PID, command, %MEM clearly visible                            |
|                                                                  |
+------------------------------------------------------------------+


### COMPLETE ANALYSIS EXAMPLE

```bash
$ echo "=== TOP 5 RAM-CONSUMING PROCESSES ===" && \
  ps aux --sort=-%mem | awk 'NR==1 || NR<=6 {
    if(NR==1) printf "%-8s %-6s %-6s %-6s %s\n", "USER", "PID", "%MEM", "RSS(MB)", "COMMAND"
    else printf "%-8s %-6s %-6s %-6.0f %s\n", $1, $2, $4, $6/1024, $11
  }'
```

Output:
```
=== TOP 5 RAM-CONSUMING PROCESSES ===
USER     PID    %MEM   RSS(MB) COMMAND
user1    2345   15.2   120     firefox
user1    3456   8.5    85      code
root     1234   3.2    32      gnome-shell
user1    4567   2.8    28      slack
root     789    2.1    21      Xorg
```

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 4f <<<                         |
|                                                                  |
|  Terminal showing complete analysis:                             |
|  - Top 5 RAM-consuming processes                                 |
|  - Formatted table with USER, PID, %MEM, RSS, COMMAND            |
|  - Clear identification of memory usage per process              |
|                                                                  |
+------------------------------------------------------------------+


## COMPLETE COMMAND SEQUENCE

```bash
# Task 1: Open terminal
# (Use Ctrl+Alt+T)

# Task 2: Inspect RAM usage
free -h
free -m
free -h -t
cat /proc/meminfo | head -20

# Task 3: Calculate total, free, and cached RAM
free -m | grep Mem | awk '{print "Total:", $2, "MB"}'
free -m | grep Mem | awk '{print "Free:", $4, "MB"}'
free -m | grep Mem | awk '{print "Cached:", $6, "MB"}'
grep -E "MemTotal|MemFree|^Cached" /proc/meminfo

# Task 4: Find top memory-consuming process
ps aux --sort=-%mem | head -11
ps aux --sort=-%mem | awk 'NR==2 {print "Top Process:", $11, "using", $4"% RAM"}'
top    # (press M to sort by memory, q to quit)
```


## FREE COMMAND OPTIONS REFERENCE

| Option | Description |
|--------|-------------|
| -b | Display in bytes |
| -k | Display in kilobytes (default) |
| -m | Display in megabytes |
| -g | Display in gigabytes |
| -h | Human-readable (auto KB/MB/GB) |
| -t | Show total row (RAM + Swap) |
| -s N | Repeat every N seconds |
| -c N | Repeat N times then exit |
| -l | Show low and high memory stats |
| -w | Wide output (separate buffers/cache) |


## PS COMMAND MEMORY-RELATED OPTIONS

| Field | Description |
|-------|-------------|
| %mem | Percentage of physical memory used |
| rss | Resident Set Size (KB) - actual RAM |
| vsz | Virtual Memory Size (KB) |
| size | Approximate swap space needed |

Sort options:
- --sort=-%mem  : Sort by memory descending
- --sort=%mem   : Sort by memory ascending
- --sort=-rss   : Sort by RSS descending


## SCREENSHOTS CHECKLIST

Print this checklist and mark each screenshot as you take it:

- [ ] SCREENSHOT 1  - Terminal window opened
- [ ] SCREENSHOT 2a - free -h output showing RAM and Swap
- [ ] SCREENSHOT 2b - free -h -t showing total row
- [ ] SCREENSHOT 2c - /proc/meminfo detailed output
- [ ] SCREENSHOT 3a - Extracted Total, Free, Cached values with awk
- [ ] SCREENSHOT 3b - grep /proc/meminfo for specific values
- [ ] SCREENSHOT 3c - Memory summary with calculated values
- [ ] SCREENSHOT 4a - ps aux --sort=-%mem showing top processes
- [ ] SCREENSHOT 4b - Single top memory process identification
- [ ] SCREENSHOT 4c - top command sorted by memory (press M)
- [ ] SCREENSHOT 4d - htop showing memory usage (if installed)
- [ ] SCREENSHOT 4e - One-liner showing top RAM process
- [ ] SCREENSHOT 4f - Complete analysis with top 5 processes

TOTAL: 13 Screenshots Required
