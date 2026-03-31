# PROCESSES HIERARCHY - ANSWERS

## TASK 1: Open terminal of your choosing

Methods:
- Press Ctrl + Alt + T
- Activities > Search "Terminal"
- Right-click on Desktop > Open Terminal

**Screenshot 1:** Terminal window opened

![Terminal window opened](../../../docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/7_Processes%20Hierarchy/Terminal%20window%20opened.png)


## TASK 2: Create script.sh with sleep 5m content

Method 1: Using cat with heredoc
```bash
$ cat > script.sh << 'EOF'
#!/bin/bash

sleep 5m
EOF
```

Method 2: Using echo
```bash
$ echo -e '#!/bin/bash\n\nsleep 5m' > script.sh
```

Verify:
```bash
$ cat script.sh
```

Expected Output:
```
#!/bin/bash

sleep 5m
```

Explanation:
- `#!/bin/bash` = shebang line, specifies interpreter
- `sleep 5m` = sleep for 5 minutes (keeps script running)

**Screenshot 2:** Script creation and verification

![Script creation](../../../docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/7_Processes%20Hierarchy/Script%20creation%20(cat%20command)%20and%20cat%20output%20showing%20content.png)


## TASK 3: Make executable and run in background

Make executable:
```bash
$ chmod +x script.sh
```

Run in background:
```bash
$ ./script.sh &
```

Expected Output:
```
[1] 12345
```

Explanation:
- `&` at the end runs the command in background
- `[1]` = job number
- `12345` = Process ID (PID) - yours will be different

**Screenshot 3:** chmod +x and background execution

![chmod and background run](../../../docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/7_Processes%20Hierarchy/chmod%20+x%20and%20.-script.sh%20%26%20with%20%5B1%5D%20PID%20output.png)


## TASK 4: Verify script running with jobs command

Command:
```bash
$ jobs
```

Expected Output:
```
[1]+  Running                 ./script.sh &
```

Detailed output:
```bash
$ jobs -l
```

Expected Output:
```
[1]+ 12345 Running                 ./script.sh &
```

Explanation:
- `[1]` = job number
- `+` = current job (default for fg/bg commands)
- `12345` = PID
- `Running` = job status
- `./script.sh &` = command

Job statuses:
- Running = currently executing
- Stopped = suspended (Ctrl+Z)
- Done = completed

**Screenshot 4:** jobs command output

![jobs and jobs -l](../../../docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/7_Processes%20Hierarchy/jobs%20and%20jobs%20-l%20showing%20Running%20status%20and%20PID.png)


## TASK 5: Inspect processes with top and ps commands

### METHOD 1: Using top (interactive)

```bash
$ top
```

Expected Output (interactive screen):
```
top - 10:15:32 up 2 days,  3:22,  1 user,  load average: 0.00, 0.01, 0.05
Tasks: 125 total,   1 running, 124 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.3 us,  0.2 sy,  0.0 ni, 99.5 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :   3936.0 total,   2500.0 free,    800.0 used,    636.0 buff/cache
MiB Swap:   2048.0 total,   2048.0 free,      0.0 used.   2900.0 avail Mem

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
12345 user1     20   0    5500   1200    1100 S   0.0   0.0   0:00.01 script.sh
12346 user1     20   0    5500   1000    900  S   0.0   0.0   0:00.00 sleep
```

Press 'q' to quit top.

Useful top commands:
- q = quit
- k = kill process (enter PID)
- u = filter by user
- P = sort by CPU
- M = sort by memory
- h = help

**Screenshot 5a:** top command showing processes

![top command](../../../docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/7_Processes%20Hierarchy/top%20command%20showing%20script.sh%20and%20sleep%20processes.png)


### METHOD 2: Using ps (snapshot)

Basic ps:
```bash
$ ps
```

Expected Output:
```
  PID TTY          TIME CMD
11111 pts/0    00:00:00 bash
12345 pts/0    00:00:00 script.sh
12346 pts/0    00:00:00 sleep
12347 pts/0    00:00:00 ps
```

Full listing:
```bash
$ ps -f
```

Expected Output:
```
UID        PID  PPID  C STIME TTY          TIME CMD
user1    11111 11110  0 10:00 pts/0    00:00:00 bash
user1    12345 11111  0 10:15 pts/0    00:00:00 /bin/bash ./script.sh
user1    12346 12345  0 10:15 pts/0    00:00:00 sleep 5m
user1    12347 11111  0 10:16 pts/0    00:00:00 ps -f
```

All processes:
```bash
$ ps aux
```

Expected Output:
```
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.1 171520 10240 ?        Ss   Jan14   0:05 /usr/lib/systemd
...
user1    12345  0.0  0.0   5500  1200 pts/0    S    10:15   0:00 /bin/bash ./script.sh
user1    12346  0.0  0.0   5500  1000 pts/0    S    10:15   0:00 sleep 5m
...
```

**Screenshot 5b:** ps aux | grep script output

![ps aux grep](../../../docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/7_Processes%20Hierarchy/ps%20aux%20,%20grep%20script%20showing%20script.sh%20process.png)


## TASK 6: Find the PID of your script

