# MSSQL SERVER INSTALLATION - ANSWER

This guide walks you through installing SQL Server 2019 Developer Edition
with two instances: a default instance via GUI and a named instance via
command line. Both instances will use Mixed Authentication Mode with
custom file locations.

================================================================================
                    PART 1: PREPARATION
================================================================================

STEP 1.1: SYSTEM REQUIREMENTS
-----------------------------

Minimum Requirements for SQL Server 2019:
- Processor: 1.4 GHz 64-bit processor (2.0 GHz recommended)
- Memory: 1 GB minimum (4 GB recommended)
- Disk Space: 6 GB minimum
- Operating System: Windows Server 2016 or later

STEP 1.2: CREATE CUSTOM DIRECTORIES
-----------------------------------

Before installation, create directories for SQL Server files:

Open PowerShell as Administrator and run:

    # Create directories for Default Instance
    New-Item -ItemType Directory -Path "C:\SQLServer\MSSQLSERVER\Data" -Force
    New-Item -ItemType Directory -Path "C:\SQLServer\MSSQLSERVER\Log" -Force
    New-Item -ItemType Directory -Path "C:\SQLServer\MSSQLSERVER\Backup" -Force

    # Create directories for Named Instance
    New-Item -ItemType Directory -Path "C:\SQLServer\YOURINSTANCE\Data" -Force
    New-Item -ItemType Directory -Path "C:\SQLServer\YOURINSTANCE\Log" -Force
    New-Item -ItemType Directory -Path "C:\SQLServer\YOURINSTANCE\Backup" -Force

Or using Command Prompt:

    mkdir C:\SQLServer\MSSQLSERVER\Data
    mkdir C:\SQLServer\MSSQLSERVER\Log
    mkdir C:\SQLServer\MSSQLSERVER\Backup
    mkdir C:\SQLServer\YOURINSTANCE\Data
    mkdir C:\SQLServer\YOURINSTANCE\Log
    mkdir C:\SQLServer\YOURINSTANCE\Backup

STEP 1.3: MOUNT SQL SERVER ISO
------------------------------

1. Locate your SQL Server 2019 Developer Edition ISO file
2. Right-click the ISO file -> "Mount"
3. Note the drive letter (e.g., D:\ or E:\)
4. The setup.exe file will be in the root of the mounted drive

================================================================================
                    PART 2: DEFAULT INSTANCE INSTALLATION (GUI)
================================================================================

STEP 2.1: LAUNCH SQL SERVER SETUP
---------------------------------

1. Open the mounted ISO drive
2. Double-click "setup.exe"
3. SQL Server Installation Center opens

STEP 2.2: START NEW INSTALLATION
--------------------------------

1. In left panel, click "Installation"
2. Click "New SQL Server stand-alone installation or add features to an existing installation"
3. Wait for setup to initialize

STEP 2.3: PRODUCT KEY
---------------------

1. Select "Specify a free edition: Developer"
   (Developer Edition is free for development/testing)
2. Click Next

STEP 2.4: LICENSE TERMS
-----------------------

1. Check "I accept the license terms and Privacy Statement"
2. Click Next

STEP 2.5: MICROSOFT UPDATE
--------------------------

1. Optionally check "Use Microsoft Update to check for updates"
2. Click Next

STEP 2.6: INSTALL RULES
-----------------------

1. Setup runs installation rules check
2. Verify all rules pass (or show warnings only)
3. Click Next

Note: Windows Firewall warning is expected - we'll configure it later

STEP 2.7: FEATURE SELECTION
---------------------------

1. Select ONLY:
   [X] Database Engine Services

   Do NOT select:
   [ ] SQL Server Replication
   [ ] Machine Learning Services
   [ ] Full-Text and Semantic Extractions
   [ ] Data Quality Services
   [ ] Analysis Services
   [ ] Reporting Services
   [ ] Integration Services
   [ ] Master Data Services

2. Instance Features location: Leave default or customize
3. Shared Features location: Leave default
4. Click Next

STEP 2.8: INSTANCE CONFIGURATION
--------------------------------

1. Select: "Default instance"
   - Instance name will be: MSSQLSERVER
   - Instance ID: MSSQLSERVER

2. Instance root directory: C:\Program Files\Microsoft SQL Server\
   (or customize as needed)

3. Click Next

