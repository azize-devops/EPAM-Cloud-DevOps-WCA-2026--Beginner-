# WORKING WITH FILES, LINKS - ANSWERS

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


## TASK 2: Create script and script.sh files with content

### Method 1: Using echo with heredoc

```bash
$ cat > script << 'EOF'
#!/bin/bash

echo Hello
EOF

$ cat > script.sh << 'EOF'
#!/bin/bash

echo Hello
EOF
```


### Method 2: Using nano editor

```bash
$ nano script
```

(Type the following content:)
```
#!/bin/bash

echo Hello
```

(Press Ctrl+O to save, Enter to confirm, Ctrl+X to exit)

```bash
$ nano script.sh
```

(Same content, same save procedure)


### Method 3: Using vi/vim editor

```bash
$ vi script
```

(Press i for insert mode, type content, press Esc, type :wq)

```bash
$ vi script.sh
```

(Same procedure)


### Method 4: Using echo commands

```bash
$ echo -e '#!/bin/bash\n\necho Hello' > script
$ echo -e '#!/bin/bash\n\necho Hello' > script.sh
```


Verify file contents:
```bash
$ cat script
$ cat script.sh
```

Output:
```
#!/bin/bash

echo Hello
```

> **Screenshot 2**
>
> Terminal showing:
> - File creation command (cat with heredoc or echo)
> - $ cat script
> - Output:
>   #!/bin/bash
>
>   echo Hello
> - $ cat script.sh
> - Same output for script.sh


## TASK 3: Make files executable with chmod +x

Command:
```bash
$ chmod +x script script.sh
```

Verify permissions:
```bash
$ ls -la script script.sh
```

Output (before chmod):
```
-rw-r--r--. 1 Name_Surname Name_Surname 24 Jan 15 10:00 script
-rw-r--r--. 1 Name_Surname Name_Surname 24 Jan 15 10:00 script.sh
```

Output (after chmod):
```
-rwxr-xr-x. 1 Name_Surname Name_Surname 24 Jan 15 10:00 script
-rwxr-xr-x. 1 Name_Surname Name_Surname 24 Jan 15 10:00 script.sh
```

Explanation:
- chmod = change mode (permissions)
- +x = add execute permission
- Before: -rw-r--r-- (read/write only)
- After:  -rwxr-xr-x (read/write/execute)

> **Screenshot 3**
>
> Terminal showing:
> - $ chmod +x script script.sh
> - $ ls -la script script.sh
> - Output with x permission visible:
>   -rwxr-xr-x ... script
>   -rwxr-xr-x ... script.sh
>   (x permissions for user, group, others)


## TASK 4: Create soft link (symbolic link) called script_soft for file script

Command:
```bash
$ ln -s script script_soft
```

Explanation:
- ln = link command
- -s = symbolic (soft) link
- First argument: target file (script)
- Second argument: link name (script_soft)

Verify:
```bash
$ ls -la script_soft
```

Output:
```
lrwxrwxrwx. 1 Name_Surname Name_Surname 6 Jan 15 10:02 script_soft -> script
```

Explanation of output:
- l at the beginning indicates it's a symbolic link
- -> script shows it points to the original file
- Size (6) is the length of the target filename "script"

> **Screenshot 4**
>
> Terminal showing:
> - $ ln -s script script_soft
> - $ ls -la script_soft
> - Output showing soft link:
>   lrwxrwxrwx ... script_soft -> script
>   (l = link type, arrow shows target)


## TASK 5: Create hard link called script_hard for file script.sh

Command:
```bash
$ ln script.sh script_hard
```

Explanation:
- ln without -s creates a hard link
- Hard link shares the same inode as original file
- It's essentially another name for the same data on disk

Verify:
```bash
$ ls -la script.sh script_hard
```

Output:
```
-rwxr-xr-x. 2 Name_Surname Name_Surname 24 Jan 15 10:00 script_hard
-rwxr-xr-x. 2 Name_Surname Name_Surname 24 Jan 15 10:00 script.sh
```

