FUNCTIONS - ANSWER
==================

This guide covers bash functions, function arguments, return values,
nested function calls, and local variables.


================================================================================
                        FUNCTION SYNTAX
================================================================================

Two ways to define functions:

    # Method 1: Using function keyword
    function name {
        commands
    }

    # Method 2: Using parentheses (POSIX compatible)
    name() {
        commands
    }

Calling a function:

    name           # No arguments
    name arg1 arg2 # With arguments

Function arguments:
    $1, $2, ...    # Positional arguments (within function)
    $@             # All arguments
    $#             # Number of arguments

================================================================================


TASK 1: Create the script
-------------------------

Create the script file:

    cd ~
    touch square_plus.sh
    chmod +x square_plus.sh

Edit the script:

    vim square_plus.sh
    # or
    nano square_plus.sh

Script content:

    #!/bin/bash

    # Function 1: Multiplies argument by itself (squares it)
    square() {
        local num=$1
        echo $((num * num))
    }

    # Function 2: Calls square for each argument and adds 1
    process_args() {
        for arg in "$@"; do
            # Call square function and get result
            squared=$(square $arg)
            # Add 1 to the result
            result=$((squared + 1))
            # Output to console
            echo $result
        done
    }

    # Call the second function with all script arguments
    process_args "$@"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                   |
|                                                          |
|  Show: Creating square_plus.sh                           |
|  Expected: touch and chmod commands                      |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                   |
|                                                          |
|  Show: Script content in editor or via cat               |
|  Expected: Complete script with both functions           |
+----------------------------------------------------------+

Alternative - Create using cat heredoc:

    cat > ~/square_plus.sh << 'EOF'
    #!/bin/bash

    # Function 1: Multiplies argument by itself (squares it)
    square() {
        local num=$1
        echo $((num * num))
    }

    # Function 2: Calls square for each argument and adds 1
    process_args() {
        for arg in "$@"; do
            # Call square function and get result
            squared=$(square $arg)
            # Add 1 to the result
            result=$((squared + 1))
            # Output to console
            echo $result
        done
    }

    # Call the second function with all script arguments
    process_args "$@"
    EOF

    chmod +x ~/square_plus.sh

+----------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                   |
|                                                          |
|  Show: cat square_plus.sh output                         |
|  Expected: Complete script displayed                     |
+----------------------------------------------------------+


UNDERSTANDING THE SCRIPT LINE BY LINE
-------------------------------------

