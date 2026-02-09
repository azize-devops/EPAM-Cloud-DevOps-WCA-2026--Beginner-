SYSTEMD - ANSWERS
=================

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
TASK 2: List unit files available in your system
================================================================================

METHOD 1: List all unit files
-----------------------------
$ systemctl list-unit-files

Output:
UNIT FILE                                  STATE           VENDOR PRESET
proc-sys-fs-binfmt_misc.automount          static          -
-.mount                                    generated       -
boot.mount                                 generated       -
dev-hugepages.mount                        static          -
...
httpd.service                              disabled        disabled
sshd.service                               enabled         enabled
...

Explanation:
- UNIT FILE: Name of the unit file
- STATE: Current state (enabled, disabled, static, masked)
- VENDOR PRESET: Default state from vendor

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 2a <<<                         |
|                                                                  |
|  Terminal showing:                                               |
|  - $ systemctl list-unit-files                                   |
|  - List of unit files with STATE column                          |
|  - Various services listed (.service files)                      |
|  - enabled/disabled states visible                               |
|                                                                  |
+------------------------------------------------------------------+


METHOD 2: List only service unit files
--------------------------------------
$ systemctl list-unit-files --type=service

Output:
UNIT FILE                                  STATE           VENDOR PRESET
auditd.service                             enabled         enabled
chronyd.service                            enabled         enabled
crond.service                              enabled         enabled
dbus.service                               static          -
...

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 2b <<<                         |
|                                                                  |
|  Terminal showing:                                               |
|  - $ systemctl list-unit-files --type=service                    |
|  - Only .service unit files listed                               |
|  - STATE column visible (enabled, disabled, static)              |
|                                                                  |
+------------------------------------------------------------------+


METHOD 3: List currently loaded units
-------------------------------------
$ systemctl list-units

Output:
UNIT                                      LOAD   ACTIVE SUB       DESCRIPTION
...
crond.service                             loaded active running   Command Scheduler
dbus.service                              loaded active running   D-Bus System Message Bus
firewalld.service                         loaded active running   firewalld
...

METHOD 4: Count unit files
--------------------------
$ systemctl list-unit-files | wc -l

Output:
245

UNIT FILE STATES EXPLAINED:
+------------+----------------------------------------------------------+
| State      | Description                                              |
+------------+----------------------------------------------------------+
| enabled    | Starts automatically at boot                             |
| disabled   | Does not start at boot                                   |
| static     | Cannot be enabled, only started manually or dependency   |
| masked     | Completely disabled, cannot start                        |
| generated  | Generated dynamically by a generator                     |
+------------+----------------------------------------------------------+


================================================================================
TASK 3: Install httpd using yum
================================================================================

Command:
$ sudo yum install httpd -y

Output:
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
...
Resolving Dependencies
--> Running transaction check
---> Package httpd.x86_64 0:2.4.6-97.el7.centos.5 will be installed
...
Installed:
  httpd.x86_64 0:2.4.6-97.el7.centos.5

Dependency Installed:
  apr.x86_64 0:1.4.8-7.el7
  apr-util.x86_64 0:1.5.2-6.el7
  httpd-tools.x86_64 0:2.4.6-97.el7.centos.5
  mailcap.noarch 0:2.1.41-2.el7

Complete!

Note for CentOS 8+/RHEL 8+:
$ sudo dnf install httpd -y

Verify installation:
$ rpm -qa | grep httpd

Output:
httpd-2.4.6-97.el7.centos.5.x86_64
httpd-tools-2.4.6-97.el7.centos.5.x86_64

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                          |
|                                                                  |
|  Terminal showing:                                               |
|  - $ sudo yum install httpd -y                                   |
|  - Installation progress                                         |
|  - "Complete!" message at the end                                |
|  - Installed packages listed (httpd, apr, apr-util, etc.)        |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 4: Start httpd service
================================================================================

Command:
$ sudo systemctl start httpd

Verify it's running:
$ systemctl is-active httpd

Output:
active

Alternative verification:
$ ps aux | grep httpd

