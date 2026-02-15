# SOFTWARE MANAGEMENT - ANSWER

This guide covers package management in CentOS using yum (Yellowdog Updater Modified),
including searching, installing, and removing packages.


TASK 1: Open terminal of your choosing
---------------------------------------

Open a terminal application:
- From GUI: Applications > System Tools > Terminal
- Or use keyboard shortcut: Ctrl + Alt + T
- Or right-click on desktop and select "Open Terminal"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                   |
|                                                          |
|  Show: Terminal window opened and ready                  |
|  Prompt visible: [user@hostname ~]$                      |
+----------------------------------------------------------+


TASK 2: Find httpd package in yum repos
---------------------------------------

Use yum search to find packages:

    yum search httpd

This command searches package names and descriptions for "httpd".

Expected output:
    ============================== N/S matched: httpd ==============================
    httpd.x86_64 : Apache HTTP Server
    httpd-devel.x86_64 : Development interfaces for the Apache HTTP server
    httpd-filesystem.x86_64 : The basic directory layout for the Apache HTTP server
    httpd-manual.noarch : Documentation for the Apache HTTP server
    httpd-tools.x86_64 : Tools for use with the Apache HTTP Server
    ...

Alternative commands:

    # Search only in package names
    yum list httpd

    # Get detailed info about the package
    yum info httpd

    # List all available packages matching pattern
    yum list available | grep httpd

+----------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                   |
|                                                          |
|  Show: Output of "yum search httpd" command              |
|  Expected: List of httpd-related packages                |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                   |
|                                                          |
|  Show: Output of "yum info httpd" command                |
|  Expected: Detailed package information (version, size,  |
|            repository, description)                      |
+----------------------------------------------------------+


TASK 3: Install httpd
---------------------

Install httpd using yum install:

    sudo yum install httpd

Or to skip confirmation prompt:

    sudo yum install httpd -y

Expected output:
    Resolving Dependencies
    --> Running transaction check
    ---> Package httpd.x86_64 0:2.4.6-xxx.el7.centos will be installed
    ...
    Installed:
      httpd.x86_64 0:2.4.6-xxx.el7.centos

    Complete!

+----------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                   |
|                                                          |
|  Show: yum install httpd command execution               |
|  Expected: Dependency resolution and installation        |
|            progress with "Complete!" message             |
+----------------------------------------------------------+


TASK 4: Start httpd. Verify that it's running
----------------------------------------------

Start the httpd service:

    sudo systemctl start httpd

Verify the service is running:

    # Check service status
    sudo systemctl status httpd

    # Alternative: check if process is running
    ps aux | grep httpd

    # Alternative: check if port 80 is listening
    ss -tlnp | grep :80

Expected output from systemctl status:
    ● httpd.service - The Apache HTTP Server
       Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; vendor preset: disabled)
       Active: active (running) since Wed 2020-12-23 11:19:56 UTC; 5s ago
     Main PID: 1234 (httpd)
       Status: "Total requests: 0; Current requests/sec: 0; Current traffic:   0 B/sec"
       CGroup: /system.slice/httpd.service
               ├─1234 /usr/sbin/httpd -DFOREGROUND
               ├─1235 /usr/sbin/httpd -DFOREGROUND
               ...

+----------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                   |
|                                                          |
|  Show: systemctl start httpd command                     |
|  Expected: Command executes without error                |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                   |
|                                                          |
|  Show: systemctl status httpd output                     |
|  Expected: Active: active (running) status               |
|            Green dot indicator                           |
+----------------------------------------------------------+

Optional - Test with curl:

    curl http://localhost

+----------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                   |
|                                                          |
|  Show: curl http://localhost or browser test             |
|  Expected: Apache test page or welcome message           |
+----------------------------------------------------------+


TASK 5: Remove httpd
--------------------

First stop the service, then remove the package:

    # Stop the service
    sudo systemctl stop httpd

    # Remove the package
    sudo yum remove httpd

    # Or to skip confirmation
    sudo yum remove httpd -y

