# SPECIAL VARIABLES - ANSWER

This guide covers bash special (reserved) variables including positional
parameters, argument handling, and exit codes.


================================================================================
                      UNDERSTANDING SPECIAL VARIABLES
================================================================================

Special variables in bash are predefined variables with special meanings:

Positional Parameters:
    $0              Script name
    $1, $2, ...     Arguments (1st, 2nd, ...)
    ${10}, ${11}    Arguments 10+ (braces required)

Argument Related:
    $#              Number of arguments
    $@              All arguments (as separate words)
    $*              All arguments (as single word)

Process Related:
    $$              Current shell PID
    $!              Last background process PID
    $?              Exit status of last command

Other:
    $_              Last argument of previous command
    $-              Current shell options

================================================================================


TASK 1: Create the script
-------------------------

Create my_script.sh in your home directory:

    cd ~
    touch my_script.sh
    chmod +x my_script.sh

Edit the script:

    vim my_script.sh
    # or
    nano my_script.sh

Script content:

    #!/bin/bash

    # Print the name of the script
    echo $0

    # Print all arguments
    echo $@

    # Print the number of arguments
    echo $#

    # Print 2nd and 4th argument
    echo $2 $4

    # Compare 1st and 2nd arguments and print exit code
    [[ $1 -eq $2 ]]
    echo $?

+----------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                   |
|                                                          |
|  Show: Creating my_script.sh                             |
|  Expected: touch and chmod commands                      |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                   |
|                                                          |
|  Show: Script content in editor                          |
|  Expected: Complete script with all special variables    |
+----------------------------------------------------------+

Alternative - Create using cat heredoc:

    cat > ~/my_script.sh << 'EOF'
    #!/bin/bash

    # Print the name of the script
    echo $0

    # Print all arguments
    echo $@

    # Print the number of arguments
    echo $#

    # Print 2nd and 4th argument
    echo $2 $4

    # Compare 1st and 2nd arguments and print exit code
    [[ $1 -eq $2 ]]
    echo $?
    EOF

    chmod +x ~/my_script.sh

+----------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                   |
|                                                          |
|  Show: Creating script with heredoc                      |
|  Expected: Script created via command line               |
+----------------------------------------------------------+

Verify script content:

    cat ~/my_script.sh

+----------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                   |
|                                                          |
|  Show: cat my_script.sh output                           |
|  Expected: Complete script displayed                     |
+----------------------------------------------------------+


UNDERSTANDING THE SCRIPT LINE BY LINE
-------------------------------------

Line: echo $0
- $0 = name of the script (./my_script.sh)
- Prints how the script was called

Line: echo $@
- $@ = all arguments passed to the script
- With args "1 2 hello world", prints: 1 2 hello world

Line: echo $#
- $# = count of arguments
- With 4 arguments, prints: 4

Line: echo $2 $4
- $2 = second argument (2)
- $4 = fourth argument (world)
- Prints: 2 world

Line: [[ $1 -eq $2 ]]
- Compares $1 and $2 numerically
- -eq = equal (numeric comparison)
- Returns exit code 0 if equal, 1 if not equal

Line: echo $?
- $? = exit status of previous command
- 0 = success (arguments were equal)
- 1 = failure (arguments were not equal)


TASK 2: Execute with ./my_script.sh 1 2 hello world
---------------------------------------------------

Run the script with the specified arguments:

    ./my_script.sh 1 2 hello world

Expected output:
    ./my_script.sh
    1 2 hello world
    4
    2 world
    1

+----------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                   |
|                                                          |
|  Show: First script execution                            |
|  Expected: All 5 lines of output                         |
+----------------------------------------------------------+

Output breakdown:

    ./my_script.sh       <- $0 (script name)
    1 2 hello world      <- $@ (all arguments)
    4                    <- $# (argument count)
    2 world              <- $2 and $4 (2nd and 4th args)
    1                    <- $? (exit code: 1 != 2, so exit 1)

+----------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                   |
|                                                          |
|  Show: Output with annotations                           |
|  Expected: Each line explained                           |
+----------------------------------------------------------+

Why is the last output 1?
- [[ 1 -eq 2 ]] checks if 1 equals 2
- Since 1 != 2, the comparison fails
- Failed comparison returns exit code 1


TASK 3 & 4: Change arguments so -eq returns 0
---------------------------------------------

For -eq to return 0 (success), the first two arguments must be equal.

Run with matching first two arguments:

    ./my_script.sh 5 5 hello world

Expected output:
    ./my_script.sh
    5 5 hello world
    4
    5 world
    0                    <- Now 0 because 5 == 5

+----------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                   |
|                                                          |
|  Show: Second script execution with equal args           |
|  Expected: Last line is 0                                |
+----------------------------------------------------------+

Other valid examples:

    ./my_script.sh 10 10 hello world    # 10 == 10, exit 0
    ./my_script.sh 0 0 foo bar          # 0 == 0, exit 0
    ./my_script.sh 42 42 test data      # 42 == 42, exit 0

+----------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                   |
|                                                          |
|  Show: Multiple test runs with equal arguments           |
|  Expected: Exit code 0 in each case                      |
+----------------------------------------------------------+


UNDERSTANDING [[ ]] AND EXIT CODES
----------------------------------

The [[ ]] construct:
- Modern bash conditional expression
- Returns exit code 0 for true, 1 for false
- More powerful than [ ] (single brackets)

Exit codes:
    0 = Success (true)
    1 = Failure (false)
    Other = Various error codes

Example:

    [[ 5 -eq 5 ]]
    echo $?           # 0 (true, equal)

    [[ 5 -eq 3 ]]
    echo $?           # 1 (false, not equal)

