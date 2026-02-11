FUNDAMENTALS OF POWERSHELL FUNCTIONS - TASK
============================================

GOALS:
------

- Learn to create PowerShell functions
- Use switch parameters
- Handle pipeline input in functions


TASKS:
------

1. Create a function that will get a string and one of several switch
   parameters and return the text from string with the color passed
   with switch parameter.

   Example 1:
   Write-ColoredMessage -Text "Hello there!" -Red
   # Returns: Hello there! (in red color)

   Example 2:
   Write-ColoredMessage -Text "Hello there!" -Blue
   # Returns: Hello there! (in blue color)

2. Create a function that will get any array from the pipeline.
   Function output should be formatted according to the example below.

   Example 1:
   1..3 | Get-ValueFromPipe
   # Returns:
   Value from pipe: 1
   Value from pipe: 2
   Value from pipe: 3

   Example 2:
   @("First", "Second") | Get-ValueFromPipe
   # Returns:
   Value from pipe: First
   Value from pipe: Second


SELF-REVIEW:
------------

- Task 1: Function accepts -Text parameter
- Task 1: Switch parameters for colors work (-Red, -Blue, etc.)
- Task 1: Text displayed in specified color
- Task 2: Function accepts pipeline input
- Task 2: Each value formatted with "Value from pipe:" prefix


HELPFUL MATERIALS:
------------------

https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_functions
https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_functions_advanced_parameters
https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_pipelines
