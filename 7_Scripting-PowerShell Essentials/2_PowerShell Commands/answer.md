# POWERSHELL COMMANDS - ANSWER

This guide covers PowerShell cmdlets, filtering processes, finding commands,
and using the PowerShell help system.


## POWERSHELL CMDLET STRUCTURE

PowerShell cmdlets follow a Verb-Noun naming convention:

```
Verb-Noun
```

Common verbs:
| Verb    | Description              | Example           |
|---------|--------------------------|-------------------|
| Get     | Retrieve data            | Get-Process       |
| Set     | Modify data              | Set-Location      |
| New     | Create something new     | New-Item          |
| Remove  | Delete something         | Remove-Item       |
| Start   | Begin an operation       | Start-Service     |
| Stop    | End an operation         | Stop-Process      |
| Update  | Refresh/update           | Update-Help       |


## TASK 1: Get Processes Starting with C

Open PowerShell and run:

```powershell
Get-Process -Name C*
```

Or using Where-Object filter:

```powershell
Get-Process | Where-Object { $_.Name -like "C*" }
```

Or shorter syntax:

```powershell
Get-Process | Where Name -like "C*"
```

> **Screenshot 1**
>
> Show: Get-Process -Name C* command
> Expected: Command entered in PowerShell

Expected output (examples):

```
Handles  NPM(K)    PM(K)      WS(K)     CPU(s)     Id  SI ProcessName
-------  ------    -----      -----     ------     --  -- -----------
    234      15     3456       8912       0.23   1234   1 Calculator
    567      32    12345      45678       1.45   2345   1 Chrome
    123      10     2345       5678       0.12   3456   1 cmd
    ...
```

> **Screenshot 2**
>
> Show: Output of processes starting with C
> Expected: List of processes like Chrome, cmd, etc.

Understanding the command:

```powershell
Get-Process      # Cmdlet to get running processes
-Name C*         # Filter parameter: Name starts with C
*                # Wildcard: matches any characters after C
```

Alternative methods:

```powershell
# Case-sensitive match (capital C only)
Get-Process | Where-Object { $_.Name -clike "C*" }

# Get specific properties only
Get-Process -Name C* | Select-Object Name, Id, CPU

# Sort by CPU usage
Get-Process -Name C* | Sort-Object CPU -Descending
```

> **Screenshot 3**
>
> Show: Alternative filtering methods
> Expected: Different output formats


## TASK 2: Find Cmdlets for Windows Services

Use Get-Command to find service-related cmdlets:

```powershell
Get-Command -Noun Service
```

> **Screenshot 4**
>
> Show: Get-Command -Noun Service
> Expected: Command entered in PowerShell

Expected output:

```
CommandType     Name                       Version    Source
-----------     ----                       -------    ------
Cmdlet          Get-Service                3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          New-Service                3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          Restart-Service            3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          Resume-Service             3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          Set-Service                3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          Start-Service              3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          Stop-Service               3.1.0.0    Microsoft.PowerShell.Management
Cmdlet          Suspend-Service            3.1.0.0    Microsoft.PowerShell.Management
```

> **Screenshot 5**
>
> Show: List of service cmdlets
> Expected: All service-related cmdlets displayed

Alternative: Search by keyword

```powershell
Get-Command *Service*
```

Or search with wildcard:

```powershell
Get-Command -Name *-Service
```

Understanding the command:

```powershell
Get-Command      # Cmdlet to find available commands
-Noun Service    # Filter by noun part of cmdlet name
```

> **Screenshot 6**
>
> Show: Alternative search with *Service*
> Expected: More results including related commands


## TASK 3: Get Online Help for Get-ChildItem

Open online help in browser:

```powershell
Get-Help Get-ChildItem -Online
```

> **Screenshot 7**
>
> Show: Get-Help Get-ChildItem -Online command
> Expected: Command entered in PowerShell

This will open your default web browser with the official
Microsoft documentation page for Get-ChildItem.

> **Screenshot 8**
>
> Show: Browser with Get-ChildItem documentation
> Expected: Microsoft Docs page opened

Alternative help options:

```powershell
# Basic help
Get-Help Get-ChildItem

# Detailed help
Get-Help Get-ChildItem -Detailed

# Full help with examples
Get-Help Get-ChildItem -Full

# Show only examples
Get-Help Get-ChildItem -Examples

# Show in separate window
Get-Help Get-ChildItem -ShowWindow
```

> **Screenshot 9**
>
> Show: Get-Help with -Examples parameter
> Expected: Usage examples displayed

Understanding Get-ChildItem:

```powershell
Get-ChildItem    # Lists items in a location (like 'dir' or 'ls')

# Examples:
Get-ChildItem                    # List current directory
Get-ChildItem C:\Windows         # List specific directory
Get-ChildItem -Recurse           # List recursively
Get-ChildItem *.txt              # List .txt files only
```


