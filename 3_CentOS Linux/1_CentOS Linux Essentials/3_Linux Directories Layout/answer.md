# LINUX DIRECTORIES LAYOUT - ANSWERS

## TASK 1: Open terminal of your choosing

Methods:
- Press Ctrl + Alt + T
- Activities > Search "Terminal"
- Right-click on Desktop > Open Terminal

**Screenshot 1:** Terminal window opened

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/3_Linux%20Directories%20Layout/Terminal%20window%20opened.png" alt="Terminal window opened" width="900">
</p>


## TASK 2: Create folders empty, full, final in home directory

Command:
```bash
$ cd ~
$ mkdir empty full final
```

OR in one command:
```bash
$ mkdir ~/empty ~/full ~/final
```

Verify:
```bash
$ ls -la
```

Output:
```
drwxr-xr-x. 2 Name_Surname Name_Surname 4096 Jan 15 10:00 empty
drwxr-xr-x. 2 Name_Surname Name_Surname 4096 Jan 15 10:00 final
drwxr-xr-x. 2 Name_Surname Name_Surname 4096 Jan 15 10:00 full
```

**Screenshot 2:** mkdir empty full final and ls verification

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/3_Linux%20Directories%20Layout/mkdir%20empty%20full%20final%20and%20ls%20verification.png" alt="mkdir empty full final and ls verification" width="900">
</p>


## TASK 3: Create file file1 in full directory

Command:
```bash
$ touch ~/full/file1
```

Verify:
```bash
$ ls -la ~/full/
```

Output:
```
-rw-r--r--. 1 Name_Surname Name_Surname 0 Jan 15 10:01 file1
```

**Screenshot 3:** touch ~/full/file1 and ls verification

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/3_Linux%20Directories%20Layout/touch%20~-full-file1%20and%20ls%20verification.png" alt="touch ~/full/file1 and ls verification" width="900">
</p>


## TASK 4: Copy empty folder to final folder

Command:
```bash
$ cp -r ~/empty ~/final/
```

Explanation:
- cp = copy
- -r = recursive (required for directories)
- Copies empty folder INTO final folder

Verify:
```bash
$ ls -la ~/final/
```

Output:
```
drwxr-xr-x. 2 Name_Surname Name_Surname 4096 Jan 15 10:02 empty
```

Current structure:
```
~/empty        (still exists)
~/final/empty  (copy)
```

**Screenshot 4:** cp -r ~/empty ~/final/ and verification

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/3_Linux%20Directories%20Layout/cp%20-r%20~-empty%20~-final-%20and%20verification.png" alt="cp -r ~/empty ~/final/ and verification" width="900">
</p>


## TASK 5: Move empty folder to final directory under new name empty_moved

Command:
```bash
$ mv ~/empty ~/final/empty_moved
```

Explanation:
- mv = move (also used for renaming)
- Moves and renames in one operation
- Original ~/empty no longer exists

Verify:
```bash
$ ls -la ~/final/
$ ls -la ~/ | grep empty
```

Output (~/final/):
```
drwxr-xr-x. 2 Name_Surname Name_Surname 4096 Jan 15 10:02 empty
drwxr-xr-x. 2 Name_Surname Name_Surname 4096 Jan 15 10:00 empty_moved
```

Output (home - no empty folder):
```
(no output for grep empty)
```

Current structure:
```
~/final/empty        (from task 4)
~/final/empty_moved  (moved and renamed)
```

**Screenshot 5:** mv ~/empty ~/final/empty_moved and verification

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/3_Linux%20Directories%20Layout/mv%20~-empty%20~-final-empty_moved%20and%20verification.png" alt="mv ~/empty ~/final/empty_moved and verification" width="900">
</p>


## TASK 6: Create nested folder inside full directory

Command:
```bash
$ mkdir ~/full/nested
```

Verify:
```bash
$ ls -la ~/full/
```