Expected output:
    Resolving Dependencies
    --> Running transaction check
    ---> Package httpd.x86_64 0:2.4.6-xxx.el7.centos will be erased
    ...
    Removed:
      httpd.x86_64 0:2.4.6-xxx.el7.centos

    Complete!

Alternative removal commands:

    # Remove package and unused dependencies
    sudo yum autoremove httpd

    # Erase (same as remove)
    sudo yum erase httpd

+----------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                   |
|                                                          |
|  Show: yum remove httpd command execution                |
|  Expected: Package removal with "Complete!" message      |
+----------------------------------------------------------+


TASK 6: Find nginx package in yum repos
---------------------------------------

Search for nginx in yum repositories:

    yum search nginx

Expected output (on fresh CentOS without EPEL):
    Warning: No matches found for: nginx
    No matches found

This is because nginx is not available in the default CentOS repositories.
It requires the EPEL (Extra Packages for Enterprise Linux) repository.

+----------------------------------------------------------+
|                    >>> SCREENSHOT 9 <<<                   |
|                                                          |
|  Show: yum search nginx with no results                  |
|  Expected: "No matches found" or empty result            |
|            (before EPEL installation)                    |
+----------------------------------------------------------+


TASK 7: Find out which repo provides nginx and install it
---------------------------------------------------------

To find which repository provides nginx:

    # Check available repos
    yum repolist

    # Search for nginx with provides
    yum provides nginx

nginx is provided by the EPEL repository. Install EPEL:

    sudo yum install epel-release -y

Verify EPEL is installed:

    yum repolist

Expected output after EPEL installation:
    repo id                     repo name                                    status
    base/7/x86_64               CentOS-7 - Base                              10,072
    epel/x86_64                 Extra Packages for Enterprise Linux 7        13,xxx
    extras/7/x86_64             CentOS-7 - Extras                            xxx
    updates/7/x86_64            CentOS-7 - Updates                           xxx

Now search for nginx again:

    yum search nginx

Expected output:
    ============================== N/S matched: nginx ==============================
    nginx.x86_64 : A high performance web server and reverse proxy server
    nginx-all-modules.noarch : A meta package that installs all available Nginx modules
    ...

+----------------------------------------------------------+
|                    >>> SCREENSHOT 10 <<<                  |
|                                                          |
|  Show: yum install epel-release command                  |
|  Expected: EPEL repository installation                  |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 11 <<<                  |
|                                                          |
|  Show: yum repolist after EPEL installation              |
|  Expected: epel repository visible in the list           |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 12 <<<                  |
|                                                          |
|  Show: yum search nginx after EPEL installation          |
|  Expected: nginx packages found from EPEL repo           |
+----------------------------------------------------------+


TASK 8: Install nginx
---------------------

Now install nginx from EPEL repository:

    sudo yum install nginx -y

Expected output:
    Resolving Dependencies
    --> Running transaction check
    ---> Package nginx.x86_64 1:1.xx.x-x.el7 will be installed
    --> Processing Dependency: nginx-filesystem...
    ...
    Installed:
      nginx.x86_64 1:1.xx.x-x.el7

    Dependency Installed:
      nginx-filesystem.noarch ...

    Complete!

+----------------------------------------------------------+
|                    >>> SCREENSHOT 13 <<<                  |
|                                                          |
|  Show: yum install nginx command execution               |
|  Expected: nginx installation from epel repo             |
|            with dependencies and "Complete!" message     |
+----------------------------------------------------------+


TASK 9: Start nginx. Verify that it's running
----------------------------------------------

Start the nginx service:

    sudo systemctl start nginx

Verify the service is running:

    sudo systemctl status nginx