## TASK 4: Update Help for New-Item Cmdlet

Run PowerShell as Administrator first!

```powershell
Update-Help -Module Microsoft.PowerShell.Management -Force
```

Or update help for specific cmdlet's module:

```powershell
# First, find which module contains New-Item
Get-Command New-Item

# Output shows: Microsoft.PowerShell.Management

# Then update that module's help
Update-Help -Module Microsoft.PowerShell.Management
```

> **Screenshot 10**
>
> Show: Update-Help command
> Expected: Command running in Admin PowerShell

Expected output during update:

```
# Progress bars and/or completion message
# May show warnings for modules that can't be updated
```

> **Screenshot 11**
>
> Show: Help update progress/completion
> Expected: Update successful or progress shown

Alternative: Update all help files

```powershell
Update-Help -Force
```

Note: -Force overwrites existing help files even if current.

Verify help is updated:

```powershell
Get-Help New-Item -Full
```

> **Screenshot 12**
>
> Show: Detailed help for New-Item after update
> Expected: Full documentation displayed


## GET-COMMAND OPTIONS

| Parameter      | Description                              | Example              |
|----------------|------------------------------------------|----------------------|
| -Name          | Filter by command name                   | Get-Command -Name *Process* |
| -Noun          | Filter by noun part                      | Get-Command -Noun Service |
| -Verb          | Filter by verb part                      | Get-Command -Verb Get |
| -Module        | Filter by module                         | Get-Command -Module ActiveDirectory |
| -CommandType   | Filter by type (Cmdlet, Function, etc.)  | Get-Command -CommandType Cmdlet |

Examples:

```powershell
# Find all "Get" commands
Get-Command -Verb Get

# Find all commands in a module
Get-Command -Module Microsoft.PowerShell.Management

# Find aliases
Get-Command -CommandType Alias
```


## GET-HELP OPTIONS

| Parameter      | Description                              |
|----------------|------------------------------------------|
| -Detailed      | Shows detailed help including parameters |
| -Full          | Shows complete help documentation        |
| -Examples      | Shows only usage examples                |
| -Online        | Opens help in web browser                |
| -ShowWindow    | Opens help in separate GUI window        |
| -Parameter     | Shows help for specific parameter        |

Examples:

```powershell
Get-Help Get-Process -Detailed
Get-Help Get-Process -Examples
Get-Help Get-Process -Parameter Name
Get-Help Get-Process -ShowWindow
```


## COMMON FILTERING TECHNIQUES

Using Where-Object:

```powershell
# Processes using more than 100MB memory
Get-Process | Where-Object { $_.WorkingSet -gt 100MB }

# Services that are running
Get-Service | Where-Object { $_.Status -eq "Running" }

# Files modified today
Get-ChildItem | Where-Object { $_.LastWriteTime -gt (Get-Date).Date }
```

Comparison operators:

| Operator | Description              | Example                    |
|----------|--------------------------|----------------------------|
| -eq      | Equal                    | $_.Status -eq "Running"    |
| -ne      | Not equal                | $_.Status -ne "Stopped"    |
| -gt      | Greater than             | $_.CPU -gt 10              |
| -lt      | Less than                | $_.Count -lt 5             |
| -like    | Wildcard match           | $_.Name -like "C*"         |
| -match   | Regex match              | $_.Name -match "^[Cc]"     |


## TROUBLESHOOTING

Problem: "Get-Process : Cannot find a process with name 'C*'"
Solution: This means no processes start with C currently
    - Try lowercase: Get-Process -Name c*
    - Or use case-insensitive: Get-Process | Where Name -like "C*"

Problem: "Update-Help : Access is denied"
Solution: Run PowerShell as Administrator
    - Right-click PowerShell -> Run as Administrator

Problem: "Update-Help : Failed to update Help for module"
Solution: Some modules don't have updateable help
    - Use -Force parameter
    - Try: Update-Help -Force -ErrorAction SilentlyContinue

Problem: Online help doesn't open
Solution: Check internet connection and default browser
    - Try: Start-Process "https://docs.microsoft.com"

Problem: Help shows minimal information
Solution: Update help files first

```powershell
Update-Help -Force
```

> **Screenshot 13**
>
> Show: All tasks completed
> Expected: Summary of successful commands


## SELF-REVIEW CHECKLIST

- [ ] Get-Process -Name C* returns processes starting with C
- [ ] Understand wildcard (*) usage in PowerShell
- [ ] Get-Command -Noun Service lists all service cmdlets
- [ ] Know how to search for cmdlets by verb or noun
- [ ] Get-Help -Online opens browser documentation
- [ ] Understand different Get-Help parameters
- [ ] Update-Help successfully updated help files
- [ ] PowerShell was run as Administrator for Update-Help
- [ ] Understand Where-Object filtering
- [ ] Know common comparison operators (-eq, -like, -gt, etc.)
