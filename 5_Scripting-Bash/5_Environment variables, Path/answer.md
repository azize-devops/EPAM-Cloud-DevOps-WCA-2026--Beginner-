# ENVIRONMENT VARIABLES, PATH - ANSWER

This guide covers environment variables, the export command, PATH manipulation,
and making scripts globally accessible.


================================================================================
                    UNDERSTANDING ENVIRONMENT VARIABLES
================================================================================

Environment Variables:
- Special variables available to all child processes
- Used to configure system behavior and applications
- Created using the export command
- Inherited by child processes automatically

Local vs Environment Variables:
- Local: VAR=value (available only in current shell)
- Environment: export VAR=value (available to child processes)

Common Environment Variables:
- $HOME: User's home directory
- $USER: Current username
- $PATH: Directories to search for executables
- $SHELL: Current shell path
- $PWD: Current working directory
- $EDITOR: Default text editor

================================================================================


TASK 1: Create homework folder
------------------------------

Create the homework folder in your home directory:

    cd ~
    mkdir homework

Verify the folder was created:

    ls -la | grep homework

+----------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                   |
|                                                          |
|  Show: mkdir homework and ls output                      |
|  Expected: homework folder in listing                    |
+----------------------------------------------------------+


TASK 2: Create SURNAME environment variable
-------------------------------------------

Create the environment variable using export:

    export SURNAME=YourSurname

    # Example:
    export SURNAME=Smith

Verify the variable is set:

    echo $SURNAME

Expected output:
    Smith

+----------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                   |
|                                                          |
|  Show: export SURNAME and echo $SURNAME                  |
|  Expected: Surname value displayed                       |
+----------------------------------------------------------+

Understanding export:

    # Local variable (NOT available to child processes)
    SURNAME=Smith
    bash -c 'echo $SURNAME'    # Empty - child can't see it

    # Environment variable (available to child processes)
    export SURNAME=Smith
    bash -c 'echo $SURNAME'    # Smith - child can see it

+----------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                   |
|                                                          |
|  Show: Difference between local and exported variable    |
|  Expected: Child process can access exported variable    |
+----------------------------------------------------------+


TASK 3: Verify SURNAME in environment using env
-----------------------------------------------

Use env command to list all environment variables:

    env | grep SURNAME

Expected output:
    SURNAME=Smith

Alternative methods:

    # Using printenv
    printenv SURNAME

    # Using printenv to list all
    printenv | grep SURNAME

    # Using export to list exported variables
    export | grep SURNAME

    # Using set (shows all variables, including local)
    set | grep SURNAME

+----------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                   |
|                                                          |
|  Show: env | grep SURNAME output                         |
|  Expected: SURNAME=YourSurname                           |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                   |
|                                                          |
|  Show: printenv SURNAME output                           |
|  Expected: YourSurname                                   |
+----------------------------------------------------------+


TASK 4: Create script that prints SURNAME
-----------------------------------------

Create a script in the homework folder:

    cd ~/homework
    touch my_script.sh
    chmod +x my_script.sh

Edit the script:

    vim my_script.sh
    # or
    nano my_script.sh

Script content:

    #!/bin/bash
    echo $SURNAME

+----------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                   |
|                                                          |
|  Show: Creating my_script.sh                             |
|  Expected: touch and chmod commands                      |
+----------------------------------------------------------+

Alternative - Create using echo:

    echo '#!/bin/bash' > ~/homework/my_script.sh
    echo 'echo $SURNAME' >> ~/homework/my_script.sh
    chmod +x ~/homework/my_script.sh

Alternative - Create using cat heredoc:

    cat > ~/homework/my_script.sh << 'EOF'
    #!/bin/bash
    echo $SURNAME
    EOF
    chmod +x ~/homework/my_script.sh

+----------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                   |
|                                                          |
|  Show: Script content                                    |
|  Expected: #!/bin/bash and echo $SURNAME                 |
+----------------------------------------------------------+

Verify script content:

    cat ~/homework/my_script.sh

Expected output:
    #!/bin/bash
    echo $SURNAME

