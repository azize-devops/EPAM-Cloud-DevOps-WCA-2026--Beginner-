# VARIABLE TYPES AND DECLARATIONS - ANSWER

This guide covers PowerShell variables, data types, type declarations,
and how to check the type of objects.


## POWERSHELL VARIABLE BASICS

Variable syntax:

```powershell
$variableName = value
```

Naming rules:
- Must start with $
- Can contain letters, numbers, underscores
- Case-insensitive ($Name = $name)

Common data types:

| Type            | Description              | Example                    |
|-----------------|--------------------------|----------------------------|
| [string]        | Text                     | "Hello World"              |
| [int]           | Integer (32-bit)         | 42                         |
| [long]          | Integer (64-bit)         | 9999999999                 |
| [double]        | Decimal number           | 3.14159                    |
| [bool]          | Boolean                  | $true, $false              |
| [datetime]      | Date and time            | Get-Date                   |
| [array]         | Collection               | @(1, 2, 3)                 |
| [hashtable]     | Key-value pairs          | @{Name="John"; Age=30}     |


## TASK 1: Create String Variable with 19790525

Create a string variable:

```powershell
[string]$myDate = "19790525"
```

Or simply (PowerShell infers string from quotes):

```powershell
$myDate = "19790525"
```

> **Screenshot 1**
>
> Show: Creating string variable
> Expected: [string]$myDate = "19790525"

Verify the variable value:

```powershell
$myDate
```

Expected output:

```
19790525
```

> **Screenshot 2**
>
> Show: Variable value displayed
> Expected: 19790525 shown

Check the variable type:

```powershell
$myDate.GetType()
```

Expected output:

```
IsPublic IsSerial Name                                     BaseType
-------- -------- ----                                     --------
True     True     String                                   System.Object
```

> **Screenshot 3**
>
> Show: GetType() result
> Expected: String type confirmed

Alternative: Get just the type name

```powershell
$myDate.GetType().Name
```

Expected output:

```
String
```

Why use [string] for numbers?
- Without [string]: $myDate = 19790525 creates an Int32
- With [string]: $myDate = "19790525" creates a String
- The quotes or [string] cast ensure it's treated as text


## TASK 2: List Home Directory Path and Get Datatype

Get user's home directory path:

```powershell
$homePath = $HOME
```

Or:

```powershell
$homePath = $env:USERPROFILE
```

Display the path:

```powershell
$homePath
```

Expected output (example):

```
C:\Users\YourUsername
```

> **Screenshot 4**
>
> Show: Home directory path
> Expected: C:\Users\Username displayed

Get the datatype:

```powershell
$homePath.GetType()
```

Expected output:

```
IsPublic IsSerial Name                                     BaseType
-------- -------- ----                                     --------
True     True     String                                   System.Object
```

> **Screenshot 5**
>
> Show: GetType() for home path
> Expected: String type shown

Short form:

```powershell
$homePath.GetType().Name
```

Expected output:

```
String
```

Alternative: Using Get-Item

```powershell
$homeDir = Get-Item $HOME
$homeDir.GetType().Name
```

Expected output:

```
DirectoryInfo
```


## TASK 3: List Single File and Get Datatype

Get a single file from home directory:

```powershell
$singleFile = Get-ChildItem $HOME -File | Select-Object -First 1
```

Or specify a known file:

```powershell
$singleFile = Get-Item "$HOME\Desktop.ini"
```

Or:

```powershell
$singleFile = Get-ChildItem $HOME -File -Name "*.txt" | Select-Object -First 1
```

> **Screenshot 6**
>
> Show: Getting single file
> Expected: File object stored in variable

Display the file:

```powershell
$singleFile
```

Expected output (example):

```
    Directory: C:\Users\YourUsername

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         1/15/2024   9:30 AM            256 .gitconfig
```

> **Screenshot 7**
>
> Show: Single file information
> Expected: File details displayed

Get the datatype:

```powershell
$singleFile.GetType()
```

Expected output:

```
IsPublic IsSerial Name                                     BaseType
-------- -------- ----                                     --------
True     True     FileInfo                                 System.IO.FileSystemInfo
```

> **Screenshot 8**
>
> Show: GetType() for file
> Expected: FileInfo type shown

Short form:

```powershell
$singleFile.GetType().Name
```

Expected output:

```
FileInfo
```

Understanding FileInfo:
- Full type: System.IO.FileInfo
- Represents a file on the filesystem
- Has properties: Name, Length, Extension, LastWriteTime, etc.


## TASK 4: List Single Directory and Get Datatype

Get a single directory from home directory:

```powershell
$singleDir = Get-ChildItem $HOME -Directory | Select-Object -First 1
```

Or specify a known directory:

```powershell
$singleDir = Get-Item "$HOME\Desktop"
```

Or:

```powershell
$singleDir = Get-Item "$HOME\Documents"
```

> **Screenshot 9**
>
> Show: Getting single directory
> Expected: Directory object stored in variable

Display the directory:

```powershell
$singleDir
```

Expected output (example):

```
    Directory: C:\Users\YourUsername

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----         1/20/2024  10:15 AM                Desktop
```

