# WHAT IS BASH - ANSWER

This guide introduces Bash (Bourne Again SHell), the most widely used
command-line interpreter in Linux/Unix systems.


================================================================================
                           WHAT IS BASH?
================================================================================

Bash (Bourne Again SHell) is:
- A command-line interpreter (shell)
- A scripting language
- The default shell on most Linux distributions and macOS
- An enhanced replacement for the original Bourne Shell (sh)

Key features:
- Command history and editing
- Tab completion
- Job control
- Shell scripting capabilities
- Customizable environment

================================================================================


TASK 1: Open a terminal
-----------------------

Different ways to open a terminal:

On Linux (CentOS/Ubuntu):
- Applications > System Tools > Terminal
- Right-click desktop > Open Terminal
- Keyboard shortcut: Ctrl + Alt + T

On macOS:
- Applications > Utilities > Terminal
- Spotlight search (Cmd + Space) > type "Terminal"

On Windows (Git Bash):
- Start Menu > Git > Git Bash
- Right-click in folder > "Git Bash Here"

On Windows (WSL):
- Start Menu > Ubuntu (or other distro)
- Windows Terminal > Ubuntu tab

+----------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                   |
|                                                          |
|  Show: Terminal window opened                            |
|  Expected: Command prompt visible                        |
|  Example: [user@hostname ~]$ or user@hostname:~$         |
+----------------------------------------------------------+

