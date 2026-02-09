VIRTUALIZATION INTRODUCTION - ANSWERS
=====================================

================================================================================
TASK 1: Get acquainted with VirtualBox user manual
================================================================================

Open the official VirtualBox user manual:
https://www.virtualbox.org/manual/

Key Chapters to Review:
-----------------------
1. Chapter 1 - First Steps
   - 1.1 Why is Virtualization Useful?
   - 1.2 Some Terminology
   - 1.3 Features Overview
   - 1.4 Supported Host Operating Systems
   - 1.5 Installing Oracle VM VirtualBox

2. Chapter 1.8 - Creating Your First Virtual Machine
   - Step-by-step VM creation guide
   - Hardware allocation (RAM, CPU, Disk)
   - Installation media setup

3. Chapter 1.9 - Running Your Virtual Machine
   - Starting and stopping VMs
   - Keyboard and mouse capture
   - Host key usage

4. Chapter 1.14 - Cloning Virtual Machines
   - Full clone vs Linked clone
   - Cloning process

Important Concepts to Understand:
---------------------------------
- Host OS: The operating system running on your physical computer
- Guest OS: The operating system running inside the virtual machine
- Virtual Machine (VM): A software-based computer running inside your physical computer
- Hypervisor: Software that creates and manages virtual machines (VirtualBox is a Type 2 hypervisor)
- Host Key: Special key for controlling VM (default: Right Ctrl)

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                          |
|                                                                  |
|  Browser showing:                                                |
|  - VirtualBox User Manual webpage                                |
|  - URL: https://www.virtualbox.org/manual/                       |
|  - Table of Contents visible                                     |
|  - Chapter 1 "First Steps" visible                               |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 2: Download and install VirtualBox
================================================================================

STEP 1: Download VirtualBox
---------------------------
1. Go to https://www.virtualbox.org/
2. Click on "Downloads" in the navigation menu
3. Select your host operating system:
   - Windows hosts
   - macOS / Intel hosts
   - Linux distributions

Download Links (as of current version):
- Windows: VirtualBox-7.x.x-xxxxx-Win.exe
- macOS: VirtualBox-7.x.x-xxxxx-OSX.dmg
- Linux: Select your distribution (Ubuntu, Fedora, etc.)

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 2a <<<                         |
|                                                                  |
|  Browser showing:                                                |
|  - VirtualBox download page                                      |
|  - URL: https://www.virtualbox.org/wiki/Downloads                |
|  - Platform packages section visible                             |
|  - "Windows hosts" link highlighted/visible                      |
|                                                                  |
+------------------------------------------------------------------+


STEP 2: Install VirtualBox (Windows)
------------------------------------
1. Locate downloaded file (e.g., VirtualBox-7.0.14-161095-Win.exe)
2. Double-click to run installer
3. Click "Next" on Welcome screen
4. Choose installation location (default is recommended)
5. Select features to install:
   [x] VirtualBox USB Support
   [x] VirtualBox Networking
   [x] VirtualBox Python Support
6. Click "Next" and then "Yes" for network interface warning
7. Click "Install"
8. Click "Finish" when complete

Installation Notes:
- During installation, network connectivity may be temporarily interrupted
- Windows may ask for permission to install device software - click "Install"
- Restart may be required

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 2b <<<                         |
|                                                                  |
|  Installation wizard showing:                                    |
|  - VirtualBox Setup window                                       |
|  - Feature selection screen                                      |
|  - All features checked                                          |
|  - Installation path visible                                     |
|                                                                  |
+------------------------------------------------------------------+

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 2c <<<                         |
|                                                                  |
|  Installation complete showing:                                  |
|  - "Installation complete" message                               |
|  - "Finish" button visible                                       |
|  - Checkbox "Start Oracle VM VirtualBox after installation"      |
|                                                                  |
+------------------------------------------------------------------+


STEP 3: Verify Installation
---------------------------
1. Open VirtualBox from Start Menu or Desktop shortcut
2. VirtualBox Manager window should appear
3. Check version: Help > About VirtualBox

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 2d <<<                         |
|                                                                  |
|  VirtualBox Manager showing:                                     |
|  - Main VirtualBox window open                                   |
|  - Welcome message or empty VM list                              |
|  - Menu bar visible (File, Machine, Help)                        |
|  - Version number visible in title bar or About dialog           |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 3: Download Ubuntu Server or Desktop
================================================================================

