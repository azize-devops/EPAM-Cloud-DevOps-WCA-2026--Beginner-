# BASIC SHELL USAGE - ANSWERS

## TASK 1: Open terminal of your choosing

Methods to open terminal in CentOS:

**Method 1: Using Application Menu**
- Activities > Search "Terminal" > Click Terminal

**Method 2: Using Keyboard Shortcut**
- Press Ctrl + Alt + T

**Method 3: Right-click on Desktop**
- Right-click > Open Terminal

**Screenshot 1:** Terminal window opened

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/2_Basic%20Shell%20Usage/Terminal%20window%20opened.png" alt="Terminal window opened" width="900">
</p>


## TASK 2: Navigate to home folder of your user

Command:
```bash
$ cd ~
```

OR

```bash
$ cd
```

OR

```bash
$ cd /home/Name_Surname
```

Explanation:
- ~ (tilde) is a shortcut for the current user's home directory
- cd without arguments also goes to home directory
- /home/Name_Surname is the absolute path

Verify:
```bash
$ pwd
```

Expected Output:
```
/home/Name_Surname
```

**Screenshot 2:** cd ~ and pwd showing home directory path

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/2_Basic%20Shell%20Usage/cd%20~%20and%20pwd%20showing%20home%20directory%20path.png" alt="cd ~ and pwd showing home directory path" width="900">
</p>


## TASK 3: Create folder homework in current directory

Command:
```bash
$ mkdir homework
```

Verify:
```bash
$ ls -la
```

Expected Output:
```
drwxr-xr-x. 2 Name_Surname Name_Surname 4096 Jan 15 10:00 homework
```

Explanation:
- mkdir = make directory
- Creates a new directory named "homework" in current location

**Screenshot 3:** mkdir homework and ls -la showing homework folder

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/2_Basic%20Shell%20Usage/mkdir%20homework%20and%20ls%20-la%20showing%20homework%20folder.png" alt="mkdir homework and ls -la showing homework folder" width="900">
</p>


## TASK 4: Create file file1 in homework folder

Command:
```bash
$ touch homework/file1
```

Verify:
```bash
$ ls -la homework/
```

Expected Output:
```
-rw-r--r--. 1 Name_Surname Name_Surname 0 Jan 15 10:01 file1
```

Explanation:
- touch creates an empty file (or updates timestamp if file exists)
- homework/file1 specifies the path where file should be created

**Screenshot 4:** touch homework-file1 and ls -la homework showing file1

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/2_Basic%20Shell%20Usage/touch%20homework-file1%20and%20ls%20-la%20homework%20showing%20file1.png" alt="touch homework-file1 and ls -la homework showing file1" width="900">
</p>


## TASK 5: Write your name and surname into file1 file

**Method 1: Using echo (Recommended)**
```bash
$ echo "Name Surname" > homework/file1
```

**Method 2: Using cat with heredoc**
```bash
$ cat > homework/file1 << EOF
Name Surname
EOF
```

**Method 3: Using nano editor**
```bash
$ nano homework/file1
```
(Type your name, then Ctrl+O to save, Ctrl+X to exit)

Explanation:
- > redirects output and overwrites file content
- >> would append to file instead of overwriting

**Screenshot 5:** echo Name Surname > homework/file1

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/2_Basic%20Shell%20Usage/echo%20Name%20Surname%20-%20homeworkfile1.png" alt="echo Name Surname - homeworkfile1" width="900">
</p>


## TASK 6: View the contents of file1 in the console

Command:
```bash
$ cat homework/file1
```

Expected Output:
```
Name Surname
```

Alternative Commands:
```bash
$ less homework/file1    # View with pagination (press q to quit)
$ more homework/file1    # View with pagination
$ head homework/file1    # View first 10 lines
$ tail homework/file1    # View last 10 lines
```

Explanation:
- cat = concatenate and display file contents
- Useful for viewing small files

**Screenshot 6:** cat homework-file1 showing your name

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/2_Basic%20Shell%20Usage/cat%20homework-file1%20showing%20your%20name.png" alt="cat homework-file1 showing your name" width="900">
</p>


## TASK 7: Navigate to the root of your file system

Command:
```bash
$ cd /
```

Verify:
```bash
$ pwd
```

Expected Output:
```
/
```

Explanation:
- / is the root directory of the Linux file system
- All other directories branch from root

List root directory contents:
```bash
$ ls
```

Expected Output:
```
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```

**Screenshot 7:** cd / and pwd showing root (/)

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/2_Basic%20Shell%20Usage/cd%20-%20and%20pwd%20showing%20root%20(-).png" alt="cd - and pwd showing root" width="900">
</p>


## TASK 8: Create file2 with same timestamp as file1 (without changing directory)

Command:
```bash
$ touch -r /home/Name_Surname/homework/file1 /home/Name_Surname/homework/file2
```

OR using ~ shortcut:
```bash
$ touch -r ~/homework/file1 ~/homework/file2
```

Verify:
```bash
$ ls -la ~/homework/
```

Expected Output:
```
-rw-r--r--. 1 Name_Surname Name_Surname 13 Jan 15 10:01 file1
-rw-r--r--. 1 Name_Surname Name_Surname  0 Jan 15 10:01 file2
```

Explanation:
- touch -r <reference_file> <new_file>
- -r option copies the timestamp from reference file to new file
- Both files now have identical timestamps
- We used absolute paths so we didn't need to change directory

Verify current directory is still root:
```bash
$ pwd
```

Expected Output:
```
/
```

**Screenshot 8:** touch -r and ls -la showing MATCHING timestamps

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/2_Basic%20Shell%20Usage/touch%20-r%20and%20ls%20-la%20showing%20MATCHING%20timestamps.png" alt="touch -r and ls -la showing MATCHING timestamps" width="900">
</p>


