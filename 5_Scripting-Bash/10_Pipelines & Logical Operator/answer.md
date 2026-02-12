# PIPELINES & LOGICAL OPERATORS - ANSWER

This guide covers bash pipelines (|), logical AND (&&), logical OR (||),
and command chaining for complex one-liner operations.


================================================================================
                     UNDERSTANDING PIPELINES AND OPERATORS
================================================================================

Pipelines (|):
    command1 | command2       # Output of command1 becomes input of command2

Logical Operators:
    command1 && command2      # Run command2 ONLY IF command1 succeeds (exit 0)
    command1 || command2      # Run command2 ONLY IF command1 fails (exit != 0)

Command Separators:
    command1 ; command2       # Run both regardless of success/failure
    command1 & command2       # Run command1 in background, then command2

Grouping:
    (cmd1 && cmd2) || cmd3    # Group commands with parentheses
    { cmd1 && cmd2; } || cmd3 # Group in current shell (note: space and ;)

================================================================================


TASK 1: One-liner with && and ||
--------------------------------

The command structure:

    mkdir Name_Surname && \
    touch Name_Surname/my_file && \
    echo "Hello" > Name_Surname/my_file && \
    cat Name_Surname/my_file || \
    echo "Something went wrong"

As a single line:

    mkdir Name_Surname && touch Name_Surname/my_file && echo "Hello" > Name_Surname/my_file && cat Name_Surname/my_file || echo "Something went wrong"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                   |
|                                                          |
|  Show: The complete one-liner command                    |
|  Expected: Full command visible in terminal              |
+----------------------------------------------------------+

Understanding the logic:

    mkdir Name_Surname                      # Step 1: Create folder
         &&                                 # IF successful, continue
    touch Name_Surname/my_file              # Step 2: Create file
         &&                                 # IF successful, continue
    echo "Hello" > Name_Surname/my_file     # Step 3: Write to file
         &&                                 # IF successful, continue
    cat Name_Surname/my_file                # Step 4: Display contents
         ||                                 # IF ANY step failed
    echo "Something went wrong"             # Error message

Flow diagram:

    mkdir ─success─> touch ─success─> echo ─success─> cat ─> "Hello"
      │                │                │               │
      └──fail──────────┴────fail────────┴───fail────────┴──> "Something went wrong"


TASK 2: Run the command two times
---------------------------------

First run:

    mkdir Name_Surname && touch Name_Surname/my_file && echo "Hello" > Name_Surname/my_file && cat Name_Surname/my_file || echo "Something went wrong"

Expected output:
    Hello

+----------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                   |
|                                                          |
|  Show: First run of the command                          |
|  Expected: "Hello" output                                |
+----------------------------------------------------------+

Explanation of first run:
    1. mkdir Name_Surname     -> SUCCESS (folder created)
    2. touch .../my_file      -> SUCCESS (file created)
    3. echo "Hello" > ...     -> SUCCESS (content written)
    4. cat .../my_file        -> SUCCESS (displays "Hello")
    5. || branch NOT executed (all succeeded)

+----------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                   |
|                                                          |
|  Show: Verify folder and file were created               |
|  Expected: ls -la Name_Surname showing my_file           |
+----------------------------------------------------------+

Second run (same command):

    mkdir Name_Surname && touch Name_Surname/my_file && echo "Hello" > Name_Surname/my_file && cat Name_Surname/my_file || echo "Something went wrong"

Expected output:
    mkdir: cannot create directory 'Name_Surname': File exists
    Something went wrong

+----------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                   |
|                                                          |
|  Show: Second run of the command                         |
|  Expected: mkdir error and "Something went wrong"        |
+----------------------------------------------------------+

Explanation of second run:
    1. mkdir Name_Surname     -> FAILS (folder already exists)
    2. && chain breaks immediately
    3. || branch executes -> "Something went wrong"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                   |
|                                                          |
|  Show: Both runs together for comparison                 |
|  Expected: First shows "Hello", second shows error       |
+----------------------------------------------------------+


TASK 3: Filter and modify /etc/passwd output
---------------------------------------------

Understanding /etc/passwd format:

    username:password:UID:GID:comment:home:shell

    Example:
    nobody:*:-2:-2:Unprivileged User:/var/empty:/usr/bin/false
           └─ we want to find this shell ──────┘

The command:

    grep "/usr/bin/false" /etc/passwd | sed 's|/usr/bin/false|/bin/bash|g'

Or using awk:

    grep "/usr/bin/false" /etc/passwd | awk -F: '{OFS=":"; $7="/bin/bash"; print}'

+----------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                   |
|                                                          |
|  Show: grep and sed pipeline command                     |
|  Expected: Command visible in terminal                   |
+----------------------------------------------------------+

Understanding the pipeline:

    grep "/usr/bin/false" /etc/passwd
    │
    │  Filters only lines containing /usr/bin/false
    │  Output:
    │    nobody:*:-2:-2:Unprivileged User:/var/empty:/usr/bin/false
    │    daemon:*:1:1:System Services:/var/root:/usr/bin/false
    │
    └──> | (pipe to next command)
         │
         sed 's|/usr/bin/false|/bin/bash|g'
         │
         │  Substitutes /usr/bin/false with /bin/bash
         │  Output:
         │    nobody:*:-2:-2:Unprivileged User:/var/empty:/bin/bash
         │    daemon:*:1:1:System Services:/var/root:/bin/bash

+----------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                   |
|                                                          |
|  Show: Command execution and output                      |
|  Expected: Modified passwd entries with /bin/bash        |
+----------------------------------------------------------+