Option A: Ubuntu Desktop (Recommended for beginners)
-----------------------------------------------------
1. Go to https://ubuntu.com/download/desktop
2. Click "Download" for the latest LTS version (e.g., Ubuntu 24.04 LTS)
3. Save the ISO file (approximately 4-5 GB)

File name example: ubuntu-24.04-desktop-amd64.iso

Option B: Ubuntu Server (Lightweight, no GUI)
----------------------------------------------
1. Go to https://ubuntu.com/download/server
2. Click "Download Ubuntu Server"
3. Save the ISO file (approximately 2 GB)

File name example: ubuntu-24.04-live-server-amd64.iso

LTS Explained:
- LTS = Long Term Support
- 5 years of security updates and support
- Recommended for stability

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 3a <<<                         |
|                                                                  |
|  Browser showing:                                                |
|  - Ubuntu download page                                          |
|  - URL: https://ubuntu.com/download/desktop                      |
|    OR https://ubuntu.com/download/server                         |
|  - Ubuntu version visible (e.g., 24.04 LTS)                      |
|  - Download button visible                                       |
|                                                                  |
+------------------------------------------------------------------+

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 3b <<<                         |
|                                                                  |
|  File explorer showing:                                          |
|  - Downloaded ISO file                                           |
|  - File name: ubuntu-xx.xx-desktop-amd64.iso                     |
|    OR ubuntu-xx.xx-live-server-amd64.iso                         |
|  - File size visible (approximately 4-5 GB for Desktop           |
|    or 2 GB for Server)                                           |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 4: Create VM1 and install Ubuntu
================================================================================

PART A: Create Virtual Machine
------------------------------

Step 1: Open VirtualBox and click "New"
1. Open Oracle VM VirtualBox Manager
2. Click "New" button (or Machine > New)

Step 2: Name and Operating System
1. Name: VM1
2. Folder: (default location or choose)
3. ISO Image: Select downloaded Ubuntu ISO file
4. Type: Linux
5. Version: Ubuntu (64-bit)
6. Check "Skip Unattended Installation" if you want manual install

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 4a <<<                         |
|                                                                  |
|  VirtualBox "Create Virtual Machine" dialog showing:             |
|  - Name: VM1                                                     |
|  - ISO Image: path to ubuntu ISO file                            |
|  - Type: Linux                                                   |
|  - Version: Ubuntu (64-bit)                                      |
|                                                                  |
+------------------------------------------------------------------+

Step 3: Hardware Configuration
1. Base Memory: 2048 MB (minimum) - 4096 MB (recommended)
2. Processors: 2 CPUs (or more if available)
3. Enable EFI: Optional (check if needed)

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 4b <<<                         |
|                                                                  |
|  Hardware configuration showing:                                 |
|  - Memory (RAM) slider: 2048 MB or 4096 MB                       |
|  - Processors slider: 2 CPUs                                     |
|  - Green zone indicator for safe allocation                      |
|                                                                  |
+------------------------------------------------------------------+

Step 4: Virtual Hard Disk
1. Select "Create a virtual hard disk now"
2. Disk Size: 25 GB (minimum) - 50 GB (recommended)
3. Pre-allocate Full Size: Optional (better performance if checked)

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 4c <<<                         |
|                                                                  |
|  Virtual Hard Disk configuration showing:                        |
|  - "Create a Virtual Hard Disk Now" selected                     |
|  - Disk Size: 25 GB or more                                      |
|  - Disk file location visible                                    |
|                                                                  |
+------------------------------------------------------------------+

Step 5: Review and Finish
1. Review all settings in Summary
2. Click "Finish"

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 4d <<<                         |
|                                                                  |
|  Summary screen showing:                                         |
|  - Name: VM1                                                     |
|  - Operating System: Ubuntu (64-bit)                             |
|  - Memory: 2048 MB or 4096 MB                                    |
|  - Processors: 2                                                 |
|  - Storage: 25 GB or more                                        |
|  - "Finish" button visible                                       |
|                                                                  |
+------------------------------------------------------------------+


PART B: Install Ubuntu on VM1
-----------------------------

Step 1: Start VM1
1. Select VM1 in VirtualBox Manager
2. Click "Start" button

Step 2: Ubuntu Installation Process
1. Wait for Ubuntu to boot from ISO
2. Select "Install Ubuntu" (or "Try Ubuntu" first to test)
3. Choose language
4. Select keyboard layout
5. Choose installation type:
   - Normal installation (recommended)
   - Minimal installation