Output:
```
-rw-r--r--. 1 Name_Surname Name_Surname    0 Jan 15 10:01 file1
drwxr-xr-x. 2 Name_Surname Name_Surname 4096 Jan 15 10:03 nested
```

Current structure of ~/full:
```
~/full/
├── file1
└── nested/
```

**Screenshot 6:** mkdir ~/full/nested and ls verification

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/3_Linux%20Directories%20Layout/mkdir%20~-full-nested%20and%20ls%20verification.png" alt="mkdir ~/full/nested and ls verification" width="900">
</p>


## TASK 7: Copy all contents of full directory to final folder

Command:
```bash
$ cp -r ~/full/* ~/final/
```

Explanation:
- * = wildcard, matches all files and folders
- Copies contents (file1 and nested) but NOT the full folder itself

Verify:
```bash
$ ls -la ~/final/
```

Output:
```
drwxr-xr-x. 2 Name_Surname Name_Surname 4096 Jan 15 10:02 empty
drwxr-xr-x. 2 Name_Surname Name_Surname 4096 Jan 15 10:00 empty_moved
-rw-r--r--. 1 Name_Surname Name_Surname    0 Jan 15 10:04 file1
drwxr-xr-x. 2 Name_Surname Name_Surname 4096 Jan 15 10:04 nested
```

Current structure of ~/final:
```
~/final/
├── empty/
├── empty_moved/
├── file1          (copied from full)
└── nested/        (copied from full)
```

