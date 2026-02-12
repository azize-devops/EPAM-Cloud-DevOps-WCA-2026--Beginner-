# WHILE LOOP - ANSWER

This guide covers bash while loops, infinite loops, reading user input,
and implementing a simple console/shell functionality.


================================================================================
                        WHILE LOOP SYNTAX
================================================================================

Basic while loop:

    while [[ condition ]]; do
        # commands
    done

Infinite loop:

    while true; do
        # commands (use break to exit)
    done

    # Alternative
    while :; do
        # commands
    done

Reading input in loop:

    while read line; do
        echo $line
    done < file.txt

    # Or from user
    while read -p "Enter: " input; do
        echo "You entered: $input"
    done

================================================================================


TASK 1: Create console.sh script
---------------------------------

Create the script file:

    cd ~
    touch console.sh
    chmod +x console.sh

Edit the script:

    vim console.sh
    # or
    nano console.sh

Script content:

    #!/bin/bash

    # Infinite loop for console functionality
    while true; do
        # Read user input
        read -p "" input

        # Parse the command (first word)
        cmd=$(echo $input | awk '{print $1}')

        # Parse the argument (rest of the line)
        arg=$(echo $input | awk '{print $2}')

        # Process commands using case statement
        case $cmd in
            ls)
                if [[ -n "$arg" ]]; then
                    ls $arg
                else
                    ls
                fi
                ;;
            pwd)
                pwd
                ;;
            hi)
                echo "Hello $USER"
                ;;
            exit)
                break
                ;;
            *)
                echo "Unknown command: $cmd"
                ;;
        esac

        # Empty line for readability
        echo ""
    done

+----------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                   |
|                                                          |
|  Show: Creating console.sh                               |
|  Expected: touch and chmod commands                      |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                   |
|                                                          |
|  Show: Script content in editor or via cat               |
|  Expected: Complete script with while loop               |
+----------------------------------------------------------+

Alternative - Create using cat heredoc:

    cat > ~/console.sh << 'EOF'
    #!/bin/bash

    # Infinite loop for console functionality
    while true; do
        # Read user input
        read -p "" input

        # Parse the command (first word)
        cmd=$(echo $input | awk '{print $1}')

        # Parse the argument (rest of the line)
        arg=$(echo $input | awk '{print $2}')

        # Process commands using case statement
        case $cmd in
            ls)
                if [[ -n "$arg" ]]; then
                    ls $arg
                else
                    ls
                fi
                ;;
            pwd)
                pwd
                ;;
            hi)
                echo "Hello $USER"
                ;;
            exit)
                break
                ;;
            *)
                echo "Unknown command: $cmd"
                ;;
        esac

        # Empty line for readability
        echo ""
    done
    EOF

    chmod +x ~/console.sh

+----------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                   |
|                                                          |
|  Show: cat console.sh output                             |
|  Expected: Complete script displayed                     |
+----------------------------------------------------------+


UNDERSTANDING THE SCRIPT LINE BY LINE
-------------------------------------

Line: while true; do
- Creates an infinite loop
- "true" always returns exit code 0
- Loop continues until "break" is encountered

Line: read -p "" input
- read: reads a line from standard input
- -p "": prompt string (empty in this case)
- input: variable to store the user's input

Line: cmd=$(echo $input | awk '{print $1}')
- Extracts the first word (command) from input
- awk '{print $1}' prints the first field

Line: arg=$(echo $input | awk '{print $2}')
- Extracts the second word (argument) from input
- Used for commands like "ls ~"

Line: case $cmd in
- Starts case statement to handle different commands

Line: ls)
- Matches when user types "ls"
- Checks if argument exists, lists directory

Line: pwd)
- Matches when user types "pwd"
- Runs pwd command to show current directory

Line: hi)
- Matches when user types "hi"
- $USER is environment variable with current username

Line: exit)
- Matches when user types "exit"
- "break" exits the while loop, ending script

Line: *)
- Default case for unknown commands

Line: done
- Ends the while loop


TASK 2: Run and test the script
-------------------------------

Start the script:

    ./console.sh

+----------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                   |
|                                                          |
|  Show: Starting ./console.sh                             |
|  Expected: Script starts, waiting for input              |
+----------------------------------------------------------+

Test ls command:

    ls ~

Expected output: Contents of home directory

+----------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                   |
|                                                          |
|  Show: ls ~ command in console.sh                        |
|  Expected: List of files in home directory               |
+----------------------------------------------------------+

Test pwd command:

    pwd

Expected output: /home/username (or current directory path)

+----------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                   |
|                                                          |
|  Show: pwd command in console.sh                         |
|  Expected: Current working directory path                |
+----------------------------------------------------------+

Test hi command:

    hi

Expected output: Hello <your_username>

+----------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                   |
|                                                          |
|  Show: hi command in console.sh                          |
|  Expected: Hello Username                                |
+----------------------------------------------------------+

Test exit command:

    exit

Expected: Script ends, returns to normal shell

+----------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                   |
|                                                          |
|  Show: exit command in console.sh                        |
|  Expected: Script terminates                             |
+----------------------------------------------------------+


COMPLETE TEST SESSION
---------------------

    $ ./console.sh

    ls ~
    Desktop  Documents  Downloads  console.sh

    pwd
    /home/username

    hi
    Hello username

    ls /tmp
    file1.txt  file2.txt

    exit
    $

+----------------------------------------------------------+
|                    >>> SCREENSHOT 9 <<<                   |
|                                                          |
|  Show: Complete test session                             |
|  Expected: All commands tested in sequence               |
+----------------------------------------------------------+


