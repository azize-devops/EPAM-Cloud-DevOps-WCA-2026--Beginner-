INPUT/OUTPUT - ANSWER
=====================

This guide covers bash data streams (stdin, stdout, stderr),
redirection operators, and writing to files and terminal.


================================================================================
                        DATA STREAMS IN BASH
================================================================================

Every process has three standard streams:

| Stream | Name   | File Descriptor | Default      |
|--------|--------|-----------------|--------------|
| stdin  | Input  | 0               | Keyboard     |
| stdout | Output | 1               | Terminal     |
| stderr | Error  | 2               | Terminal     |

Diagram:

                    ┌─────────────┐
    stdin (0) ────► │             │ ────► stdout (1)
    (keyboard)      │   Process   │       (terminal)
                    │             │ ────► stderr (2)
                    └─────────────┘       (terminal)

================================================================================


TASK 1: Create the script
-------------------------

Create the script file:

    cd ~
    touch poem.sh
    chmod +x poem.sh

Edit the script:

    vim poem.sh
    # or
    nano poem.sh

Script content:

    #!/bin/bash

    # Ask user for filename
    read -p "Enter filename: " filename

    # Define the poem
    poem="An old silent pond...
    A frog jumps into the pond,
    splash! Silence again.

    Autumn moonlight-
    a worm digs silently
    into the chestnut.

    In the twilight rain
    these brilliant-hued hibiscus -
    A lovely sunset."

    # Write poem to file
    echo "$poem" > "$filename"

    # Output poem to terminal (stdout)
    echo "$poem"

    # Output message to stderr
    echo "Task finished" >&2

+----------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                   |
|                                                          |
|  Show: Creating poem.sh                                  |
|  Expected: touch and chmod commands                      |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                   |
|                                                          |
|  Show: Script content in editor or via cat               |
|  Expected: Complete script with poem                     |
+----------------------------------------------------------+

Alternative - Create using cat heredoc:

    cat > ~/poem.sh << 'SCRIPT'
    #!/bin/bash

    # Ask user for filename
    read -p "Enter filename: " filename

    # Define the poem using heredoc
    poem=$(cat << 'EOF'
    An old silent pond...
    A frog jumps into the pond,
    splash! Silence again.

    Autumn moonlight-
    a worm digs silently
    into the chestnut.

    In the twilight rain
    these brilliant-hued hibiscus -
    A lovely sunset.
    EOF
    )

    # Write poem to file
    echo "$poem" > "$filename"

    # Output poem to terminal (stdout)
    echo "$poem"

    # Output message to stderr
    echo "Task finished" >&2
    SCRIPT

    chmod +x ~/poem.sh

+----------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                   |
|                                                          |
|  Show: cat poem.sh output                                |
|  Expected: Complete script displayed                     |
+----------------------------------------------------------+


UNDERSTANDING THE SCRIPT LINE BY LINE
-------------------------------------

Line: read -p "Enter filename: " filename
- Prompts user and stores input in $filename

Line: poem="..."
- Stores multiline poem in variable
- Quotes preserve newlines and formatting

Line: echo "$poem" > "$filename"
- Writes poem to specified file
- > creates/overwrites the file

Line: echo "$poem"
- Outputs poem to stdout (terminal)

Line: echo "Task finished" >&2
- >&2 redirects output to stderr
- This message goes to error stream, not standard output


UNDERSTANDING >&2
-----------------

    echo "message"        # Goes to stdout (fd 1)
    echo "message" >&2    # Goes to stderr (fd 2)

The >&2 syntax means:
- & indicates file descriptor (not filename)
- 2 is the file descriptor for stderr
- Redirects stdout of this command to stderr

Why use stderr for "Task finished"?
- Allows separating program output from status messages
- stdout can be redirected while seeing status messages
- Follows Unix convention for status/error messages


TASK 2: Run with redirections
-----------------------------