Output:
root      1234  0.0  0.2 224024  5000 ?        Ss   10:00   0:00 /usr/sbin/httpd -DFOREGROUND
apache    1235  0.0  0.1 226108  3500 ?        S    10:00   0:00 /usr/sbin/httpd -DFOREGROUND
apache    1236  0.0  0.1 226108  3500 ?        S    10:00   0:00 /usr/sbin/httpd -DFOREGROUND
...

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                          |
|                                                                  |
|  Terminal showing:                                               |
|  - $ sudo systemctl start httpd                                  |
|  - $ systemctl is-active httpd                                   |
|  - Output: active                                                |
|  - OR: ps aux | grep httpd showing httpd processes               |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 5: Check httpd service status
================================================================================

Command:
$ systemctl status httpd

Output:
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; vendor preset: disabled)
   Active: active (running) since Tue 2024-01-15 10:00:00 UTC; 5min ago
     Docs: man:httpd(8)
           man:apachectl(8)
 Main PID: 1234 (httpd)
   Status: "Total requests: 0; Current requests/sec: 0; Current traffic:   0 B/sec"
   CGroup: /system.slice/httpd.service
           ├─1234 /usr/sbin/httpd -DFOREGROUND
           ├─1235 /usr/sbin/httpd -DFOREGROUND
           ├─1236 /usr/sbin/httpd -DFOREGROUND
           ├─1237 /usr/sbin/httpd -DFOREGROUND
           └─1238 /usr/sbin/httpd -DFOREGROUND

Jan 15 10:00:00 hostname systemd[1]: Starting The Apache HTTP Server...
Jan 15 10:00:00 hostname httpd[1234]: AH00558: httpd: Could not reliably determine...
Jan 15 10:00:00 hostname systemd[1]: Started The Apache HTTP Server.

Understanding the output:
+----------------+----------------------------------------------------------+
| Field          | Description                                              |
+----------------+----------------------------------------------------------+
| Loaded         | Unit file location and enable status                     |
| Active         | Current state and since when                             |
| Main PID       | Process ID of main process                               |
| CGroup         | Control group with all related processes                 |
| Log entries    | Recent journal entries for this service                  |
+----------------+----------------------------------------------------------+

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                          |
|                                                                  |
|  Terminal showing:                                               |
|  - $ systemctl status httpd                                      |
|  - ● httpd.service - The Apache HTTP Server                      |
|  - Loaded: loaded (...; disabled; ...)                           |
|  - Active: active (running) since ...                            |
|  - Main PID and CGroup information                               |
|  - Journal log entries at bottom                                 |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 6: Restart httpd service
================================================================================

Command:
$ sudo systemctl restart httpd

Verify:
$ systemctl status httpd

Output shows new start time:
   Active: active (running) since Tue 2024-01-15 10:10:00 UTC; 2s ago
                                                             ^^^^^^
                                                             Just restarted

Alternative commands:
+------------------+----------------------------------------------------------+
| Command          | Description                                              |
+------------------+----------------------------------------------------------+
| restart          | Stop and start the service                               |
| reload           | Reload configuration without stopping                    |
| reload-or-restart| Reload if supported, otherwise restart                   |
| try-restart      | Restart only if already running                          |
+------------------+----------------------------------------------------------+

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                          |
|                                                                  |
|  Terminal showing:                                               |
|  - $ sudo systemctl restart httpd                                |
|  - $ systemctl status httpd                                      |
|  - Active: active (running) since ... ; Xs ago                   |
|  - New start time showing service was restarted                  |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 7: Enable httpd service and verify
================================================================================

Enable the service:
$ sudo systemctl enable httpd

Output:
Created symlink /etc/systemd/system/multi-user.target.wants/httpd.service → /usr/lib/systemd/system/httpd.service.

Explanation:
- "enable" creates a symlink so service starts at boot
- The symlink is in the appropriate target directory


Verify it's enabled:
$ systemctl is-enabled httpd

Output:
enabled


Alternative verification:
$ systemctl status httpd | grep Loaded

Output:
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; vendor preset: disabled)
                                                          ^^^^^^^
                                                          Now shows "enabled"


Check with list-unit-files:
$ systemctl list-unit-files | grep httpd

Output:
httpd.service                              enabled         disabled

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                          |
|                                                                  |
|  Terminal showing:                                               |
|  - $ sudo systemctl enable httpd                                 |
|  - Created symlink ... message                                   |
|  - $ systemctl is-enabled httpd                                  |
|  - Output: enabled                                               |
|  - $ systemctl status httpd                                      |
|  - Loaded line showing "enabled"                                 |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 8: Create script.sh with sleep 1000 content
================================================================================

Method 1: Using cat with heredoc
--------------------------------
$ cat > ~/script.sh << 'EOF'
#!/bin/bash

