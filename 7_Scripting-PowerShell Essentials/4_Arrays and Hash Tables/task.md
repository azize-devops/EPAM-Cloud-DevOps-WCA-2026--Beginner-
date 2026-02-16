# Arrays and Hash Tables - Task

## Goals

- Learn to work with arrays and hash tables in PowerShell
- Understand multi-dimensional arrays
- Practice nested data structures

## Tasks

1. Create a two-dimensional strongly defined string array.

2. Put three different hash-tables in an array.
   Retrieve value from the second key in the third hash table in array.

3. Create a nested hash-table and retrieve values by keys from it.

4. Transform hash-table into ordered hash-table sorted by value.

   **Example:**

   ```powershell
   $hashTable = @{a = 12; g = 4; h = 1; t = 7; d = 8}
   ```

   **Result:**

   | Name | Value |
   |------|-------|
   | h    | 1     |
   | g    | 4     |
   | t    | 7     |
   | d    | 8     |
   | a    | 12    |

## Self-Review

- **Task 1:** 2D string array created and accessible
- **Task 2:** Value retrieved from hash table in array
- **Task 3:** Nested hash table values accessible by keys
- **Task 4:** Hash table sorted by value and displayed in order

## Helpful Materials

- https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_arrays
- https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_hash_tables