6. Choose "Erase disk and install Ubuntu" (safe for VM)
7. Set timezone
8. Create user account:
   - Your name: [Your Name]
   - Computer name: VM1
   - Username: [username]
   - Password: [password]
9. Wait for installation to complete
10. Click "Restart Now" when prompted
11. Press Enter when asked to remove installation medium

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 4e <<<                         |
|                                                                  |
|  Ubuntu installer showing:                                       |
|  - Welcome screen with "Install Ubuntu" option                   |
|  - Language selection visible                                    |
|                                                                  |
+------------------------------------------------------------------+

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 4f <<<                         |
|                                                                  |
|  Ubuntu installer showing:                                       |
|  - Installation type selection                                   |
|  - "Erase disk and install Ubuntu" selected                      |
|  - This is safe for VM - it only erases virtual disk             |
|                                                                  |
+------------------------------------------------------------------+

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 4g <<<                         |
|                                                                  |
|  Ubuntu installer showing:                                       |
|  - User account creation screen                                  |
|  - Your name field                                               |
|  - Computer's name: VM1                                          |
|  - Username field                                                |
|  - Password field                                                |
|                                                                  |
+------------------------------------------------------------------+

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 4h <<<                         |
|                                                                  |
|  Ubuntu installation complete showing:                           |
|  - "Installation Complete" message                               |
|  - "Restart Now" button                                          |
|                                                                  |
+------------------------------------------------------------------+

Step 3: First Boot and Login
1. VM1 restarts
2. Ubuntu login screen appears
3. Enter password and login
4. Complete initial setup wizard (if shown)

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 4i <<<                         |
|                                                                  |
|  Ubuntu Desktop showing:                                         |
|  - Ubuntu desktop after login                                    |
|  - Desktop icons visible                                         |
|  - Top bar with time and system icons                            |
|  - This confirms successful installation                         |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 5: VM control options - start, stop, reboot, save state
================================================================================

HOST KEY INFORMATION
--------------------
- Default Host Key: Right Ctrl
- Host Key is used for:
  - Releasing mouse from VM (Host Key)
  - Keyboard shortcuts (Host Key + other key)
- You can change Host Key in: File > Preferences > Input

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 5a <<<                         |
|                                                                  |
|  VirtualBox Preferences showing:                                 |
|  - File > Preferences > Input                                    |
|  - Host Key Combination visible                                  |
|  - Default: Right Ctrl                                           |
|                                                                  |
+------------------------------------------------------------------+


START OPTIONS
-------------

1. Normal Start
   - Select VM > Click "Start"
   - OR Double-click on VM
   - Opens VM in new window

2. Headless Start
   - Select VM > Click dropdown arrow next to Start > Headless Start
   - VM runs in background without window
   - Useful for servers

3. Detachable Start
   - Select VM > Click dropdown arrow next to Start > Detachable Start
   - Can close window while VM keeps running

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 5b <<<                         |
|                                                                  |
|  VirtualBox Manager showing:                                     |
|  - VM1 selected                                                  |
|  - Start button dropdown menu expanded                           |
|  - Options visible: Normal Start, Headless Start, Detachable     |
|                                                                  |
+------------------------------------------------------------------+


STOP OPTIONS (Machine Menu or Close Window)
-------------------------------------------

When you close VM window or use Machine menu, you get options:

1. Save State (Recommended)
   - Machine > Save State
   - Keyboard: Host Key + Q, then choose "Save State"
   - Saves current state to disk
   - Fast resume next time
   - Like "hibernate" on real computer

2. Send Shutdown Signal (ACPI Shutdown)
   - Machine > ACPI Shutdown
   - Sends power button signal to guest OS
   - Guest OS shuts down gracefully
   - Like pressing power button briefly

3. Power Off (Force)
   - Machine > Power Off
   - Immediately cuts power
   - Like pulling plug - may cause data loss
   - Use only if guest OS is frozen

4. Send Pause Signal
   - Machine > Pause
   - Keyboard: Host Key + P
   - Freezes VM state
   - Can resume with same command

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 5c <<<                         |
|                                                                  |
|  VM window showing Machine menu:                                 |
|  - Machine menu expanded                                         |
|  - Options visible:                                              |
|    - Pause                                                       |
|    - Reset                                                       |
|    - ACPI Shutdown                                               |
|    - Power Off                                                   |
|    - Save State                                                  |
|                                                                  |
+------------------------------------------------------------------+

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 5d <<<                         |
|                                                                  |
|  Close VM dialog showing:                                        |
|  - "Close Virtual Machine" dialog                                |
|  - Options:                                                      |
|    ( ) Save the machine state                                    |
|    ( ) Send the shutdown signal                                  |
|    ( ) Power off the machine                                     |
|                                                                  |
+------------------------------------------------------------------+


