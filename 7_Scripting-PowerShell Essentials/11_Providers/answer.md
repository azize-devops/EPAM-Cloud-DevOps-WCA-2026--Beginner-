# PROVIDERS - ANSWER

This guide covers PowerShell providers, which allow accessing different
data stores using consistent cmdlets like Get-ChildItem, Set-Location, etc.


## PROVIDERS OVERVIEW

View available providers:

```powershell
Get-PSProvider
```

Common providers:

| Provider     | Drive(s)      | Description                    |
|--------------|---------------|--------------------------------|
| FileSystem   | C:, D:        | File system access             |
| Registry     | HKLM:, HKCU:  | Windows Registry               |
| Environment  | Env:          | Environment variables          |
| Variable     | Variable:     | PowerShell variables           |
| Function     | Function:     | PowerShell functions           |
| Alias        | Alias:        | Command aliases                |
| Certificate  | Cert:         | Certificate store              |


## TASK 1: Navigate Registry Provider

Goal: Access and read Windows Registry using provider cmdlets.

Switch to registry drive:

```powershell
Set-Location HKLM:
```

Or:

```powershell
cd HKLM:
```

> **Screenshot 1**
>
> Show: Switch to HKLM drive
> Expected: Prompt shows HKLM:\

List registry keys:

```powershell
Get-ChildItem HKLM:\SOFTWARE
```

> **Screenshot 2**
>
> Show: Registry keys listed
> Expected: Software subkeys displayed

Navigate to specific key:

```powershell
Set-Location HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion
Get-ChildItem
```

> **Screenshot 3**
>
> Show: CurrentVersion key contents
> Expected: Subkeys listed

Read registry values:

```powershell
Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion
```

Expected output (partial):

```
ProgramFilesDir        : C:\Program Files
CommonFilesDir         : C:\Program Files\Common Files
ProductId              : XXXXX-XXXXX-XXXXX-XXXXX
...
```

> **Screenshot 4**
>
> Show: Registry values
> Expected: Property values displayed

Get specific value:

```powershell
(Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion).ProgramFilesDir
```

Expected output:

```
C:\Program Files
```

> **Screenshot 5**
>
> Show: Specific registry value
> Expected: Single value returned


## TASK 2: Environment Provider

Goal: Work with environment variables using the Env: provider.

List all environment variables:

```powershell
Get-ChildItem Env:
```

> **Screenshot 6**
>
> Show: Environment variables
> Expected: All variables listed

Get specific variable:

```powershell
Get-Item Env:PATH
```

Or:

```powershell
$env:PATH
```

> **Screenshot 7**
>
> Show: PATH variable
> Expected: PATH value displayed

Create/modify environment variable:

```powershell
$env:MY_VARIABLE = "Hello World"
Get-Item Env:MY_VARIABLE
```

> **Screenshot 8**
>
> Show: Create environment variable
> Expected: New variable created

Remove environment variable:

```powershell
Remove-Item Env:MY_VARIABLE
```


## TASK 3: Variable Provider

Goal: Access PowerShell variables through the Variable: provider.

List all variables:

```powershell
Get-ChildItem Variable:
```

> **Screenshot 9**
>
> Show: Variable provider
> Expected: All variables listed

Get specific variable:

```powershell
Get-Item Variable:PSVersionTable
```

Or directly:

```powershell
$PSVersionTable
```

> **Screenshot 10**
>
> Show: PSVersionTable
> Expected: Version info displayed

Variable properties:

```powershell
Get-Item Variable:ErrorActionPreference | Format-List *
```

> **Screenshot 11**
>
> Show: Variable properties
> Expected: Name, Value, Options displayed


## TASK 4: Function and Alias Providers

Goal: Work with functions and aliases as drives.

List all functions:

```powershell
Get-ChildItem Function:
```

> **Screenshot 12**
>
> Show: Function provider
> Expected: Functions listed

Get function definition:

```powershell
Get-Item Function:prompt
```

Or see the code:

```powershell
(Get-Item Function:prompt).Definition
```

> **Screenshot 13**
>
> Show: Function definition
> Expected: Prompt function code

List all aliases:

```powershell
Get-ChildItem Alias:
```

Find alias for command:

```powershell
Get-Alias -Definition Get-ChildItem
```

Expected output:

```
CommandType     Name
-----------     ----
Alias           dir -> Get-ChildItem
Alias           gci -> Get-ChildItem
Alias           ls -> Get-ChildItem
```

> **Screenshot 14**
>
> Show: Aliases for Get-ChildItem
> Expected: dir, gci, ls shown


## TASK 5: Certificate Provider

Goal: Access certificate store (Windows).

Navigate to certificate store:

```powershell
Set-Location Cert:\CurrentUser\My
Get-ChildItem
```

> **Screenshot 15**
>
> Show: User certificates
> Expected: Certificates listed