Run the script with full redirections:

    ./poem.sh > /dev/null 2> stderr

When prompted, enter:
    output

+----------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                   |
|                                                          |
|  Show: Running ./poem.sh with redirections               |
|  Expected: No visible output (all redirected)            |
+----------------------------------------------------------+

Understanding the command:

    ./poem.sh           # Run the script
    > /dev/null         # Redirect stdout to null (discard)
    2> stderr           # Redirect stderr to file named "stderr"

Note: The prompt "Enter filename:" goes to stdout,
      so it will also be hidden when redirecting to /dev/null.

Alternative (see prompt):

    # First run without redirections to see the prompt
    ./poem.sh
    # Enter: output

    # Then verify with redirections
    ./poem.sh <<< "output" > /dev/null 2> stderr

+----------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                   |
|                                                          |
|  Show: Alternative run with here-string                  |
|  Expected: Silent execution                              |
+----------------------------------------------------------+


VERIFY THE RESULTS
------------------

Check that no output was displayed:
(Terminal should show nothing after running)

+----------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                   |
|                                                          |
|  Show: No terminal output after script run               |
|  Expected: Clean terminal, no poem visible               |
+----------------------------------------------------------+

Verify the poem file was created:

    cat output

Expected output:
    An old silent pond...
    A frog jumps into the pond,
    splash! Silence again.

    Autumn moonlight-
    a worm digs silently
    into the chestnut.

    In the twilight rain
    these brilliant-hued hibiscus -
    A lovely sunset.

+----------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                   |
|                                                          |
|  Show: cat output                                        |
|  Expected: Complete poem displayed                       |
+----------------------------------------------------------+

Verify the stderr file was created:

    cat stderr

Expected output:
    Task finished

+----------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                   |
|                                                          |
|  Show: cat stderr                                        |
|  Expected: "Task finished" message                       |
+----------------------------------------------------------+

List all created files:

    ls -la output stderr

+----------------------------------------------------------+
|                    >>> SCREENSHOT 9 <<<                   |
|                                                          |
|  Show: ls -la output stderr                              |
|  Expected: Both files exist with content                 |
+----------------------------------------------------------+


================================================================================
                        REDIRECTION OPERATORS
================================================================================

| Operator   | Description                           | Example           |
|------------|---------------------------------------|-------------------|
| >          | Redirect stdout to file (overwrite)   | cmd > file        |
| >>         | Redirect stdout to file (append)      | cmd >> file       |
| 2>         | Redirect stderr to file               | cmd 2> file       |
| 2>>        | Redirect stderr to file (append)      | cmd 2>> file      |
| &>         | Redirect both stdout and stderr       | cmd &> file       |
| 2>&1       | Redirect stderr to stdout             | cmd 2>&1          |
| 1>&2       | Redirect stdout to stderr             | cmd 1>&2          |
| >&2        | Same as 1>&2                          | echo "err" >&2    |
| <          | Redirect stdin from file              | cmd < file        |
| <<         | Here document                         | cmd << EOF        |
| <<<        | Here string                           | cmd <<< "string"  |

================================================================================


================================================================================
                        COMMON REDIRECTION PATTERNS
================================================================================

Discard output:

    command > /dev/null           # Discard stdout
    command 2> /dev/null          # Discard stderr
    command &> /dev/null          # Discard both

Save output to file:

    command > output.txt          # Save stdout
    command 2> errors.txt         # Save stderr
    command > out.txt 2> err.txt  # Save both separately
    command &> all.txt            # Save both together

Append to file:

    command >> log.txt            # Append stdout
    command 2>> errors.txt        # Append stderr

Combine streams:

    command 2>&1                  # Merge stderr into stdout
    command 2>&1 | tee log.txt    # Show and save output

Redirect to stderr:

    echo "Error message" >&2      # Send to stderr

================================================================================


================================================================================
                        SPECIAL FILES
================================================================================

