# WMI AND CIM - ANSWER

This guide covers Windows Management Instrumentation (WMI) and
Common Information Model (CIM) for system management and querying.


## WMI AND CIM OVERVIEW

WMI vs CIM:

```
WMI (Windows Management Instrumentation)
    - Legacy technology
    - Uses DCOM protocol
    - Get-WmiObject cmdlet (deprecated)

CIM (Common Information Model)
    - Modern replacement for WMI
    - Uses WS-Man protocol (WinRM)
    - Get-CimInstance cmdlet (recommended)
```

Common CIM cmdlets:

```powershell
Get-CimInstance      # Query CIM classes
Get-CimClass         # Get class information
Invoke-CimMethod     # Call methods
New-CimSession       # Create remote session
Remove-CimSession    # Close remote session
```

Common WMI/CIM classes:

```
Win32_Processor           CPU information
Win32_PhysicalMemory      RAM modules
Win32_OperatingSystem     OS information
Win32_LogicalDisk         Disk drives
Win32_Service             Windows services
Win32_QuickFixEngineering Installed hotfixes
Win32_LogonSession        Logon sessions
Win32_Account             User accounts
```


## TASK 1: Get Remote Computer System Information

Goal: Retrieve CPU, RAM, disk, OS, hotfixes, and services from remote computer.

Complete script1.ps1:

```powershell
param(
    [Parameter(Mandatory=$false)]
    [string]$ComputerName = $env:COMPUTERNAME,

    [System.Management.Automation.PSCredential]$Credential
)

Write-Host "=" * 60 -ForegroundColor Cyan
Write-Host "SYSTEM INFORMATION REPORT" -ForegroundColor Cyan
Write-Host "Computer: $ComputerName" -ForegroundColor Cyan
Write-Host "Date: $(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')" -ForegroundColor Cyan
Write-Host "=" * 60 -ForegroundColor Cyan

$cimParams = @{}
if ($ComputerName -ne $env:COMPUTERNAME) {
    $cimParams['ComputerName'] = $ComputerName
}

# CPU INFORMATION
Write-Host "`n[CPU INFORMATION]" -ForegroundColor Yellow
Write-Host "-" * 40

$cpu = Get-CimInstance -ClassName Win32_Processor @cimParams
Write-Host "Model:          $($cpu.Name)"
Write-Host "Cores:          $($cpu.NumberOfCores)"
Write-Host "Logical CPUs:   $($cpu.NumberOfLogicalProcessors)"

# RAM INFORMATION
Write-Host "`n[RAM INFORMATION]" -ForegroundColor Yellow
Write-Host "-" * 40

$os = Get-CimInstance -ClassName Win32_OperatingSystem @cimParams
$totalRAM = [math]::Round($os.TotalVisibleMemorySize / 1MB, 2)
$freeRAM = [math]::Round($os.FreePhysicalMemory / 1MB, 2)
Write-Host "Total RAM:      $totalRAM GB"
Write-Host "Free RAM:       $freeRAM GB"

# DISK INFORMATION
Write-Host "`n[DISK INFORMATION]" -ForegroundColor Yellow
Write-Host "-" * 40

$disks = Get-CimInstance -ClassName Win32_LogicalDisk @cimParams |
    Where-Object { $_.DriveType -eq 3 }

foreach ($disk in $disks) {
    $totalGB = [math]::Round($disk.Size / 1GB, 2)
    $freeGB = [math]::Round($disk.FreeSpace / 1GB, 2)
    Write-Host "Drive $($disk.DeviceID) - Total: $totalGB GB, Free: $freeGB GB"
}

# OPERATING SYSTEM
Write-Host "`n[OPERATING SYSTEM]" -ForegroundColor Yellow
Write-Host "-" * 40

Write-Host "OS Name:        $($os.Caption)"
Write-Host "Version:        $($os.Version)"

# INSTALLED HOT FIXES
Write-Host "`n[INSTALLED HOT FIXES]" -ForegroundColor Yellow
Write-Host "-" * 40

$hotfixes = Get-CimInstance -ClassName Win32_QuickFixEngineering @cimParams |
    Sort-Object InstalledOn -Descending | Select-Object -First 5

foreach ($fix in $hotfixes) {
    Write-Host "  $($fix.HotFixID) - $($fix.Description)"
}

$totalHotfixes = (Get-CimInstance -ClassName Win32_QuickFixEngineering @cimParams).Count
Write-Host "Total hotfixes: $totalHotfixes"

# STOPPED SERVICES
Write-Host "`n[STOPPED SERVICES]" -ForegroundColor Yellow
Write-Host "-" * 40

