# BASIC SHELL USAGE - ANSWERS

================================================================================
TASK 1: Open terminal of your choosing
================================================================================

Methods to open terminal in CentOS:

Method 1: Using Application Menu
- Activities > Search "Terminal" > Click Terminal

Method 2: Using Keyboard Shortcut
- Press Ctrl + Alt + T

Method 3: Right-click on Desktop
- Right-click > Open Terminal

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                          |
|                                                                  |
|  Terminal window showing:                                        |
|  - Empty terminal with command prompt                            |
|  - Username and hostname visible (user@hostname:~$)              |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 2: Navigate to home folder of your user
================================================================================

Command:
$ cd ~

OR

$ cd

OR

$ cd /home/Name_Surname

Explanation:
- ~ (tilde) is a shortcut for the current user's home directory
- cd without arguments also goes to home directory
- /home/Name_Surname is the absolute path

Verify:
$ pwd

Expected Output:
/home/Name_Surname

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                          |
|                                                                  |
|  Terminal showing:                                               |
|  - $ cd ~                                                        |
|  - $ pwd                                                         |
|  - /home/Name_Surname                                            |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 3: Create folder homework in current directory
================================================================================

Command:
$ mkdir homework

Verify:
$ ls -la

Expected Output:
drwxr-xr-x. 2 Name_Surname Name_Surname 4096 Jan 15 10:00 homework

Explanation:
- mkdir = make directory
- Creates a new directory named "homework" in current location

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                          |
|                                                                  |
|  Terminal showing:                                               |
|  - $ mkdir homework                                              |
|  - $ ls -la                                                      |
|  - Output with "homework" directory listed                       |
|  - drwxr-xr-x ... homework                                       |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 4: Create file file1 in homework folder
================================================================================

Command:
$ touch homework/file1

Verify:
$ ls -la homework/

Expected Output:
-rw-r--r--. 1 Name_Surname Name_Surname 0 Jan 15 10:01 file1

Explanation:
- touch creates an empty file (or updates timestamp if file exists)
- homework/file1 specifies the path where file should be created

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                          |
|                                                                  |
|  Terminal showing:                                               |
|  - $ touch homework/file1                                        |
|  - $ ls -la homework/                                            |
|  - Output showing file1 with size 0                              |
|  - -rw-r--r-- ... 0 ... file1                                    |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 5: Write your name and surname into file1 file
================================================================================

Method 1: Using echo (Recommended)
$ echo "Name Surname" > homework/file1

Method 2: Using cat with heredoc
$ cat > homework/file1 << EOF
Name Surname
EOF

Method 3: Using nano editor
$ nano homework/file1
(Type your name, then Ctrl+O to save, Ctrl+X to exit)

Explanation:
- > redirects output and overwrites file content
- >> would append to file instead of overwriting

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                          |
|                                                                  |
|  Terminal showing:                                               |
|  - $ echo "Name Surname" > homework/file1                        |
|  - Command executes without error                                |
|  - Prompt returns to next line                                   |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 6: View the contents of file1 in the console
================================================================================

Command:
$ cat homework/file1

Expected Output:
Name Surname

Alternative Commands:
$ less homework/file1    # View with pagination (press q to quit)
$ more homework/file1    # View with pagination
$ head homework/file1    # View first 10 lines
$ tail homework/file1    # View last 10 lines

Explanation:
- cat = concatenate and display file contents
- Useful for viewing small files

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                          |
|                                                                  |
|  Terminal showing:                                               |
|  - $ cat homework/file1                                          |
|  - Name Surname                                                  |
|  - Your actual name and surname displayed                        |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 7: Navigate to the root of your file system
================================================================================

Command:
$ cd /

Verify:
$ pwd

Expected Output:
/

Explanation:
- / is the root directory of the Linux file system
- All other directories branch from root

List root directory contents:
$ ls

Expected Output:
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                          |
|                                                                  |
|  Terminal showing:                                               |
|  - $ cd /                                                        |
|  - $ pwd                                                         |
|  - /                                                             |
|  - (Optional: $ ls showing root directories)                     |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 8: Create file2 with same timestamp as file1 (without changing directory)
================================================================================

