POSITIONAL ARGUMENTS - ANSWER
=============================

This guide covers bash positional arguments, arrays, indexing,
and circular iteration through arguments.


================================================================================
                     POSITIONAL ARGUMENTS REFERENCE
================================================================================

| Variable | Description                              |
|----------|------------------------------------------|
| $0       | Script name                              |
| $1       | First argument                           |
| $2       | Second argument                          |
| ...      | ...                                      |
| $9       | Ninth argument                           |
| ${10}    | Tenth argument (braces required for 10+) |
| $#       | Number of arguments                      |
| $@       | All arguments (as separate words)        |
| $*       | All arguments (as single string)         |
| ${!#}    | Last argument (indirect reference)       |

================================================================================


TASK 1: Create the script
-------------------------

Create the script file:

    cd ~
    touch args_processor.sh
    chmod +x args_processor.sh

Edit the script:

    vim args_processor.sh
    # or
    nano args_processor.sh

Script content:

    #!/bin/bash

    # Store arguments in array
    args=("$@")
    count=$#

    # Part 1: Print all arguments with labels
    for (( i=0; i<count; i++ )); do
        echo "Arg$((i+1)): ${args[i]}"
    done

    # Part 2: Add each argument with the next one
    # For last argument, add the first one
    result=""
    for (( i=0; i<count; i++ )); do
        # Calculate next index (circular)
        next=$(( (i + 1) % count ))

        # Add current and next
        sum=$(( args[i] + args[next] ))

        # Append to result
        result="$result$sum "
    done

    # Print the sums
    echo $result

+----------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                   |
|                                                          |
|  Show: Creating args_processor.sh                        |
|  Expected: touch and chmod commands                      |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                   |
|                                                          |
|  Show: Script content in editor or via cat               |
|  Expected: Complete script with loops                    |
+----------------------------------------------------------+

Alternative - Create using cat heredoc:

    cat > ~/args_processor.sh << 'EOF'
    #!/bin/bash

    # Store arguments in array
    args=("$@")
    count=$#

    # Part 1: Print all arguments with labels
    for (( i=0; i<count; i++ )); do
        echo "Arg$((i+1)): ${args[i]}"
    done

    # Part 2: Add each argument with the next one
    # For last argument, add the first one
    result=""
    for (( i=0; i<count; i++ )); do
        # Calculate next index (circular)
        next=$(( (i + 1) % count ))

        # Add current and next
        sum=$(( args[i] + args[next] ))

        # Append to result
        result="$result$sum "
    done

    # Print the sums
    echo $result
    EOF

    chmod +x ~/args_processor.sh

+----------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                   |
|                                                          |
|  Show: cat args_processor.sh output                      |
|  Expected: Complete script displayed                     |
+----------------------------------------------------------+


UNDERSTANDING THE SCRIPT LINE BY LINE
-------------------------------------

Line: args=("$@")
- Creates an array from all arguments
- "$@" preserves argument boundaries

Line: count=$#
- Stores the number of arguments

Line: for (( i=0; i<count; i++ )); do
- C-style for loop, iterating from 0 to count-1
- Array indices start at 0

Line: echo "Arg$((i+1)): ${args[i]}"
- $((i+1)) converts 0-based index to 1-based label
- ${args[i]} accesses array element at index i

Line: next=$(( (i + 1) % count ))
- Modulo operation for circular indexing
- When i = 6 (last), next = (6+1) % 7 = 0 (first)

Line: sum=$(( args[i] + args[next] ))
- Adds current element with next element

Line: result="$result$sum "
- Appends sum to result string with space


TASK 2: Run with 7 1 5 7 4 3 6
------------------------------

Run the script:

    ./args_processor.sh 7 1 5 7 4 3 6

Expected output:
    Arg1: 7
    Arg2: 1
    Arg3: 5
    Arg4: 7
    Arg5: 4
    Arg6: 3
    Arg7: 6
    8 6 12 11 7 9 13

+----------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                   |
|                                                          |
|  Show: ./args_processor.sh 7 1 5 7 4 3 6                 |
|  Expected: All Arg lines and sum result                  |
+----------------------------------------------------------+

Calculation breakdown for sums:

    Arguments:  7   1   5   7   4   3   6
    Indices:    0   1   2   3   4   5   6

    Iteration 0: args[0] + args[1] = 7 + 1 = 8
    Iteration 1: args[1] + args[2] = 1 + 5 = 6
    Iteration 2: args[2] + args[3] = 5 + 7 = 12
    Iteration 3: args[3] + args[4] = 7 + 4 = 11
    Iteration 4: args[4] + args[5] = 4 + 3 = 7
    Iteration 5: args[5] + args[6] = 3 + 6 = 9
    Iteration 6: args[6] + args[0] = 6 + 7 = 13  (circular!)

    Result: 8 6 12 11 7 9 13

+----------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                   |
|                                                          |
|  Show: Calculation breakdown                             |
|  Expected: Step-by-step addition                         |
+----------------------------------------------------------+

Visual representation:

    7 ──► 1 ──► 5 ──► 7 ──► 4 ──► 3 ──► 6
    │     │     │     │     │     │     │
    └──+──┘     │     │     │     │     │
       8        │     │     │     │     │
          └──+──┘     │     │     │     │
             6        │     │     │     │
                └──+──┘     │     │     │
                   12       │     │     │
                      └──+──┘     │     │
                         11       │     │
                            └──+──┘     │
                               7        │
                                  └──+──┘
                                     9
    6 + 7 = 13 (last wraps to first)

+----------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                   |
|                                                          |
|  Show: Circular addition visualization                   |
|  Expected: Understanding of wrap-around                  |
+----------------------------------------------------------+


================================================================================
                        ARRAY OPERATIONS IN BASH
================================================================================

Creating arrays:

    arr=(a b c d e)              # Direct assignment
    arr=("$@")                   # From arguments
    arr=($(command))             # From command output

Accessing elements:

    ${arr[0]}                    # First element
    ${arr[1]}                    # Second element
    ${arr[-1]}                   # Last element
    ${arr[@]}                    # All elements
    ${#arr[@]}                   # Number of elements

Slicing:

    ${arr[@]:1}                  # All except first
    ${arr[@]:1:3}                # 3 elements starting at index 1
    ${arr[@]: -2}                # Last 2 elements

Modifying:

    arr+=(new)                   # Append element
    arr[0]=value                 # Set specific index
    unset arr[2]                 # Remove element

================================================================================


================================================================================
                        MODULO FOR CIRCULAR INDEXING
================================================================================

The modulo operator (%) returns the remainder of division.
This is perfect for circular/wrap-around behavior.

Example with 7 elements (indices 0-6):

    (0 + 1) % 7 = 1    # Next of 0 is 1
    (1 + 1) % 7 = 2    # Next of 1 is 2
    (5 + 1) % 7 = 6    # Next of 5 is 6
    (6 + 1) % 7 = 0    # Next of 6 wraps to 0!

Visual:

         0
       /   \
      6     1
      |     |
      5     2
       \   /
         4───3

    Moving clockwise: 0→1→2→3→4→5→6→0→1...

================================================================================


================================================================================
                        ACCESSING ARGUMENTS BY INDEX
================================================================================

Method 1: Using array

    args=("$@")
    echo ${args[0]}              # First argument
    echo ${args[1]}              # Second argument

Method 2: Using indirect reference

    for (( i=1; i<=$#; i++ )); do
        echo "Arg$i: ${!i}"      # ${!i} = value of $i-th argument
    done

Method 3: Using shift

    while [[ $# -gt 0 ]]; do
        echo "Arg: $1"
        shift                    # Removes first argument
    done

Method 4: Using set

    set -- a b c d               # Set positional parameters
    echo $1                      # a
    echo $2                      # b

================================================================================


================================================================================
                        ALTERNATIVE SCRIPT VERSIONS
================================================================================

Version using indirect references:

    #!/bin/bash

    count=$#

    # Print arguments
    for (( i=1; i<=count; i++ )); do
        echo "Arg$i: ${!i}"
    done

    # Calculate sums
    result=""
    for (( i=1; i<=count; i++ )); do
        if [[ $i -eq $count ]]; then
            next=1
        else
            next=$((i + 1))
        fi
        sum=$(( ${!i} + ${!next} ))
        result="$result$sum "
    done

    echo $result

Version using shift:

    #!/bin/bash

    # Store first for circular addition
    first=$1
    args=("$@")
    count=$#
    i=1

    # Print and prepare sums
    result=""
    while [[ $# -gt 0 ]]; do
        echo "Arg$i: $1"

        if [[ $# -eq 1 ]]; then
            sum=$(( $1 + first ))
        else
            sum=$(( $1 + $2 ))
        fi
        result="$result$sum "

        shift
        ((i++))
    done

    echo $result

+----------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                   |
|                                                          |
|  Show: Alternative script approach                       |
|  Expected: Same output, different method                 |
+----------------------------------------------------------+


================================================================================
                             TROUBLESHOOTING
================================================================================

Problem: "bad array subscript"
Solution: Ensure array is properly declared
    args=("$@")    # Correct
    args=$@        # Wrong - not an array

Problem: Index out of bounds
Solution: Use modulo for circular access
    next=$(( (i + 1) % count ))

Problem: Arguments with spaces not handled
Solution: Quote all expansions
    args=("$@")           # Quoted
    echo "${args[i]}"     # Quoted

Problem: ${!i} not working
Solution: Indirect reference needs variable number
    i=2
    echo ${!i}            # Prints value of $2

Problem: Off-by-one errors
Solution: Remember arrays start at 0
    args[0] = first argument
    Arg1 = args[0]

Problem: Empty result string
Solution: Check loop logic and string concatenation
    result="$result$sum "    # Include space

================================================================================


================================================================================
                              SELF-REVIEW CHECKLIST
================================================================================

[ ] Script created and executable
[ ] Script accepts any number of arguments
[ ] Arguments stored in array correctly
[ ] Part 1 prints "Arg1: value" format for each
[ ] Part 2 adds each argument with next one
[ ] Last argument adds with first (circular)
[ ] Running with "7 1 5 7 4 3 6" produces correct output
[ ] Understand modulo operator for circular indexing
[ ] Understand array syntax in bash
[ ] Understand difference between $@ and $*

================================================================================
