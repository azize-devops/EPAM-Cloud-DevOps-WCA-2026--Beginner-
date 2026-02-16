# VARIABLES - ANSWER

This guide covers bash variables including declaration, usage, and sourcing
variables from external files.


## UNDERSTANDING BASH VARIABLES

Variable Basics:
- Variables store data (strings, numbers, etc.)
- No spaces around = when assigning: VAR=value (not VAR = value)
- Access variable value with $VAR or ${VAR}
- Variable names: letters, numbers, underscores (cannot start with number)
- Case-sensitive: VAR and var are different variables

Variable Types:
- Local variables: Available only in current shell/script
- Environment variables: Available to child processes (exported)
- Special variables: $0, $1, $?, $$, etc.


## TASK 1: Create vars file in home folder

Navigate to home directory and create the vars file:

```bash
cd ~
touch vars
```

> **Screenshot 1**
> - Show: Creating vars file
> - Expected: File created in home directory


## TASK 2: Add FILE variable to vars file

Edit the vars file and add the variable:

Method 1 - Using echo:

```bash
echo 'FILE=new_file' > ~/vars
```

Method 2 - Using vim:

```bash
vim ~/vars
# Press 'i', type: FILE=new_file
# Press Esc, then :wq
```

Method 3 - Using nano:

```bash
nano ~/vars
# Type: FILE=new_file
# Press Ctrl+O to save, Ctrl+X to exit
```

> **Screenshot 2**
> - Show: Adding FILE variable to vars file
> - Expected: FILE=new_file in the file

Verify the content:

```bash
cat ~/vars
```

Expected output:
```
FILE=new_file
```

> **Screenshot 3**
> - Show: cat ~/vars output
> - Expected: FILE=new_file

IMPORTANT: Variable assignment rules:

```bash
FILE=new_file      # Correct - no spaces
FILE="new_file"    # Correct - with quotes
FILE = new_file    # WRONG - spaces cause error
```


## TASK 3: Create the script

Create script.sh in home directory:

```bash
cd ~
touch script.sh
chmod +x script.sh
```

Edit the script with your preferred editor:

```bash
vim script.sh
# or
nano script.sh
```

Script content:

```bash
#!/bin/bash

# Define NAME variable with your name
NAME=Name_Surname

# Source the vars file to load FILE variable
source ~/vars
# Alternative: . ~/vars

# Create folder with name from NAME variable
mkdir -p "$NAME"

# Create file in the folder with name from FILE variable
touch "$NAME/$FILE"

# List contents of home folder
ls ~

# List contents of created folder
ls "$NAME"
```

> **Screenshot 4**
> - Show: Creating script.sh
> - Expected: touch and chmod commands executed

> **Screenshot 5**
> - Show: Script content in editor
> - Expected: Complete script with NAME, source, mkdir, etc.

Alternative - Create script using heredoc:

```bash
cat > ~/script.sh << 'EOF'
#!/bin/bash

# Define NAME variable with your name
NAME=Name_Surname

# Source the vars file to load FILE variable
source ~/vars

# Create folder with name from NAME variable
mkdir -p "$NAME"

# Create file in the folder with name from FILE variable
touch "$NAME/$FILE"

# List contents of home folder
ls ~

# List contents of created folder
ls "$NAME"
EOF

chmod +x ~/script.sh
```

> **Screenshot 6**
> - Show: Creating script with heredoc
> - Expected: Script created via command line

Verify script content:

```bash
cat ~/script.sh
```

> **Screenshot 7**
> - Show: cat ~/script.sh output
> - Expected: Complete script displayed


## UNDERSTANDING THE SOURCE COMMAND

The source command (or . shorthand) executes commands from a file
in the current shell environment.

```bash
source filename
# or
. filename
```

Why use source?
- Variables defined in sourced file become available in current script
- Functions defined in sourced file become available
- Changes to environment persist in current shell

Without source (running as subprocess):

```bash
bash vars        # Variables NOT available in parent script
```

With source:

```bash
source vars      # Variables ARE available in current script
```

> **Screenshot 8**
> - Show: Explanation of source command
> - Expected: Understanding source vs running as subprocess


## TASK 4: Execute the script

Run the script:

```bash
cd ~
./script.sh
```

Expected output:
```
Name_Surname  script.sh  vars  (and other files in home)
new_file
```

> **Screenshot 9**
> - Show: Script execution
> - Expected: Output showing folder listing and new_file

> **Screenshot 10**
> - Show: First line of output (home folder listing)
> - Expected: Name_Surname script.sh vars visible

> **Screenshot 11**
> - Show: Second line of output (folder contents)
> - Expected: new_file


## VERIFICATION

Verify the folder and file were created:

```bash
# Check folder exists
ls -la ~/Name_Surname

# Check file exists
ls -la ~/Name_Surname/new_file
```

Expected output:
```
drwxr-xr-x 2 user user 4096 Feb 10 12:00 .
drwxr-xr-x 5 user user 4096 Feb 10 12:00 ..
-rw-r--r-- 1 user user    0 Feb 10 12:00 new_file
```

