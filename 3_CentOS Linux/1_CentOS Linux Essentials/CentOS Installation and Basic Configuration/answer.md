# CENTOS INSTALLATION AND BASIC CONFIGURATION - ANSWERS

================================================================================
TASK 1: Install latest CentOS distribution on hypervisor
================================================================================

STEP 1: Download CentOS Stream ISO
----------------------------------
1. Go to https://www.centos.org/download/
2. Select "CentOS Stream 9" or "CentOS Stream 10"
3. Choose your architecture (x86_64 for most PCs)
4. Download the DVD ISO or Boot ISO:
   - DVD ISO (~9GB): Contains all packages, works offline
   - Boot ISO (~1GB): Minimal, downloads packages during install

[SCREENSHOT REQUIRED] - CentOS download page


STEP 2: Create Virtual Machine in VirtualBox
--------------------------------------------
1. Open VirtualBox
2. Click "New" to create a new VM
3. Configure VM settings:

   | Setting                | Recommended Value       |
   |------------------------|-------------------------|
   | Name                   | CentOS-Stream-9         |
   | Type                   | Linux                   |
   | Version                | Red Hat (64-bit)        |
   | Memory (RAM)           | 2048 MB (minimum)       |
   | Hard Disk              | Create a virtual disk   |
   | Disk Size              | 40 GB (minimum)         |
   | Disk Type              | VDI (dynamically alloc) |

4. Before starting, go to Settings > Storage:
   - Click on "Empty" under Controller: IDE
   - Click the disk icon on the right
   - Choose "Choose a disk file"
   - Select the downloaded CentOS ISO

5. Go to Settings > Network:
   - Adapter 1: Attached to "NAT" or "Bridged Adapter"

[SCREENSHOT REQUIRED] - VirtualBox VM creation settings


STEP 3: Start Installation
--------------------------
1. Start the VM
2. Select "Install CentOS Stream 9" from boot menu
3. Wait for the installer to load

[SCREENSHOT REQUIRED] - CentOS boot menu


================================================================================
TASK 2: Configure user "Name_Surname" during installation
================================================================================

STEP 1: Installation Summary Screen
-----------------------------------
After language selection, you'll see the Installation Summary screen.

STEP 2: Create User
-------------------
1. Click on "User Creation"
2. Fill in the details:

   | Field                  | Value                   |
   |------------------------|-------------------------|
   | Full Name              | Name Surname            |
   | User Name              | Name_Surname            |
   | Make this user admin   | [x] Check this box      |
   | Require password       | [x] Check this box      |
   | Password               | YourSecurePassword123!  |

3. Click "Done"

[SCREENSHOT REQUIRED] - User Creation screen with Name_Surname configured


STEP 3: Set Root Password (Optional but Recommended)
----------------------------------------------------
1. Click on "Root Password"
2. Enter a strong root password
3. Optionally disable root SSH login for security
4. Click "Done"

[SCREENSHOT REQUIRED] - Root Password configuration


================================================================================
TASK 3: Configure network during installation
================================================================================

STEP 1: Access Network Settings
-------------------------------
1. In Installation Summary, click "Network & Host Name"

STEP 2: Configure Network
-------------------------
1. Select your network interface (e.g., enp0s3, eth0)
2. Toggle the switch to "ON" to enable the interface
3. Click "Configure..." for advanced settings:

   AUTOMATIC (DHCP) - Recommended for beginners:
   | Setting                | Value                   |
   |------------------------|-------------------------|
   | IPv4 Method            | Automatic (DHCP)        |
   | IPv6 Method            | Automatic               |
   | Connect automatically  | [x] Checked             |

   MANUAL (Static IP) - For specific requirements:
   | Setting                | Example Value           |
   |------------------------|-------------------------|
   | IPv4 Method            | Manual                  |
   | Address                | 192.168.1.100           |
   | Netmask                | 255.255.255.0           |
   | Gateway                | 192.168.1.1             |
   | DNS Servers            | 8.8.8.8, 8.8.4.4        |

4. Set Host Name at the bottom:
   - Example: centos-vm.localdomain
   - Click "Apply"

5. Click "Done"

[SCREENSHOT REQUIRED] - Network & Host Name configuration screen


================================================================================
TASK 4: Configure custom sizes for /home, /opt, /var directories
================================================================================

STEP 1: Access Storage Configuration
------------------------------------
1. In Installation Summary, click "Installation Destination"
2. Select your virtual hard disk
3. Under "Storage Configuration", select "Custom"
4. Click "Done" to enter manual partitioning

[SCREENSHOT REQUIRED] - Installation Destination with "Custom" selected


STEP 2: Create Partitions
-------------------------
Click "+" to add each partition. Recommended layout for 40GB disk:

| Mount Point| Size       | File System | Purpose                          |
|------------|------------|-------------|----------------------------------|
| /boot      | 1 GiB      | xfs         | Boot files                       |
| /boot/efi  | 600 MiB    | EFI System  | EFI boot (UEFI systems only)     |
| swap       | 2 GiB      | swap        | Virtual memory                   |
| /          | 10 GiB     | xfs         | Root filesystem                  |
| /home      | 10 GiB     | xfs         | User home directories            |
| /opt       | 5 GiB      | xfs         | Optional/third-party software    |
| /var       | 10 GiB     | xfs         | Variable data (logs, databases)  |

Note: Remaining space can be added to /home or / as needed.


STEP 3: Create Each Partition
-----------------------------

For /boot:
1. Click "+"
2. Mount Point: /boot
3. Desired Capacity: 1 GiB
4. Click "Add mount point"
5. Verify File System: xfs

