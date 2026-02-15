# USING JOURNALCTL - ANSWERS

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


## TASK 2: Install nginx service

### STEP 1: Install EPEL repository

```bash
$ sudo yum install epel-release -y
```

Output:
```
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
...
Resolving Dependencies
--> Running transaction check
---> Package epel-release.noarch 0:7-14 will be installed
...
Installed:
  epel-release.noarch 0:7-14

Complete!
```

Explanation:
- EPEL = Extra Packages for Enterprise Linux
- nginx is not in default CentOS repositories
- EPEL provides additional packages including nginx

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 2a <<<                         |
|                                                                  |
|  Terminal showing:                                               |
|  - $ sudo yum install epel-release -y                            |
|  - Installation progress                                         |
|  - "Complete!" message                                           |
|                                                                  |
+------------------------------------------------------------------+


### STEP 2: Install nginx

```bash
$ sudo yum install nginx -y
```

Output:
```
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
...
Resolving Dependencies
--> Running transaction check
---> Package nginx.x86_64 1:1.20.1-10.el7 will be installed
...
Installed:
  nginx.x86_64 1:1.20.1-10.el7

Dependency Installed:
  nginx-filesystem.noarch 1:1.20.1-10.el7
  ...

Complete!
```

Verify installation:
```bash
$ nginx -v
```

Output:
```
nginx version: nginx/1.20.1
```

```bash
$ rpm -qa | grep nginx
```

Output:
```
nginx-1.20.1-10.el7.x86_64
nginx-filesystem-1.20.1-10.el7.noarch
```

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 2b <<<                         |
|                                                                  |
|  Terminal showing:                                               |
|  - $ sudo yum install nginx -y                                   |
|  - Installation progress                                         |
|  - "Complete!" message                                           |
|  - $ nginx -v                                                    |
|  - nginx version: nginx/1.20.1                                   |
|                                                                  |
+------------------------------------------------------------------+


## TASK 3: Start nginx service

Command:
```bash
$ sudo systemctl start nginx
```

Verify:
```bash
$ systemctl status nginx
```

Output:
```
● nginx.service - The nginx HTTP and reverse proxy server
   Loaded: loaded (/usr/lib/systemd/system/nginx.service; disabled; vendor preset: disabled)
   Active: active (running) since Tue 2024-01-15 10:00:00 UTC; 5s ago
  Process: 1234 ExecStart=/usr/sbin/nginx (code=exited, status=0/SUCCESS)
  Process: 1233 ExecStartPre=/usr/sbin/nginx -t (code=exited, status=0/SUCCESS)
  Process: 1232 ExecStartPre=/usr/bin/rm -f /run/nginx.pid (code=exited, status=0/SUCCESS)
 Main PID: 1235 (nginx)
   CGroup: /system.slice/nginx.service
           ├─1235 nginx: master process /usr/sbin/nginx
           └─1236 nginx: worker process

Jan 15 10:00:00 hostname systemd[1]: Starting The nginx HTTP and reverse proxy server...
Jan 15 10:00:00 hostname nginx[1233]: nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
Jan 15 10:00:00 hostname nginx[1233]: nginx: configuration file /etc/nginx/nginx.conf test is successful
Jan 15 10:00:00 hostname systemd[1]: Started The nginx HTTP and reverse proxy server.
```

Alternative verification:
```bash
$ systemctl is-active nginx
```

Output:
```
active
```

```bash
$ curl http://localhost
```

Output:
```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
...
```

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                          |
|                                                                  |
|  Terminal showing:                                               |
|  - $ sudo systemctl start nginx                                  |
|  - $ systemctl status nginx                                      |
|  - ● nginx.service - The nginx HTTP and reverse proxy server     |
|  - Active: active (running) since ...                            |
|  - Log entries showing successful start                          |
|  - "configuration file ... syntax is ok"                         |
|  - "Started The nginx HTTP and reverse proxy server"             |
|                                                                  |
+------------------------------------------------------------------+


## TASK 4: Stop nginx service

Command:
```bash
$ sudo systemctl stop nginx
```

Verify:
```bash
$ systemctl status nginx
```

Output:
```
● nginx.service - The nginx HTTP and reverse proxy server
   Loaded: loaded (/usr/lib/systemd/system/nginx.service; disabled; vendor preset: disabled)
   Active: inactive (dead)
                    ^^^^^^^^^^^^^^
                    Service is stopped

Jan 15 10:00:00 hostname systemd[1]: Starting The nginx HTTP and reverse proxy server...
Jan 15 10:00:00 hostname systemd[1]: Started The nginx HTTP and reverse proxy server.
Jan 15 10:05:00 hostname systemd[1]: Stopping The nginx HTTP and reverse proxy server...
Jan 15 10:05:00 hostname systemd[1]: Stopped The nginx HTTP and reverse proxy server.
```