REBOOT/RESET OPTIONS
--------------------

1. Soft Reboot (from Guest OS)
   - Inside Ubuntu: Click Power icon > Restart
   - Terminal: sudo reboot

2. Hard Reset (from VirtualBox)
   - Machine > Reset
   - Keyboard: Host Key + R
   - Like pressing reset button on physical PC
   - May cause data loss

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 5e <<<                         |
|                                                                  |
|  Ubuntu Power menu showing:                                      |
|  - Power menu in Ubuntu (top right corner)                       |
|  - "Restart" option visible                                      |
|  - OR terminal window with "sudo reboot" command                 |
|                                                                  |
+------------------------------------------------------------------+


KEYBOARD SHORTCUTS SUMMARY
--------------------------

+----------------------+----------------------------------------+
| Shortcut             | Action                                 |
+----------------------+----------------------------------------+
| Host Key             | Release mouse/keyboard from VM         |
| Host Key + F         | Toggle fullscreen mode                 |
| Host Key + L         | Toggle seamless mode                   |
| Host Key + C         | Toggle scaled mode                     |
| Host Key + Home      | Show Machine menu                      |
| Host Key + P         | Pause VM                               |
| Host Key + R         | Reset (hard reboot)                    |
| Host Key + Q         | Close VM (shows options dialog)        |
| Host Key + S         | Take snapshot                          |
| Host Key + E         | Take screenshot                        |
| Host Key + Del       | Send Ctrl+Alt+Del to VM                |
+----------------------+----------------------------------------+

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 5f <<<                         |
|                                                                  |
|  VM in fullscreen mode showing:                                  |
|  - Ubuntu desktop in fullscreen                                  |
|  - No VirtualBox window borders visible                          |
|  - (Press Host Key + F to toggle fullscreen)                     |
|                                                                  |
+------------------------------------------------------------------+


MOUSE CAPTURE
-------------
- When you click inside VM, mouse is "captured"
- Mouse cursor is confined to VM window
- Press Host Key to release mouse
- Auto-capture can be disabled in VM settings

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 5g <<<                         |
|                                                                  |
|  VM window showing mouse capture message:                        |
|  - Message at bottom of VM window                                |
|  - "Right Ctrl to release mouse" or similar                      |
|  - Mouse cursor inside VM                                        |
|                                                                  |
+------------------------------------------------------------------+


SAVE STATE DEMONSTRATION
------------------------

1. With VM running, press Host Key + Q
2. Select "Save the machine state"
3. Click "OK"
4. VM window closes, state is saved
5. In VirtualBox Manager, VM1 shows "Saved" state

To Resume:
1. Select VM1
2. Click "Start"
3. VM resumes from exact saved state

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 5h <<<                         |
|                                                                  |
|  VirtualBox Manager showing:                                     |
|  - VM1 in list                                                   |
|  - State: "Saved"                                                |
|  - Restore button or Start button visible                        |
|  - Preview/thumbnail shows saved state                           |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 6: Clone VM1 to create VM2
================================================================================

CLONING METHODS
---------------

1. Full Clone
   - Creates completely independent copy
   - Takes more disk space
   - VM2 is completely separate from VM1
   - Recommended for most cases

2. Linked Clone
   - Uses VM1's disk as base
   - Only stores differences
   - Takes less disk space
   - VM2 depends on VM1 - don't delete VM1!
   - Faster to create

STEP-BY-STEP: Clone VM1 to VM2
------------------------------

Step 1: Ensure VM1 is powered off
- VM must be in "Powered Off" or "Saved" state to clone
- If running, shut down VM1 first

Step 2: Select VM1 and start Clone wizard
- Right-click on VM1 > Clone
- OR select VM1 > Machine menu > Clone

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 6a <<<                         |
|                                                                  |
|  VirtualBox Manager showing:                                     |
|  - Right-click context menu on VM1                               |
|  - "Clone..." option highlighted                                 |
|  - VM1 state: Powered Off or Saved                               |
|                                                                  |
+------------------------------------------------------------------+

Step 3: Configure Clone Settings

