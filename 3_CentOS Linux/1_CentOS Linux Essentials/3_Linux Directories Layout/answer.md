# LINUX DIRECTORIES LAYOUT - ANSWERS

## TASK 1: Open terminal of your choosing

Methods:
- Press Ctrl + Alt + T
- Activities > Search "Terminal"
- Right-click on Desktop > Open Terminal

> **Screenshot 1**
>
> Terminal window showing:
> - Empty terminal with command prompt
> - Username and hostname visible (user@hostname:~$)


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

> **Screenshot 2**
>
> Terminal showing:
> - $ cd ~
> - $ mkdir empty full final
> - $ ls -la
> - Output with all three directories listed:
>   drwxr-xr-x ... empty
>   drwxr-xr-x ... final
>   drwxr-xr-x ... full


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

> **Screenshot 3**
>
> Terminal showing:
> - $ touch ~/full/file1
> - $ ls -la ~/full/
> - Output showing file1:
>   -rw-r--r-- ... 0 ... file1


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

> **Screenshot 4**
>
> Terminal showing:
> - $ cp -r ~/empty ~/final/
> - $ ls -la ~/final/
> - Output showing empty folder inside final:
>   drwxr-xr-x ... empty
> - $ ls -la ~/
> - Original empty folder still exists in home directory


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

> **Screenshot 5**
>
> Terminal showing:
> - $ mv ~/empty ~/final/empty_moved
> - $ ls -la ~/final/
> - Both directories visible:
>   drwxr-xr-x ... empty
>   drwxr-xr-x ... empty_moved
> - $ ls -la ~/ | grep empty
> - (no output - empty folder moved from home)


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

> **Screenshot 6**
>
> Terminal showing:
> - $ mkdir ~/full/nested
> - $ ls -la ~/full/
> - Output showing file1 and nested directory:
>   -rw-r--r-- ... file1
>   drwxr-xr-x ... nested


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

> **Screenshot 7**
>
> Terminal showing:
> - $ cp -r ~/full/* ~/final/
> - $ ls -la ~/final/
> - Output showing all items:
>   drwxr-xr-x ... empty
>   drwxr-xr-x ... empty_moved
>   -rw-r--r-- ... file1        <-- copied from full
>   drwxr-xr-x ... nested       <-- copied from full


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

> **Screenshot 8**
>
> Terminal showing:
> - $ cp -r ~/full ~/final/
> - $ ls -la ~/final/
> - Output showing full directory added:
>   drwxr-xr-x ... empty
>   drwxr-xr-x ... empty_moved
>   -rw-r--r-- ... file1
>   drwxr-xr-x ... full        <-- entire directory copied
>   drwxr-xr-x ... nested
> - $ ls -la ~/final/full/
> - Contents of copied full directory:
>   -rw-r--r-- ... file1
>   drwxr-xr-x ... nested


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

> **Screenshot 9**
>
> Terminal showing:
> - $ mv ~/full ~/final/full_moved
> - $ ls -la ~/final/
> - Output showing full_moved added:
>   drwxr-xr-x ... empty
>   drwxr-xr-x ... empty_moved
>   -rw-r--r-- ... file1
>   drwxr-xr-x ... full
>   drwxr-xr-x ... full_moved  <-- moved and renamed
>   drwxr-xr-x ... nested
> - $ ls -la ~/ | grep full
> - (no output - original full folder moved)


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

> **Screenshot 10**
>
> Terminal showing tree command output:
> - $ tree ~
> - /home/Name_Surname
>   - final
>     - empty
>     - empty_moved
>     - file1
>     - full
>       - file1
>       - nested
>     - full_moved
>       - file1
>       - nested
>     - nested
>
> This shows the complete directory structure


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


## SCREENSHOTS CHECKLIST

Print this checklist and mark each screenshot as you take it:

- [ ] SCREENSHOT 1  - Terminal window opened
- [ ] SCREENSHOT 2  - mkdir empty full final and ls verification
- [ ] SCREENSHOT 3  - touch ~/full/file1 and ls verification
- [ ] SCREENSHOT 4  - cp -r ~/empty ~/final/ and verification
- [ ] SCREENSHOT 5  - mv ~/empty ~/final/empty_moved and verification
- [ ] SCREENSHOT 6  - mkdir ~/full/nested and ls verification
- [ ] SCREENSHOT 7  - cp -r ~/full/* ~/final/ and verification
- [ ] SCREENSHOT 8  - cp -r ~/full ~/final/ and verification
- [ ] SCREENSHOT 9  - mv ~/full ~/final/full_moved and verification
- [ ] SCREENSHOT 10 - tree ~ showing final structure

TOTAL: 10 Screenshots Required