Alternative verification:
```bash
$ systemctl is-active nginx
```

Output:
```
inactive
```

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                          |
|                                                                  |
|  Terminal showing:                                               |
|  - $ sudo systemctl stop nginx                                   |
|  - $ systemctl status nginx                                      |
|  - Active: inactive (dead)                                       |
|  - Log entries showing "Stopped The nginx..."                    |
|                                                                  |
+------------------------------------------------------------------+


## TASK 5: Check nginx service logs

### METHOD 1: Using journalctl with -u flag (unit)

```bash
$ journalctl -u nginx
```

Output:
```
-- Logs begin at Mon 2024-01-01 00:00:00 UTC, end at Tue 2024-01-15 10:10:00 UTC. --
Jan 15 10:00:00 hostname systemd[1]: Starting The nginx HTTP and reverse proxy server...
Jan 15 10:00:00 hostname nginx[1233]: nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
Jan 15 10:00:00 hostname nginx[1233]: nginx: configuration file /etc/nginx/nginx.conf test is successful
Jan 15 10:00:00 hostname systemd[1]: Started The nginx HTTP and reverse proxy server.
Jan 15 10:05:00 hostname systemd[1]: Stopping The nginx HTTP and reverse proxy server...
Jan 15 10:05:00 hostname systemd[1]: Stopped The nginx HTTP and reverse proxy server.
```

Explanation:
- `-u nginx` = Show logs only for nginx unit
- Shows all logs from beginning of journal

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 5a <<<                         |
|                                                                  |
|  Terminal showing:                                               |
|  - $ journalctl -u nginx                                         |
|  - Log entries for nginx service                                 |
|  - Start and stop events visible                                 |
|  - Configuration test messages                                   |
|                                                                  |
+------------------------------------------------------------------+


### METHOD 2: Show recent logs with -n flag

```bash
$ journalctl -u nginx -n 20
```

Output:
```
-- Logs begin at Mon 2024-01-01 00:00:00 UTC, end at Tue 2024-01-15 10:10:00 UTC. --
(Last 20 lines of nginx logs)
```

Explanation:
- `-n 20` = Show last 20 log entries


### METHOD 3: Follow logs in real-time with -f flag

```bash
$ journalctl -u nginx -f
```

Output:
```
(Continuously displays new log entries as they appear)
(Press Ctrl+C to stop)
```

Explanation:
- `-f` = Follow mode (like tail -f)
- Useful for real-time monitoring


### METHOD 4: Show logs since specific time

```bash
$ journalctl -u nginx --since "1 hour ago"

$ journalctl -u nginx --since "2024-01-15 10:00:00"

$ journalctl -u nginx --since today
```

Explanation:
- `--since` = Show logs from specified time
- Accepts various time formats


### METHOD 5: Show logs in specific time range

```bash
$ journalctl -u nginx --since "2024-01-15 09:00:00" --until "2024-01-15 11:00:00"
```


### METHOD 6: Show logs from current boot only

```bash
$ journalctl -u nginx -b
```

Explanation:
- `-b` = Current boot only
- `-b -1` = Previous boot


### METHOD 7: Show logs with extra details

```bash
$ journalctl -u nginx -o verbose
```

Output:
```
Tue 2024-01-15 10:00:00.123456 UTC [s=abc123;i=1234;b=xyz789;...]
    _TRANSPORT=journal
    _PID=1233
    _UID=0
    _GID=0
    _COMM=nginx
    _EXE=/usr/sbin/nginx
    _CMDLINE=nginx: master process /usr/sbin/nginx
    MESSAGE=nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
    ...
```

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 5b <<<                         |
|                                                                  |
|  Terminal showing:                                               |
|  - $ journalctl -u nginx -n 10                                   |
|  - OR $ journalctl -u nginx --since "1 hour ago"                 |
|  - Recent nginx log entries                                      |
|  - Timestamps and messages visible                               |
|                                                                  |
+------------------------------------------------------------------+


## TASK 6: Introduce error by moving nginx config file

Command:
```bash
$ sudo mv /etc/nginx/nginx.conf ./
```

Explanation:
- Moves nginx.conf from /etc/nginx/ to current directory (./)
- nginx will fail to start without its configuration file

Verify config is moved:
```bash
$ ls -la /etc/nginx/nginx.conf
```

Output:
```
ls: cannot access '/etc/nginx/nginx.conf': No such file or directory
```