Understanding the prompt:

    [user@hostname ~]$
     │    │        │ │
     │    │        │ └── $ = regular user (# = root)
     │    │        └──── ~ = current directory (home)
     │    └───────────── hostname = computer name
     └────────────────── user = username

+----------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                   |
|                                                          |
|  Show: Terminal prompt details                           |
|  Expected: Username, hostname, current directory visible |
+----------------------------------------------------------+


TASK 2: Verify bash version
---------------------------

Run the bash version command:

    bash --version

Expected output (example):
    GNU bash, version 4.4.20(1)-release (x86_64-redhat-linux-gnu)
    Copyright (C) 2016 Free Software Foundation, Inc.
    License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>

    This is free software; you are free to change and redistribute it.
    There is NO WARRANTY, to the extent permitted by law.

+----------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                   |
|                                                          |
|  Show: bash --version output                             |
|  Expected: GNU bash version and copyright info           |
+----------------------------------------------------------+

Alternative version check methods:

    # Check using $BASH_VERSION variable
    echo $BASH_VERSION

    # Check which shell is being used
    echo $SHELL

    # Check current shell process
    echo $0

    # Display shell and version
    echo "Shell: $SHELL, Version: $BASH_VERSION"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                   |
|                                                          |
|  Show: Alternative version check commands                |
|  Expected: BASH_VERSION and SHELL variables displayed    |
+----------------------------------------------------------+


TASK 3: Run some commands
-------------------------

Try running various basic commands:

Example 1: Hello World

    echo "Hello world"

Expected output:
    Hello world

+----------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                   |
|                                                          |
|  Show: echo "Hello world" output                         |
|  Expected: Hello world                                   |
+----------------------------------------------------------+

Example 2: Print current date and time

    date

Expected output:
    Tue Feb 10 12:30:45 UTC 2026

+----------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                   |
|                                                          |
|  Show: date command output                               |
|  Expected: Current date and time                         |
+----------------------------------------------------------+

Example 3: Print current directory

    pwd

Expected output:
    /home/username

+----------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                   |
|                                                          |
|  Show: pwd command output                                |
|  Expected: Current working directory path                |
+----------------------------------------------------------+

Example 4: List files in current directory

    ls
    ls -la

+----------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                   |
|                                                          |
|  Show: ls and ls -la output                              |
|  Expected: File listing in current directory             |
+----------------------------------------------------------+

Example 5: Display system information

    uname -a

Expected output:
    Linux hostname 3.10.0-1160.el7.x86_64 #1 SMP ... x86_64 GNU/Linux

+----------------------------------------------------------+
|                    >>> SCREENSHOT 9 <<<                   |
|                                                          |
|  Show: uname -a output                                   |
|  Expected: System and kernel information                 |
+----------------------------------------------------------+

Example 6: Show current user

    whoami

Expected output:
    username

+----------------------------------------------------------+
|                    >>> SCREENSHOT 10 <<<                  |
|                                                          |
|  Show: whoami command output                             |
|  Expected: Current username                              |
+----------------------------------------------------------+

Example 7: Show hostname

    hostname

+----------------------------------------------------------+
|                    >>> SCREENSHOT 11 <<<                  |
|                                                          |
|  Show: hostname command output                           |
|  Expected: Computer hostname                             |
+----------------------------------------------------------+

Example 8: Clear the screen

    clear
    # or use keyboard shortcut: Ctrl + L

+----------------------------------------------------------+
|                    >>> SCREENSHOT 12 <<<                  |
|                                                          |
|  Show: Terminal after clear command                      |
|  Expected: Clean terminal with only prompt               |
+----------------------------------------------------------+


================================================================================
                        BASIC BASH COMMANDS REFERENCE
================================================================================

| Command        | Description                              | Example           |
|----------------|------------------------------------------|-------------------|
| echo           | Print text to screen                     | echo "Hello"      |
| pwd            | Print working directory                  | pwd               |
| ls             | List directory contents                  | ls -la            |
| cd             | Change directory                         | cd /home          |
| date           | Display date and time                    | date              |
| whoami         | Display current username                 | whoami            |
| hostname       | Display computer name                    | hostname          |
| uname          | Display system information               | uname -a          |
| clear          | Clear terminal screen                    | clear             |
| history        | Show command history                     | history           |
| man            | Show manual page                         | man ls            |
| exit           | Exit the shell                           | exit              |

================================================================================


================================================================================
                          ECHO COMMAND OPTIONS
================================================================================

| Command                    | Description                              |
|----------------------------|------------------------------------------|
| echo "Hello"               | Print text with newline                  |
| echo -n "Hello"            | Print without trailing newline           |
| echo -e "Hello\nWorld"     | Enable escape sequences (\n, \t, etc.)   |
| echo $VARIABLE             | Print variable value                     |
| echo "Value: $VAR"         | Print text with variable interpolation   |
| echo 'Value: $VAR'         | Print literal text (no interpolation)    |
| echo *                     | Print all files in current directory     |
| echo "Hello" > file.txt    | Redirect output to file (overwrite)      |
| echo "Hello" >> file.txt   | Redirect output to file (append)         |

Example escape sequences with -e:
    echo -e "Line1\nLine2"      # Newline
    echo -e "Col1\tCol2"        # Tab
    echo -e "\e[31mRed\e[0m"    # Colored text (red)

================================================================================


================================================================================
                          KEYBOARD SHORTCUTS
================================================================================

| Shortcut       | Description                              |
|----------------|------------------------------------------|
| Ctrl + C       | Cancel current command                   |
| Ctrl + D       | Exit shell (EOF)                         |
| Ctrl + L       | Clear screen (same as 'clear')           |
| Ctrl + A       | Move cursor to beginning of line         |
| Ctrl + E       | Move cursor to end of line               |
| Ctrl + U       | Delete from cursor to beginning          |
| Ctrl + K       | Delete from cursor to end                |
| Ctrl + W       | Delete word before cursor                |
| Ctrl + R       | Search command history                   |
| Tab            | Auto-complete command or filename        |
| Tab Tab        | Show all possible completions            |
| Up Arrow       | Previous command in history              |
| Down Arrow     | Next command in history                  |

================================================================================


================================================================================
                         SHELL TYPES AND DIFFERENCES
================================================================================

| Shell  | Description                                    | Path          |
|--------|------------------------------------------------|---------------|
| bash   | Bourne Again Shell (most common)               | /bin/bash     |
| sh     | Bourne Shell (original)                        | /bin/sh       |
| zsh    | Z Shell (macOS default since Catalina)         | /bin/zsh      |
| fish   | Friendly Interactive Shell                     | /usr/bin/fish |
| ksh    | Korn Shell                                     | /bin/ksh      |
| csh    | C Shell                                        | /bin/csh      |
| tcsh   | Enhanced C Shell                               | /bin/tcsh     |

Check available shells on your system:
    cat /etc/shells

================================================================================


================================================================================
                           BASH CONFIGURATION FILES
================================================================================

| File              | When Loaded                              |
|-------------------|------------------------------------------|
| /etc/profile      | System-wide, login shells                |
| /etc/bashrc       | System-wide, all interactive shells      |
| ~/.bash_profile   | User-specific, login shells              |
| ~/.bashrc         | User-specific, interactive non-login     |
| ~/.bash_logout    | User-specific, on logout                 |
| ~/.bash_history   | Command history storage                  |

Login vs Non-Login Shell:
- Login shell: When you log in (SSH, TTY login)
- Non-login shell: Opening terminal in GUI

================================================================================


================================================================================
                             TROUBLESHOOTING
================================================================================

Problem: "command not found"
Solutions:
- Check spelling of the command
- Verify the command is installed: which <command>
- Check PATH: echo $PATH
- Install the package containing the command

Problem: Permission denied
Solutions:
- Check file permissions: ls -la
- Run with sudo if needed: sudo <command>
- Make script executable: chmod +x script.sh

Problem: bash --version shows old version
Solutions:
- Check if newer bash is installed elsewhere
- On macOS, install newer bash via Homebrew: brew install bash
- Update your system packages

Problem: Different shell than expected
Solutions:
- Check current shell: echo $0
- Change default shell: chsh -s /bin/bash

================================================================================


================================================================================
                              SELF-REVIEW CHECKLIST
================================================================================

[ ] Terminal opened successfully
[ ] Command prompt visible and understood
[ ] bash --version shows version information
[ ] $BASH_VERSION variable displays correctly
[ ] echo "Hello world" prints Hello world
[ ] date command shows current date/time
[ ] pwd shows current directory
[ ] ls lists directory contents
[ ] Basic keyboard shortcuts work (Ctrl+L, Tab)

================================================================================

