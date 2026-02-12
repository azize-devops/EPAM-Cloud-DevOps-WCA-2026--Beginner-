# POWERSHELL CONSOLE - ANSWER

This guide covers PowerShell execution policies, how to configure them,
and how to bypass restrictions when necessary.


================================================================================
                     EXECUTION POLICIES OVERVIEW
================================================================================

PowerShell execution policies control the conditions under which PowerShell
loads configuration files and runs scripts.

| Policy         | Description                                          |
|----------------|------------------------------------------------------|
| Restricted     | No scripts can run. PowerShell is interactive only. |
| AllSigned      | Only scripts signed by trusted publisher can run.   |
| RemoteSigned   | Local scripts run. Downloaded scripts need signing. |
| Unrestricted   | All scripts run. Warning for downloaded scripts.    |
| Bypass         | Nothing is blocked, no warnings or prompts.         |
| Undefined      | No execution policy set in current scope.           |

================================================================================


TASK 1: Set Execution Policy to RemoteSigned
---------------------------------------------

Step 1: Open PowerShell as Administrator

- Press Win+X and select "Windows PowerShell (Admin)"
- Or search "PowerShell", right-click, "Run as Administrator"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                   |
|                                                          |
|  Show: Opening PowerShell as Administrator               |
|  Expected: Right-click menu with "Run as Administrator"  |
+----------------------------------------------------------+

Step 2: Check current execution policy

    Get-ExecutionPolicy

+----------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                   |
|                                                          |
|  Show: Get-ExecutionPolicy command                       |
|  Expected: Current policy displayed (likely Restricted)  |
+----------------------------------------------------------+

Step 3: Set execution policy to RemoteSigned

    Set-ExecutionPolicy RemoteSigned

When prompted, type 'Y' and press Enter to confirm:

    Execution Policy Change
    The execution policy helps protect you from scripts that you do not trust.
    ...
    Do you want to change the execution policy?
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"): Y

+----------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                   |
|                                                          |
|  Show: Set-ExecutionPolicy RemoteSigned command          |
|  Expected: Confirmation prompt and policy change         |
+----------------------------------------------------------+

Step 4: Verify the change

    Get-ExecutionPolicy

Expected output:

    RemoteSigned

+----------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                   |
|                                                          |
|  Show: Verification of RemoteSigned policy               |
|  Expected: "RemoteSigned" displayed                      |
+----------------------------------------------------------+

Alternative: Set without confirmation prompt

    Set-ExecutionPolicy RemoteSigned -Force

Or specify scope:

    Set-ExecutionPolicy RemoteSigned -Scope LocalMachine


TASK 2: Restricted Policy with Bypass
--------------------------------------

Step 1: Set execution policy to Restricted

    Set-ExecutionPolicy Restricted -Force

+----------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                   |
|                                                          |
|  Show: Setting Restricted policy                         |
|  Expected: Policy set without errors                     |
+----------------------------------------------------------+

Step 2: Verify Restricted policy

    Get-ExecutionPolicy

Expected output:

    Restricted

+----------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                   |
|                                                          |
|  Show: Verification of Restricted policy                 |
|  Expected: "Restricted" displayed                        |
+----------------------------------------------------------+

Step 3: Create a test script (module1-task2.ps1)

Create a simple PowerShell script for testing:

    # In PowerShell or Notepad, create the file:
    Set-Content -Path "C:\module1-task2.ps1" -Value 'Write-Host "Hello from PowerShell script!"'

Or manually create module1-task2.ps1 with content:

    Write-Host "Hello from PowerShell script!"
    Write-Host "Script executed successfully!"
    Get-Date

+----------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                   |
|                                                          |
|  Show: Creating module1-task2.ps1 script                 |
|  Expected: Script file with test content                 |
+----------------------------------------------------------+

Step 4: Try running the script normally (will fail)

    .\module1-task2.ps1

Expected error:

    .\module1-task2.ps1 : File C:\module1-task2.ps1 cannot be loaded because
    running scripts is disabled on this system.
    ...
    + CategoryInfo          : SecurityError: (:) [], PSSecurityException
    + FullyQualifiedErrorId : UnauthorizedAccess

+----------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                   |
|                                                          |
|  Show: Script execution blocked                          |
|  Expected: UnauthorizedAccess error message              |
+----------------------------------------------------------+

Step 5: Open CMD (Command Prompt)

- Press Win+R, type "cmd", press Enter
- Or search "Command Prompt" in Start menu

+----------------------------------------------------------+
|                    >>> SCREENSHOT 9 <<<                   |
|                                                          |
|  Show: Opening Command Prompt                            |
|  Expected: CMD window opened                             |
+----------------------------------------------------------+

Step 6: Run script from CMD using Bypass

    powershell -ExecutionPolicy Bypass -File "C:\module1-task2.ps1"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 10 <<<                  |
|                                                          |
|  Show: Running script with Bypass from CMD               |
|  Expected: Command entered in CMD                        |
+----------------------------------------------------------+