```bash
$ ls -la ./nginx.conf
```

Output:
```
-rw-r--r--. 1 root root 2469 Jan 15 10:00 ./nginx.conf
```

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                          |
|                                                                  |
|  Terminal showing:                                               |
|  - $ sudo mv /etc/nginx/nginx.conf ./                            |
|  - $ ls -la /etc/nginx/nginx.conf                                |
|  - ls: cannot access ... No such file or directory               |
|  - $ ls -la ./nginx.conf                                         |
|  - File exists in current directory                              |
|                                                                  |
+------------------------------------------------------------------+


## TASK 7: Attempt to start nginx service

Command:
```bash
$ sudo systemctl start nginx
```

Output:
```
Job for nginx.service failed because the control process exited with error code.
See "systemctl status nginx.service" and "journalctl -xe" for details.
```

Check status:
```bash
$ systemctl status nginx
```

Output:
```
● nginx.service - The nginx HTTP and reverse proxy server
   Loaded: loaded (/usr/lib/systemd/system/nginx.service; disabled; vendor preset: disabled)
   Active: failed (Result: exit-code) since Tue 2024-01-15 10:15:00 UTC; 5s ago
  Process: 2345 ExecStartPre=/usr/sbin/nginx -t (code=exited, status=1/FAILURE)

Jan 15 10:15:00 hostname systemd[1]: Starting The nginx HTTP and reverse proxy server...
Jan 15 10:15:00 hostname nginx[2345]: nginx: [emerg] open() "/etc/nginx/nginx.conf" failed (2: No such file or directory)
Jan 15 10:15:00 hostname nginx[2345]: nginx: configuration file /etc/nginx/nginx.conf test failed
Jan 15 10:15:00 hostname systemd[1]: nginx.service: control process exited, code=exited status=1
Jan 15 10:15:00 hostname systemd[1]: Failed to start The nginx HTTP and reverse proxy server.
Jan 15 10:15:00 hostname systemd[1]: Unit nginx.service entered failed state.
Jan 15 10:15:00 hostname systemd[1]: nginx.service failed.
```

Important observations:
- Active: failed (Result: exit-code)
- nginx: [emerg] open() "/etc/nginx/nginx.conf" failed (2: No such file or directory)
- nginx: configuration file /etc/nginx/nginx.conf test failed

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                          |
|                                                                  |
|  Terminal showing:                                               |
|  - $ sudo systemctl start nginx                                  |
|  - "Job for nginx.service failed..." message                     |
|  - $ systemctl status nginx                                      |
|  - Active: failed (Result: exit-code)                            |
|  - Error message:                                                |
|    nginx: [emerg] open() "/etc/nginx/nginx.conf" failed          |
|    (2: No such file or directory)                                |
|  - "Failed to start The nginx HTTP and reverse proxy server"     |
|                                                                  |
+------------------------------------------------------------------+


## TASK 8: View only error entries in nginx logs

### METHOD 1: Using priority filter -p (Recommended)

```bash
$ journalctl -u nginx -p err
```

Output:
```
-- Logs begin at Mon 2024-01-01 00:00:00 UTC, end at Tue 2024-01-15 10:20:00 UTC. --
Jan 15 10:15:00 hostname nginx[2345]: nginx: [emerg] open() "/etc/nginx/nginx.conf" failed (2: No such file or directory)
Jan 15 10:15:00 hostname nginx[2345]: nginx: configuration file /etc/nginx/nginx.conf test failed
```

Explanation:
- `-p err` = Show only error level and above
- Priority levels (0-7):
  - 0 = emerg (emergency)
  - 1 = alert
  - 2 = crit (critical)
  - 3 = err (error)
  - 4 = warning
  - 5 = notice
  - 6 = info
  - 7 = debug

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 8a <<<                         |
|                                                                  |
|  Terminal showing:                                               |
|  - $ journalctl -u nginx -p err                                  |
|  - Only error messages displayed:                                |
|    nginx: [emerg] open() "/etc/nginx/nginx.conf" failed          |
|    (2: No such file or directory)                                |
|    nginx: configuration file ... test failed                     |
|                                                                  |
+------------------------------------------------------------------+


### METHOD 2: Using priority range

```bash
$ journalctl -u nginx -p err..emerg
```

Shows errors from "err" to "emerg" level.

```bash
$ journalctl -u nginx -p 0..3
```

Shows priorities 0 through 3 (emerg, alert, crit, err).


### METHOD 3: Combining with other filters

```bash
$ journalctl -u nginx -p err --since "10 minutes ago"

$ journalctl -u nginx -p err -n 5

$ journalctl -u nginx -p err --no-pager
```