| File         | Description                              |
|--------------|------------------------------------------|
| /dev/null    | Discards all data written to it          |
| /dev/zero    | Provides infinite zero bytes             |
| /dev/urandom | Provides random bytes                    |
| /dev/stdin   | Link to process's stdin                  |
| /dev/stdout  | Link to process's stdout                 |
| /dev/stderr  | Link to process's stderr                 |
| /dev/tty     | Current terminal                         |

Examples:

    # Discard output
    command > /dev/null

    # Always write to terminal (even if stdout redirected)
    echo "Always visible" > /dev/tty

================================================================================


================================================================================
                        HEREDOC AND HERE STRING
================================================================================

Heredoc (multi-line input):

    cat << EOF
    Line 1
    Line 2
    Line 3
    EOF

Heredoc to file:

    cat > file.txt << EOF
    Content goes here
    Multiple lines
    EOF

Here string (single line):

    cat <<< "This is a string"

    # Useful for providing input
    ./script.sh <<< "input value"

Quoted heredoc (no variable expansion):

    cat << 'EOF'
    $HOME will not expand
    EOF

================================================================================


================================================================================
                        ALTERNATIVE SCRIPT VERSIONS
================================================================================

Version with heredoc for poem:

    #!/bin/bash

    read -p "Enter filename: " filename

    # Write poem using heredoc
    cat << 'EOF' | tee "$filename"
    An old silent pond...
    A frog jumps into the pond,
    splash! Silence again.

    Autumn moonlight-
    a worm digs silently
    into the chestnut.

    In the twilight rain
    these brilliant-hued hibiscus -
    A lovely sunset.
    EOF

    echo "Task finished" >&2

Version with printf:

    #!/bin/bash

    read -p "Enter filename: " filename

    poem="An old silent pond...
    A frog jumps into the pond,
    splash! Silence again.

    Autumn moonlight-
    a worm digs silently
    into the chestnut.

    In the twilight rain
    these brilliant-hued hibiscus -
    A lovely sunset."

    # Write to file and stdout
    printf "%s\n" "$poem" | tee "$filename"

    # Status to stderr
    printf "Task finished\n" >&2

+----------------------------------------------------------+
|                    >>> SCREENSHOT 10 <<<                  |
|                                                          |
|  Show: Alternative script version with tee               |
|  Expected: Same functionality, different approach        |
+----------------------------------------------------------+


================================================================================
                             TROUBLESHOOTING
================================================================================

Problem: Prompt not visible when redirecting
Cause: read -p prompt goes to stdout
Solution: Use /dev/tty for prompt
    read -p "Enter filename: " filename < /dev/tty

Problem: "Task finished" not in stderr file
Solution: Ensure >&2 syntax is correct
    echo "Task finished" >&2    # Correct
    echo "Task finished" 2>     # Wrong

Problem: Poem formatting lost
Solution: Quote the variable
    echo "$poem"     # Preserves newlines
    echo $poem       # Loses formatting

Problem: File not created
Solution: Check filename variable
    echo "Writing to: $filename"
    ls -la "$filename"

Problem: Redirect order matters
    # Correct order:
    cmd > file 2>&1    # stderr goes where stdout goes (file)

    # Wrong order:
    cmd 2>&1 > file    # stderr goes to original stdout (terminal)

================================================================================


================================================================================
                              SELF-REVIEW CHECKLIST
================================================================================

[ ] Script created and executable
[ ] Script reads filename from user
[ ] Poem is written to specified file
[ ] Poem is output to stdout (terminal)
[ ] "Task finished" is sent to stderr with >&2
[ ] Running with > /dev/null hides stdout
[ ] Running with 2> stderr captures stderr to file
[ ] cat output shows the poem
[ ] cat stderr shows "Task finished"
[ ] Understand stdout vs stderr
[ ] Understand file descriptors (0, 1, 2)

================================================================================