STEP 2.9: SERVER CONFIGURATION - SERVICE ACCOUNTS
-------------------------------------------------

1. Service Accounts tab:

   SQL Server Database Engine:
   - Account Name: NT Service\MSSQLSERVER (default) or create dedicated account
   - Startup Type: Automatic

   SQL Server Agent:
   - Account Name: NT Service\SQLSERVERAGENT (default)
   - Startup Type: Automatic (change from Manual if desired)

2. Collation tab:
   - Database Engine: SQL_Latin1_General_CP1_CI_AS (default)
   - Click Next

STEP 2.10: DATABASE ENGINE CONFIGURATION
----------------------------------------

This is the critical configuration step!

1. Server Configuration tab:

   Authentication Mode:
   (*) Mixed Mode (SQL Server authentication and Windows authentication)

   Enter SQL Server system administrator (sa) password:
   - Password: [Enter strong password - e.g., P@ssw0rd123!]
   - Confirm password: [Same password]

   Specify SQL Server administrators:
   - Click "Add Current User" to add yourself
   - Or click "Add..." to add specific users/groups

2. Data Directories tab (IMPORTANT - Custom Locations):

   - Data root directory: C:\SQLServer\MSSQLSERVER
   - User database directory: C:\SQLServer\MSSQLSERVER\Data
   - User database log directory: C:\SQLServer\MSSQLSERVER\Log
   - Backup directory: C:\SQLServer\MSSQLSERVER\Backup
   - Temp DB directory: C:\SQLServer\MSSQLSERVER\Data
   - Temp DB log directory: C:\SQLServer\MSSQLSERVER\Log

3. TempDB tab:
   - Number of files: 4 (recommended: 1 per CPU core, up to 8)
   - Initial size: 64 MB each
   - Autogrowth: 64 MB

4. MaxDOP tab:
   - Leave recommended value or adjust based on CPU cores

5. Memory tab:
   - Leave recommended or set Min/Max server memory

6. FILESTREAM tab:
   - Leave unchecked unless needed

7. Click Next

STEP 2.11: READY TO INSTALL
---------------------------

1. Review the configuration summary
2. Note the Configuration file path (useful for command line installation)
3. Click "Install"

STEP 2.12: COMPLETE INSTALLATION
--------------------------------

1. Wait for installation to complete (10-20 minutes)
2. Verify all features show "Succeeded"
3. Click "Close"

STEP 2.13: VERIFY DEFAULT INSTANCE
----------------------------------

PowerShell:

    # Check SQL Server service
    Get-Service -Name "MSSQLSERVER"

    # Expected output:
    # Status   Name               DisplayName
    # ------   ----               -----------
    # Running  MSSQLSERVER        SQL Server (MSSQLSERVER)

    # Check SQL Server Agent service
    Get-Service -Name "SQLSERVERAGENT"

Command Prompt:

    sc query MSSQLSERVER

================================================================================
                    PART 3: NAMED INSTANCE INSTALLATION (COMMAND LINE)
================================================================================

This section installs a named instance using command line parameters.

STEP 3.1: UNDERSTAND SETUP PARAMETERS
-------------------------------------

Key setup.exe parameters:

    /ACTION=Install              - Installation action
    /FEATURES=SQLENGINE          - Database Engine only
    /INSTANCENAME=name           - Named instance name
    /INSTANCEID=name             - Instance ID
    /SQLSYSADMINACCOUNTS="user"  - SQL Server admin accounts
    /SECURITYMODE=SQL            - Enable Mixed Authentication
    /SAPWD="password"            - SA password
    /SQLUSERDBDIR="path"         - User database directory
    /SQLUSERDBLOGDIR="path"      - User database log directory
    /SQLBACKUPDIR="path"         - Backup directory
    /SQLTEMPDBDIR="path"         - TempDB directory
    /SQLTEMPDBLOGDIR="path"      - TempDB log directory
    /TCPENABLED=1                - Enable TCP/IP
    /NPENABLED=1                 - Enable Named Pipes
    /IACCEPTSQLSERVERLICENSETERMS - Accept license
    /Q or /QS                    - Quiet or Quiet Simple mode

STEP 3.2: CREATE INSTALLATION SCRIPT
------------------------------------