Explanation:
- `--no-pager` = Output directly without using less


### METHOD 4: Grep for specific error patterns

```bash
$ journalctl -u nginx | grep -i error

$ journalctl -u nginx | grep -i "failed"

$ journalctl -u nginx | grep -E "error|failed|emerg"
```

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 8b <<<                         |
|                                                                  |
|  Terminal showing:                                               |
|  - $ journalctl -u nginx -p err --since "10 minutes ago"         |
|  - Error entries with timestamp                                  |
|  - OR $ journalctl -u nginx | grep -i failed                     |
|  - Filtered error messages                                       |
|                                                                  |
+------------------------------------------------------------------+


### METHOD 5: Using journalctl -xe for detailed errors

```bash
$ journalctl -xe
```

Output:
```
(Shows last journal entries with explanations)
...
Jan 15 10:15:00 hostname nginx[2345]: nginx: [emerg] open() "/etc/nginx/nginx.conf" failed (2: No such file or directory)
-- Subject: Unit nginx.service has failed
-- Defined-By: systemd
--
-- Unit nginx.service has failed.
--
-- The result is failed.
Jan 15 10:15:00 hostname nginx[2345]: nginx: configuration file /etc/nginx/nginx.conf test failed
...
```

Explanation:
- `-x` = Add explanatory text from message catalog
- `-e` = Jump to end of journal


### METHOD 6: JSON output for parsing

```bash
$ journalctl -u nginx -p err -o json-pretty
```

Output:
```json
{
    "__CURSOR" : "s=abc...",
    "__REALTIME_TIMESTAMP" : "1705315500123456",
    "_PID" : "2345",
    "_COMM" : "nginx",
    "MESSAGE" : "nginx: [emerg] open() \"/etc/nginx/nginx.conf\" failed (2: No such file or directory)",
    "PRIORITY" : "3",
    ...
}
```

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 8c <<<                         |
|                                                                  |
|  Terminal showing:                                               |
|  - $ journalctl -xe                                              |
|  - Detailed error explanation                                    |
|  - "Unit nginx.service has failed"                               |
|  - Error context and description                                 |
|                                                                  |
+------------------------------------------------------------------+


## RESTORE NGINX (After completing the task)

Move config file back:
```bash
$ sudo mv ./nginx.conf /etc/nginx/nginx.conf
```

Verify:
```bash
$ ls -la /etc/nginx/nginx.conf
```

Output:
```
-rw-r--r--. 1 root root 2469 Jan 15 10:00 /etc/nginx/nginx.conf
```

Start nginx:
```bash
$ sudo systemctl start nginx
```

Verify:
```bash
$ systemctl status nginx
```

Output:
```
● nginx.service - The nginx HTTP and reverse proxy server
   Active: active (running) since ...
```

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 9 (Optional) <<<               |
|                                                                  |
|  Terminal showing:                                               |
|  - $ sudo mv ./nginx.conf /etc/nginx/nginx.conf                  |
|  - $ sudo systemctl start nginx                                  |
|  - $ systemctl status nginx                                      |
|  - Active: active (running)                                      |
|  - nginx successfully restored and running                       |
|                                                                  |
+------------------------------------------------------------------+


## COMPLETE COMMAND SEQUENCE

```bash
# Task 1: Open terminal
# (Use Ctrl+Alt+T)

# Task 2: Install nginx
sudo yum install epel-release -y
sudo yum install nginx -y

# Task 3: Start nginx
sudo systemctl start nginx
systemctl status nginx

# Task 4: Stop nginx
sudo systemctl stop nginx
systemctl status nginx

# Task 5: Check nginx logs
journalctl -u nginx
journalctl -u nginx -n 20
journalctl -u nginx --since "1 hour ago"

# Task 6: Move config file to introduce error
sudo mv /etc/nginx/nginx.conf ./

# Task 7: Attempt to start nginx (will fail)
sudo systemctl start nginx
systemctl status nginx

# Task 8: View error logs
journalctl -u nginx -p err
journalctl -xe

# Restore (optional)
sudo mv ./nginx.conf /etc/nginx/nginx.conf
sudo systemctl start nginx
```


## JOURNALCTL COMMANDS REFERENCE

### BASIC USAGE:

| Command                 | Description                    |
|-------------------------|--------------------------------|
| journalctl              | Show all logs                  |
| journalctl -u <unit>    | Show logs for specific unit    |
| journalctl -f           | Follow logs in real-time       |
| journalctl -n <N>       | Show last N entries            |
| journalctl -r           | Show in reverse order (newest first) |
| journalctl -e           | Jump to end of journal         |
| journalctl -x           | Add explanatory messages       |
| journalctl -xe          | Combine -x and -e (common usage) |