## TASK 9: Find files modified less than 1 hour ago

Command:
```bash
$ find /home/Name_Surname -type f -mmin -60
```

OR using ~:
```bash
$ find ~ -type f -mmin -60
```

Expected Output:
```
/home/Name_Surname/homework/file1
/home/Name_Surname/homework/file2
```

Explanation:
- find = search for files
- /home/Name_Surname or ~ = starting directory to search
- -type f = only files (not directories)
- -mmin -60 = modified less than 60 minutes ago
  - -60 means "less than 60 minutes"
  - +60 would mean "more than 60 minutes"
  - 60 (no sign) would mean "exactly 60 minutes"

Alternative with -mtime (days):
```bash
$ find ~ -type f -mtime 0    # Modified today (less than 24 hours ago)
```

**Screenshot 9:** find ~ -type f -mmin -60 showing file1 and file2

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/2_Basic%20Shell%20Usage/find%20~%20-type%20f%20-mmin%20-60%20showing%20file1%20and%20file2.png" alt="find ~ -type f -mmin -60 showing file1 and file2" width="900">
</p>


## TASK 10: Review your task

Verification Commands:

**1. Check both files exist with same timestamp:**
```bash
$ ls -la ~/homework/
```

Expected Output:
```
total 4
drwxr-xr-x. 2 Name_Surname Name_Surname 4096 Jan 15 10:01 .
drwx------. 5 Name_Surname Name_Surname 4096 Jan 15 10:00 ..
-rw-r--r--. 1 Name_Surname Name_Surname   13 Jan 15 10:01 file1
-rw-r--r--. 1 Name_Surname Name_Surname    0 Jan 15 10:01 file2
```

**2. Check file1 contains your name:**
```bash
$ cat ~/homework/file1
```

Expected Output:
```
Name Surname
```

**3. Check find command works:**
```bash
$ find ~ -type f -mmin -60
```

Expected Output:
```
/home/Name_Surname/homework/file1
/home/Name_Surname/homework/file2
```

**Screenshot 10:** All verification commands (ls, cat, find)

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/2_Basic%20Shell%20Usage/All%20verification%20commands%20(ls,%20cat,%20find).png" alt="All verification commands (ls, cat, find)" width="900">
</p>


## TASK 11: Remove homework folder

Command:
```bash
$ rm -r ~/homework
```

OR with force (no confirmation):
```bash
$ rm -rf ~/homework
```

Verify:
```bash
$ ls -la ~/ | grep homework
```

Expected Output:
```
(no output - folder is deleted)
```

OR:
```bash
$ ls ~/homework
```

Expected Output:
```
ls: cannot access '/home/Name_Surname/homework': No such file or directory
```

Explanation:
- rm = remove
- -r = recursive (required for directories with contents)
- -f = force (no confirmation prompts)
- Be VERY careful with rm -rf, especially as root!

**Screenshot 11:** rm -rf ~/homework and verification folder is gone

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/2_Basic%20Shell%20Usage/rm%20-rf%20~-homework%20and%20verification%20folder%20is%20gone.png" alt="rm -rf ~/homework and verification folder is gone" width="900">
</p>


## COMPLETE COMMAND SEQUENCE SUMMARY

```bash
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
```


## SELF-REVIEW CHECKLIST

| No. | Check | Status |
|-----|-------|--------|
| 1 | ls -la ~/homework shows 2 files | [ ] |
| 2 | Both files have same timestamp | [ ] |
| 3 | cat ~/homework/file1 shows your name | [ ] |
| 4 | find command returns file1 and file2 | [ ] |
| 5 | homework folder removed successfully | [ ] |


## USEFUL COMMANDS REFERENCE

### NAVIGATION:

| Command | Description |
|---------|-------------|
| cd | Change to home directory |
| cd ~ | Change to home directory |
| cd / | Change to root directory |
| cd .. | Go up one directory |
| cd - | Go to previous directory |
| pwd | Print working directory |

### FILE OPERATIONS:

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

### VIEWING FILES:

| Command | Description |
|---------|-------------|
| cat file | Display file contents |
| less file | View file with pagination |
| head file | View first 10 lines |
| head -n 5 file | View first 5 lines |
| tail file | View last 10 lines |
| tail -f file | Follow file updates (logs) |

### FIND COMMAND:

| Option | Description |
|--------|-------------|
| -type f | Files only |
| -type d | Directories only |
| -name "*.txt" | By name pattern |
| -mmin -60 | Modified < 60 minutes ago |
| -mmin +60 | Modified > 60 minutes ago |
| -mtime -1 | Modified < 1 day ago |
| -size +10M | Larger than 10 MB |


## SCREENSHOTS CHECKLIST

Print this checklist and mark each screenshot as you take it:

- [ ] SCREENSHOT 1  - Terminal window opened
- [ ] SCREENSHOT 2  - cd ~ and pwd showing home directory path
- [ ] SCREENSHOT 3  - mkdir homework and ls -la showing homework folder
- [ ] SCREENSHOT 4  - touch homework/file1 and ls -la homework/ showing file1
- [ ] SCREENSHOT 5  - echo "Name Surname" > homework/file1
- [ ] SCREENSHOT 6  - cat homework/file1 showing your name
- [ ] SCREENSHOT 7  - cd / and pwd showing root (/)
- [ ] SCREENSHOT 8  - touch -r and ls -la showing MATCHING timestamps
- [ ] SCREENSHOT 9  - find ~ -type f -mmin -60 showing file1 and file2
- [ ] SCREENSHOT 10 - All verification commands (ls, cat, find)
- [ ] SCREENSHOT 11 - rm -rf ~/homework and verification folder is gone

TOTAL: 11 Screenshots Required