Test the script with full path:

    ~/homework/my_script.sh

Expected output:
    Smith

+----------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                   |
|                                                          |
|  Show: Running script with full path                     |
|  Expected: Surname displayed                             |
+----------------------------------------------------------+


TASK 5: Add homework folder to PATH
-----------------------------------

Add the homework folder to your PATH:

    export PATH="$PATH:$HOME/homework"

    # Or using explicit path:
    export PATH="$PATH:/home/username/homework"

Verify PATH was updated:

    echo $PATH

You should see ~/homework (or /home/username/homework) at the end of PATH.

+----------------------------------------------------------+
|                    >>> SCREENSHOT 9 <<<                   |
|                                                          |
|  Show: export PATH command                               |
|  Expected: Command executed                              |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 10 <<<                  |
|                                                          |
|  Show: echo $PATH output                                 |
|  Expected: homework folder visible in PATH               |
+----------------------------------------------------------+

Understanding PATH:
- PATH is a colon-separated list of directories
- Shell searches these directories for executables
- Searched left to right (first match wins)
- Add to end: PATH="$PATH:/new/path"
- Add to beginning: PATH="/new/path:$PATH"


TASK 6: Run script by name only
-------------------------------

Now you can run the script from anywhere by typing only its name:

    my_script.sh

Expected output:
    Smith

+----------------------------------------------------------+
|                    >>> SCREENSHOT 11 <<<                  |
|                                                          |
|  Show: Running my_script.sh without path                 |
|  Expected: Surname displayed                             |
+----------------------------------------------------------+

Verify which script is being executed:

    which my_script.sh

Expected output:
    /home/username/homework/my_script.sh

+----------------------------------------------------------+
|                    >>> SCREENSHOT 12 <<<                  |
|                                                          |
|  Show: which my_script.sh output                         |
|  Expected: Full path to script in homework folder        |
+----------------------------------------------------------+

Test from different directories:

    cd /tmp
    my_script.sh

    cd /
    my_script.sh

The script should work from any directory.

+----------------------------------------------------------+
|                    >>> SCREENSHOT 13 <<<                  |
|                                                          |
|  Show: Running script from different directories         |
|  Expected: Same output (Surname) regardless of location  |
+----------------------------------------------------------+


MAKING CHANGES PERMANENT
------------------------

The changes made above are temporary (lost when terminal closes).

To make them permanent, add to shell configuration file:

For Bash (~/.bashrc or ~/.bash_profile):

    echo 'export SURNAME=Smith' >> ~/.bashrc
    echo 'export PATH="$PATH:$HOME/homework"' >> ~/.bashrc

    # Reload configuration
    source ~/.bashrc

+----------------------------------------------------------+
|                    >>> SCREENSHOT 14 <<<                  |
|                                                          |
|  Show: Adding to .bashrc for persistence                 |
|  Expected: export commands added to file                 |
+----------------------------------------------------------+

For system-wide (all users):

    # Add to /etc/environment or /etc/profile.d/custom.sh
    sudo vim /etc/profile.d/custom.sh


UNSETTING ENVIRONMENT VARIABLES
-------------------------------

To remove an environment variable:

    unset SURNAME

    # Verify it's removed
    echo $SURNAME      # Empty
    env | grep SURNAME # No output

+----------------------------------------------------------+
|                    >>> SCREENSHOT 15 <<<                  |
|                                                          |
|  Show: unset SURNAME and verification                    |
|  Expected: Variable no longer exists                     |
+----------------------------------------------------------+


================================================================================
                    ENVIRONMENT VARIABLE COMMANDS
================================================================================

| Command                    | Description                              |
|----------------------------|------------------------------------------|
| export VAR=value           | Create/set environment variable          |
| export VAR                 | Export existing variable to environment  |
| unset VAR                  | Remove variable                          |
| env                        | List all environment variables           |
| printenv                   | Print environment variables              |
| printenv VAR               | Print specific variable                  |
| set                        | List all variables (including local)     |
| export                     | List all exported variables              |
| export -n VAR              | Remove export (make local)               |