Expected output:
    ● nginx.service - The nginx HTTP and reverse proxy server
       Loaded: loaded (/usr/lib/systemd/system/nginx.service; disabled; vendor preset: disabled)
       Active: active (running) since Wed 2020-12-23 11:21:58 UTC; 3s ago
      Process: 1234 ExecStart=/usr/sbin/nginx (code=exited, status=0/SUCCESS)
      Process: 1233 ExecStartPre=/usr/sbin/nginx -t (code=exited, status=0/SUCCESS)
      Process: 1232 ExecStartPre=/usr/bin/rm -f /run/nginx.pid (code=exited, status=0/SUCCESS)
     Main PID: 1235 (nginx)
       CGroup: /system.slice/nginx.service
               ├─1235 nginx: master process /usr/sbin/nginx
               └─1236 nginx: worker process

Additional verification commands:

    # Check if nginx process is running
    ps aux | grep nginx

    # Check if port 80 is listening
    ss -tlnp | grep :80

    # Test with curl
    curl http://localhost

+----------------------------------------------------------+
|                    >>> SCREENSHOT 14 <<<                  |
|                                                          |
|  Show: systemctl start nginx command                     |
|  Expected: Command executes without error                |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 15 <<<                  |
|                                                          |
|  Show: systemctl status nginx output                     |
|  Expected: Active: active (running) status               |
|            Green dot, PID information                    |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 16 <<<                  |
|                                                          |
|  Show: curl http://localhost or browser test             |
|  Expected: nginx welcome page                            |
+----------------------------------------------------------+


================================================================================
                              YUM COMMAND REFERENCE
================================================================================

| Command                        | Description                                 |
|--------------------------------|---------------------------------------------|
| yum search <package>           | Search for packages by name/description     |
| yum list <package>             | List packages matching pattern              |
| yum list available             | List all available packages                 |
| yum list installed             | List all installed packages                 |
| yum info <package>             | Show detailed package information           |
| yum install <package>          | Install a package                           |
| yum install <package> -y       | Install without confirmation                |
| yum remove <package>           | Remove a package                            |
| yum autoremove <package>       | Remove package and unused dependencies      |
| yum update                     | Update all packages                         |
| yum update <package>           | Update specific package                     |
| yum provides <file>            | Find which package provides a file          |
| yum repolist                   | List enabled repositories                   |
| yum repolist all               | List all repositories (enabled/disabled)    |
| yum clean all                  | Clean yum cache                             |
| yum history                    | Show transaction history                    |
| yum grouplist                  | List available package groups               |
| yum groupinstall <group>       | Install a package group                     |

================================================================================


================================================================================
                           IMPORTANT REPOSITORIES
================================================================================

| Repository     | Description                                              |
|----------------|----------------------------------------------------------|
| base           | Base CentOS packages                                     |
| updates        | Security and bug fix updates                             |
| extras         | Additional CentOS packages                               |
| epel           | Extra Packages for Enterprise Linux (community)          |
| epel-release   | Package that enables EPEL repository                     |

To enable EPEL:
    sudo yum install epel-release

================================================================================


================================================================================
                             TROUBLESHOOTING
================================================================================

Problem: "No package httpd available"
Solution: Run "yum clean all" and try again, or check your internet connection

Problem: "Cannot find a valid baseurl for repo"
Solution: Check network connectivity, DNS settings, or repository configuration

Problem: "Package nginx not found"
Solution: Install EPEL repository first: sudo yum install epel-release

Problem: Port 80 already in use
Solution: Stop the conflicting service or use different port in config

Problem: Permission denied
Solution: Use sudo for package management commands

================================================================================


================================================================================
                              SELF-REVIEW CHECKLIST
================================================================================

[ ] Terminal opened successfully
[ ] yum search httpd shows available httpd packages
[ ] httpd installed successfully
[ ] httpd service started and status shows "active (running)"
[ ] httpd removed successfully
[ ] yum search nginx initially shows no results (before EPEL)
[ ] EPEL repository installed (yum install epel-release)
[ ] yum repolist shows epel in the list
[ ] yum search nginx now shows nginx packages
[ ] nginx installed successfully from EPEL
[ ] nginx service started and status shows "active (running)"

================================================================================