### TIME FILTERS:

| Command                            | Description              |
|------------------------------------|--------------------------|
| journalctl --since "1 hour ago"    | Logs from last hour      |
| journalctl --since today           | Logs from today          |
| journalctl --since yesterday       | Logs from yesterday      |
| journalctl --since "2024-01-15"    | Logs from specific date  |
| journalctl --until "1 hour ago"    | Logs until 1 hour ago    |
| journalctl -b                      | Logs from current boot   |
| journalctl -b -1                   | Logs from previous boot  |

### PRIORITY FILTERS:

| Command                 | Description                       |
|-------------------------|-----------------------------------|
| journalctl -p emerg     | Emergency only (level 0)          |
| journalctl -p alert     | Alert and above (level 0-1)       |
| journalctl -p crit      | Critical and above (level 0-2)    |
| journalctl -p err       | Error and above (level 0-3)       |
| journalctl -p warning   | Warning and above (level 0-4)     |
| journalctl -p notice    | Notice and above (level 0-5)      |
| journalctl -p info      | Info and above (level 0-6)        |
| journalctl -p debug     | All including debug (level 0-7)   |

### OUTPUT FORMATS:

| Command                     | Description              |
|-----------------------------|--------------------------|
| journalctl -o short         | Default format           |
| journalctl -o short-precise | With microseconds        |
| journalctl -o verbose       | All fields               |
| journalctl -o json          | JSON format              |
| journalctl -o json-pretty   | Pretty JSON format       |
| journalctl -o cat           | Message only             |

### OTHER USEFUL OPTIONS:

| Command                          | Description                 |
|----------------------------------|-----------------------------|
| journalctl --disk-usage          | Show journal disk usage     |
| journalctl --list-boots          | List all boots              |
| journalctl _PID=<pid>            | Logs for specific PID       |
| journalctl _UID=<uid>            | Logs for specific user      |
| journalctl --no-pager            | Output without paging       |
| journalctl --vacuum-size=100M    | Clean up journal to 100MB   |


## LOG PRIORITY LEVELS

| Level | Name    | Description                          |
|-------|---------|--------------------------------------|
| 0     | emerg   | System is unusable                   |
| 1     | alert   | Action must be taken immediately     |
| 2     | crit    | Critical conditions                  |
| 3     | err     | Error conditions                     |
| 4     | warning | Warning conditions                   |
| 5     | notice  | Normal but significant condition     |
| 6     | info    | Informational messages               |
| 7     | debug   | Debug-level messages                 |

Using `-p err` shows levels 0-3 (emerg, alert, crit, err)


## COMMON TROUBLESHOOTING SCENARIOS

**1. SERVICE WON'T START:**
```bash
$ systemctl status <service>
$ journalctl -u <service> -p err --since "5 minutes ago"
$ journalctl -xe
```

**2. SYSTEM SLOW OR UNRESPONSIVE:**
```bash
$ journalctl -p warning --since "1 hour ago"
$ journalctl -k    # Kernel messages only
```

**3. CHECK BOOT ISSUES:**
```bash
$ journalctl -b    # Current boot
$ journalctl -b -1 # Previous boot
```

**4. MONITOR LIVE ISSUES:**
```bash
$ journalctl -f
$ journalctl -u <service> -f
```

**5. FIND SPECIFIC ERRORS:**
```bash
$ journalctl | grep -i "error"
$ journalctl -p err
```


## SCREENSHOTS CHECKLIST

Print this checklist and mark each screenshot as you take it:

- [ ] SCREENSHOT 1  - Terminal window opened
- [ ] SCREENSHOT 2a - yum install epel-release complete
- [ ] SCREENSHOT 2b - yum install nginx complete with version check
- [ ] SCREENSHOT 3  - systemctl start nginx and status showing active
- [ ] SCREENSHOT 4  - systemctl stop nginx and status showing inactive
- [ ] SCREENSHOT 5a - journalctl -u nginx showing all logs
- [ ] SCREENSHOT 5b - journalctl -u nginx with time filter
- [ ] SCREENSHOT 6  - mv nginx.conf and verification
- [ ] SCREENSHOT 7  - Failed start attempt with error message
- [ ] SCREENSHOT 8a - journalctl -u nginx -p err showing only errors
- [ ] SCREENSHOT 8b - journalctl with grep or additional filters
- [ ] SCREENSHOT 8c - journalctl -xe with detailed explanation
- [ ] SCREENSHOT 9  - (Optional) Restored nginx running again

TOTAL: 12-13 Screenshots Required