Open Notepad and create a batch file (InstallNamedInstance.bat):

    @echo off
    echo Installing SQL Server 2019 Named Instance...

    D:\setup.exe ^
    /ACTION=Install ^
    /FEATURES=SQLENGINE ^
    /INSTANCENAME=YOURINSTANCE ^
    /INSTANCEID=YOURINSTANCE ^
    /SQLSYSADMINACCOUNTS="BUILTIN\Administrators" ^
    /SECURITYMODE=SQL ^
    /SAPWD="P@ssw0rd123!" ^
    /SQLUSERDBDIR="C:\SQLServer\YOURINSTANCE\Data" ^
    /SQLUSERDBLOGDIR="C:\SQLServer\YOURINSTANCE\Log" ^
    /SQLBACKUPDIR="C:\SQLServer\YOURINSTANCE\Backup" ^
    /SQLTEMPDBDIR="C:\SQLServer\YOURINSTANCE\Data" ^
    /SQLTEMPDBLOGDIR="C:\SQLServer\YOURINSTANCE\Log" ^
    /SQLTEMPDBFILECOUNT=4 ^
    /SQLTEMPDBFILESIZE=64 ^
    /SQLTEMPDBFILEGROWTH=64 ^
    /SQLTEMPDBLOGFILESIZE=64 ^
    /SQLTEMPDBLOGFILEGROWTH=64 ^
    /TCPENABLED=1 ^
    /NPENABLED=1 ^
    /SQLSVCSTARTUPTYPE=Automatic ^
    /AGTSVCSTARTUPTYPE=Automatic ^
    /BROWSERSVCSTARTUPTYPE=Automatic ^
    /IACCEPTSQLSERVERLICENSETERMS ^
    /QS

    echo Installation complete!
    pause

NOTE: Replace "D:\setup.exe" with your actual ISO drive letter.
NOTE: Replace "YOURINSTANCE" with your desired instance name.
NOTE: Use a strong password for SAPWD.

STEP 3.3: RUN INSTALLATION FROM COMMAND LINE
--------------------------------------------

Method 1: Using Batch File

1. Save the batch file (e.g., C:\InstallNamedInstance.bat)
2. Open Command Prompt as Administrator
3. Run:
    C:\InstallNamedInstance.bat

Method 2: Direct Command Line

Open Command Prompt as Administrator and run (single line):

    D:\setup.exe /ACTION=Install /FEATURES=SQLENGINE /INSTANCENAME=YOURINSTANCE /INSTANCEID=YOURINSTANCE /SQLSYSADMINACCOUNTS="BUILTIN\Administrators" /SECURITYMODE=SQL /SAPWD="P@ssw0rd123!" /SQLUSERDBDIR="C:\SQLServer\YOURINSTANCE\Data" /SQLUSERDBLOGDIR="C:\SQLServer\YOURINSTANCE\Log" /SQLBACKUPDIR="C:\SQLServer\YOURINSTANCE\Backup" /SQLTEMPDBDIR="C:\SQLServer\YOURINSTANCE\Data" /SQLTEMPDBLOGDIR="C:\SQLServer\YOURINSTANCE\Log" /TCPENABLED=1 /NPENABLED=1 /IACCEPTSQLSERVERLICENSETERMS /QS

Method 3: Using PowerShell

    $setupPath = "D:\setup.exe"

    $arguments = @(
        "/ACTION=Install",
        "/FEATURES=SQLENGINE",
        "/INSTANCENAME=YOURINSTANCE",
        "/INSTANCEID=YOURINSTANCE",
        '/SQLSYSADMINACCOUNTS="BUILTIN\Administrators"',
        "/SECURITYMODE=SQL",
        '/SAPWD="P@ssw0rd123!"',
        '/SQLUSERDBDIR="C:\SQLServer\YOURINSTANCE\Data"',
        '/SQLUSERDBLOGDIR="C:\SQLServer\YOURINSTANCE\Log"',
        '/SQLBACKUPDIR="C:\SQLServer\YOURINSTANCE\Backup"',
        '/SQLTEMPDBDIR="C:\SQLServer\YOURINSTANCE\Data"',
        '/SQLTEMPDBLOGDIR="C:\SQLServer\YOURINSTANCE\Log"',
        "/TCPENABLED=1",
        "/NPENABLED=1",
        "/IACCEPTSQLSERVERLICENSETERMS",
        "/QS"
    )

    Start-Process -FilePath $setupPath -ArgumentList $arguments -Wait -NoNewWindow