$stoppedServices = Get-CimInstance -ClassName Win32_Service @cimParams |
    Where-Object { $_.State -eq 'Stopped' -and $_.StartMode -eq 'Auto' }

Write-Host "Auto-start services that are stopped: $($stoppedServices.Count)"

Write-Host "`n" + "=" * 60 -ForegroundColor Cyan
Write-Host "END OF REPORT" -ForegroundColor Cyan
```

> **Screenshot 1**
>
> Show: script1.ps1 content
> Expected: Complete system info script

Test the script (local computer):

```powershell
.\script1.ps1
```

Test the script (remote computer):

```powershell
.\script1.ps1 -ComputerName "Server01"
```

> **Screenshot 2**
>
> Show: Script execution
> Expected: System information displayed


## TASK 2: Get Logon Sessions with User Information

Goal: Get interactive and remote logon sessions with associated user info.

Logon Types reference:

```
0  - System
2  - Interactive (local logon)
3  - Network
4  - Batch
5  - Service
10 - RemoteInteractive (RDP)
11 - CachedInteractive
```

Complete script2.ps1:

```powershell
param(
    [Parameter(Mandatory=$false)]
    [string]$ComputerName = $env:COMPUTERNAME
)

Write-Host "=" * 60 -ForegroundColor Cyan
Write-Host "LOGON SESSIONS REPORT" -ForegroundColor Cyan
Write-Host "Computer: $ComputerName" -ForegroundColor Cyan
Write-Host "=" * 60 -ForegroundColor Cyan

$logonTypes = @{
    0  = "System"
    2  = "Interactive"
    3  = "Network"
    5  = "Service"
    10 = "RemoteInteractive"
    11 = "CachedInteractive"
}

$cimParams = @{}
if ($ComputerName -ne $env:COMPUTERNAME) {
    $cimParams['ComputerName'] = $ComputerName
}

# Get logon sessions - Interactive (2) and RemoteInteractive (10)
Write-Host "`n[INTERACTIVE AND REMOTE LOGON SESSIONS]" -ForegroundColor Yellow
Write-Host "-" * 50

$sessions = Get-CimInstance -ClassName Win32_LogonSession @cimParams |
    Where-Object { $_.LogonType -in @(2, 10) }

$sessionCount = 0
foreach ($session in $sessions) {
    $sessionCount++

    # Get associated user account
    $query = "ASSOCIATORS OF {Win32_LogonSession.LogonId='$($session.LogonId)'} WHERE AssocClass=Win32_LoggedOnUser"
    $user = Get-CimInstance -Query $query @cimParams

    Write-Host "`n--- Session $sessionCount ---" -ForegroundColor Green
    Write-Host "Session ID:     $($session.LogonId)"
    Write-Host "Logon Type:     $($session.LogonType) ($($logonTypes[$session.LogonType]))"
    Write-Host "Start Time:     $($session.StartTime)"

    if ($user) {
        Write-Host "User:           $($user.Domain)\$($user.Name)"
    }
}

# SESSION SUMMARY
Write-Host "`n" + "-" * 50 -ForegroundColor Yellow
Write-Host "[ALL LOGON SESSIONS SUMMARY]" -ForegroundColor Yellow

$allSessions = Get-CimInstance -ClassName Win32_LogonSession @cimParams

$allSessions | Group-Object LogonType | ForEach-Object {
    $typeName = $logonTypes[[int]$_.Name]
    if (-not $typeName) { $typeName = "Type $($_.Name)" }
    Write-Host "  $typeName : $($_.Count) sessions"
}

# CURRENTLY LOGGED ON USERS
Write-Host "`n[USER ACCOUNTS ON SYSTEM]" -ForegroundColor Yellow
Write-Host "-" * 50

$accounts = Get-CimInstance -ClassName Win32_UserAccount @cimParams |
    Where-Object { $_.LocalAccount -eq $true }

foreach ($acct in $accounts) {
    $status = if ($acct.Disabled) { "Disabled" } else { "Enabled" }
    Write-Host "  $($acct.Name) [$status]"
}