Line: square() {
- Defines a function named "square"
- Uses () syntax (POSIX compatible)

Line: local num=$1
- "local" makes variable scoped to function
- $1 is the first argument passed TO THE FUNCTION

Line: echo $((num * num))
- Calculates num * num (square)
- echo outputs the result (function's "return value")

Line: process_args() {
- Defines second function
- This function calls the first one

Line: for arg in "$@"; do
- Loops through all arguments passed to process_args
- "$@" inside function = function's arguments

Line: squared=$(square $arg)
- Calls square function with $arg
- Captures output using $() command substitution

Line: result=$((squared + 1))
- Adds 1 to the squared value

Line: echo $result
- Outputs final result to console

Line: process_args "$@"
- Calls process_args with all script arguments
- "$@" here = script's command line arguments


NESTED FUNCTION FLOW
--------------------

    Script called with: 5 6 1 3 9
                │
                ▼
    process_args("5", "6", "1", "3", "9")
                │
        ┌───────┴───────┐
        │   for each    │
        │   argument    │
        └───────┬───────┘
                │
        ┌───────▼───────┐
        │  square(5)    │──► 25
        │  25 + 1       │──► 26 ──► echo
        └───────┬───────┘
                │
        ┌───────▼───────┐
        │  square(6)    │──► 36
        │  36 + 1       │──► 37 ──► echo
        └───────┬───────┘
                │
        ┌───────▼───────┐
        │  square(1)    │──► 1
        │  1 + 1        │──► 2  ──► echo
        └───────┬───────┘
                │
        ┌───────▼───────┐
        │  square(3)    │──► 9
        │  9 + 1        │──► 10 ──► echo
        └───────┬───────┘
                │
        ┌───────▼───────┐
        │  square(9)    │──► 81
        │  81 + 1       │──► 82 ──► echo
        └───────────────┘


TASK 2: Run with 5 6 1 3 9
--------------------------

Run the script:

    ./square_plus.sh 5 6 1 3 9

Expected output:
    26
    37
    2
    10
    82

+----------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                   |
|                                                          |
|  Show: ./square_plus.sh 5 6 1 3 9                        |
|  Expected: 26, 37, 2, 10, 82 (each on new line)          |
+----------------------------------------------------------+

Calculation breakdown:

    Argument: 5
        square(5) = 5 * 5 = 25
        25 + 1 = 26

    Argument: 6
        square(6) = 6 * 6 = 36
        36 + 1 = 37

    Argument: 1
        square(1) = 1 * 1 = 1
        1 + 1 = 2

    Argument: 3
        square(3) = 3 * 3 = 9
        9 + 1 = 10

    Argument: 9
        square(9) = 9 * 9 = 81
        81 + 1 = 82

+----------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                   |
|                                                          |
|  Show: Calculation breakdown                             |
|  Expected: Step-by-step for each number                  |
+----------------------------------------------------------+


ADDITIONAL TEST CASES
---------------------

Test with different arguments:

    ./square_plus.sh 2 4 10

Expected output:
    5      # 2*2 + 1 = 5
    17     # 4*4 + 1 = 17
    101    # 10*10 + 1 = 101

+----------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                   |
|                                                          |
|  Show: Additional test with 2 4 10                       |
|  Expected: 5, 17, 101                                    |
+----------------------------------------------------------+


================================================================================
                        FUNCTION RETURN VALUES
================================================================================

Method 1: Using echo (recommended for values)

    get_value() {
        echo "result"
    }
    value=$(get_value)    # Capture with $()

Method 2: Using return (exit status only, 0-255)

    check_status() {
        if [[ condition ]]; then
            return 0    # Success
        else
            return 1    # Failure
        fi
    }
    check_status && echo "OK"

Method 3: Using global variable

    result=""
    calculate() {
        result=$((5 + 5))
    }
    calculate
    echo $result    # 10

Comparison:

| Method   | Range      | Use Case                    |
|----------|------------|-----------------------------|
| echo     | Any string | Return data/values          |
| return   | 0-255      | Return status/success       |
| global   | Any        | Modify external state       |

================================================================================


================================================================================
                        LOCAL VS GLOBAL VARIABLES
================================================================================

Global variable (default):

    name="global"

    change_name() {
        name="changed"    # Modifies global
    }

    echo $name       # global
    change_name
    echo $name       # changed

Local variable:

    name="global"

    change_name() {
        local name="local"    # Only exists in function
        echo $name            # local
    }

    echo $name       # global
    change_name      # prints: local
    echo $name       # still global

Best practice: Use "local" for function variables to avoid side effects.

================================================================================


================================================================================
                        FUNCTION ARGUMENTS
================================================================================

Arguments inside functions are separate from script arguments:

    #!/bin/bash

    show_args() {
        echo "Function received: $@"
        echo "First arg: $1"
        echo "Count: $#"
    }

    echo "Script received: $@"
    show_args "a" "b" "c"

Running: ./script.sh x y z

Output:
    Script received: x y z
    Function received: a b c
    First arg: a
    Count: 3

Passing script args to function:

    show_args "$@"    # Pass all script args to function

================================================================================


================================================================================
                        ALTERNATIVE SCRIPT VERSIONS
================================================================================

Version with return status:

    #!/bin/bash

    square() {
        local num=$1
        local result=$((num * num))
        echo $result
        return 0
    }

    process_args() {
        local arg
        for arg in "$@"; do
            local squared
            squared=$(square "$arg")
            if [[ $? -eq 0 ]]; then
                echo $((squared + 1))
            fi
        done
    }

    process_args "$@"

Version using function keyword:

    #!/bin/bash

    function square {
        echo $(($1 * $1))
    }

    function process_args {
        for arg in "$@"; do
            echo $(($(square $arg) + 1))
        done
    }

    process_args "$@"

One-liner version (compact):

    #!/bin/bash
    square() { echo $(($1 * $1)); }
    process() { for a in "$@"; do echo $(($(square $a) + 1)); done; }
    process "$@"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                   |
|                                                          |
|  Show: Alternative script version                        |
|  Expected: Same output, different style                  |
+----------------------------------------------------------+


================================================================================
                        COMMON FUNCTION PATTERNS
================================================================================

Validation function:

    validate_number() {
        [[ $1 =~ ^[0-9]+$ ]] && return 0 || return 1
    }

    if validate_number "123"; then
        echo "Valid"
    fi

Error handling:

    divide() {
        if [[ $2 -eq 0 ]]; then
            echo "Error: Division by zero" >&2
            return 1
        fi
        echo $(($1 / $2))
        return 0
    }

Recursive function:

    factorial() {
        if [[ $1 -le 1 ]]; then
            echo 1
        else
            local prev=$(factorial $(($1 - 1)))
            echo $(($1 * prev))
        fi
    }

    factorial 5    # 120

+----------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                   |
|                                                          |
|  Show: Additional function examples                      |
|  Expected: Various function patterns                     |
+----------------------------------------------------------+


================================================================================
                             TROUBLESHOOTING
================================================================================

Problem: "command not found" when calling function
Solution: Define function before calling it
    # Functions must be defined before use
    my_func() { echo "hello"; }
    my_func    # Works

Problem: Function modifying wrong variable
Solution: Use "local" keyword
    my_func() {
        local var="value"    # Won't affect global
    }

Problem: Can't capture function output
Solution: Use command substitution
    result=$(my_func)    # Correct
    result=my_func       # Wrong - just stores "my_func"

Problem: $1 in function is empty
Solution: Pass arguments when calling
    my_func $arg    # Pass argument
    my_func         # $1 will be empty

Problem: Return value larger than 255
Solution: Use echo instead of return
    # return only works for 0-255 (exit codes)
    echo $large_number    # Use echo for values

Problem: Function not seeing script arguments
Solution: Explicitly pass them
    my_func "$@"    # Pass script args to function

================================================================================


================================================================================
                              SELF-REVIEW CHECKLIST
================================================================================

[ ] Script created and executable
[ ] First function (square) multiplies argument by itself
[ ] Second function calls first function (nested)
[ ] Second function adds 1 to squared result
[ ] Second function outputs each result
[ ] Running with "5 6 1 3 9" outputs: 26, 37, 2, 10, 82
[ ] Understand function definition syntax
[ ] Understand local vs global variables
[ ] Understand how to capture function output with $()
[ ] Understand function arguments ($1, $@, $#)

================================================================================