Alternative approaches:

    # Using only awk
    awk -F: '$7=="/usr/bin/false" {$7="/bin/bash"; print}' OFS=":" /etc/passwd

    # Using grep with extended regex and sed
    grep -E ":/usr/bin/false$" /etc/passwd | sed 's|/usr/bin/false$|/bin/bash|'

+----------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                   |
|                                                          |
|  Show: Alternative command variations                    |
|  Expected: Same output with different approaches         |
+----------------------------------------------------------+


CLEANUP (Optional)
------------------

Remove the test folder after completing the task:

    rm -rf Name_Surname

Verify removal:

    ls -la | grep Name_Surname

+----------------------------------------------------------+
|                    >>> SCREENSHOT 9 <<<                   |
|                                                          |
|  Show: Cleanup commands                                  |
|  Expected: Folder removed successfully                   |
+----------------------------------------------------------+


================================================================================
                        PIPELINE OPERATORS
================================================================================

| Operator | Name        | Description                           |
|----------|-------------|---------------------------------------|
| |        | Pipe        | Send output to next command's input   |
| |&       | Pipe all    | Send stdout AND stderr to next cmd    |
| >        | Redirect    | Send output to file (overwrite)       |
| >>       | Append      | Send output to file (append)          |
| <        | Input       | Read input from file                  |
| 2>       | Stderr      | Redirect error output                 |
| 2>&1     | Combine     | Redirect stderr to stdout             |

Examples:

    ls | wc -l                    # Count files
    cat file | grep pattern       # Search in file
    echo "text" > file            # Write to file
    echo "more" >> file           # Append to file
    command 2>/dev/null           # Suppress errors

================================================================================


================================================================================
                        LOGICAL OPERATORS
================================================================================

| Operator | Name | Behavior                                      |
|----------|------|-----------------------------------------------|
| &&       | AND  | Execute next only if previous succeeded (0)   |
| ||       | OR   | Execute next only if previous failed (!=0)    |
| ;        | SEQ  | Execute next regardless of previous result    |
| &        | BG   | Run previous in background, continue          |

Truth table:

    cmd1 && cmd2:
    cmd1 SUCCESS (0)  -> cmd2 runs
    cmd1 FAILURE (1)  -> cmd2 skipped

    cmd1 || cmd2:
    cmd1 SUCCESS (0)  -> cmd2 skipped
    cmd1 FAILURE (1)  -> cmd2 runs

Examples:

    # Only echo if directory exists
    cd /tmp && echo "Success"

    # Echo error message if command fails
    cd /nonexistent || echo "Failed"

    # Combine both (if-then-else pattern)
    cd /tmp && echo "Exists" || echo "Not found"

================================================================================


================================================================================
                        COMMON PIPELINE PATTERNS
================================================================================

Counting:
    ls | wc -l                    # Count files
    cat file | wc -w              # Count words
    cat file | wc -c              # Count characters

Filtering:
    cat file | grep pattern       # Find lines with pattern
    cat file | grep -v pattern    # Find lines WITHOUT pattern
    cat file | head -5            # First 5 lines
    cat file | tail -5            # Last 5 lines

Transforming:
    cat file | sort               # Sort lines
    cat file | uniq               # Remove duplicates
    cat file | tr 'a-z' 'A-Z'     # Uppercase
    cat file | sed 's/old/new/'   # Replace text
    cat file | awk '{print $1}'   # Print first column

Combining:
    cat file | grep pattern | wc -l           # Count matches
    cat file | sort | uniq | wc -l            # Count unique lines
    ps aux | grep process | awk '{print $2}'  # Get PID

================================================================================


================================================================================
                        SED COMMAND BASICS
================================================================================

Syntax:
    sed 's/pattern/replacement/flags'

Flags:
    g       Global (replace all occurrences)
    i       Case insensitive
    p       Print
    d       Delete

Delimiters (any character works):
    sed 's/old/new/'              # Using /
    sed 's|old|new|'              # Using | (useful for paths)
    sed 's#old#new#'              # Using #

Examples:
    echo "hello" | sed 's/l/L/'        # heLlo (first only)
    echo "hello" | sed 's/l/L/g'       # heLLo (all)
    sed 's|/usr/bin|/bin|g' file       # Replace paths

================================================================================


================================================================================
                             TROUBLESHOOTING
================================================================================

Problem: Command after && doesn't run
Cause: Previous command failed (exit code != 0)
Solution: Check each command individually
    mkdir test; echo $?    # Check exit code

Problem: Command after || always runs
Cause: Previous command is failing
Solution: Verify the command works standalone

Problem: "Something went wrong" on second run
Expected: mkdir fails because folder exists
Solution: This is correct behavior for the exercise

Problem: Pipeline output is empty
Cause: First command produced no output
Solution: Test each command separately
    grep pattern file      # Test grep alone
    cat file              # Verify file has content

Problem: sed not replacing paths correctly
Cause: Using / as delimiter with paths containing /
Solution: Use different delimiter
    sed 's|/old/path|/new/path|g'    # Use | instead

Problem: Permissions error
Solution: Check file/folder permissions
    ls -la folder
    chmod +x script.sh

================================================================================


================================================================================
                              SELF-REVIEW CHECKLIST
================================================================================

[ ] Understand difference between | (pipe) and || (OR)
[ ] Understand && (AND) operator behavior
[ ] One-liner command creates folder, file, writes, and displays
[ ] First run outputs "Hello"
[ ] Second run outputs error and "Something went wrong"
[ ] Understand why second run fails (folder exists)
[ ] grep command filters /etc/passwd correctly
[ ] sed command replaces shell path correctly
[ ] Pipeline combines grep and sed output
[ ] Understand exit codes: 0 = success, non-zero = failure

================================================================================