Explanation of output:
- No 'l' at beginning (it's a regular file, not a link)
- Number '2' indicates link count (2 names point to same data)
- Both files have identical size and permissions

Check inode numbers (same inode = hard link):
```bash
$ ls -li script.sh script_hard
```

Output:
```
12345678 -rwxr-xr-x. 2 Name_Surname Name_Surname 24 Jan 15 10:00 script_hard
12345678 -rwxr-xr-x. 2 Name_Surname Name_Surname 24 Jan 15 10:00 script.sh
```

(Same inode number confirms hard link)

> **Screenshot 5**
>
> Terminal showing:
> - $ ln script.sh script_hard
> - $ ls -li script.sh script_hard
> - Output showing same inode number:
>   12345678 -rwxr-xr-x. 2 ... script_hard
>   12345678 -rwxr-xr-x. 2 ... script.sh
>   (same inode, link count = 2)


## TASK 6: Execute both script_soft and script_hard files

Commands:
```bash
$ ./script_soft
$ ./script_hard
```

Output (for both):
```
Hello
```

Alternative execution methods:
```bash
$ bash script_soft
$ bash script_hard
```

OR:
```bash
$ /bin/bash script_soft
$ /bin/bash script_hard
```

> **Screenshot 6**
>
> Terminal showing:
> - $ ./script_soft
> - Hello
> - $ ./script_hard
> - Hello
> - Both scripts produce same output


## TASK 7: Remove original files script and script.sh

Command:
```bash
$ rm script script.sh
```

Verify:
```bash
$ ls -la script script.sh
```

Output:
```
ls: cannot access 'script': No such file or directory
ls: cannot access 'script.sh': No such file or directory
```

Check remaining files:
```bash
$ ls -la script_soft script_hard
```

Output:
```
lrwxrwxrwx. 1 Name_Surname Name_Surname  6 Jan 15 10:02 script_soft -> script
-rwxr-xr-x. 1 Name_Surname Name_Surname 24 Jan 15 10:00 script_hard
```

Note:
- script_soft still exists but points to non-existent file (broken link)
- script_hard still exists with link count now 1

> **Screenshot 7**
>
> Terminal showing:
> - $ rm script script.sh
> - $ ls -la script script.sh
> - ls: cannot access 'script': No such file or directory
> - ls: cannot access 'script.sh': No such file or directory
> - $ ls -la script_soft script_hard
> - Both links still exist:
>   lrwxrwxrwx ... script_soft -> script (broken)
>   -rwxr-xr-x. 1 ... script_hard (link count now 1)


## TASK 8: Execute both script_soft and script_hard files (after deletion)

Execute soft link:
```bash
$ ./script_soft
```

Output:
```
bash: ./script_soft: No such file or directory
```

OR:
```
-bash: ./script_soft: No such file or directory
```

Explanation:
- Soft link points to "script" which no longer exists
- The link is "broken" or "dangling"
- System cannot find the target file


Execute hard link:
```bash
$ ./script_hard
```

Output:
```
Hello
```

Explanation:
- Hard link still works!
- Hard link shares the same inode (data blocks) as original
- Deleting original only removed one name, data still exists
- script_hard is now the only name pointing to that data

> **Screenshot 8**
>
> Terminal showing:
> - $ ./script_soft
> - bash: ./script_soft: No such file or directory
>   (SOFT LINK FAILS - target file deleted)
>
> - $ ./script_hard
> - Hello
>   (HARD LINK WORKS - data still exists)
>
> This demonstrates the key difference between soft and hard links


## TASK 9: Remove all created files

Command:
```bash
$ rm script_soft script_hard
```

Verify:
```bash
$ ls -la script*
```

Output:
```
ls: cannot access 'script*': No such file or directory
```

All files cleaned up successfully.

> **Screenshot 9**
>
> Terminal showing:
> - $ rm script_soft script_hard
> - $ ls -la script*
> - ls: cannot access 'script*': No such file or directory
> - All files removed successfully


## SOFT LINK vs HARD LINK COMPARISON

| Feature                 | Soft Link (Symbolic)       | Hard Link                 |
|-------------------------|----------------------------|---------------------------|
| Command                 | ln -s target link          | ln target link            |
| Points to               | File path (name)           | Inode (data blocks)       |
| If original deleted     | BROKEN (doesn't work)      | STILL WORKS               |
| Can link to directory   | YES                        | NO (usually)              |
| Can cross filesystems   | YES                        | NO                        |
| ls -l display           | l--------- name -> target  | ---------- name           |
| Inode                   | Different from target      | Same as target            |
| File size               | Length of target path      | Same as target            |

### VISUAL REPRESENTATION:

**SOFT LINK:**
```
+-------------+       +-------------+       +-------------+
| script_soft | ----> |   script    | ----> |  Data on    |
| (link file) |  path | (original)  | inode |    disk     |
+-------------+       +-------------+       +-------------+
                             |
                        If deleted,
                      link is broken!
```

**HARD LINK:**
```
+-------------+
|   script.sh | ----+
|  (original) |     |     +-------------+
+-------------+     +---> |  Data on    |
                    |     |    disk     |
+-------------+     |     +-------------+
| script_hard | ----+
|    (link)   |  Same inode
+-------------+

Both names point directly to data.
If one is deleted, other still works!
```


## COMPLETE COMMAND SEQUENCE

```bash
# Task 1: Open terminal
# (Use Ctrl+Alt+T or GUI)

# Task 2: Create script files
cat > script << 'EOF'
#!/bin/bash

echo Hello
EOF

cat > script.sh << 'EOF'
#!/bin/bash

echo Hello
EOF

# Task 3: Make executable
chmod +x script script.sh

# Task 4: Create soft link
ln -s script script_soft

# Task 5: Create hard link
ln script.sh script_hard

# Task 6: Execute both links
./script_soft
./script_hard

# Task 7: Remove original files
rm script script.sh

# Task 8: Execute links again (soft fails, hard works)
./script_soft    # ERROR
./script_hard    # Hello

# Task 9: Cleanup
rm script_soft script_hard
```


## USEFUL COMMANDS REFERENCE

### LINK COMMANDS:

| Command                 | Description                         |
|-------------------------|-------------------------------------|
| ln -s target link       | Create symbolic (soft) link         |
| ln target link          | Create hard link                    |
| ls -l                   | Show links (soft links show ->)     |
| ls -li                  | Show inode numbers                  |
| readlink link           | Show target of symbolic link        |
| readlink -f link        | Show absolute path of target        |
| unlink link             | Remove a link                       |
| find . -type l          | Find all symbolic links             |
| find . -xtype l         | Find broken symbolic links          |

### PERMISSION COMMANDS:

| Command                 | Description                         |
|-------------------------|-------------------------------------|
| chmod +x file           | Add execute permission              |
| chmod -x file           | Remove execute permission           |
| chmod 755 file          | rwxr-xr-x (owner full, others read/exec) |
| chmod 644 file          | rw-r--r-- (owner rw, others read)   |
| chmod u+x file          | Add execute for user (owner) only   |


## SCREENSHOTS CHECKLIST

Print this checklist and mark each screenshot as you take it:

- [ ] SCREENSHOT 1  - Terminal window opened
- [ ] SCREENSHOT 2  - File creation and cat showing content
- [ ] SCREENSHOT 3  - chmod +x and ls -la showing execute permission (x)
- [ ] SCREENSHOT 4  - ln -s and ls -la showing soft link (l... -> target)
- [ ] SCREENSHOT 5  - ln and ls -li showing hard link (same inode number)
- [ ] SCREENSHOT 6  - Both ./script_soft and ./script_hard outputting "Hello"
- [ ] SCREENSHOT 7  - rm and verification that originals are deleted
- [ ] SCREENSHOT 8  - Soft link ERROR + Hard link "Hello" output
- [ ] SCREENSHOT 9  - Cleanup and verification all files removed

TOTAL: 9 Screenshots Required
