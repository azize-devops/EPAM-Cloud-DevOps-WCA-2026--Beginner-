# RUNNING COMMANDS - ANSWER

This guide covers basic command-line operations including navigation,
file creation, copying, editing, and script execution.


TASK 1: Open a terminal
-----------------------

Open your preferred terminal application:

On Linux:
- Applications > System Tools > Terminal
- Keyboard shortcut: Ctrl + Alt + T

On macOS:
- Applications > Utilities > Terminal

On Windows:
- Git Bash or WSL terminal

+----------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                   |
|                                                          |
|  Show: Terminal window opened                            |
|  Expected: Command prompt visible                        |
+----------------------------------------------------------+


TASK 2: Go to home folder using cd
----------------------------------

Navigate to your home directory:

    cd ~

    # Or simply:
    cd

    # Or using full path:
    cd /home/username

Verify you're in home directory:

    pwd

Expected output:
    /home/username

+----------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                   |
|                                                          |
|  Show: cd ~ and pwd commands                             |
|  Expected: Home directory path displayed                 |
+----------------------------------------------------------+

Understanding cd command:

    cd ~           # Go to home directory
    cd             # Same as cd ~ (go to home)
    cd /           # Go to root directory
    cd ..          # Go up one directory level
    cd ../..       # Go up two directory levels
    cd -           # Go to previous directory
    cd /path/to/dir # Go to absolute path
    cd path/to/dir  # Go to relative path


TASK 3: Create homework folder using mkdir
------------------------------------------

Create a new directory called homework:

    mkdir homework

Verify the directory was created:

    ls -la

Expected output (partial):
    drwxr-xr-x  2 user user 4096 Feb 10 12:00 homework

+----------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                   |
|                                                          |
|  Show: mkdir homework and ls -la output                  |
|  Expected: homework directory in listing                 |
+----------------------------------------------------------+

Understanding mkdir command:

    mkdir dirname           # Create single directory
    mkdir dir1 dir2 dir3    # Create multiple directories
    mkdir -p path/to/dir    # Create nested directories
    mkdir -v dirname        # Verbose (show what's created)
    mkdir -m 755 dirname    # Create with specific permissions

+----------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                   |
|                                                          |
|  Show: ls -la showing homework folder details            |
|  Expected: Directory permissions and ownership           |
+----------------------------------------------------------+


TASK 4: Create file1 using touch
--------------------------------

Navigate into homework folder and create file1:

    cd homework
    touch file1

    # Or create without changing directory:
    touch ~/homework/file1

Verify the file was created:

    ls -la

Expected output:
    -rw-r--r-- 1 user user 0 Feb 10 12:01 file1

+----------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                   |
|                                                          |
|  Show: touch file1 and ls -la output                     |
|  Expected: file1 with size 0 (empty file)                |
+----------------------------------------------------------+

Understanding touch command:

    touch filename           # Create empty file (or update timestamp)
    touch file1 file2 file3  # Create multiple files
    touch -t 202602101200 file # Set specific timestamp
    touch -r ref_file file   # Use timestamp from reference file

Alternative ways to create files:

    # Using echo
    echo "" > file1

    # Using cat
    cat > file1
    # Press Ctrl+D to save

    # Using text editor
    vim file1
    nano file1

+----------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                   |
|                                                          |
|  Show: File created verification                         |
|  Expected: file1 exists in homework directory            |
+----------------------------------------------------------+


TASK 5: Copy file1 to file2 using cp
------------------------------------

Copy file1 to file2 in the same directory:

    cp file1 file2

    # Or with full paths:
    cp ~/homework/file1 ~/homework/file2

Verify both files exist:

    ls -la

Expected output:
    -rw-r--r-- 1 user user 0 Feb 10 12:01 file1
    -rw-r--r-- 1 user user 0 Feb 10 12:02 file2

+----------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                   |
|                                                          |
|  Show: cp file1 file2 and ls output                      |
|  Expected: Both file1 and file2 in listing               |
+----------------------------------------------------------+

Understanding cp command:

    cp source dest           # Copy file
    cp file1 file2 file3 dir # Copy multiple files to directory
    cp -r dir1 dir2          # Copy directory recursively
    cp -i source dest        # Interactive (prompt before overwrite)
    cp -v source dest        # Verbose (show what's copied)
    cp -p source dest        # Preserve permissions and timestamps
    cp -a source dest        # Archive (preserve everything)

+----------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                   |
|                                                          |
|  Show: ls ~/homework/ showing both files                 |
|  Expected: file1 file2                                   |
+----------------------------------------------------------+


TASK 6: Edit file2 and add echo command
---------------------------------------

Edit file2 using a text editor:

Method 1 - Using vim:
    vim file2
    # Press 'i' to enter insert mode
    # Type: echo "hello world"
    # Press Esc, then type :wq to save and quit

Method 2 - Using nano:
    nano file2
    # Type: echo "hello world"
    # Press Ctrl+O to save, Ctrl+X to exit

Method 3 - Using echo (command line):
    echo 'echo "hello world"' > file2

Method 4 - Using cat with heredoc:
    cat > file2 << 'EOF'
    echo "hello world"
    EOF

+----------------------------------------------------------+
|                    >>> SCREENSHOT 9 <<<                   |
|                                                          |
|  Show: Editing file2 in text editor                      |
|  Expected: echo "hello world" in the file                |
+----------------------------------------------------------+

Verify file content:

    cat file2

Expected output:
    echo "hello world"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 10 <<<                  |
|                                                          |
|  Show: cat file2 output                                  |
|  Expected: echo "hello world"                            |
+----------------------------------------------------------+


TASK 7: Make file2 executable
-----------------------------