STEP 3.4: MONITOR INSTALLATION PROGRESS
---------------------------------------

- /QS (Quiet Simple) shows progress in command window
- /Q (Quiet) shows no UI at all

Check installation logs at:
    C:\Program Files\Microsoft SQL Server\150\Setup Bootstrap\Log\

STEP 3.5: VERIFY NAMED INSTANCE
-------------------------------

PowerShell:

    # Check Named Instance service
    Get-Service -Name "MSSQL`$YOURINSTANCE"

    # Expected output:
    # Status   Name                    DisplayName
    # ------   ----                    -----------
    # Running  MSSQL$YOURINSTANCE      SQL Server (YOURINSTANCE)

    # List all SQL Server instances
    Get-Service | Where-Object { $_.Name -like "MSSQL*" }

Command Prompt:

    sc query MSSQL$YOURINSTANCE

STEP 3.6: VERIFY BOTH INSTANCES ARE RUNNING
-------------------------------------------

    Get-Service | Where-Object { $_.Name -like "MSSQL*" -or $_.Name -like "SQL*" }

Expected services:
    - MSSQLSERVER (Default Instance)
    - MSSQL$YOURINSTANCE (Named Instance)
    - SQLSERVERAGENT (Agent for Default)
    - SQLAgent$YOURINSTANCE (Agent for Named)
    - SQLBrowser (SQL Server Browser)

================================================================================
                    PART 4: INSTALL SQL SERVER MANAGEMENT STUDIO (SSMS)
================================================================================

STEP 4.1: DOWNLOAD SSMS
-----------------------

1. Open browser and go to:
   https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms

2. Click "Download SQL Server Management Studio (SSMS)"
3. Save the installer (SSMS-Setup-ENU.exe)

Or use PowerShell to download:

    # Download SSMS (check for latest version URL)
    $ssmsUrl = "https://aka.ms/ssmsfullsetup"
    $ssmsInstaller = "C:\Temp\SSMS-Setup-ENU.exe"

    New-Item -ItemType Directory -Path "C:\Temp" -Force
    Invoke-WebRequest -Uri $ssmsUrl -OutFile $ssmsInstaller

STEP 4.2: INSTALL SSMS ON VM
----------------------------

Method 1: GUI Installation

1. Run SSMS-Setup-ENU.exe
2. Click "Install"
3. Wait for installation (10-15 minutes)
4. Click "Restart" if prompted, or "Close"

Method 2: Silent Installation (Command Line)

    # Silent install
    C:\Temp\SSMS-Setup-ENU.exe /install /quiet /norestart

    # Silent install with log
    C:\Temp\SSMS-Setup-ENU.exe /install /quiet /norestart /log C:\Temp\ssms_install.log

STEP 4.3: INSTALL SSMS ON HOST MACHINE
--------------------------------------

1. Download SSMS on your host machine (same URL)
2. Run installer and follow GUI prompts
3. Install completes in 10-15 minutes

STEP 4.4: VERIFY SSMS INSTALLATION
----------------------------------

1. Open Start Menu
2. Search for "SQL Server Management Studio"
3. Launch SSMS

================================================================================
                    PART 5: CONFIGURE REMOTE CONNECTIONS
================================================================================

STEP 5.1: OPEN SQL SERVER CONFIGURATION MANAGER
-----------------------------------------------

1. Open Start Menu
2. Search for "SQL Server 2019 Configuration Manager"
3. Or run: SQLServerManager15.msc

STEP 5.2: ENABLE TCP/IP FOR DEFAULT INSTANCE
--------------------------------------------

1. In left panel, expand "SQL Server Network Configuration"
2. Click "Protocols for MSSQLSERVER"
3. In right panel:
   - Right-click "TCP/IP" -> Enable
   - Right-click "TCP/IP" -> Properties

4. TCP/IP Properties:

   Protocol tab:
   - Enabled: Yes
   - Listen All: Yes

   IP Addresses tab:
   - Scroll to "IPAll" section at bottom
   - TCP Dynamic Ports: (clear this field - leave empty)
   - TCP Port: 1433

5. Click OK

STEP 5.3: ENABLE TCP/IP FOR NAMED INSTANCE
------------------------------------------

1. Click "Protocols for YOURINSTANCE"
2. Right-click "TCP/IP" -> Enable
3. Right-click "TCP/IP" -> Properties