+----------------------------------------------------------+
|                    >>> SCREENSHOT 9 <<<                   |
|                                                          |
|  Show: [[ ]] exit code examples                          |
|  Expected: 0 for true, 1 for false                       |
+----------------------------------------------------------+


ADDITIONAL EXAMPLES WITH SPECIAL VARIABLES
------------------------------------------

Example script showing all special variables:

    #!/bin/bash

    echo "Script name: $0"
    echo "First argument: $1"
    echo "Second argument: $2"
    echo "All arguments (\$@): $@"
    echo "All arguments (\$*): $*"
    echo "Number of arguments: $#"
    echo "Process ID: $$"
    echo "Exit status of last command: $?"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 10 <<<                  |
|                                                          |
|  Show: Extended example script                           |
|  Expected: All special variables demonstrated            |
+----------------------------------------------------------+


DIFFERENCE BETWEEN $@ AND $*
----------------------------

Both represent all arguments, but behave differently when quoted:

    # Script: test_args.sh
    #!/bin/bash
    echo "Using \$@:"
    for arg in "$@"; do
        echo "  Arg: $arg"
    done

    echo "Using \$*:"
    for arg in "$*"; do
        echo "  Arg: $arg"
    done

Running: ./test_args.sh "hello world" foo bar

Output:
    Using $@:
      Arg: hello world     <- Preserves "hello world" as one arg
      Arg: foo
      Arg: bar
    Using $*:
      Arg: hello world foo bar    <- All as single string

+----------------------------------------------------------+
|                    >>> SCREENSHOT 11 <<<                  |
|                                                          |
|  Show: Difference between $@ and $*                      |
|  Expected: $@ preserves argument boundaries              |
+----------------------------------------------------------+

Best Practice: Use "$@" (with quotes) to preserve argument boundaries.


================================================================================
                        SPECIAL VARIABLES REFERENCE
================================================================================

| Variable | Description                                  | Example Value     |
|----------|----------------------------------------------|-------------------|
| $0       | Script name                                  | ./my_script.sh    |
| $1       | First argument                               | hello             |
| $2       | Second argument                              | world             |
| ${10}    | Tenth argument (braces needed)               | arg10             |
| $#       | Number of arguments                          | 4                 |
| $@       | All arguments (separate words)               | arg1 arg2 arg3    |
| $*       | All arguments (single word)                  | "arg1 arg2 arg3"  |
| $?       | Exit status of last command                  | 0 or 1            |
| $$       | Current process ID                           | 12345             |
| $!       | PID of last background process               | 12346             |
| $_       | Last argument of previous command            | lastarg           |
| $-       | Current shell options                        | himBHs            |

================================================================================


================================================================================
                        NUMERIC COMPARISON OPERATORS
================================================================================

Used inside [[ ]] or [ ]:

| Operator | Description              | Example            |
|----------|--------------------------|--------------------|
| -eq      | Equal                    | [[ $a -eq $b ]]    |
| -ne      | Not equal                | [[ $a -ne $b ]]    |
| -gt      | Greater than             | [[ $a -gt $b ]]    |
| -ge      | Greater than or equal    | [[ $a -ge $b ]]    |
| -lt      | Less than                | [[ $a -lt $b ]]    |
| -le      | Less than or equal       | [[ $a -le $b ]]    |

Note: These are for INTEGER comparison only!

================================================================================


================================================================================
                        STRING COMPARISON OPERATORS
================================================================================

Used inside [[ ]]:

| Operator | Description              | Example            |
|----------|--------------------------|--------------------|
| ==       | Equal                    | [[ $a == $b ]]     |
| !=       | Not equal                | [[ $a != $b ]]     |
| <        | Less than (alphabetical) | [[ $a < $b ]]      |
| >        | Greater than             | [[ $a > $b ]]      |
| -z       | String is empty          | [[ -z $a ]]        |
| -n       | String is not empty      | [[ -n $a ]]        |

================================================================================


================================================================================
                           EXIT CODES
================================================================================

| Code | Meaning                                              |
|------|------------------------------------------------------|
| 0    | Success                                              |
| 1    | General error                                        |
| 2    | Misuse of shell command                              |
| 126  | Command not executable                               |
| 127  | Command not found                                    |
| 128  | Invalid exit argument                                |
| 130  | Script terminated by Ctrl+C                          |
| 255  | Exit status out of range                             |

Setting exit code in script:
    exit 0    # Success
    exit 1    # Failure

================================================================================


================================================================================
                             TROUBLESHOOTING
================================================================================

Problem: $10 doesn't work (shows $1 followed by 0)
Solution: Use braces for arguments 10+: ${10}

Problem: Arguments with spaces not handled correctly
Solution: Quote variables: "$1" instead of $1

Problem: -eq gives "integer expression expected"
Solution: -eq is for numbers only. Use == for strings:
    [[ "hello" == "hello" ]]

Problem: $? shows wrong exit code
Solution: $? only shows LAST command's exit code.
    Capture immediately: result=$?

Problem: Script name shows full path instead of ./script.sh
Solution: $0 shows exactly how script was called.
    Use $(basename $0) for just the filename.

================================================================================


================================================================================
                              SELF-REVIEW CHECKLIST
================================================================================

[ ] my_script.sh created and is executable
[ ] Script prints $0 (script name)
[ ] Script prints $@ (all arguments)
[ ] Script prints $# (argument count)
[ ] Script prints $2 and $4 (2nd and 4th arguments)
[ ] Script uses [[ $1 -eq $2 ]] for comparison
[ ] Script prints $? (exit code)
[ ] First run with "1 2 hello world" shows exit code 1
[ ] Second run with equal first two args shows exit code 0
[ ] Understand difference between $@ and $*
[ ] Understand numeric comparison operators

================================================================================