Command:
$ touch -r /home/Name_Surname/homework/file1 /home/Name_Surname/homework/file2

OR using ~ shortcut:
$ touch -r ~/homework/file1 ~/homework/file2

Verify:
$ ls -la ~/homework/

Expected Output:
-rw-r--r--. 1 Name_Surname Name_Surname 13 Jan 15 10:01 file1
-rw-r--r--. 1 Name_Surname Name_Surname  0 Jan 15 10:01 file2

Explanation:
- touch -r <reference_file> <new_file>
- -r option copies the timestamp from reference file to new file
- Both files now have identical timestamps
- We used absolute paths so we didn't need to change directory

Verify current directory is still root:
$ pwd

Expected Output:
/

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                          |
|                                                                  |
|  Terminal showing:                                               |
|  - $ pwd                                                         |
|  - /    <-- still at root                                        |
|  - $ touch -r ~/homework/file1 ~/homework/file2                  |
|  - $ ls -la ~/homework/                                          |
|  - file1 and file2 with SAME TIMESTAMP:                          |
|    ... Jan 15 10:01 file1                                        |
|    ... Jan 15 10:01 file2                                        |
|        ^^^^^^^^^^^^^ timestamps match                            |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 9: Find files modified less than 1 hour ago
================================================================================

Command:
$ find /home/Name_Surname -type f -mmin -60

OR using ~:
$ find ~ -type f -mmin -60

Expected Output:
/home/Name_Surname/homework/file1
/home/Name_Surname/homework/file2

Explanation:
- find = search for files
- /home/Name_Surname or ~ = starting directory to search
- -type f = only files (not directories)
- -mmin -60 = modified less than 60 minutes ago
  - -60 means "less than 60 minutes"
  - +60 would mean "more than 60 minutes"
  - 60 (no sign) would mean "exactly 60 minutes"

Alternative with -mtime (days):
$ find ~ -type f -mtime 0    # Modified today (less than 24 hours ago)

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 9 <<<                          |
|                                                                  |
|  Terminal showing:                                               |
|  - $ find ~ -type f -mmin -60                                    |
|  - /home/Name_Surname/homework/file1                             |
|  - /home/Name_Surname/homework/file2                             |
|  - Both files appear in the output                               |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 10: Review your task
================================================================================

Verification Commands:

1. Check both files exist with same timestamp:
$ ls -la ~/homework/

Expected Output:
total 4
drwxr-xr-x. 2 Name_Surname Name_Surname 4096 Jan 15 10:01 .
drwx------. 5 Name_Surname Name_Surname 4096 Jan 15 10:00 ..
-rw-r--r--. 1 Name_Surname Name_Surname   13 Jan 15 10:01 file1
-rw-r--r--. 1 Name_Surname Name_Surname    0 Jan 15 10:01 file2

2. Check file1 contains your name:
$ cat ~/homework/file1

Expected Output:
Name Surname

3. Check find command works:
$ find ~ -type f -mmin -60

Expected Output:
/home/Name_Surname/homework/file1
/home/Name_Surname/homework/file2

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 10 <<<                         |
|                                                                  |
|  Terminal showing ALL verification commands:                     |
|  - $ ls -la ~/homework/                                          |
|    (shows file1 and file2 with same timestamp)                   |
|  - $ cat ~/homework/file1                                        |
|    Name Surname                                                  |
|  - $ find ~ -type f -mmin -60                                    |
|    (shows both files)                                            |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 11: Remove homework folder
================================================================================

Command:
$ rm -r ~/homework

OR with force (no confirmation):
$ rm -rf ~/homework

Verify:
$ ls -la ~/ | grep homework

Expected Output:
(no output - folder is deleted)

OR:
$ ls ~/homework

Expected Output:
ls: cannot access '/home/Name_Surname/homework': No such file or directory