4. TCP/IP Properties:

   IP Addresses tab -> IPAll:
   - TCP Dynamic Ports: (leave as is for dynamic, OR clear and set static)
   - TCP Port: 1434 (if using static port)

   For dynamic port (recommended for named instances):
   - Leave "TCP Dynamic Ports" with its assigned value
   - Leave "TCP Port" empty
   - SQL Server Browser will advertise the dynamic port

5. Click OK

STEP 5.4: CONFIGURE SQL SERVER BROWSER SERVICE
----------------------------------------------

SQL Server Browser is required for named instance discovery:

1. In left panel, click "SQL Server Services"
2. Right-click "SQL Server Browser" -> Properties
3. Service tab:
   - Start Mode: Automatic
4. Click OK
5. Right-click "SQL Server Browser" -> Start

PowerShell:

    Set-Service -Name "SQLBrowser" -StartupType Automatic
    Start-Service -Name "SQLBrowser"

STEP 5.5: RESTART SQL SERVER SERVICES
-------------------------------------

After changing network configuration, restart services:

SQL Server Configuration Manager:
1. Click "SQL Server Services"
2. Right-click "SQL Server (MSSQLSERVER)" -> Restart
3. Right-click "SQL Server (YOURINSTANCE)" -> Restart

PowerShell:

    Restart-Service -Name "MSSQLSERVER" -Force
    Restart-Service -Name "MSSQL`$YOURINSTANCE" -Force

STEP 5.6: CONFIGURE WINDOWS FIREWALL
------------------------------------

Method 1: Using Windows Firewall GUI

1. Open "Windows Defender Firewall with Advanced Security"
2. Click "Inbound Rules" -> "New Rule..."

Rule 1: SQL Server Default Instance (Port 1433)
- Rule Type: Port
- Protocol: TCP
- Specific local ports: 1433
- Action: Allow the connection
- Profile: All (Domain, Private, Public)
- Name: SQL Server Default Instance (TCP 1433)

Rule 2: SQL Server Named Instance (if static port 1434)
- Rule Type: Port
- Protocol: TCP
- Specific local ports: 1434
- Action: Allow the connection
- Profile: All
- Name: SQL Server Named Instance (TCP 1434)

Rule 3: SQL Server Browser (UDP 1434)
- Rule Type: Port
- Protocol: UDP
- Specific local ports: 1434
- Action: Allow the connection
- Profile: All
- Name: SQL Server Browser (UDP 1434)

Method 2: Using PowerShell

    # Allow SQL Server Default Instance (TCP 1433)
    New-NetFirewallRule -DisplayName "SQL Server Default Instance (TCP 1433)" `
        -Direction Inbound -Protocol TCP -LocalPort 1433 -Action Allow

    # Allow SQL Server Named Instance (TCP 1434) - if using static port
    New-NetFirewallRule -DisplayName "SQL Server Named Instance (TCP 1434)" `
        -Direction Inbound -Protocol TCP -LocalPort 1434 -Action Allow

    # Allow SQL Server Browser (UDP 1434)
    New-NetFirewallRule -DisplayName "SQL Server Browser (UDP 1434)" `
        -Direction Inbound -Protocol UDP -LocalPort 1434 -Action Allow

    # Alternative: Allow SQL Server executables (for dynamic ports)
    New-NetFirewallRule -DisplayName "SQL Server Database Engine" `
        -Direction Inbound -Program "C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\MSSQL\Binn\sqlservr.exe" -Action Allow

    New-NetFirewallRule -DisplayName "SQL Server Database Engine (Named)" `
        -Direction Inbound -Program "C:\Program Files\Microsoft SQL Server\MSSQL15.YOURINSTANCE\MSSQL\Binn\sqlservr.exe" -Action Allow

Method 3: Using netsh

    netsh advfirewall firewall add rule name="SQL Server TCP 1433" dir=in action=allow protocol=TCP localport=1433
    netsh advfirewall firewall add rule name="SQL Server TCP 1434" dir=in action=allow protocol=TCP localport=1434
    netsh advfirewall firewall add rule name="SQL Browser UDP 1434" dir=in action=allow protocol=UDP localport=1434

================================================================================
                    PART 6: TEST REMOTE CONNECTIONS
================================================================================

STEP 6.1: TEST LOCAL CONNECTIONS FIRST (ON VM)
----------------------------------------------

Open SSMS on the VM:

1. Connect to Default Instance:
   - Server name: localhost
   - Authentication: SQL Server Authentication
   - Login: sa
   - Password: [your sa password]
   - Click Connect

2. Connect to Named Instance:
   - Server name: localhost\YOURINSTANCE
   - Authentication: SQL Server Authentication
   - Login: sa
   - Password: [your sa password]
   - Click Connect

STEP 6.2: GET VM IP ADDRESS
---------------------------

On the VM, run:

    ipconfig

Note the IPv4 address (e.g., 192.168.1.100)

STEP 6.3: TEST REMOTE CONNECTION WITH SSMS (HOST MACHINE)
---------------------------------------------------------

Open SSMS on your host machine:

1. Connect to Default Instance:
   - Server name: 192.168.1.100 (VM IP address)
   - Authentication: SQL Server Authentication
   - Login: sa
   - Password: [your sa password]
   - Click Connect

2. Connect to Named Instance:
   - Server name: 192.168.1.100\YOURINSTANCE
   - Authentication: SQL Server Authentication
   - Login: sa
   - Password: [your sa password]
   - Click Connect

If using static port for named instance:
   - Server name: 192.168.1.100,1434

STEP 6.4: TEST REMOTE CONNECTION WITH POWERSHELL
------------------------------------------------

First, install SqlServer PowerShell module (if not installed):

    Install-Module -Name SqlServer -AllowClobber -Force

Test connection to Default Instance:

    # Using Invoke-Sqlcmd
    $serverName = "192.168.1.100"
    $username = "sa"
    $password = "P@ssw0rd123!"

    Invoke-Sqlcmd -ServerInstance $serverName `
        -Username $username `
        -Password $password `
        -Query "SELECT @@SERVERNAME AS ServerName, @@VERSION AS Version"

