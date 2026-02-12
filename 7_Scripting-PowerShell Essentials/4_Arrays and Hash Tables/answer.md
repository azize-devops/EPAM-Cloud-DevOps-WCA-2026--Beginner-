# ARRAYS AND HASH TABLES - ANSWER

This guide covers PowerShell arrays, multi-dimensional arrays, hash tables,
nested structures, and sorting techniques.


================================================================================
                     ARRAYS AND HASH TABLES OVERVIEW
================================================================================

Arrays:
    @()                          # Empty array
    @(1, 2, 3)                   # Array with values
    [string[]]@("a", "b")        # Strongly typed array

Hash Tables:
    @{}                          # Empty hash table
    @{Key1 = "Value1"}           # Hash table with key-value
    [ordered]@{}                 # Ordered hash table

================================================================================


TASK 1: Two-Dimensional Strongly Defined String Array
-----------------------------------------------------

Create a 2D string array:

    [string[,]]$matrix = [string[,]]::new(3, 3)

Or with values:

    [string[,]]$matrix = @(
        @("A1", "A2", "A3"),
        @("B1", "B2", "B3"),
        @("C1", "C2", "C3")
    )

+----------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                   |
|                                                          |
|  Show: Creating 2D string array                          |
|  Expected: Array declaration with values                 |
+----------------------------------------------------------+

Alternative - Using New-Object:

    $rows = 3
    $cols = 3
    [string[,]]$matrix = New-Object 'string[,]' $rows, $cols

Populate the array:

    $matrix[0,0] = "Row1-Col1"
    $matrix[0,1] = "Row1-Col2"
    $matrix[0,2] = "Row1-Col3"
    $matrix[1,0] = "Row2-Col1"
    $matrix[1,1] = "Row2-Col2"
    $matrix[1,2] = "Row2-Col3"
    $matrix[2,0] = "Row3-Col1"
    $matrix[2,1] = "Row3-Col2"
    $matrix[2,2] = "Row3-Col3"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                   |
|                                                          |
|  Show: Populating 2D array                               |
|  Expected: Values assigned to matrix positions           |
+----------------------------------------------------------+

Access elements:

    $matrix[0,0]    # First row, first column
    $matrix[1,2]    # Second row, third column
    $matrix[2,1]    # Third row, second column

Expected output:

    PS> $matrix[0,0]
    Row1-Col1

    PS> $matrix[1,2]
    Row2-Col3

+----------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                   |
|                                                          |
|  Show: Accessing 2D array elements                       |
|  Expected: Individual values displayed                   |
+----------------------------------------------------------+

Verify it's strongly typed:

    $matrix.GetType().Name

Expected output:

    String[,]

Alternative approach - Jagged array (array of arrays):

    [string[][]]$jagged = @(
        [string[]]@("A", "B", "C"),
        [string[]]@("D", "E", "F"),
        [string[]]@("G", "H", "I")
    )

    $jagged[0][1]    # Access: "B"


TASK 2: Array of Hash Tables - Retrieve Value
----------------------------------------------

Create three hash tables and put them in an array:

    $ht1 = @{
        Name = "John"
        Age = 30
        City = "New York"
    }

    $ht2 = @{
        Name = "Jane"
        Age = 25
        City = "Boston"
    }

    $ht3 = @{
        Name = "Bob"
        Age = 35
        City = "Chicago"
    }

    $arrayOfHT = @($ht1, $ht2, $ht3)

+----------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                   |
|                                                          |
|  Show: Creating array of hash tables                     |
|  Expected: Three hash tables in array                    |
+----------------------------------------------------------+

Or in a single statement:

    $arrayOfHT = @(
        @{Name = "John"; Age = 30; City = "New York"},
        @{Name = "Jane"; Age = 25; City = "Boston"},
        @{Name = "Bob"; Age = 35; City = "Chicago"}
    )

+----------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                   |
|                                                          |
|  Show: Single statement array of hash tables             |
|  Expected: Compact array definition                      |
+----------------------------------------------------------+