Explanation:
- rm = remove
- -r = recursive (required for directories with contents)
- -f = force (no confirmation prompts)
- Be VERY careful with rm -rf, especially as root!

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 11 <<<                         |
|                                                                  |
|  Terminal showing:                                               |
|  - $ rm -rf ~/homework                                           |
|  - $ ls ~/homework                                               |
|  - ls: cannot access '/home/.../homework': No such file or dir   |
|  - OR                                                            |
|  - $ ls -la ~/ | grep homework                                   |
|  - (no output - folder doesn't exist)                            |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
COMPLETE COMMAND SEQUENCE SUMMARY
================================================================================

# Task 2: Go to home directory
cd ~

# Task 3: Create homework folder
mkdir homework

# Task 4: Create file1
touch homework/file1

# Task 5: Write name to file1
echo "Name Surname" > homework/file1

# Task 6: View file1 contents
cat homework/file1

# Task 7: Go to root directory
cd /

# Task 8: Create file2 with same timestamp as file1
touch -r ~/homework/file1 ~/homework/file2

# Task 9: Find recently modified files
find ~ -type f -mmin -60

# Task 10: Review (verify all steps)
ls -la ~/homework/
cat ~/homework/file1

# Task 11: Remove homework folder
rm -rf ~/homework

# Final verification
ls -la ~/ | grep homework


================================================================================
SELF-REVIEW CHECKLIST
================================================================================

| No. | Check | Status |
|-----|-------|--------|
| 1 | ls -la ~/homework shows 2 files | [ ] |
| 2 | Both files have same timestamp | [ ] |
| 3 | cat ~/homework/file1 shows your name | [ ] |
| 4 | find command returns file1 and file2 | [ ] |
| 5 | homework folder removed successfully | [ ] |


================================================================================
USEFUL COMMANDS REFERENCE
================================================================================

NAVIGATION:

| Command | Description |
|---------|-------------|
| cd | Change to home directory |
| cd ~ | Change to home directory |
| cd / | Change to root directory |
| cd .. | Go up one directory |
| cd - | Go to previous directory |
| pwd | Print working directory |

FILE OPERATIONS:

| Command | Description |
|---------|-------------|
| touch file | Create empty file / update timestamp |
| touch -r a b | Copy timestamp from file a to file b |
| mkdir dir | Create directory |
| rm file | Remove file |
| rm -r dir | Remove directory recursively |
| rm -rf dir | Remove directory forcefully |
| cp src dest | Copy file |
| mv src dest | Move/rename file |

VIEWING FILES:

| Command | Description |
|---------|-------------|
| cat file | Display file contents |
| less file | View file with pagination |
| head file | View first 10 lines |
| head -n 5 file | View first 5 lines |
| tail file | View last 10 lines |
| tail -f file | Follow file updates (logs) |

FIND COMMAND:

| Option | Description |
|--------|-------------|
| -type f | Files only |
| -type d | Directories only |
| -name "*.txt" | By name pattern |
| -mmin -60 | Modified < 60 minutes ago |
| -mmin +60 | Modified > 60 minutes ago |
| -mtime -1 | Modified < 1 day ago |
| -size +10M | Larger than 10 MB |


================================================================================
SCREENSHOTS CHECKLIST
================================================================================

Print this checklist and mark each screenshot as you take it:

[ ] SCREENSHOT 1  - Terminal window opened
[ ] SCREENSHOT 2  - cd ~ and pwd showing home directory path
[ ] SCREENSHOT 3  - mkdir homework and ls -la showing homework folder
[ ] SCREENSHOT 4  - touch homework/file1 and ls -la homework/ showing file1
[ ] SCREENSHOT 5  - echo "Name Surname" > homework/file1
[ ] SCREENSHOT 6  - cat homework/file1 showing your name
[ ] SCREENSHOT 7  - cd / and pwd showing root (/)
[ ] SCREENSHOT 8  - touch -r and ls -la showing MATCHING timestamps
[ ] SCREENSHOT 9  - find ~ -type f -mmin -60 showing file1 and file2
[ ] SCREENSHOT 10 - All verification commands (ls, cat, find)
[ ] SCREENSHOT 11 - rm -rf ~/homework and verification folder is gone

TOTAL: 11 Screenshots Required