Add execute permission to file2:

    chmod +x file2

    # Or be more specific:
    chmod u+x file2      # Add execute for user only
    chmod 755 file2      # rwxr-xr-x (common for scripts)

Verify permissions changed:

    ls -la file2

Expected output (before):
    -rw-r--r-- 1 user user 20 Feb 10 12:03 file2

Expected output (after chmod +x):
    -rwxr-xr-x 1 user user 20 Feb 10 12:03 file2

+----------------------------------------------------------+
|                    >>> SCREENSHOT 11 <<<                  |
|                                                          |
|  Show: chmod +x file2 and ls -la output                  |
|  Expected: 'x' permission visible in file listing        |
+----------------------------------------------------------+

Understanding chmod:

    chmod +x file        # Add execute for all
    chmod -x file        # Remove execute for all
    chmod u+x file       # Add execute for user (owner)
    chmod g+x file       # Add execute for group
    chmod o+x file       # Add execute for others
    chmod 755 file       # rwxr-xr-x (owner: rwx, group: r-x, others: r-x)
    chmod 700 file       # rwx------ (owner only)

Permission numbers:
    4 = read (r)
    2 = write (w)
    1 = execute (x)

    7 = 4+2+1 = rwx
    6 = 4+2   = rw-
    5 = 4+1   = r-x
    4 = 4     = r--


TASK 8: Execute file2 script
----------------------------

Run the script using one of these methods:

Method 1 - Using ./
    ./file2

Method 2 - Using bash explicitly
    bash file2

Method 3 - Using source (runs in current shell)
    source file2
    # or
    . file2

Method 4 - Using full path
    ~/homework/file2

Expected output:
    hello world

+----------------------------------------------------------+
|                    >>> SCREENSHOT 12 <<<                  |
|                                                          |
|  Show: ./file2 execution                                 |
|  Expected: hello world                                   |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 13 <<<                  |
|                                                          |
|  Show: bash file2 execution                              |
|  Expected: hello world                                   |
+----------------------------------------------------------+

Why ./ is needed:
- For security, current directory (.) is not in PATH
- Using ./ explicitly tells shell to look in current directory
- Without ./, shell searches PATH directories only

Check your PATH:
    echo $PATH


FINAL VERIFICATION
------------------

Verify all tasks completed:

    ls ~/homework/

Expected output:
    file1  file2

+----------------------------------------------------------+
|                    >>> SCREENSHOT 14 <<<                  |
|                                                          |
|  Show: ls ~/homework/ final output                       |
|  Expected: file1 file2                                   |
+----------------------------------------------------------+


================================================================================
                        FILE OPERATION COMMANDS
================================================================================

| Command              | Description                                      |
|----------------------|--------------------------------------------------|
| touch file           | Create empty file or update timestamp            |
| mkdir dir            | Create directory                                 |
| mkdir -p a/b/c       | Create nested directories                        |
| cp src dest          | Copy file                                        |
| cp -r src dest       | Copy directory recursively                       |
| mv src dest          | Move or rename file/directory                    |
| rm file              | Remove file                                      |
| rm -r dir            | Remove directory recursively                     |
| rm -rf dir           | Force remove (no prompts)                        |
| cat file             | Display file contents                            |
| less file            | View file with pagination                        |
| head file            | Show first 10 lines                              |
| tail file            | Show last 10 lines                               |

================================================================================


================================================================================
                        NAVIGATION COMMANDS
================================================================================

| Command              | Description                                      |
|----------------------|--------------------------------------------------|
| pwd                  | Print working directory                          |
| cd dir               | Change to directory                              |
| cd ~                 | Go to home directory                             |
| cd ..                | Go up one level                                  |
| cd -                 | Go to previous directory                         |
| ls                   | List directory contents                          |
| ls -la               | List all files with details                      |
| ls -lh               | List with human-readable sizes                   |

================================================================================


================================================================================
                        SCRIPT EXECUTION METHODS
================================================================================

| Method               | Description                                      |
|----------------------|--------------------------------------------------|
| ./script.sh          | Execute in new subshell (needs +x permission)    |
| bash script.sh       | Execute with bash (no +x needed)                 |
| sh script.sh         | Execute with sh                                  |
| source script.sh     | Execute in current shell                         |
| . script.sh          | Same as source                                   |
| /full/path/script.sh | Execute using absolute path                      |

Note: ./script.sh requires execute permission, bash script.sh does not.

================================================================================


================================================================================
                             TROUBLESHOOTING
================================================================================

Problem: "Permission denied" when running ./file2
Solutions:
- Make file executable: chmod +x file2
- Use bash instead: bash file2

Problem: "mkdir: cannot create directory: File exists"
Solution: Directory already exists, use ls to verify

Problem: "cp: cannot stat 'file1': No such file or directory"
Solutions:
- Verify file exists: ls -la
- Check you're in correct directory: pwd

Problem: "command not found" when running ./file2
Solutions:
- Verify file exists in current directory
- Check file has correct content: cat file2
- Ensure shebang is correct if used

Problem: Script runs but no output
Solutions:
- Verify file content: cat file2
- Check for typos in echo command
- Run with bash -x file2 to debug

================================================================================


================================================================================
                              SELF-REVIEW CHECKLIST
================================================================================

[ ] Terminal opened successfully
[ ] Navigated to home directory (cd ~)
[ ] Created homework directory (mkdir homework)
[ ] Created file1 in homework (touch file1)
[ ] Copied file1 to file2 (cp file1 file2)
[ ] Edited file2 with echo "hello world"
[ ] Made file2 executable (chmod +x file2)
[ ] Executed file2 and got "hello world" output
[ ] ls ~/homework/ shows: file1 file2

================================================================================