Name and Path:
1. Name: VM2
2. Path: (default or choose location)
3. MAC Address Policy: Generate new MAC addresses for all adapters
   (Important! Each VM needs unique MAC addresses)

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 6b <<<                         |
|                                                                  |
|  Clone Virtual Machine dialog showing:                           |
|  - Name: VM2                                                     |
|  - Path setting                                                  |
|  - MAC Address Policy dropdown:                                  |
|    "Generate new MAC addresses for all network adapters"         |
|                                                                  |
+------------------------------------------------------------------+

Step 4: Select Clone Type
1. Full Clone (Recommended)
   - Creates independent copy
   - Takes more space but VM2 is standalone

2. Linked Clone
   - Faster and smaller
   - VM2 depends on VM1

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 6c <<<                         |
|                                                                  |
|  Clone Type selection showing:                                   |
|  - ( ) Full Clone                                                |
|  - ( ) Linked Clone                                              |
|  - Description of each option visible                            |
|  - "Full Clone" selected (recommended)                           |
|                                                                  |
+------------------------------------------------------------------+

Step 5: Select Snapshots (if any)
- If VM1 has snapshots:
  - Current machine state
  - Everything (all snapshots)
- If no snapshots, this step is skipped

Step 6: Click "Finish" and wait
- Cloning process begins
- Progress bar shows status
- May take several minutes for Full Clone

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 6d <<<                         |
|                                                                  |
|  Clone progress showing:                                         |
|  - Progress bar                                                  |
|  - "Cloning virtual machine..." message                          |
|  - Percentage complete                                           |
|                                                                  |
+------------------------------------------------------------------+

Step 7: Verify VM2 in VirtualBox Manager
- VM2 appears in VM list
- VM2 is separate from VM1
- Both VMs can run simultaneously (with different MAC addresses)

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 6e <<<                         |
|                                                                  |
|  VirtualBox Manager showing:                                     |
|  - VM list with both VM1 and VM2                                 |
|  - VM2 visible as new entry                                      |
|  - Both VMs show "Powered Off" state                             |
|                                                                  |
+------------------------------------------------------------------+


VERIFY CLONE WORKS
------------------