sleep 1000
EOF


Method 2: Using echo
--------------------
$ echo -e '#!/bin/bash\n\nsleep 1000' > ~/script.sh


Method 3: Using nano/vi editor
------------------------------
$ nano ~/script.sh

(Type the content, then Ctrl+O to save, Ctrl+X to exit)


Verify content:
$ cat ~/script.sh

Output:
#!/bin/bash

sleep 1000

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                          |
|                                                                  |
|  Terminal showing:                                               |
|  - Script creation command (cat, echo, or nano)                  |
|  - $ cat ~/script.sh                                             |
|  - Output:                                                       |
|    #!/bin/bash                                                   |
|                                                                  |
|    sleep 1000                                                    |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 9: Make the script executable
================================================================================

Command:
$ chmod +x ~/script.sh

Verify:
$ ls -la ~/script.sh

Output:
-rwxr-xr-x. 1 user1 user1 24 Jan 15 10:15 /home/user1/script.sh
 ^^^
 Execute permission (x) is set

Alternative verification:
$ file ~/script.sh

Output:
/home/user1/script.sh: Bourne-Again shell script, ASCII text executable

Test the script (optional - press Ctrl+C to stop):
$ ~/script.sh &
$ ps aux | grep script.sh

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 9 <<<                          |
|                                                                  |
|  Terminal showing:                                               |
|  - $ chmod +x ~/script.sh                                        |
|  - $ ls -la ~/script.sh                                          |
|  - -rwxr-xr-x ... script.sh (x permission visible)               |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 10: Create test.service unit file
================================================================================

UNDERSTANDING SYSTEMD UNIT FILE LOCATIONS
------------------------------------------
+--------------------------------+------------------------------------------+
| Location                       | Purpose                                  |
+--------------------------------+------------------------------------------+
| /etc/systemd/system/           | Local/admin unit files (highest priority)|
| /run/systemd/system/           | Runtime unit files                       |
| /usr/lib/systemd/system/       | Package-installed unit files             |
+--------------------------------+------------------------------------------+

For custom services, use: /etc/systemd/system/


STEP 1: Create the unit file
-----------------------------
$ sudo nano /etc/systemd/system/test.service

OR using cat:
$ sudo bash -c 'cat > /etc/systemd/system/test.service << EOF
[Unit]
Description=Test Service - Running script.sh
After=network.target

[Service]
Type=simple
ExecStart=/home/user1/script.sh
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF'

IMPORTANT: Replace /home/user1 with your actual home directory path!

To find your home directory:
$ echo $HOME

Output:
/home/user1


UNIT FILE CONTENT EXPLAINED:
----------------------------

[Unit]
Description=Test Service - Running script.sh    # Description shown in status
After=network.target                            # Start after network is up

[Service]
Type=simple                                     # Simple long-running process
ExecStart=/home/user1/script.sh                 # Command to run
Restart=on-failure                              # Restart if it fails

[Install]
WantedBy=multi-user.target                      # Start in multi-user mode


SERVICE TYPES:
+------------+----------------------------------------------------------+
| Type       | Description                                              |
+------------+----------------------------------------------------------+
| simple     | Default. Process in ExecStart is the main process        |
| forking    | Process forks and parent exits (traditional daemons)     |
| oneshot    | Process exits after completing (like scripts)            |
| notify     | Like simple but process signals when ready               |
| idle       | Like simple but waits until all jobs done                |
+------------+----------------------------------------------------------+


STEP 2: Verify the unit file
----------------------------
$ cat /etc/systemd/system/test.service

Output:
[Unit]
Description=Test Service - Running script.sh
After=network.target

[Service]
Type=simple
ExecStart=/home/user1/script.sh
Restart=on-failure

[Install]
WantedBy=multi-user.target

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 10a <<<                        |
|                                                                  |
|  Terminal showing:                                               |
|  - Creation of unit file (nano or cat command)                   |
|  - $ cat /etc/systemd/system/test.service                        |
|  - [Unit] section with Description                               |
|  - [Service] section with ExecStart                              |
|  - [Install] section with WantedBy                               |
|                                                                  |
+------------------------------------------------------------------+


STEP 3: Reload systemd daemon
-----------------------------
After creating or modifying unit files, reload systemd:

$ sudo systemctl daemon-reload

