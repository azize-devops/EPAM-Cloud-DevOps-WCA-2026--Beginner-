# Advanced Functions and Scripts - Task

## Goals

- Create advanced PowerShell functions
- Use CmdletBinding and SupportsShouldProcess
- Implement parameter validation
- Handle file system operations safely

## Tasks

1. Write an advanced function to show the size of files in specific folder and sort all of them by size.

2. Write an advanced function to delete file from a specific folder if file size greater than specified size in Kb.

## Self-Review

- **Task 1:** Function accepts folder path parameter
- **Task 1:** Files listed with their sizes
- **Task 1:** Files sorted by size (ascending or descending)
- **Task 2:** Function accepts folder path and size threshold
- **Task 2:** Only files larger than threshold are deleted
- **Task 2:** SupportsShouldProcess for `-WhatIf`/`-Confirm` support

## Helpful Materials

- https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_functions_advanced
- https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_functions_cmdletbindingattribute
- https://docs.microsoft.com/en-us/powershell/scripting/learn/ps101/09-functions
