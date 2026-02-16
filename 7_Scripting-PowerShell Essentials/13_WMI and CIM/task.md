# WMI and CIM - Task

## Goals

- Understand WMI (Windows Management Instrumentation)
- Use CIM cmdlets for system management
- Query remote computer information

## Tasks

1. Write a script to get from selected remote computer the following data:
   - CPU model and max clock speed
   - Amount of RAM (total, free)
   - OS disk free space
   - OS version
   - Installed hot fixes
   - List of stopped services

2. Write a script to get all interactive and remote logon sessions for the selected remote computer. And for every session find associated user information.

   **Hint:** use classes `Win32_LogonSession` and `Win32_Account`.

## Self-Review

- **Task 1:** CPU information retrieved (model, clock speed)
- **Task 1:** RAM information retrieved (total, free)
- **Task 1:** Disk space information retrieved
- **Task 1:** OS version displayed
- **Task 1:** Hot fixes listed
- **Task 1:** Stopped services listed
- **Task 2:** Logon sessions retrieved
- **Task 2:** User information associated with sessions

## Helpful Materials

- https://docs.microsoft.com/en-us/powershell/module/cimcmdlets/get-ciminstance
- https://docs.microsoft.com/en-us/windows/win32/cimwin32prov/win32-processor
- https://docs.microsoft.com/en-us/windows/win32/cimwin32prov/win32-logonsession