Write-Host "`n" + "=" * 60 -ForegroundColor Cyan
Write-Host "END OF REPORT" -ForegroundColor Cyan
```

> **Screenshot 3**
>
> Show: script2.ps1 content
> Expected: Complete logon session script

Test the script:

```powershell
.\script2.ps1
```

> **Screenshot 4**
>
> Show: Script execution
> Expected: Logon sessions displayed


## COMMON WMI/CIM CLASSES REFERENCE

| Class                        | Description                              |
|------------------------------|------------------------------------------|
| Win32_Processor              | CPU information                          |
| Win32_PhysicalMemory         | RAM modules                              |
| Win32_OperatingSystem        | OS details, memory totals                |
| Win32_LogicalDisk            | Disk drives and space                    |
| Win32_DiskDrive              | Physical disk drives                     |
| Win32_Service                | Windows services                         |
| Win32_Process                | Running processes                        |
| Win32_QuickFixEngineering    | Installed hotfixes                       |
| Win32_ComputerSystem         | Computer information                     |
| Win32_NetworkAdapterConfig   | Network configuration                    |
| Win32_LogonSession           | Logon sessions                           |
| Win32_LoggedOnUser           | Logged on users association              |
| Win32_UserAccount            | User accounts only                       |


## CIM CMDLETS REFERENCE

| Cmdlet              | Description                    | Example               |
|---------------------|--------------------------------|-----------------------|
| Get-CimInstance     | Query CIM class                | Get-CimInstance Win32_OS|
| Get-CimClass        | Get class metadata             | Get-CimClass Win32_*  |
| Invoke-CimMethod    | Call class method              | Invoke-CimMethod ...  |
| New-CimSession      | Create remote session          | New-CimSession -CN srv|
| Remove-CimSession   | Close session                  | Remove-CimSession $s  |

Remote connection:

```powershell
# Using ComputerName
Get-CimInstance Win32_OS -ComputerName "Server01"

# Using CimSession
$session = New-CimSession -ComputerName "Server01"
Get-CimInstance Win32_OS -CimSession $session
Remove-CimSession $session

# With credentials
$cred = Get-Credential
$session = New-CimSession -ComputerName "Server01" -Credential $cred
```


## WMI QUERY LANGUAGE (WQL)

Basic query:

```powershell
Get-CimInstance -Query "SELECT * FROM Win32_Service"
```

Filtered query:

```powershell
Get-CimInstance -Query "SELECT * FROM Win32_Service WHERE State='Running'"
```

Association query:

```powershell
Get-CimInstance -Query "ASSOCIATORS OF {Win32_LogonSession.LogonId='12345'}"
```


## LOGON TYPES REFERENCE

| Type | Name                | Description                              |
|------|---------------------|------------------------------------------|
| 0    | System              | System account logon                     |
| 2    | Interactive         | Local console logon                      |
| 3    | Network             | Network logon (file shares)              |
| 4    | Batch               | Scheduled task logon                     |
| 5    | Service             | Service account logon                    |
| 7    | Unlock              | Workstation unlock                       |
| 10   | RemoteInteractive   | RDP/Remote Desktop logon                 |
| 11   | CachedInteractive   | Cached credentials logon                 |


## TROUBLESHOOTING

Problem: "Access denied" on remote computer
Solution: Ensure WinRM is enabled and you have permissions

```powershell
# On remote computer (as admin)
Enable-PSRemoting -Force
winrm quickconfig
```

Problem: "RPC server is unavailable"
Solution: Check firewall and WMI service

```powershell
# Allow WMI through firewall
netsh advfirewall firewall set rule group="Windows Management Instrumentation (WMI)" new enable=yes
```

Problem: CIM session fails with DCOM error
Solution: Use explicit DCOM protocol option

```powershell
$opt = New-CimSessionOption -Protocol Dcom
$session = New-CimSession -ComputerName "Server01" -SessionOption $opt
```

Problem: "Invalid class" error
Solution: Verify class name exists

```powershell
Get-CimClass -ClassName Win32_* | Select-Object CimClassName
```

Problem: StartTime is null for some sessions
Solution: System sessions may not have start time

```powershell
if ($session.StartTime) { $session.StartTime } else { "N/A" }
```

> **Screenshot 5**
>
> Show: Summary of WMI/CIM tasks
> Expected: Both tasks completed successfully


## SELF-REVIEW CHECKLIST

- [ ] Task 1: Get-CimInstance used to query Win32 classes
- [ ] Task 1: Win32_Processor returns CPU model and clock speed
- [ ] Task 1: Win32_OperatingSystem returns RAM info
- [ ] Task 1: Win32_LogicalDisk returns disk space
- [ ] Task 1: Win32_QuickFixEngineering returns hotfixes
- [ ] Task 1: Win32_Service filtered for stopped services
- [ ] Task 1: Script works with -ComputerName parameter
- [ ] Task 2: Win32_LogonSession returns logon sessions
- [ ] Task 2: Filtered for LogonType 2 and 10
- [ ] Task 2: ASSOCIATORS query links session to user
- [ ] Task 2: Each session shows associated user information
- [ ] Understand WMI vs CIM differences
- [ ] Know how to create CIM sessions for remote queries
- [ ] Understand WQL query syntax
- [ ] Know common Win32 class names