================================================================================


================================================================================
                           PATH MANIPULATION
================================================================================

| Operation                  | Command                                  |
|----------------------------|------------------------------------------|
| View PATH                  | echo $PATH                               |
| Add to end                 | export PATH="$PATH:/new/path"            |
| Add to beginning           | export PATH="/new/path:$PATH"            |
| Remove from PATH           | export PATH="${PATH//:\/path\/to\/remove/}" |
| Check command location     | which command                            |
| Check all locations        | type -a command                          |
| Search PATH manually       | echo $PATH | tr ':' '\n'                 |

PATH Format:
    /usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/home/user/homework
    └─────┬─────┘ └───┬───┘ └─┬─┘ └───┬────┘ └─┬──┘ └───────┬───────┘
          │          │       │        │        │            │
    Searched left to right, first match wins

================================================================================


================================================================================
                    COMMON ENVIRONMENT VARIABLES
================================================================================

| Variable     | Description                          | Example Value          |
|--------------|--------------------------------------|------------------------|
| HOME         | User's home directory                | /home/username         |
| USER         | Current username                     | username               |
| PATH         | Executable search path               | /usr/bin:/bin          |
| SHELL        | Current shell                        | /bin/bash              |
| PWD          | Current directory                    | /home/username         |
| OLDPWD       | Previous directory                   | /tmp                   |
| TERM         | Terminal type                        | xterm-256color         |
| EDITOR       | Default editor                       | vim                    |
| LANG         | Language/locale                      | en_US.UTF-8            |
| PS1          | Primary prompt                       | [\u@\h \W]\$           |
| HOSTNAME     | Computer hostname                    | mycomputer             |
| LOGNAME      | Login name                           | username               |

================================================================================


================================================================================
                    SHELL CONFIGURATION FILES
================================================================================

| File              | When Loaded                    | Use For                |
|-------------------|--------------------------------|------------------------|
| /etc/profile      | Login shell (system-wide)      | System settings        |
| /etc/bashrc       | Interactive shell (system)     | System aliases         |
| ~/.bash_profile   | Login shell (user)             | User PATH, exports     |
| ~/.bashrc         | Interactive shell (user)       | User aliases, functions|
| ~/.profile        | Login shell (sh compatible)    | Portable settings      |

Loading Order (Login Shell):
    1. /etc/profile
    2. ~/.bash_profile OR ~/.bash_login OR ~/.profile

Loading Order (Interactive Non-Login):
    1. /etc/bashrc
    2. ~/.bashrc

================================================================================


================================================================================
                             TROUBLESHOOTING
================================================================================

Problem: "command not found" after adding to PATH
Solutions:
- Check PATH is correct: echo $PATH
- Verify directory exists: ls -la ~/homework
- Verify script is executable: ls -la ~/homework/my_script.sh
- Check for typos in script name

Problem: Variable not available in child process
Solution: Use export to make it environment variable
    export SURNAME=Smith

Problem: Changes lost after terminal restart
Solution: Add export commands to ~/.bashrc

Problem: env doesn't show variable but echo does
Solution: Variable is local, not exported. Use:
    export VARNAME

Problem: PATH changes not working
Solutions:
- Don't forget quotes: export PATH="$PATH:/new"
- Check syntax (colons, not spaces)
- Reload: source ~/.bashrc

Problem: Wrong version of command running
Solution: Check with 'which' and 'type -a':
    which python
    type -a python

================================================================================


================================================================================
                              SELF-REVIEW CHECKLIST
================================================================================

[ ] homework folder created in home directory
[ ] SURNAME environment variable created with export
[ ] env | grep SURNAME shows the variable
[ ] my_script.sh created in homework folder
[ ] my_script.sh is executable (chmod +x)
[ ] my_script.sh contains echo $SURNAME
[ ] Script works with full path: ~/homework/my_script.sh
[ ] homework folder added to PATH
[ ] echo $PATH shows homework folder
[ ] Script runs with just: my_script.sh
[ ] which my_script.sh shows correct path
[ ] Script works from any directory

================================================================================