================================================================================
                        WHILE LOOP VARIATIONS
================================================================================

Counter-based loop:

    i=1
    while [[ $i -le 5 ]]; do
        echo "Count: $i"
        ((i++))
    done

Reading file line by line:

    while read line; do
        echo "Line: $line"
    done < /etc/passwd

Reading with IFS (field separator):

    while IFS=: read user pass uid gid name home shell; do
        echo "User: $user, Shell: $shell"
    done < /etc/passwd

Infinite loop with condition break:

    while true; do
        read -p "Enter (q to quit): " input
        [[ $input == "q" ]] && break
        echo "You entered: $input"
    done

Loop with multiple conditions:

    a=1
    b=10
    while [[ $a -lt 5 && $b -gt 5 ]]; do
        echo "a=$a, b=$b"
        ((a++))
        ((b--))
    done

================================================================================


================================================================================
                        READ COMMAND OPTIONS
================================================================================

| Option    | Description                              |
|-----------|------------------------------------------|
| -p        | Display prompt before reading            |
| -s        | Silent mode (don't echo input)           |
| -t N      | Timeout after N seconds                  |
| -n N      | Read only N characters                   |
| -r        | Raw input (don't interpret backslashes)  |
| -a array  | Read into array                          |

Examples:

    # With prompt
    read -p "Enter name: " name

    # Password (hidden input)
    read -s -p "Password: " pass

    # Timeout
    read -t 5 -p "Quick! " answer

    # Single character
    read -n 1 -p "Continue? (y/n) " choice

    # Into array
    read -a arr -p "Enter words: "
    echo "${arr[0]}"

================================================================================


================================================================================
                        PARSING USER INPUT
================================================================================

Using awk:

    input="ls /home/user"
    cmd=$(echo $input | awk '{print $1}')      # ls
    arg=$(echo $input | awk '{print $2}')      # /home/user

Using cut:

    cmd=$(echo $input | cut -d' ' -f1)         # First field
    arg=$(echo $input | cut -d' ' -f2-)        # Rest of line

Using parameter expansion:

    cmd=${input%% *}                           # First word
    arg=${input#* }                            # After first space

Using read with array:

    read -a parts <<< "$input"
    cmd=${parts[0]}
    arg=${parts[1]}

================================================================================


================================================================================
                        USEFUL ENVIRONMENT VARIABLES
================================================================================

| Variable | Description                              |
|----------|------------------------------------------|
| $USER    | Current username                         |
| $HOME    | Home directory path                      |
| $PWD     | Current working directory                |
| $SHELL   | Current shell path                       |
| $HOSTNAME| Computer hostname                        |
| $LOGNAME | Login name                               |

Examples:

    echo "Hello $USER"              # Hello username
    echo "Home: $HOME"              # Home: /home/username
    echo "Shell: $SHELL"            # Shell: /bin/bash

================================================================================


================================================================================
                        ENHANCED SCRIPT VERSION
================================================================================

Version with prompt and more commands:

    #!/bin/bash

    echo "Simple Console - Type 'help' for commands"

    while true; do
        read -p ">>> " input

        # Handle empty input
        [[ -z "$input" ]] && continue

        cmd=$(echo $input | awk '{print $1}')
        arg=$(echo $input | awk '{$1=""; print $0}' | xargs)

        case $cmd in
            ls)
                ls ${arg:-"."}
                ;;
            pwd)
                pwd
                ;;
            cd)
                cd ${arg:-$HOME} 2>/dev/null || echo "Directory not found"
                ;;
            hi)
                echo "Hello $USER"
                ;;
            date)
                date
                ;;
            whoami)
                whoami
                ;;
            help)
                echo "Commands: ls, pwd, cd, hi, date, whoami, exit"
                ;;
            exit|quit|q)
                echo "Goodbye!"
                break
                ;;
            *)
                echo "Unknown command: $cmd (type 'help')"
                ;;
        esac
    done

+----------------------------------------------------------+
|                    >>> SCREENSHOT 10 <<<                  |
|                                                          |
|  Show: Enhanced script with prompt                       |
|  Expected: More polished console experience              |
+----------------------------------------------------------+


================================================================================
                             TROUBLESHOOTING
================================================================================

Problem: Script doesn't exit with "exit"
Solution: Use "break" not "exit" to leave the loop
    exit)
        break    # Correct - exits loop
        ;;

Problem: "read: command not found"
Solution: Ensure shebang is #!/bin/bash not #!/bin/sh
    #!/bin/bash

Problem: $USER is empty
Solution: Use $LOGNAME or $(whoami) as alternative
    echo "Hello $(whoami)"

Problem: Arguments not parsed correctly
Solution: Check awk field numbers
    awk '{print $1}'   # First word
    awk '{print $2}'   # Second word
    awk '{print $NF}'  # Last word

Problem: Loop doesn't wait for input
Solution: Ensure read command is correct
    read -p "prompt" variable

Problem: Script exits immediately
Solution: Check for syntax errors
    bash -n console.sh    # Syntax check

================================================================================


================================================================================
                              SELF-REVIEW CHECKLIST
================================================================================

[ ] Script created and executable
[ ] Script uses while true for infinite loop
[ ] Script uses read to get user input
[ ] Script parses command and argument from input
[ ] ls command lists directory contents
[ ] ls with argument lists specified directory
[ ] pwd command shows current directory
[ ] hi command shows "Hello <username>"
[ ] exit command breaks the loop and ends script
[ ] Unknown commands show error message
[ ] Understand break vs exit in loops

================================================================================