For swap:
1. Click "+"
2. Mount Point: swap
3. Desired Capacity: 2 GiB
4. Click "Add mount point"

For / (root):
1. Click "+"
2. Mount Point: /
3. Desired Capacity: 10 GiB
4. Click "Add mount point"

For /home:
1. Click "+"
2. Mount Point: /home
3. Desired Capacity: 10 GiB
4. Click "Add mount point"

For /opt:
1. Click "+"
2. Mount Point: /opt
3. Desired Capacity: 5 GiB
4. Click "Add mount point"

For /var:
1. Click "+"
2. Mount Point: /var
3. Desired Capacity: 10 GiB
4. Click "Add mount point"

[SCREENSHOT REQUIRED] - Manual Partitioning screen showing all partitions


STEP 4: Accept Changes
----------------------
1. Click "Done"
2. Review the "Summary of Changes" dialog
3. Click "Accept Changes"

[SCREENSHOT REQUIRED] - Summary of Changes dialog


================================================================================
STEP 5: Complete Installation
================================================================================

1. Click "Begin Installation"
2. Wait for installation to complete (10-30 minutes)
3. Click "Reboot System" when prompted
4. Remove the ISO from virtual drive before reboot

[SCREENSHOT REQUIRED] - Installation progress/completion screen


================================================================================
SELF-REVIEW VERIFICATION
================================================================================

--------------------------------------------------------------------------------
TEST 1: Login with user "Name_Surname"
--------------------------------------------------------------------------------

1. After reboot, you'll see the login screen
2. Enter username: Name_Surname
3. Enter your password
4. You should see the desktop or command prompt

[SCREENSHOT REQUIRED] - Successful login screen or desktop

Verification Command:
$ whoami
Name_Surname

$ id
uid=1000(Name_Surname) gid=1000(Name_Surname) groups=1000(Name_Surname),10(wheel)


--------------------------------------------------------------------------------
TEST 2: Verify internet access
--------------------------------------------------------------------------------

Method 1: Ping Test
$ ping -c 4 8.8.8.8

Expected Output:
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=117 time=15.2 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=117 time=14.8 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=117 time=15.1 ms
64 bytes from 8.8.8.8: icmp_seq=4 ttl=117 time=14.9 ms

--- 8.8.8.8 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3005ms


Method 2: DNS Test
$ ping -c 4 google.com

Expected Output:
PING google.com (142.250.x.x) 56(84) bytes of data.
64 bytes from ...: icmp_seq=1 ttl=117 time=20.1 ms
...


Method 3: curl Test
$ curl -I https://www.google.com

Expected Output:
HTTP/2 200
...

[SCREENSHOT REQUIRED] - Terminal showing successful ping results


--------------------------------------------------------------------------------
TEST 3: Verify partition sizes with df -h
--------------------------------------------------------------------------------

Command:
$ df -h

Expected Output:
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda2        10G  2.5G  7.5G  25% /
/dev/sda1       1.0G  200M  800M  20% /boot
/dev/sda5        10G  100M  9.9G   1% /home
/dev/sda6       5.0G   50M  5.0G   1% /opt
/dev/sda7        10G  500M  9.5G   5% /var

Verify that:
- /home shows approximately 10 GiB
- /opt shows approximately 5 GiB
- /var shows approximately 10 GiB

[SCREENSHOT REQUIRED] - Terminal showing df -h output with correct partition sizes


Additional Verification Command:
$ lsblk

This shows all block devices and their mount points:
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   40G  0 disk
├─sda1   8:1    0    1G  0 part /boot
├─sda2   8:2    0   10G  0 part /
├─sda3   8:3    0    2G  0 part [SWAP]
├─sda4   8:4    0   10G  0 part /home
├─sda5   8:5    0    5G  0 part /opt
└─sda6   8:6    0   10G  0 part /var

[SCREENSHOT REQUIRED] - Terminal showing lsblk output


================================================================================
TROUBLESHOOTING
================================================================================

Problem: Cannot access internet
-------------------------------
Solutions:
1. Check if network interface is up:
   $ ip link show
   $ sudo nmcli device status

2. Restart network service:
   $ sudo systemctl restart NetworkManager

3. Check IP address:
   $ ip addr show

4. Check default gateway:
   $ ip route show

5. In VirtualBox, verify network adapter is set to NAT or Bridged


Problem: Wrong partition sizes
------------------------------
Note: Partition sizes cannot be changed after installation without
data loss. If sizes are incorrect, you need to reinstall CentOS
with correct partition configuration.


Problem: Cannot login with user
-------------------------------
1. Try logging in as root (if enabled)
2. Reset user password:
   $ sudo passwd Name_Surname
3. Check if user exists:
   $ cat /etc/passwd | grep Name_Surname


================================================================================
SUMMARY OF REQUIRED SCREENSHOTS
================================================================================

| Step | Screenshot Description                                        |
|------|---------------------------------------------------------------|
|  1   | CentOS download page                                          |
|  2   | VirtualBox VM creation settings                               |
|  3   | CentOS boot menu                                              |
|  4   | User Creation screen with Name_Surname                        |
|  5   | Root Password configuration                                   |
|  6   | Network & Host Name configuration                             |
|  7   | Installation Destination with "Custom" selected               |
|  8   | Manual Partitioning showing all partitions                    |
|  9   | Summary of Changes dialog                                     |
|  10  | Installation completion screen                                |
|  11  | Successful login (desktop or terminal)                        |
|  12  | Terminal: ping test results                                   |
|  13  | Terminal: df -h output                                        |
|  14  | Terminal: lsblk output                                        |

TOTAL: 14 Screenshots Required