> **Screenshot 10**
>
> Show: Single directory information
> Expected: Directory details displayed

Get the datatype:

```powershell
$singleDir.GetType()
```

Expected output:

```
IsPublic IsSerial Name                                     BaseType
-------- -------- ----                                     --------
True     True     DirectoryInfo                            System.IO.FileSystemInfo
```

> **Screenshot 11**
>
> Show: GetType() for directory
> Expected: DirectoryInfo type shown

Short form:

```powershell
$singleDir.GetType().Name
```

Expected output:

```
DirectoryInfo
```

Understanding DirectoryInfo:
- Full type: System.IO.DirectoryInfo
- Represents a directory on the filesystem
- Has properties: Name, FullName, Parent, CreationTime, etc.


## COMPLETE EXAMPLE SESSION

```powershell
PS> [string]$myDate = "19790525"
PS> $myDate.GetType().Name
String

PS> $homePath = $HOME
PS> $homePath
C:\Users\YourUsername
PS> $homePath.GetType().Name
String

PS> $singleFile = Get-ChildItem $HOME -File | Select-Object -First 1
PS> $singleFile.GetType().Name
FileInfo

PS> $singleDir = Get-ChildItem $HOME -Directory | Select-Object -First 1
PS> $singleDir.GetType().Name
DirectoryInfo
```

> **Screenshot 12**
>
> Show: Complete session with all tasks
> Expected: All four tasks completed


## VARIABLE TYPE CASTING

Explicit type casting:

```powershell
[string]$str = 19790525      # Force string type
[int]$num = "42"             # Convert string to int
[datetime]$date = "2024-01-15"  # Convert to datetime
```

Check and compare types:

```powershell
$var -is [string]            # Returns $true if string
$var -isnot [int]            # Returns $true if NOT int
$var.GetType().Name          # Get type name
```

Type conversion examples:

```powershell
# String to Integer
[int]"100"                   # Result: 100

# Integer to String
[string]42                   # Result: "42"

# String to DateTime
[datetime]"2024-01-15"       # Result: January 15, 2024
```


## COMMON POWERSHELL TYPES

| .NET Type                  | PowerShell Alias | Example               |
|----------------------------|------------------|-----------------------|
| System.String              | [string]         | "Hello"               |
| System.Int32               | [int]            | 42                    |
| System.Int64               | [long]           | 9999999999            |
| System.Double              | [double]         | 3.14                  |
| System.Boolean             | [bool]           | $true                 |
| System.DateTime            | [datetime]       | Get-Date              |
| System.Array               | [array]          | @(1,2,3)              |
| System.Collections.Hashtable| [hashtable]     | @{a=1}                |
| System.IO.FileInfo         | -                | Get-Item file.txt     |
| System.IO.DirectoryInfo    | -                | Get-Item C:\Folder    |


## USEFUL TYPE CHECKING METHODS

| Method/Operator            | Description                    |
|----------------------------|--------------------------------|
| $var.GetType()             | Returns full type information  |
| $var.GetType().Name        | Returns type name only         |
| $var.GetType().FullName    | Returns full .NET type name    |
| $var -is [type]            | Tests if variable is type      |
| $var -isnot [type]         | Tests if variable is NOT type  |

Examples:

```powershell
$str = "Hello"
$str -is [string]           # True
$str -is [int]              # False

$num = 42
$num.GetType().FullName     # System.Int32
```


## TROUBLESHOOTING

Problem: Variable shows number instead of string
Solution: Use quotes or explicit [string] cast

```powershell
[string]$var = "19790525"    # Correct
$var = "19790525"            # Also correct (quotes)
$var = 19790525              # This is Int32!
```

Problem: GetType() returns error "Cannot call on null"
Solution: Variable is empty or null

```powershell
if ($null -ne $var) { $var.GetType() }
```

Problem: Get-ChildItem returns nothing
Solution: Directory might be empty or path wrong

```powershell
Test-Path $HOME              # Verify path exists
Get-ChildItem $HOME -Force   # Include hidden files
```

Problem: "Cannot find path" error
Solution: Check path and use quotes for spaces

```powershell
Get-Item "$HOME\My Documents"
```

Problem: Multiple items returned instead of one
Solution: Use Select-Object -First 1

```powershell
Get-ChildItem $HOME -File | Select-Object -First 1
```

> **Screenshot 13**
>
> Show: Summary of all variable types
> Expected: String, String, FileInfo, DirectoryInfo


## SELF-REVIEW CHECKLIST

- [ ] Created string variable with "19790525"
- [ ] Verified variable type is String using GetType()
- [ ] Retrieved home directory path ($HOME or $env:USERPROFILE)
- [ ] Checked home path type is String
- [ ] Got single file using Get-ChildItem -File
- [ ] Verified file type is FileInfo
- [ ] Got single directory using Get-ChildItem -Directory
- [ ] Verified directory type is DirectoryInfo
- [ ] Understand difference between String and Int types
- [ ] Understand FileInfo vs DirectoryInfo types
- [ ] Know how to use GetType() method