This tells systemd to re-read all unit files.

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 10b <<<                        |
|                                                                  |
|  Terminal showing:                                               |
|  - $ sudo systemctl daemon-reload                                |
|  - (Command completes without error)                             |
|  - $ systemctl list-unit-files | grep test                       |
|  - test.service visible in the list                              |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 11: Start test service
================================================================================

Command:
$ sudo systemctl start test.service

OR (the .service suffix is optional):
$ sudo systemctl start test

Verify it's running:
$ systemctl is-active test

Output:
active

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 11 <<<                         |
|                                                                  |
|  Terminal showing:                                               |
|  - $ sudo systemctl start test.service                           |
|  - $ systemctl is-active test                                    |
|  - Output: active                                                |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 12: Verify test service status
================================================================================

Command:
$ systemctl status test.service

Expected Output:
● test.service - Test Service - Running script.sh
   Loaded: loaded (/etc/systemd/system/test.service; static; vendor preset: disabled)
   Active: active (running) since Tue 2024-01-15 10:20:00 UTC; 5s ago
 Main PID: 2345 (script.sh)
   CGroup: /system.slice/test.service
           ├─2345 /bin/bash /home/user1/script.sh
           └─2346 sleep 1000

Jan 15 10:20:00 hostname systemd[1]: Started Test Service - Running script.sh.

Understanding the output:
- Loaded: Shows unit file location and that it's loaded
- Active: active (running) confirms service is running
- Main PID: Shows the main process ID
- CGroup: Shows all processes in the control group
  - script.sh is running
  - sleep 1000 is a child process of script.sh

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 12 <<<                         |
|                                                                  |
|  Terminal showing:                                               |
|  - $ systemctl status test.service                               |
|  - ● test.service - Test Service - Running script.sh             |
|  - Loaded: loaded (/etc/systemd/system/test.service; ...)        |
|  - Active: active (running) since ...                            |
|  - Main PID: XXXX (script.sh)                                    |
|  - CGroup showing:                                               |
|    ├─XXXX /bin/bash /home/user1/script.sh                        |
|    └─XXXX sleep 1000                                             |
|  - "Started Test Service" log message                            |
|                                                                  |
+------------------------------------------------------------------+


ADDITIONAL VERIFICATION:
------------------------

Check processes:
$ ps aux | grep -E "script.sh|sleep 1000"

Output:
root      2345  0.0  0.0  11820  1500 ?        Ss   10:20   0:00 /bin/bash /home/user1/script.sh
root      2346  0.0  0.0   7280   672 ?        S    10:20   0:00 sleep 1000

Check journal logs:
$ journalctl -u test.service

Output:
Jan 15 10:20:00 hostname systemd[1]: Starting Test Service - Running script.sh...
Jan 15 10:20:00 hostname systemd[1]: Started Test Service - Running script.sh.


================================================================================
CLEANUP (After completing the task)
================================================================================

Stop the test service:
$ sudo systemctl stop test.service

Disable and remove (optional):
$ sudo systemctl disable test.service
$ sudo rm /etc/systemd/system/test.service
$ sudo systemctl daemon-reload

Stop httpd (optional):
$ sudo systemctl stop httpd
$ sudo systemctl disable httpd


================================================================================
COMPLETE COMMAND SEQUENCE
================================================================================

# Task 1: Open terminal
# (Use Ctrl+Alt+T)

# Task 2: List unit files
systemctl list-unit-files
systemctl list-unit-files --type=service

# Task 3: Install httpd
sudo yum install httpd -y

# Task 4: Start httpd
sudo systemctl start httpd

# Task 5: Check httpd status
systemctl status httpd

# Task 6: Restart httpd
sudo systemctl restart httpd

# Task 7: Enable httpd
sudo systemctl enable httpd
systemctl is-enabled httpd

# Task 8: Create script.sh
cat > ~/script.sh << 'EOF'
#!/bin/bash

sleep 1000
EOF

# Task 9: Make executable
chmod +x ~/script.sh

# Task 10: Create test.service
sudo bash -c 'cat > /etc/systemd/system/test.service << EOF
[Unit]
Description=Test Service - Running script.sh
After=network.target

[Service]
Type=simple
ExecStart='$HOME'/script.sh
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF'
sudo systemctl daemon-reload

# Task 11: Start test service
sudo systemctl start test.service

# Task 12: Verify test service status
systemctl status test.service


================================================================================
SYSTEMD COMMANDS REFERENCE
================================================================================