**Screenshot 7:** cp -r ~/full/* ~/final/ and verification

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/3_Linux%20Directories%20Layout/cp%20-r%20~-full-%20~-final-%20and%20verification.png" alt="cp -r ~/full/* ~/final/ and verification" width="900">
</p>


## TASK 8: Copy full directory to final folder

Command:
```bash
$ cp -r ~/full ~/final/
```

Explanation:
- Copies the entire full directory (including its contents) INTO final
- Different from Task 7: copies the folder itself, not just contents

Verify:
```bash
$ ls -la ~/final/
$ ls -la ~/final/full/
```

Output (~/final/):
```
drwxr-xr-x. 2 Name_Surname Name_Surname 4096 Jan 15 10:02 empty
drwxr-xr-x. 2 Name_Surname Name_Surname 4096 Jan 15 10:00 empty_moved
-rw-r--r--. 1 Name_Surname Name_Surname    0 Jan 15 10:04 file1
drwxr-xr-x. 3 Name_Surname Name_Surname 4096 Jan 15 10:05 full
drwxr-xr-x. 2 Name_Surname Name_Surname 4096 Jan 15 10:04 nested
```

Output (~/final/full/):
```
-rw-r--r--. 1 Name_Surname Name_Surname    0 Jan 15 10:05 file1
drwxr-xr-x. 2 Name_Surname Name_Surname 4096 Jan 15 10:05 nested
```

**Screenshot 8:** cp -r ~/full ~/final/ and verification

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/3_Linux%20Directories%20Layout/cp%20-r%20~-full%20~-final-%20and%20verification.png" alt="cp -r ~/full ~/final/ and verification" width="900">
</p>


## TASK 9: Move full directory to final folder under new name full_moved

Command:
```bash
$ mv ~/full ~/final/full_moved
```

Explanation:
- Moves ~/full into ~/final and renames it to full_moved
- Original ~/full no longer exists

Verify:
```bash
$ ls -la ~/final/
$ ls -la ~/ | grep full
```

Output (~/final/):
```
drwxr-xr-x. 2 Name_Surname Name_Surname 4096 Jan 15 10:02 empty
drwxr-xr-x. 2 Name_Surname Name_Surname 4096 Jan 15 10:00 empty_moved
-rw-r--r--. 1 Name_Surname Name_Surname    0 Jan 15 10:04 file1
drwxr-xr-x. 3 Name_Surname Name_Surname 4096 Jan 15 10:05 full
drwxr-xr-x. 3 Name_Surname Name_Surname 4096 Jan 15 10:01 full_moved
drwxr-xr-x. 2 Name_Surname Name_Surname 4096 Jan 15 10:04 nested
```

Output (home - no full folder):
```
(no output)
```

**Screenshot 9:** mv ~/full ~/final/full_moved and verification

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/3_Linux%20Directories%20Layout/mv%20~-full%20~-final-full_moved%20and%20verification.png" alt="mv ~/full ~/final/full_moved and verification" width="900">
</p>


## SELF-REVIEW: Install tree and verify structure

**Step 1: Install tree utility**
```bash
$ sudo yum install tree -y
```

Output:
```
Installed:
  tree-1.8.0-10.el9.x86_64

Complete!
```


**Step 2: View directory structure**
```bash
$ tree ~
```

Expected Output:
```
/home/Name_Surname
└── final
    ├── empty
    ├── empty_moved
    ├── file1
    ├── full
    │   ├── file1
    │   └── nested
    ├── full_moved
    │   ├── file1
    │   └── nested
    └── nested
```

**Screenshot 10:** tree ~ showing final structure

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/3_Linux%20Directories%20Layout/tree%20~%20showing%20final%20structure.png" alt="tree ~ showing final structure" width="900">
</p>


## COMPLETE COMMAND SEQUENCE

```bash
# Task 1: Open terminal
# (Use Ctrl+Alt+T or GUI)

# Task 2: Create three folders
cd ~
mkdir empty full final

# Task 3: Create file1 in full
touch ~/full/file1

# Task 4: Copy empty to final
cp -r ~/empty ~/final/

# Task 5: Move empty to final as empty_moved
mv ~/empty ~/final/empty_moved

# Task 6: Create nested folder in full
mkdir ~/full/nested

# Task 7: Copy contents of full to final
cp -r ~/full/* ~/final/

# Task 8: Copy full directory to final
cp -r ~/full ~/final/

# Task 9: Move full to final as full_moved
mv ~/full ~/final/full_moved

# Self-review: Install tree and verify
sudo yum install tree -y
tree ~
```


## KEY DIFFERENCES: cp vs mv, folder vs contents

### COPY FOLDER CONTENTS vs COPY FOLDER:

| cp -r ~/full/* ~/final/ | cp -r ~/full ~/final/ |
|-------------------------|------------------------|
| Copies: file1, nested | Copies: full/ (with contents) |
| Result in final: | Result in final: |
| final/file1 | final/full/file1 |
| final/nested/ | final/full/nested/ |

### COPY vs MOVE:

| cp -r ~/empty ~/final/ | mv ~/empty ~/final/empty_moved |
|------------------------|--------------------------------|
| Original folder REMAINS | Original folder REMOVED |
| Creates a copy | Relocates and/or renames |


## USEFUL COMMANDS REFERENCE

### COPY COMMANDS:

| Command | Description |
|---------|-------------|
| cp file1 file2 | Copy file1 to file2 |
| cp file dir/ | Copy file into directory |
| cp -r dir1 dir2/ | Copy dir1 into dir2 |
| cp -r dir1/* dir2/ | Copy contents of dir1 into dir2 |
| cp -a dir1 dir2/ | Copy with all attributes preserved |

### MOVE/RENAME COMMANDS:

| Command | Description |
|---------|-------------|
| mv file1 file2 | Rename file1 to file2 |
| mv file dir/ | Move file into directory |
| mv dir1 dir2/ | Move dir1 into dir2 |
| mv dir1 dir2/newname | Move dir1 into dir2 with new name |
| mv dir1 newname | Rename dir1 to newname (same location) |

### DIRECTORY COMMANDS:

| Command | Description |
|---------|-------------|
| mkdir dir | Create directory |
| mkdir -p dir1/dir2/dir3 | Create nested directories |
| rmdir dir | Remove empty directory |
| rm -r dir | Remove directory and contents |
| tree dir | Show directory structure as tree |


