# SOFTWARE MANAGEMENT - ANSWER

This guide covers package management in CentOS using yum (Yellowdog Updater Modified),
including searching, installing, and removing packages.


## TASK 1: Open terminal of your choosing

Open a terminal application:
- From GUI: Applications > System Tools > Terminal
- Or use keyboard shortcut: Ctrl + Alt + T
- Or right-click on desktop and select "Open Terminal"

![Terminal opened successfully](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/12_Software%20Management/Terminal%20opened%20successfully.png)


## TASK 2: Find httpd package in yum repos

Use yum search to find packages:

```bash
yum search httpd
```

This command searches package names and descriptions for "httpd".

Expected output:
```
============================== N/S matched: httpd ==============================
httpd.x86_64 : Apache HTTP Server
httpd-devel.x86_64 : Development interfaces for the Apache HTTP server
httpd-filesystem.x86_64 : The basic directory layout for the Apache HTTP server
httpd-manual.noarch : Documentation for the Apache HTTP server
httpd-tools.x86_64 : Tools for use with the Apache HTTP Server
...
```

Alternative commands:

```bash
# Search only in package names
yum list httpd

# Get detailed info about the package
yum info httpd

# List all available packages matching pattern
yum list available | grep httpd
```

![yum search httpd](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/12_Software%20Management/yum%20search%20httpd%20shows%20available%20httpd%20packages.png)

![yum info httpd](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/12_Software%20Management/Show-%20Output%20of%20-yum%20info%20httpd-%20command%20.png)


## TASK 3: Install httpd

Install httpd using yum install:

```bash
sudo yum install httpd
```

Or to skip confirmation prompt:

```bash
sudo yum install httpd -y
```

Expected output:
```
Resolving Dependencies
--> Running transaction check
---> Package httpd.x86_64 0:2.4.6-xxx.el7.centos will be installed
...
Installed:
  httpd.x86_64 0:2.4.6-xxx.el7.centos

Complete!
```

![httpd installed successfully](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/12_Software%20Management/httpd%20installed%20successfully.png)


## TASK 4: Start httpd. Verify that it's running

Start the httpd service:

```bash
sudo systemctl start httpd
```

Verify the service is running:

```bash
# Check service status
sudo systemctl status httpd

# Alternative: check if process is running
ps aux | grep httpd

# Alternative: check if port 80 is listening
ss -tlnp | grep :80
```

Expected output from systemctl status:
```
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; vendor preset: disabled)
   Active: active (running) since Wed 2020-12-23 11:19:56 UTC; 5s ago
 Main PID: 1234 (httpd)
   Status: "Total requests: 0; Current requests/sec: 0; Current traffic:   0 B/sec"
   CGroup: /system.slice/httpd.service
           ├─1234 /usr/sbin/httpd -DFOREGROUND
           ├─1235 /usr/sbin/httpd -DFOREGROUND
           ...
```

![httpd service started and running](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/12_Software%20Management/httpd%20service%20started%20and%20status%20shows%20-active%20(running)-.png)


## TASK 5: Remove httpd

First stop the service, then remove the package:

```bash
# Stop the service
sudo systemctl stop httpd

# Remove the package
sudo yum remove httpd

# Or to skip confirmation
sudo yum remove httpd -y
```

Expected output:
```
Resolving Dependencies
--> Running transaction check
---> Package httpd.x86_64 0:2.4.6-xxx.el7.centos will be erased
...
Removed:
  httpd.x86_64 0:2.4.6-xxx.el7.centos

Complete!
```

Alternative removal commands:

```bash
# Remove package and unused dependencies
sudo yum autoremove httpd

# Erase (same as remove)
sudo yum erase httpd
```

![httpd removed successfully](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/12_Software%20Management/httpd%20removed%20successfully.png)


## TASK 6: Find nginx package in yum repos

Search for nginx in yum repositories:

```bash
yum search nginx
```

**CentOS 7:**
```
Warning: No matches found for: nginx
No matches found
```
nginx is not available in the default CentOS 7 repositories. It requires the EPEL (Extra Packages for Enterprise Linux) repository.

**CentOS Stream 8/9:**
```
======================== Name Exactly Matched: nginx =========================
nginx.x86_64 : A high performance web server and reverse proxy server
```
nginx is available in the default **AppStream** repository. EPEL is not required.

![yum search nginx](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/12_Software%20Management/yum%20search%20nginx%20executed-CentOS%20Stream%209-%20nginx%20packages%20found%20(AppStream).png)


## TASK 7: Find out which repo provides nginx and install it

To find which repository provides nginx:

```bash
# Check available repos
yum repolist

# Search for nginx with provides
yum provides nginx
```

**CentOS 7 (EPEL Required):**

nginx is provided by the EPEL repository. Install EPEL:

```bash
sudo yum install epel-release -y
```

Verify EPEL is installed:

```bash
yum repolist
```

Expected output after EPEL installation:
```
repo id                     repo name                                    status
base/7/x86_64               CentOS-7 - Base                              10,072
epel/x86_64                 Extra Packages for Enterprise Linux 7        13,xxx
extras/7/x86_64             CentOS-7 - Extras                            xxx
updates/7/x86_64            CentOS-7 - Updates                           xxx
```

Now search for nginx again:

```bash
yum search nginx
```

**CentOS Stream 8/9 (No EPEL Needed):**

nginx is already available in the AppStream repository:

```bash
yum info nginx
```

Expected output:
```
Available Packages
Name         : nginx
Version      : 1.xx.x
Repository   : appstream
Summary      : A high performance web server and reverse proxy server
```

![yum info nginx](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/12_Software%20Management/yum%20info%20nginx.png)


## TASK 8: Install nginx

Install nginx (from EPEL on CentOS 7, or AppStream on CentOS Stream 8/9):

```bash
sudo yum install nginx -y
```

Expected output:
```
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
```

![nginx installed successfully](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/12_Software%20Management/nginx%20installed%20successfully.png)


## TASK 9: Start nginx. Verify that it's running

Start the nginx service:

```bash
sudo systemctl start nginx
```

Verify the service is running:

```bash
sudo systemctl status nginx
```

Expected output:
```
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
```

Additional verification commands:

```bash
# Check if nginx process is running
ps aux | grep nginx

# Check if port 80 is listening
ss -tlnp | grep :80

# Test with curl
curl http://localhost
```

![nginx service started and running](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/12_Software%20Management/nginx%20service%20started%20and%20status%20shows%20-active%20(running)-.png)


## YUM COMMAND REFERENCE

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


## IMPORTANT REPOSITORIES

| Repository     | Description                                              |
|----------------|----------------------------------------------------------|
| base           | Base CentOS packages                                     |
| updates        | Security and bug fix updates                             |
| extras         | Additional CentOS packages                               |
| epel           | Extra Packages for Enterprise Linux (community)          |
| epel-release   | Package that enables EPEL repository                     |

To enable EPEL:
```bash
sudo yum install epel-release
```


## TROUBLESHOOTING

**Problem: "No package httpd available"**

Solution: Run `yum clean all` and try again, or check your internet connection

**Problem: "Cannot find a valid baseurl for repo"**

Solution: Check network connectivity, DNS settings, or repository configuration

**Problem: "Package nginx not found"**

Solution (CentOS 7): Install EPEL repository first: `sudo yum install epel-release`

Note: On CentOS Stream 8/9, nginx is available in AppStream repository by default.

**Problem: Port 80 already in use**

Solution: Stop the conflicting service or use different port in config

**Problem: Permission denied**

Solution: Use sudo for package management commands


