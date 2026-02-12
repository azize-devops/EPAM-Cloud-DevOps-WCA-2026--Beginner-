# OPERATORS AND REGULAR EXPRESSIONS - TASK

GOALS:
------

- Learn regular expression patterns in PowerShell
- Use -match, -replace operators
- Extract and transform text using regex


TASKS:
------

1. Write a script to find sequences of lowercase letters joined with
   underscore or dash.

   Example 1:
   .\task1.ps1 "A Balrog is a powerful fictional monster in Middle-earth"
   # Returns: Middle-earth

   Example 2:
   .\task1.ps1 "The symbol underscore, also called low_line or low dash."
   # Returns: low_line

2. Write a script that matches a word at the end of string.

   Example 1:
   .\task2.ps1 "Ents or talking trees are derived from the Old English word for giant"
   # Returns: giant

3. Write a script that matches an IP address.

   Example 1:
   .\task3.ps1 "Reply from 10.8.216.77: bytes=32 time<1ms TTL=128"
   # Returns: 10.8.216.77

4. Write a script to convert a date of mm-dd-yyyy format to dd-mm-yyyy format.

   Example 1:
   .\task4.ps1 "09-17-1991"
   # Returns: 17-09-1991

5. Write a script to find all words which are at least 6 characters long
   in a string.

   Example 1:
   .\task5.ps1 "Tue Sep 15 2020 15:39:48 GMT+0300 (Belarus Local Time)"
   # Returns: Belarus

6. Write a script to remove multiple spaces in a string.

   Example 1:
   .\task6.ps1 " kube-system   coredns-869cb84759-drhbg                     1/1     Running   0          4h5m"
   # Returns: kube-system coredns-869cb84759-drhbg 1/1 Running 0 4h5m


SELF-REVIEW:
------------

- Task 1: Regex finds lowercase-dash/underscore-lowercase patterns
- Task 2: Word at end of string extracted
- Task 3: IP address pattern matched
- Task 4: Date format converted correctly
- Task 5: Words with 6+ characters found
- Task 6: Multiple spaces replaced with single space


HELPFUL MATERIALS:
------------------

https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_regular_expressions
https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_comparison_operators