SERVICE CONTROL:
+----------------------------------+----------------------------------------+
| Command                          | Description                            |
+----------------------------------+----------------------------------------+
| systemctl start <service>        | Start a service                        |
| systemctl stop <service>         | Stop a service                         |
| systemctl restart <service>      | Restart a service                      |
| systemctl reload <service>       | Reload configuration                   |
| systemctl status <service>       | Show service status                    |
| systemctl enable <service>       | Enable at boot                         |
| systemctl disable <service>      | Disable at boot                        |
| systemctl is-active <service>    | Check if running                       |
| systemctl is-enabled <service>   | Check if enabled                       |
| systemctl mask <service>         | Completely disable service             |
| systemctl unmask <service>       | Unmask a masked service                |
+----------------------------------+----------------------------------------+

LISTING AND INFORMATION:
+----------------------------------+----------------------------------------+
| Command                          | Description                            |
+----------------------------------+----------------------------------------+
| systemctl list-units             | List loaded units                      |
| systemctl list-unit-files        | List all unit files                    |
| systemctl list-dependencies      | Show unit dependencies                 |
| systemctl show <service>         | Show all properties                    |
| systemctl cat <service>          | Show unit file content                 |
+----------------------------------+----------------------------------------+

SYSTEM MANAGEMENT:
+----------------------------------+----------------------------------------+
| Command                          | Description                            |
+----------------------------------+----------------------------------------+
| systemctl daemon-reload          | Reload systemd configuration           |
| systemctl reboot                 | Reboot the system                      |
| systemctl poweroff               | Power off the system                   |
| systemctl suspend                | Suspend the system                     |
| systemctl hibernate              | Hibernate the system                   |
+----------------------------------+----------------------------------------+


================================================================================
UNIT FILE TEMPLATE
================================================================================

Basic service unit file template:

[Unit]
Description=My Custom Service
Documentation=https://example.com/docs
After=network.target
Wants=network-online.target

[Service]
Type=simple
User=myuser
Group=mygroup
WorkingDirectory=/path/to/workdir
ExecStart=/path/to/command
ExecReload=/bin/kill -HUP $MAINPID
ExecStop=/bin/kill -TERM $MAINPID
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target


COMMON UNIT FILE DIRECTIVES:
+------------------+----------------------------------------------------------+
| Directive        | Description                                              |
+------------------+----------------------------------------------------------+
| Description      | Human-readable description                               |
| After            | Start after these units                                  |
| Before           | Start before these units                                 |
| Requires         | Hard dependency - fails if dependency fails              |
| Wants            | Soft dependency - doesn't fail if dependency fails       |
| Type             | Service type (simple, forking, oneshot, etc.)            |
| ExecStart        | Command to start the service                             |
| ExecStop         | Command to stop the service                              |
| ExecReload       | Command to reload configuration                          |
| Restart          | When to restart (always, on-failure, etc.)               |
| User             | Run as this user                                         |
| Group            | Run as this group                                        |
| WorkingDirectory | Working directory for the service                        |
| Environment      | Set environment variables                                |
| WantedBy         | Target that wants this service                           |
+------------------+----------------------------------------------------------+


================================================================================
SCREENSHOTS CHECKLIST
================================================================================

Print this checklist and mark each screenshot as you take it:

[ ] SCREENSHOT 1   - Terminal window opened
[ ] SCREENSHOT 2a  - systemctl list-unit-files output
[ ] SCREENSHOT 2b  - systemctl list-unit-files --type=service output
[ ] SCREENSHOT 3   - yum install httpd with "Complete!" message
[ ] SCREENSHOT 4   - systemctl start httpd and is-active check
[ ] SCREENSHOT 5   - systemctl status httpd (full status output)
[ ] SCREENSHOT 6   - systemctl restart httpd and new status
[ ] SCREENSHOT 7   - systemctl enable httpd and is-enabled verification
[ ] SCREENSHOT 8   - script.sh creation and cat output
[ ] SCREENSHOT 9   - chmod +x and ls -la showing execute permission
[ ] SCREENSHOT 10a - test.service unit file content
[ ] SCREENSHOT 10b - daemon-reload and unit file in list
[ ] SCREENSHOT 11  - systemctl start test.service
[ ] SCREENSHOT 12  - systemctl status test.service (showing script.sh and sleep)

TOTAL: 14 Screenshots Required