Retrieve value from second key in third hash table:

    # Third hash table (index 2)
    $arrayOfHT[2]

    # Get all keys from third hash table
    $arrayOfHT[2].Keys

    # Get second key (note: hash table keys are unordered!)
    # To reliably get "Age" (second defined key):
    $arrayOfHT[2].Age

    # Or by converting keys to array:
    $keys = @($arrayOfHT[2].Keys)
    $secondKey = $keys[1]
    $arrayOfHT[2][$secondKey]

+----------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                   |
|                                                          |
|  Show: Retrieving value from hash table in array         |
|  Expected: Value from third hash table displayed         |
+----------------------------------------------------------+

Expected output:

    PS> $arrayOfHT[2]

    Name                           Value
    ----                           -----
    Name                           Bob
    Age                            35
    City                           Chicago

    PS> $arrayOfHT[2].Age
    35

    PS> $arrayOfHT[2].City
    Chicago


TASK 3: Nested Hash Table and Retrieve Values
----------------------------------------------

Create a nested hash table:

    $nestedHT = @{
        Person1 = @{
            Name = "Alice"
            Age = 28
            Address = @{
                Street = "123 Main St"
                City = "Seattle"
                Zip = "98101"
            }
        }
        Person2 = @{
            Name = "Charlie"
            Age = 32
            Address = @{
                Street = "456 Oak Ave"
                City = "Portland"
                Zip = "97201"
            }
        }
    }

+----------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                   |
|                                                          |
|  Show: Creating nested hash table                        |
|  Expected: Multi-level hash table structure              |
+----------------------------------------------------------+

Retrieve values by keys:

    # Get Person1's name
    $nestedHT.Person1.Name

    # Get Person2's age
    $nestedHT.Person2.Age

    # Get Person1's city (nested 2 levels deep)
    $nestedHT.Person1.Address.City

    # Get Person2's zip code
    $nestedHT.Person2.Address.Zip

+----------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                   |
|                                                          |
|  Show: Retrieving nested values                          |
|  Expected: Values from different levels                  |
+----------------------------------------------------------+

Expected output:

    PS> $nestedHT.Person1.Name
    Alice

    PS> $nestedHT.Person2.Age
    32

    PS> $nestedHT.Person1.Address.City
    Seattle

    PS> $nestedHT.Person2.Address.Zip
    97201

Alternative bracket notation:

    $nestedHT["Person1"]["Name"]
    $nestedHT["Person1"]["Address"]["City"]

+----------------------------------------------------------+
|                    >>> SCREENSHOT 9 <<<                   |
|                                                          |
|  Show: Bracket notation access                           |
|  Expected: Same values with different syntax             |
+----------------------------------------------------------+


TASK 4: Transform Hash Table to Ordered (Sorted by Value)
---------------------------------------------------------

Create the hash table:

    $hashTable = @{a = 12; g = 4; h = 1; t = 7; d = 8}

+----------------------------------------------------------+
|                    >>> SCREENSHOT 10 <<<                  |
|                                                          |
|  Show: Original hash table                               |
|  Expected: Unordered hash table created                  |
+----------------------------------------------------------+

Display original (unordered):

    $hashTable

Expected output (order may vary):

    Name                           Value
    ----                           -----
    t                              7
    a                              12
    d                              8
    g                              4
    h                              1

Sort by value and convert to ordered hash table:

    $orderedHT = [ordered]@{}
    $hashTable.GetEnumerator() | Sort-Object Value | ForEach-Object {
        $orderedHT[$_.Key] = $_.Value
    }

+----------------------------------------------------------+
|                    >>> SCREENSHOT 11 <<<                  |
|                                                          |
|  Show: Sorting and creating ordered hash table           |
|  Expected: Sort-Object and ForEach-Object pipeline       |
+----------------------------------------------------------+

Display the ordered result:

    $orderedHT

Expected output:

    Name                           Value
    ----                           -----
    h                              1
    g                              4
    t                              7
    d                              8
    a                              12

+----------------------------------------------------------+
|                    >>> SCREENSHOT 12 <<<                  |
|                                                          |
|  Show: Ordered hash table result                         |
|  Expected: Values sorted 1, 4, 7, 8, 12                  |
+----------------------------------------------------------+

Alternative - One-liner:

    $orderedHT = [ordered]@{}; $hashTable.GetEnumerator() |
        Sort-Object Value | ForEach-Object { $orderedHT[$_.Key] = $_.Value }