Or:

```powershell
Get-ChildItem Cert:\LocalMachine\Root | Select-Object Subject, NotAfter -First 5
```

> **Screenshot 16**
>
> Show: Root certificates
> Expected: CA certificates listed


## COMPLETE SOLUTION SCRIPT

```powershell
Write-Host "=== PowerShell Providers ===" -ForegroundColor Cyan

# List providers
Write-Host "`n[Available Providers]" -ForegroundColor Yellow
Get-PSProvider | Format-Table Name, Drives -AutoSize

# Registry
Write-Host "`n[Registry - Windows Version]" -ForegroundColor Yellow
$regPath = "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion"
Get-ItemProperty $regPath | Select-Object ProductName, CurrentBuild

# Environment
Write-Host "`n[Environment Variables]" -ForegroundColor Yellow
Get-ChildItem Env: | Select-Object Name -First 5

# Variables
Write-Host "`n[PowerShell Variables]" -ForegroundColor Yellow
Get-ChildItem Variable: | Select-Object Name -First 5

# Functions
Write-Host "`n[Functions]" -ForegroundColor Yellow
Get-ChildItem Function: | Select-Object Name -First 5

# Aliases
Write-Host "`n[Aliases for Get-ChildItem]" -ForegroundColor Yellow
Get-Alias -Definition Get-ChildItem | Format-Table -AutoSize
```

> **Screenshot 17**
>
> Show: Complete provider script
> Expected: All providers demonstrated


## PROVIDER CMDLETS

Common cmdlets work across providers:

| Cmdlet            | Alias | Description                    |
|-------------------|-------|--------------------------------|
| Get-ChildItem     | dir   | List items in location         |
| Get-Item          | gi    | Get single item                |
| Set-Item          | si    | Set item value                 |
| New-Item          | ni    | Create new item                |
| Remove-Item       | rm    | Delete item                    |
| Copy-Item         | cp    | Copy item                      |
| Move-Item         | mv    | Move item                      |
| Rename-Item       | rni   | Rename item                    |
| Get-ItemProperty  | gp    | Get item properties            |
| Set-ItemProperty  | sp    | Set item property              |
| Get-Location      | pwd   | Get current location           |
| Set-Location      | cd    | Change location                |
| Push-Location     | pushd | Save and change location       |
| Pop-Location      | popd  | Return to saved location       |


## REGISTRY PATHS

Common registry paths:

```powershell
# Current user settings
HKCU:\SOFTWARE\

# Machine-wide software
HKLM:\SOFTWARE\

# Windows version
HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion

# Installed programs (64-bit)
HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall

# Installed programs (32-bit on 64-bit)
HKLM:\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Uninstall

# Services
HKLM:\SYSTEM\CurrentControlSet\Services

# Environment variables
HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment
```


## TROUBLESHOOTING

Problem: "Cannot find path" in registry
Solution: Check path and use correct hive

```powershell
# Use HKLM: or HKCU: prefix
Get-ChildItem HKLM:\SOFTWARE  # Correct
Get-ChildItem "HKEY_LOCAL_MACHINE\SOFTWARE"  # Wrong
```

Problem: "Access denied" to registry key
Solution: Run PowerShell as Administrator

```powershell
# Start elevated PowerShell
Start-Process powershell -Verb RunAs
```

Problem: Certificate provider not available
Solution: Cert: only available on Windows

```powershell
if ($IsWindows) {
    Get-ChildItem Cert:\CurrentUser\My
}
```

Problem: Cannot set environment variable
Solution: Use $env: syntax

```powershell
# Set for current session
$env:MY_VAR = "value"

# Set permanently (user)
[Environment]::SetEnvironmentVariable("MY_VAR", "value", "User")

# Set permanently (machine - requires admin)
[Environment]::SetEnvironmentVariable("MY_VAR", "value", "Machine")
```

Problem: Variable not persisting
Solution: Variables in Variable: are session-only

```powershell
# For persistence, use profile
notepad $PROFILE
# Add: $MyVar = "persistent value"
```

Problem: Function changes lost after restart
Solution: Add functions to profile

```powershell
# Edit profile
notepad $PROFILE

# Add function definition there
```

> **Screenshot 18**
>
> Show: Summary of provider tasks
> Expected: All providers working


## SELF-REVIEW CHECKLIST

- [ ] Can list providers with Get-PSProvider
- [ ] Can navigate registry with HKLM: and HKCU:
- [ ] Can read registry values with Get-ItemProperty
- [ ] Can work with environment variables via Env:
- [ ] Can access PowerShell variables via Variable:
- [ ] Can view functions via Function:
- [ ] Can list aliases via Alias:
- [ ] Understands provider-agnostic cmdlets
- [ ] Knows common registry paths
- [ ] Can create and remove provider items
- [ ] Understands session vs persistent changes