Expected output:

    Hello from PowerShell script!
    Script executed successfully!
    [Current Date/Time]

+----------------------------------------------------------+
|                    >>> SCREENSHOT 11 <<<                  |
|                                                          |
|  Show: Script output in CMD                              |
|  Expected: Script runs successfully despite Restricted   |
+----------------------------------------------------------+


BYPASS COMMAND OPTIONS
----------------------

Different ways to bypass execution policy from CMD:

Method 1: Using -ExecutionPolicy Bypass

    powershell -ExecutionPolicy Bypass -File "script.ps1"

Method 2: Using -ExecutionPolicy Unrestricted

    powershell -ExecutionPolicy Unrestricted -File "script.ps1"

Method 3: Running script content directly

    powershell -Command "& {Get-Content script.ps1 | Invoke-Expression}"

Method 4: Encoding command in Base64

    powershell -EncodedCommand <Base64EncodedCommand>

+----------------------------------------------------------+
|                    >>> SCREENSHOT 12 <<<                  |
|                                                          |
|  Show: Different bypass methods                          |
|  Expected: Alternative commands demonstrated             |
+----------------------------------------------------------+


================================================================================
                     EXECUTION POLICY SCOPES
================================================================================

Policies can be set at different scopes (in order of precedence):

| Scope          | Description                                   |
|----------------|-----------------------------------------------|
| MachinePolicy  | Set by Group Policy for all users            |
| UserPolicy     | Set by Group Policy for current user         |
| Process        | Affects only current PowerShell session      |
| CurrentUser    | Affects only current user                    |
| LocalMachine   | Affects all users on current computer        |

View all scopes:

    Get-ExecutionPolicy -List

Example output:

            Scope ExecutionPolicy
            ----- ---------------
    MachinePolicy       Undefined
       UserPolicy       Undefined
          Process       Undefined
      CurrentUser       Undefined
     LocalMachine    RemoteSigned

Set policy for specific scope:

    Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
    Set-ExecutionPolicy Bypass -Scope Process

================================================================================


================================================================================
                     COMMON POWERSHELL COMMANDS
================================================================================

| Command                    | Description                          |
|----------------------------|--------------------------------------|
| Get-ExecutionPolicy        | View current execution policy        |
| Set-ExecutionPolicy        | Change execution policy              |
| Get-Help                   | Get help for commands                |
| Get-Command                | List available commands              |
| Get-Process                | List running processes               |
| Get-Service                | List Windows services                |
| Write-Host                 | Display output to console            |
| Get-Date                   | Get current date and time            |

================================================================================


================================================================================
                     POWERSHELL VS CMD COMMANDS
================================================================================

| Task                | CMD Command        | PowerShell Command      |
|---------------------|--------------------|-------------------------|
| List directory      | dir                | Get-ChildItem (or dir)  |
| Change directory    | cd                 | Set-Location (or cd)    |
| Clear screen        | cls                | Clear-Host (or cls)     |
| Copy file           | copy               | Copy-Item               |
| Delete file         | del                | Remove-Item             |
| Show file content   | type               | Get-Content             |
| Current directory   | cd                 | Get-Location (or pwd)   |

================================================================================


================================================================================
                             TROUBLESHOOTING
================================================================================

Problem: "Access is denied" when setting execution policy
Solution: Run PowerShell as Administrator
    - Right-click PowerShell
    - Select "Run as Administrator"

Problem: Policy change doesn't persist after restart
Solution: Set policy at LocalMachine scope
    Set-ExecutionPolicy RemoteSigned -Scope LocalMachine

Problem: Group Policy overrides local settings
Solution: Check MachinePolicy and UserPolicy scopes
    Get-ExecutionPolicy -List
    - Contact your IT admin if policies are set by GPO

Problem: Script still won't run after setting RemoteSigned
Solution: Unblock downloaded script files
    Unblock-File -Path "C:\path\to\script.ps1"

Problem: "powershell is not recognized" in CMD
Solution: PowerShell not in PATH or not installed
    - Use full path: C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe

+----------------------------------------------------------+
|                    >>> SCREENSHOT 13 <<<                  |
|                                                          |
|  Show: Complete task workflow                            |
|  Expected: Both tasks completed successfully             |
+----------------------------------------------------------+


================================================================================
                     SELF-REVIEW CHECKLIST
================================================================================

[ ] PowerShell opened as Administrator
[ ] Current execution policy checked with Get-ExecutionPolicy
[ ] Execution policy set to RemoteSigned
[ ] Verified RemoteSigned policy is active
[ ] Execution policy set to Restricted
[ ] Created module1-task2.ps1 test script
[ ] Confirmed script fails to run under Restricted policy
[ ] Opened CMD (Command Prompt)
[ ] Ran script using: powershell -ExecutionPolicy Bypass -File
[ ] Script executed successfully despite Restricted policy
[ ] Understand different execution policy levels
[ ] Understand execution policy scopes

================================================================================