1. Start VM2
2. Verify Ubuntu boots correctly
3. Check hostname (may still show VM1's name inside)
4. Change hostname if needed:
   $ sudo hostnamectl set-hostname VM2
   $ sudo reboot

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 6f <<<                         |
|                                                                  |
|  VM2 running showing:                                            |
|  - Ubuntu desktop on VM2                                         |
|  - Terminal with hostname command:                               |
|    $ hostnamectl                                                 |
|  - OR VirtualBox window title showing "VM2"                      |
|                                                                  |
+------------------------------------------------------------------+


RUNNING BOTH VMs SIMULTANEOUSLY
-------------------------------

1. Start VM1
2. Start VM2
3. Both VMs run in separate windows
4. Each VM has independent resources (RAM, CPU, network)

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 6g <<<                         |
|                                                                  |
|  Desktop showing:                                                |
|  - Two VirtualBox windows side by side                           |
|  - VM1 window with Ubuntu                                        |
|  - VM2 window with Ubuntu                                        |
|  - Both running simultaneously                                   |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
COMPLETE TASK SUMMARY
================================================================================

TASK CHECKLIST:
[ ] 1. Reviewed VirtualBox user manual
[ ] 2. Downloaded and installed VirtualBox
[ ] 3. Downloaded Ubuntu ISO
[ ] 4. Created VM1 and installed Ubuntu
[ ] 5. Tested VM control options:
    [ ] Normal Start
    [ ] Save State
    [ ] ACPI Shutdown
    [ ] Power Off
    [ ] Pause
    [ ] Reset
    [ ] Fullscreen mode (Host Key + F)
    [ ] Mouse capture/release (Host Key)
[ ] 6. Cloned VM1 to create VM2


================================================================================
VIRTUALBOX CONTROLS REFERENCE
================================================================================

VM STATES:
+----------------+------------------------------------------+
| State          | Description                              |
+----------------+------------------------------------------+
| Powered Off    | VM is completely off                     |
| Saved          | VM state saved to disk                   |
| Running        | VM is currently running                  |
| Paused         | VM is frozen (can resume)                |
| Aborted        | VM crashed or was force-stopped          |
+----------------+------------------------------------------+

HOST KEY SHORTCUTS:
+----------------------+----------------------------------------+
| Shortcut             | Action                                 |
+----------------------+----------------------------------------+
| Host Key (Right Ctrl)| Release mouse from VM                  |
| Host Key + F         | Toggle fullscreen                      |
| Host Key + L         | Toggle seamless mode                   |
| Host Key + C         | Toggle scaled mode                     |
| Host Key + P         | Pause/Resume VM                        |
| Host Key + R         | Reset VM (hard reboot)                 |
| Host Key + Q         | Close VM dialog                        |
| Host Key + S         | Take snapshot                          |
| Host Key + Del       | Send Ctrl+Alt+Del to guest             |
+----------------------+----------------------------------------+

RECOMMENDED VM SETTINGS FOR UBUNTU:
+----------------+------------------------------------------+
| Setting        | Recommended Value                        |
+----------------+------------------------------------------+
| RAM            | 2048 MB minimum, 4096 MB recommended     |
| CPUs           | 2 or more                                |
| Video Memory   | 128 MB                                   |
| Disk Size      | 25 GB minimum, 50 GB recommended         |
| Network        | NAT (for internet) or Bridged (for LAN)  |
+----------------+------------------------------------------+


================================================================================
TROUBLESHOOTING
================================================================================

PROBLEM: VirtualBox won't start VM - VT-x/AMD-V error
SOLUTION: Enable virtualization in BIOS/UEFI settings

PROBLEM: Ubuntu installation is very slow
SOLUTION: Allocate more RAM (4 GB+) and enable PAE/NX in VM settings

PROBLEM: No network in guest OS
SOLUTION: Check Network adapter settings - try NAT or Bridged Adapter

PROBLEM: Cannot exit fullscreen mode
SOLUTION: Press Host Key + F (default: Right Ctrl + F)

PROBLEM: Mouse stuck in VM
SOLUTION: Press Host Key (default: Right Ctrl) to release

PROBLEM: Clone fails
SOLUTION: Ensure VM is powered off, not just saved state


================================================================================
SCREENSHOTS CHECKLIST
================================================================================

Print this checklist and mark each screenshot as you take it:

[ ] SCREENSHOT 1   - VirtualBox manual webpage in browser
[ ] SCREENSHOT 2a  - VirtualBox download page
[ ] SCREENSHOT 2b  - VirtualBox installation wizard (feature selection)
[ ] SCREENSHOT 2c  - VirtualBox installation complete
[ ] SCREENSHOT 2d  - VirtualBox Manager main window
[ ] SCREENSHOT 3a  - Ubuntu download page
[ ] SCREENSHOT 3b  - Downloaded Ubuntu ISO file
[ ] SCREENSHOT 4a  - Create VM dialog - Name and OS
[ ] SCREENSHOT 4b  - Create VM dialog - Hardware (RAM, CPU)
[ ] SCREENSHOT 4c  - Create VM dialog - Virtual Hard Disk
[ ] SCREENSHOT 4d  - Create VM dialog - Summary
[ ] SCREENSHOT 4e  - Ubuntu installer - Welcome screen
[ ] SCREENSHOT 4f  - Ubuntu installer - Installation type
[ ] SCREENSHOT 4g  - Ubuntu installer - User account creation
[ ] SCREENSHOT 4h  - Ubuntu installer - Installation complete
[ ] SCREENSHOT 4i  - Ubuntu desktop after login (VM1)
[ ] SCREENSHOT 5a  - VirtualBox Preferences - Host Key setting
[ ] SCREENSHOT 5b  - Start button dropdown menu options
[ ] SCREENSHOT 5c  - Machine menu with control options
[ ] SCREENSHOT 5d  - Close VM dialog (Save/Shutdown/Power Off)
[ ] SCREENSHOT 5e  - Ubuntu restart option or terminal reboot
[ ] SCREENSHOT 5f  - VM in fullscreen mode
[ ] SCREENSHOT 5g  - Mouse capture message
[ ] SCREENSHOT 5h  - VM in "Saved" state in Manager
[ ] SCREENSHOT 6a  - Clone option in context menu
[ ] SCREENSHOT 6b  - Clone dialog - Name and MAC address
[ ] SCREENSHOT 6c  - Clone dialog - Clone type selection
[ ] SCREENSHOT 6d  - Clone progress bar
[ ] SCREENSHOT 6e  - Both VM1 and VM2 in VirtualBox Manager
[ ] SCREENSHOT 6f  - VM2 running with hostname verification
[ ] SCREENSHOT 6g  - Both VMs running simultaneously

TOTAL: 31 Screenshots Required