Or display sorted without storing:

    $hashTable.GetEnumerator() | Sort-Object Value

Understanding the process:

    $hashTable.GetEnumerator()     # Convert to enumerable key-value pairs
    | Sort-Object Value            # Sort by the Value property
    | ForEach-Object { ... }       # Iterate and add to ordered hash table

+----------------------------------------------------------+
|                    >>> SCREENSHOT 13 <<<                  |
|                                                          |
|  Show: Complete sorting workflow                         |
|  Expected: Before and after comparison                   |
+----------------------------------------------------------+


================================================================================
                     ARRAY OPERATIONS REFERENCE
================================================================================

| Operation              | Syntax                         | Example           |
|------------------------|--------------------------------|-------------------|
| Create empty           | @()                            | $arr = @()        |
| Create with values     | @(1, 2, 3)                     | $arr = @(1,2,3)   |
| Strongly typed         | [int[]]@()                     | [int[]]$arr = @() |
| Add element            | $arr += value                  | $arr += 4         |
| Access element         | $arr[index]                    | $arr[0]           |
| Get length             | $arr.Length or $arr.Count      | $arr.Length       |
| Slice                  | $arr[start..end]               | $arr[0..2]        |
| Last element           | $arr[-1]                       | $arr[-1]          |

================================================================================


================================================================================
                     HASH TABLE OPERATIONS REFERENCE
================================================================================

| Operation              | Syntax                         | Example              |
|------------------------|--------------------------------|----------------------|
| Create empty           | @{}                            | $ht = @{}            |
| Create with values     | @{k1=v1; k2=v2}                | @{a=1; b=2}          |
| Ordered                | [ordered]@{}                   | [ordered]@{a=1}      |
| Add key                | $ht.Key = Value                | $ht.c = 3            |
| Add with Add()         | $ht.Add(Key, Value)            | $ht.Add("d", 4)      |
| Access value           | $ht.Key or $ht["Key"]          | $ht.a or $ht["a"]    |
| Remove key             | $ht.Remove("Key")              | $ht.Remove("a")      |
| Get all keys           | $ht.Keys                       | $ht.Keys             |
| Get all values         | $ht.Values                     | $ht.Values           |
| Check key exists       | $ht.ContainsKey("Key")         | $ht.ContainsKey("a") |
| Enumerate              | $ht.GetEnumerator()            | For sorting          |

================================================================================


================================================================================
                             TROUBLESHOOTING
================================================================================

Problem: "Cannot index into a null array"
Solution: Initialize array before accessing
    $arr = @()
    $arr += "value"

Problem: Hash table keys appear in random order
Solution: Use [ordered] when order matters
    [ordered]@{a = 1; b = 2; c = 3}

Problem: "Cannot convert value to type System.String[]"
Solution: Ensure proper type casting
    [string[]]$arr = @("a", "b", "c")

Problem: 2D array access doesn't work
Solution: Use comma notation for true 2D arrays
    $matrix[0,1]     # Correct for [,] arrays
    $matrix[0][1]    # Correct for jagged arrays

Problem: Sorted hash table loses order when displayed
Solution: Store in [ordered] hash table, not regular
    $orderedHT = [ordered]@{}

Problem: "Key not found" error
Solution: Check if key exists first
    if ($ht.ContainsKey("myKey")) { $ht["myKey"] }

+----------------------------------------------------------+
|                    >>> SCREENSHOT 14 <<<                  |
|                                                          |
|  Show: All tasks completed summary                       |
|  Expected: Overview of completed work                    |
+----------------------------------------------------------+


================================================================================
                     SELF-REVIEW CHECKLIST
================================================================================

[ ] Created 2D strongly typed string array [string[,]]
[ ] Can access 2D array elements with $arr[row,col]
[ ] Created array containing multiple hash tables
[ ] Retrieved value from hash table inside array
[ ] Created nested hash table (hash table inside hash table)
[ ] Retrieved values from nested structure using dot notation
[ ] Sorted hash table by value using GetEnumerator() and Sort-Object
[ ] Created ordered hash table with [ordered]
[ ] Understand difference between regular and ordered hash tables
[ ] Know how to enumerate hash table with GetEnumerator()

================================================================================