> **Screenshot 12**
> - Show: Verification of created folder and file
> - Expected: Name_Surname folder with new_file inside


## VARIABLE DECLARATION SYNTAX

| Syntax                   | Description                               |
|--------------------------|-------------------------------------------|
| VAR=value                | Assign value (no spaces!)                 |
| VAR="value with spaces"  | Assign value with spaces                  |
| VAR='literal $value'     | Assign literal string (no expansion)      |
| VAR=$(command)           | Assign command output                     |
| VAR=`command`            | Assign command output (older syntax)      |
| readonly VAR=value       | Create read-only variable                 |
| unset VAR                | Delete variable                           |
| export VAR=value         | Create environment variable               |


## ACCESSING VARIABLES

| Syntax                   | Description                               |
|--------------------------|-------------------------------------------|
| $VAR                     | Variable value                            |
| ${VAR}                   | Variable value (explicit)                 |
| ${VAR:-default}          | Use default if VAR is unset/empty         |
| ${VAR:=default}          | Set default if VAR is unset/empty         |
| ${VAR:+value}            | Use value if VAR is set                   |
| ${VAR:?error}            | Error if VAR is unset/empty               |
| ${#VAR}                  | Length of variable value                  |
| ${VAR^^}                 | Uppercase                                 |
| ${VAR,,}                 | Lowercase                                 |

Examples:

```bash
NAME="John"
echo $NAME           # John
echo ${NAME}         # John
echo ${NAME:-Guest}  # John (NAME is set)
echo ${UNSET:-Guest} # Guest (UNSET is not set)
echo ${#NAME}        # 4 (length)
echo ${NAME^^}       # JOHN (uppercase)
```


## QUOTING VARIABLES

| Type           | Example              | Behavior                        |
|----------------|----------------------|---------------------------------|
| No quotes      | $VAR                 | Word splitting, glob expansion  |
| Double quotes  | "$VAR"               | Variable expansion, no splitting|
| Single quotes  | '$VAR'               | Literal string, no expansion    |

Examples:

```bash
FILE="my file.txt"

# Without quotes - PROBLEM (word splitting)
touch $FILE         # Creates two files: "my" and "file.txt"

# With double quotes - CORRECT
touch "$FILE"       # Creates one file: "my file.txt"

# Single quotes - literal
echo '$FILE'        # Prints: $FILE (not the value)
```

Best Practice: Always quote variables "$VAR" to prevent word splitting.


## SPECIAL VARIABLES

| Variable       | Description                                       |
|----------------|---------------------------------------------------|
| $0             | Script name                                       |
| $1, $2, ...    | Positional parameters (arguments)                 |
| $#             | Number of arguments                               |
| $@             | All arguments (as separate words)                 |
| $*             | All arguments (as single word)                    |
| $?             | Exit status of last command                       |
| $$             | Current shell PID                                 |
| $!             | PID of last background command                    |
| $USER          | Current username                                  |
| $HOME          | Home directory                                    |
| $PWD           | Current directory                                 |
| $PATH          | Executable search path                            |


## SOURCE VS EXPORT

source (or .):
- Executes file in CURRENT shell
- Variables become available in current script
- Does NOT create new process

```bash
source vars      # Load variables into current shell
echo $FILE       # Works - FILE is available
```

export:
- Makes variable available to CHILD processes
- Does NOT affect parent shell

```bash
export VAR=value # Child processes can access VAR
bash -c 'echo $VAR'  # Works in child process
```

Comparison:

```bash
# vars file contains: FILE=test

# Without source:
bash vars
echo $FILE       # Empty - vars ran in subprocess

# With source:
source vars
echo $FILE       # test - vars ran in current shell
```


## TROUBLESHOOTING

Problem: "FILE: command not found"
Solution: Remove spaces around =

```bash
Wrong:  FILE = new_file
Right:  FILE=new_file
```

Problem: Variable is empty after sourcing
Solutions:
- Check file path is correct
- Verify file contains valid variable assignment
- Check for typos in variable name

Problem: "No such file or directory" when sourcing
Solutions:
- Use absolute path: source ~/vars
- Check file exists: ls -la ~/vars

Problem: Variable contains unexpected value
Solutions:
- Print variable: echo "FILE is: $FILE"
- Check for hidden characters: cat -A vars
- Verify quoting is correct

Problem: Word splitting issues
Solution: Always quote variables: "$VAR"

```bash
mkdir $NAME      # Problem if NAME has spaces
mkdir "$NAME"    # Correct
```


## SELF-REVIEW CHECKLIST

[ ] vars file created in home directory
[ ] FILE=new_file added to vars file
[ ] script.sh created in home directory
[ ] script.sh has #!/bin/bash shebang
[ ] script.sh has NAME variable defined
[ ] script.sh sources the vars file
[ ] script.sh creates folder using $NAME
[ ] script.sh creates file using $FILE
[ ] script.sh lists home folder contents
[ ] script.sh lists created folder contents
[ ] Script executes without errors
[ ] Output shows Name_Surname in home listing
[ ] Output shows new_file in folder listing
[ ] Folder ~/Name_Surname exists after execution
[ ] File ~/Name_Surname/new_file exists after execution
