# SCRIPTS - ANSWER

This guide covers creating, writing, and executing bash scripts with proper
shebang declarations and file permissions.


## TASK 1: Open a terminal

Open your preferred terminal application.

> **Screenshot 1**
> - Show: Terminal window opened
> - Expected: Command prompt visible


## TASK 2: Create script.sh in home directory

Navigate to home directory and create script.sh:

```bash
cd ~
touch script.sh

# Verify file was created
ls -la script.sh
```

> **Screenshot 2**
> - Show: Creating script.sh file
> - Expected: File created in home directory


## TASK 3: Make file executable and add shebang

Step 1: Make the file executable

```bash
chmod +x script.sh

# Verify permissions
ls -la script.sh
```

Expected output:
```
-rwxr-xr-x 1 user user 0 Feb 10 12:00 script.sh
```

> **Screenshot 3**
> - Show: chmod +x and ls -la output
> - Expected: Execute permission (x) visible

Step 2: Add the shebang line

Open the file with an editor and add the shebang as the first line:

```bash
vim script.sh
# or
nano script.sh
```

Add this as the FIRST line:

```bash
#!/bin/bash
```

> **Screenshot 4**
> - Show: Adding shebang in editor
> - Expected: #!/bin/bash as first line

Understanding the Shebang (#!):

```bash
#!/bin/bash      # Use bash interpreter
#!/bin/sh        # Use sh (Bourne shell)
#!/usr/bin/env bash  # More portable (finds bash in PATH)
#!/usr/bin/python3   # For Python scripts
#!/usr/bin/perl      # For Perl scripts
```

Why use shebang?
- Tells the system which interpreter to use
- Makes script portable across systems
- Without it, script runs in current shell (may cause issues)


## TASK 4: Write the script

Edit script.sh and add the following content:

Method 1 - Using vim:

```bash
vim script.sh
```

Press 'i' to enter insert mode, then type:

```bash
#!/bin/bash

# Write "Creating folder" to console
echo "Creating folder"

# Create a folder called Name_Surname
mkdir Name_Surname

# List folders in current directory
ls

# Write "Creating files" to console
echo "Creating files"

# Create two files called Name and Surname inside Name_Surname folder
touch Name_Surname/Name Name_Surname/Surname

# List contents of Name_Surname folder
ls Name_Surname
```

Press Esc, then type :wq to save and quit.

> **Screenshot 5**
> - Show: Complete script in editor
> - Expected: All commands visible with shebang

Method 2 - Using cat with heredoc:

```bash
cat > script.sh << 'EOF'
#!/bin/bash

# Write "Creating folder" to console
echo "Creating folder"

# Create a folder called Name_Surname
mkdir Name_Surname

# List folders in current directory
ls

# Write "Creating files" to console
echo "Creating files"

# Create two files called Name and Surname inside Name_Surname folder
touch Name_Surname/Name Name_Surname/Surname

# List contents of Name_Surname folder
ls Name_Surname
EOF
```

> **Screenshot 6**
> - Show: Creating script with heredoc
> - Expected: Script created via command line

Verify the script content:

```bash
cat script.sh
```

Expected output:
```
#!/bin/bash

# Write "Creating folder" to console
echo "Creating folder"

# Create a folder called Name_Surname
mkdir Name_Surname

# List folders in current directory
ls

# Write "Creating files" to console
echo "Creating files"

# Create two files called Name and Surname inside Name_Surname folder
touch Name_Surname/Name Name_Surname/Surname

# List contents of Name_Surname folder
ls Name_Surname
```

> **Screenshot 7**
> - Show: cat script.sh output
> - Expected: Complete script content displayed


## TASK 5: Execute the script

Run the script:

```bash
./script.sh

# Or using full path:
~/script.sh

# Or using bash:
bash script.sh
```

Expected output:
```
Creating folder
Name_Surname  script.sh  (and other files/folders in home)
Creating files
Name  Surname
```

> **Screenshot 8**
> - Show: Script execution with ./script.sh
> - Expected: All output messages and folder listings

> **Screenshot 9**
> - Show: Output showing "Creating folder" and ls output
> - Expected: Name_Surname visible in directory listing

> **Screenshot 10**
> - Show: Output showing "Creating files" and ls output
> - Expected: Name and Surname files listed


## VERIFICATION

After running the script, verify:

```bash
# Check Name_Surname folder exists
ls -la ~/Name_Surname
```

Expected output:
```
total 0
drwxr-xr-x 2 user user 4096 Feb 10 12:00 .
drwxr-xr-x 5 user user 4096 Feb 10 12:00 ..
-rw-r--r-- 1 user user    0 Feb 10 12:00 Name
-rw-r--r-- 1 user user    0 Feb 10 12:00 Surname
```

> **Screenshot 11**
> - Show: ls -la ~/Name_Surname output
> - Expected: Name and Surname files present


## RUNNING THE SCRIPT AGAIN

If you run the script again, mkdir will fail because the folder exists:

```bash
./script.sh
mkdir: cannot create directory 'Name_Surname': File exists
```

To handle this, you can modify the script:

```bash
# Create folder only if it doesn't exist
mkdir -p Name_Surname

# Or add error handling
if [ ! -d "Name_Surname" ]; then
    mkdir Name_Surname
fi
```

> **Screenshot 12**
> - Show: Running script twice (error message)
> - Expected: "File exists" error from mkdir


## IMPROVED SCRIPT VERSION

Here's an improved version with better practices:

```bash
#!/bin/bash
#
# Script: script.sh
# Description: Creates a folder and files
# Author: Your Name
# Date: 2026-02-10

# Variables
FOLDER_NAME="Name_Surname"

# Create folder
echo "Creating folder"
mkdir -p "$FOLDER_NAME"

# List current directory
ls

# Create files
echo "Creating files"
touch "$FOLDER_NAME/Name" "$FOLDER_NAME/Surname"

# List folder contents
ls "$FOLDER_NAME"

echo "Done!"
```

> **Screenshot 13**
> - Show: Improved script with variables
> - Expected: More professional script structure


## SHEBANG REFERENCE

| Shebang                  | Interpreter                               |
|--------------------------|-------------------------------------------|
| #!/bin/bash              | Bash shell                                |
| #!/bin/sh                | Bourne shell (POSIX)                      |
| #!/usr/bin/env bash      | Bash (portable, searches PATH)            |
| #!/usr/bin/env python3   | Python 3                                  |
| #!/usr/bin/env perl      | Perl                                      |
| #!/usr/bin/env ruby      | Ruby                                      |
| #!/usr/bin/env node      | Node.js                                   |

Best Practice: Use #!/usr/bin/env bash for portability across systems
where bash might be in different locations.


## SCRIPT STRUCTURE BEST PRACTICES

A well-structured script typically includes:

```bash
#!/bin/bash
#
# Script Name: myscript.sh
# Description: Brief description of what the script does
# Author: Your Name
# Date: YYYY-MM-DD
# Version: 1.0
#
# Usage: ./myscript.sh [options]
#

# Exit on error
set -e

# Variables
VARIABLE_NAME="value"

# Functions
function_name() {
    # Function code
}

# Main script
echo "Starting script..."
# ... commands ...
echo "Script completed!"
```


## COMMON SCRIPT COMMANDS

| Command                    | Description                              |
|----------------------------|------------------------------------------|
| echo "text"                | Print text to console                    |
| mkdir dirname              | Create directory                         |
| mkdir -p path/to/dir       | Create nested directories                |
| touch file                 | Create empty file                        |
| ls                         | List directory contents                  |
| ls dirname                 | List specific directory contents         |
| cd dirname                 | Change directory                         |
| pwd                        | Print working directory                  |
| cp src dest                | Copy file                                |
| mv src dest                | Move/rename file                         |
| rm file                    | Remove file                              |
| cat file                   | Display file contents                    |


## SCRIPT COMMENTS

Single-line comment:

```bash
# This is a comment
```

Multi-line comments (using heredoc):

```bash
: '
This is a
multi-line
comment
'
```

Inline comment:

```bash
echo "Hello"  # This prints Hello
```

Best Practices:
- Comment complex logic
- Explain the "why", not the "what"
- Keep comments up to date
- Use meaningful variable/function names


## TROUBLESHOOTING

Problem: "Permission denied" when running ./script.sh
Solutions:
- Make file executable: chmod +x script.sh
- Check permissions: ls -la script.sh

Problem: "bad interpreter: No such file or directory"
Solutions:
- Check shebang path is correct: head -1 script.sh
- Ensure no Windows line endings: dos2unix script.sh
- Use env: #!/usr/bin/env bash

Problem: "mkdir: cannot create directory: File exists"
Solution: Use mkdir -p or add existence check

Problem: Script runs but creates files in wrong location
Solutions:
- Use absolute paths: mkdir ~/Name_Surname
- Print current directory: pwd
- cd to correct directory first

Problem: Commands work manually but not in script
Solutions:
- Check for typos
- Verify correct quoting
- Debug with: bash -x script.sh


## DEBUGGING SCRIPTS

Debug options:

```bash
# Run with debug output
bash -x script.sh

# Add at top of script for debug mode
set -x    # Enable debug
set +x    # Disable debug

# Exit immediately on error
set -e

# Treat unset variables as error
set -u

# Common combination
set -euo pipefail
```

Debug output example:
```
+ echo 'Creating folder'
Creating folder
+ mkdir Name_Surname
+ ls
Name_Surname  script.sh
```


## SELF-REVIEW CHECKLIST

[ ] Terminal opened successfully
[ ] script.sh created in home directory
[ ] File made executable (chmod +x)
[ ] Shebang (#!/bin/bash) added as first line
[ ] Script contains echo "Creating folder"
[ ] Script contains mkdir Name_Surname
[ ] Script contains ls command
[ ] Script contains echo "Creating files"
[ ] Script contains touch for Name and Surname files
[ ] Script contains ls Name_Surname
[ ] Script executes without errors
[ ] Output shows "Creating folder"
[ ] Output shows directory listing with Name_Surname
[ ] Output shows "Creating files"
[ ] Output shows Name and Surname files