Test connection to Named Instance:

    $serverName = "192.168.1.100\YOURINSTANCE"
    $username = "sa"
    $password = "P@ssw0rd123!"

    Invoke-Sqlcmd -ServerInstance $serverName `
        -Username $username `
        -Password $password `
        -Query "SELECT @@SERVERNAME AS ServerName, @@VERSION AS Version"

Alternative using .NET SqlConnection:

    # Connect to Default Instance
    $connectionString = "Server=192.168.1.100;Database=master;User Id=sa;Password=P@ssw0rd123!;"
    $connection = New-Object System.Data.SqlClient.SqlConnection($connectionString)

    try {
        $connection.Open()
        Write-Host "Connected to Default Instance successfully!" -ForegroundColor Green

        $command = $connection.CreateCommand()
        $command.CommandText = "SELECT @@SERVERNAME"
        $result = $command.ExecuteScalar()
        Write-Host "Server Name: $result"
    }
    catch {
        Write-Host "Connection failed: $_" -ForegroundColor Red
    }
    finally {
        $connection.Close()
    }

    # Connect to Named Instance
    $connectionString = "Server=192.168.1.100\YOURINSTANCE;Database=master;User Id=sa;Password=P@ssw0rd123!;"
    $connection = New-Object System.Data.SqlClient.SqlConnection($connectionString)

    try {
        $connection.Open()
        Write-Host "Connected to Named Instance successfully!" -ForegroundColor Green

        $command = $connection.CreateCommand()
        $command.CommandText = "SELECT @@SERVERNAME"
        $result = $command.ExecuteScalar()
        Write-Host "Server Name: $result"
    }
    catch {
        Write-Host "Connection failed: $_" -ForegroundColor Red
    }
    finally {
        $connection.Close()
    }

STEP 6.5: VERIFY INSTANCE INFORMATION
-------------------------------------

Run these queries after connecting to verify configuration:

    -- Check instance name and version
    SELECT
        @@SERVERNAME AS ServerName,
        @@SERVICENAME AS InstanceName,
        @@VERSION AS Version

    -- Check authentication mode
    SELECT
        CASE SERVERPROPERTY('IsIntegratedSecurityOnly')
            WHEN 0 THEN 'Mixed Mode'
            WHEN 1 THEN 'Windows Authentication Only'
        END AS AuthenticationMode

    -- Check database file locations
    SELECT
        name AS DatabaseName,
        physical_name AS FilePath,
        type_desc AS FileType
    FROM sys.master_files
    ORDER BY database_id, type

    -- Check default data/log/backup paths
    EXEC xp_instance_regread
        N'HKEY_LOCAL_MACHINE',
        N'Software\Microsoft\MSSQLServer\MSSQLServer',
        N'DefaultData'

    EXEC xp_instance_regread
        N'HKEY_LOCAL_MACHINE',
        N'Software\Microsoft\MSSQLServer\MSSQLServer',
        N'DefaultLog'

    EXEC xp_instance_regread
        N'HKEY_LOCAL_MACHINE',
        N'Software\Microsoft\MSSQLServer\MSSQLServer',
        N'BackupDirectory'

================================================================================
                    PART 7: TROUBLESHOOTING
================================================================================

PROBLEM: Cannot connect remotely
--------------------------------

1. Verify SQL Server services are running:
   Get-Service | Where-Object { $_.Name -like "MSSQL*" }

2. Verify TCP/IP is enabled in SQL Server Configuration Manager

3. Verify SQL Server Browser is running:
   Get-Service SQLBrowser

4. Verify firewall rules:
   Get-NetFirewallRule | Where-Object { $_.DisplayName -like "*SQL*" }

5. Test port connectivity from host:
   Test-NetConnection -ComputerName 192.168.1.100 -Port 1433

6. Check SQL Server error log:
   Get-Content "C:\SQLServer\MSSQLSERVER\Log\ERRORLOG" -Tail 50

PROBLEM: Named instance not found
---------------------------------

1. Ensure SQL Server Browser is running
2. Try connecting with explicit port: SERVER\INSTANCE,PORT
3. Check if dynamic port is assigned in Configuration Manager

PROBLEM: Login failed for user 'sa'
-----------------------------------

1. Verify Mixed Mode is enabled
2. Verify sa account is enabled:

   ALTER LOGIN sa ENABLE;
   ALTER LOGIN sa WITH PASSWORD = 'NewP@ssw0rd123!';

3. Check SQL Server error log for detailed error

PROBLEM: Command line installation failed
-----------------------------------------

1. Check setup log files:
   C:\Program Files\Microsoft SQL Server\150\Setup Bootstrap\Log\

2. Common issues:
   - Missing /IACCEPTSQLSERVERLICENSETERMS
   - Invalid password (doesn't meet complexity)
   - Directory doesn't exist
   - Previous installation remnants

================================================================================
                    SUMMARY
================================================================================

Installation Completed:
-----------------------
+------------------+-------------------+------------------+
| Component        | Instance Name     | Status           |
+------------------+-------------------+------------------+
| SQL Server       | MSSQLSERVER       | Default Instance |
| (GUI Install)    |                   | Port 1433        |
+------------------+-------------------+------------------+
| SQL Server       | YOURINSTANCE      | Named Instance   |
| (CMD Install)    |                   | Dynamic Port     |
+------------------+-------------------+------------------+
| SSMS             | N/A               | VM + Host        |
+------------------+-------------------+------------------+

Custom File Locations:
----------------------
Default Instance (MSSQLSERVER):
- Data: C:\SQLServer\MSSQLSERVER\Data
- Log:  C:\SQLServer\MSSQLSERVER\Log
- Backup: C:\SQLServer\MSSQLSERVER\Backup

Named Instance (YOURINSTANCE):
- Data: C:\SQLServer\YOURINSTANCE\Data
- Log:  C:\SQLServer\YOURINSTANCE\Log
- Backup: C:\SQLServer\YOURINSTANCE\Backup

Connection Strings:
-------------------
Default Instance:
- SSMS: 192.168.1.100
- Connection String: Server=192.168.1.100;Database=master;User Id=sa;Password=xxx;

Named Instance:
- SSMS: 192.168.1.100\YOURINSTANCE
- Connection String: Server=192.168.1.100\YOURINSTANCE;Database=master;User Id=sa;Password=xxx;

Key Services:
-------------
- SQL Server (MSSQLSERVER) - Default Instance
- SQL Server (YOURINSTANCE) - Named Instance
- SQL Server Agent (MSSQLSERVER) - Agent for Default
- SQL Server Agent (YOURINSTANCE) - Agent for Named
- SQL Server Browser - Instance Discovery

Firewall Ports:
---------------
- TCP 1433 - Default Instance
- TCP 1434 - Named Instance (if static)
- UDP 1434 - SQL Server Browser