Method 1: From the background output
When you ran `./script.sh &`, the PID was shown:
```
[1] 12345
       ^
       PID
```

Method 2: Using $! variable (most recent background process)
```bash
$ echo $!
```

Expected Output:
```
12345
```

Method 3: Using jobs -l
```bash
$ jobs -l
```

Expected Output:
```
[1]+ 12345 Running                 ./script.sh &
```

Method 4: Using pgrep
```bash
$ pgrep -f script.sh
```

Expected Output:
```
12345
```

Method 5: Using ps with grep
```bash
$ ps aux | grep script.sh
```

Expected Output:
```
user1    12345  0.0  0.0   5500  1200 pts/0    S    10:15   0:00 /bin/bash ./script.sh
user1    12400  0.0  0.0   6400   800 pts/0    S+   10:20   0:00 grep script.sh
```

Method 6: Using pidof
```bash
$ pidof -x script.sh
```

Expected Output:
```
12345
```

**Screenshot 6:** Multiple PID finding methods

![PID finding methods](../../../docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/7_Processes%20Hierarchy/Multiple%20PID%20finding%20methods%20(echo%20%24!,%20pgrep,%20ps%20grep).png)


## TASK 7: Find command and user information about your script

Method 1: Using ps -p with PID
```bash
$ ps -p 12345 -o pid,user,cmd
```

Expected Output:
```
  PID USER     CMD
12345 user1    /bin/bash ./script.sh
```

Method 2: Using ps -f with grep
```bash
$ ps -f | grep script.sh
```

Expected Output:
```
user1    12345 11111  0 10:15 pts/0    00:00:00 /bin/bash ./script.sh
```

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
```bash
$ ps aux | grep script.sh
```

Expected Output:
```
user1    12345  0.0  0.0   5500  1200 pts/0    S    10:15   0:00 /bin/bash ./script.sh
```

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
```bash
$ cat /proc/12345/status | grep -E "Name|Pid|PPid|Uid"
```

Expected Output:
```
Name:   script.sh
Pid:    12345
PPid:   11111
Uid:    1001    1001    1001    1001
```

```bash
$ cat /proc/12345/cmdline | tr '\0' ' '
```

Expected Output:
```
/bin/bash ./script.sh
```

Method 5: Using ps with custom format
```bash
$ ps -p 12345 -o pid,ppid,user,uid,gid,stat,start,time,cmd
```

Expected Output:
```
  PID  PPID USER      UID   GID STAT  STARTED     TIME CMD
12345 11111 user1    1001  1001 S    10:15:00 00:00:00 /bin/bash ./script.sh
```

**Screenshot 7:** ps showing USER, PID, and COMMAND info

![ps user command info](../../../docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/7_Processes%20Hierarchy/ps%20-p%20or%20ps%20-f%20showing%20USER,%20PID,%20and%20COMMAND%20info.png)


## BONUS: Managing background processes

Bring to foreground:
```bash
$ fg %1
```

OR (if only one job):
```bash
$ fg
```

Send back to background:
(Press Ctrl+Z to suspend, then)
```bash
$ bg %1
```

Kill the process:
```bash
$ kill 12345
```

OR:
```bash
$ kill %1
```

Force kill:
```bash
$ kill -9 12345
```

Kill by name:
```bash
$ pkill -f script.sh
```

Wait for background job to complete:
```bash
$ wait %1
```


## COMPLETE COMMAND SEQUENCE

```bash
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
```


## PROCESS COMMANDS REFERENCE

### VIEWING PROCESSES:

| Command                   | Description                              |
|---------------------------|------------------------------------------|
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

### JOB CONTROL:

| Command                   | Description                              |
|---------------------------|------------------------------------------|
| command &                 | Run command in background                |
| jobs                      | List background jobs                     |
| jobs -l                   | List with PIDs                           |
| fg %n                     | Bring job n to foreground                |
| bg %n                     | Send job n to background                 |
| Ctrl+Z                    | Suspend current process                  |
| Ctrl+C                    | Terminate current process                |
| $!                        | PID of last background process           |

### KILLING PROCESSES:

| Command                   | Description                              |
|---------------------------|------------------------------------------|
| kill PID                  | Send SIGTERM (graceful stop)             |
| kill -9 PID               | Send SIGKILL (force kill)                |
| kill %n                   | Kill job number n                        |
| pkill name                | Kill by process name                     |
| pkill -f pattern          | Kill by command pattern                  |
| killall name              | Kill all processes with name             |


## PROCESS STATES (STAT column in ps)

| Code | Description                              |
|------|------------------------------------------|
|  R   | Running or runnable                      |
|  S   | Sleeping (interruptible)                 |
|  D   | Uninterruptible sleep (usually I/O)      |
|  T   | Stopped (by signal or debugger)          |
|  Z   | Zombie (terminated but not reaped)       |
|  I   | Idle kernel thread                       |

Additional characters:

| Code | Description                              |
|------|------------------------------------------|
|  <   | High priority                            |
|  N   | Low priority                             |
|  L   | Pages locked in memory                   |
|  s   | Session leader                           |
|  l   | Multi-threaded                           |
|  +   | In foreground process group              |
