# FILE PERMISSION - ANSWERS

## TASK 1: Open terminal of your choosing

Methods:
- Press Ctrl + Alt + T
- Activities > Search "Terminal"
- Right-click on Desktop > Open Terminal

**Screenshot 1:** Terminal window opened

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/6_File%20Permissions/Terminal%20window%20opened.png" alt="Terminal window opened" width="900">
</p>


## TASK 2: Create two users: user1, user2

Commands:
```bash
$ sudo useradd user1
$ sudo useradd user2
```

Set passwords:
```bash
$ sudo passwd user1
$ sudo passwd user2
```

Verify:
```bash
$ id user1
$ id user2
```

Output:
```
uid=1001(user1) gid=1001(user1) groups=1001(user1)
uid=1002(user2) gid=1002(user2) groups=1002(user2)
```

**Screenshot 2:** useradd commands and id verification

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/6_File%20Permissions/useradd%20commands%20and%20id%20verification.png" alt="useradd commands and id verification" width="900">
</p>


## TASK 3: Switch to user1

Command:
```bash
$ su - user1
```

OR:
```bash
$ sudo su - user1
```

Verify:
```bash
$ whoami
```

Output:
```
user1
```

**Screenshot 3:** Switch to user1 (su - user1) and whoami

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/6_File%20Permissions/Switch%20to%20user1%20(su%20-%20user1)%20and%20whoami.png" alt="Switch to user1 (su - user1) and whoami" width="900">
</p>


## TASK 4: Create folder /tmp/homework with 700 permissions

Commands:
```bash
$ mkdir /tmp/homework
$ chmod 700 /tmp/homework
```

OR in one command:
```bash
$ mkdir -m 700 /tmp/homework
```

Verify:
```bash
$ ls -la /tmp | grep homework
```

Output:
```
drwx------. 2 user1 user1 4096 Jan 15 10:00 homework
```

Explanation of 700:
- 7 (owner): rwx (read + write + execute)
- 0 (group): --- (no permissions)
- 0 (others): --- (no permissions)

**Screenshot 4:** mkdir and chmod 700 for /tmp/homework (drwx------)

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/6_File%20Permissions/mkdir%20and%20chmod%20700%20for%20-tmp-homework%20(drwx------).png" alt="mkdir and chmod 700 for /tmp/homework (drwx------)" width="900">
</p>


## TASK 5: Create 3 files with specific content and permissions

Create files:
```bash
$ echo "Hello" > /tmp/homework/read
$ touch /tmp/homework/write
$ echo "echo Hello" > /tmp/homework/execute
```

Set permissions:
```bash
$ chmod 400 /tmp/homework/read
$ chmod 600 /tmp/homework/write
$ chmod 700 /tmp/homework/execute
```

Verify:
```bash
$ ls -la /tmp/homework/
```

Output:
```
-r--------. 1 user1 user1  6 Jan 15 10:01 read
-rw-------. 1 user1 user1  0 Jan 15 10:01 write
-rwx------. 1 user1 user1 11 Jan 15 10:01 execute
```

Permission breakdown:

| File | Octal | Permission | user1 can... |
|------|-------|------------|--------------|
| read | 400 | r-------- | read only |
| write | 600 | rw------- | read + write |
| execute | 700 | rwx------ | read+write+execute |

**Screenshot 5:** File creation and ls -la showing 400/600/700 permissions

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/6_File%20Permissions/File%20creation%20and%20ls%20-la%20showing%20400-600-700%20permissions.png" alt="File creation and ls -la showing 400-600-700 permissions" width="900">
</p>


## TASK 6: Attempt to list /tmp/homework contents using user2

Switch to user2 (open new terminal or exit user1):
```bash
$ su - user2
```

OR:
```bash
$ sudo -u user2 ls -la /tmp/homework/
```

Attempt:
```bash
$ ls -la /tmp/homework/
```

Output:
```
ls: cannot open directory '/tmp/homework/': Permission denied
```

Explanation:
- /tmp/homework has 700 permissions
- user2 is NOT the owner and has no permissions (others = 0)
- user2 CANNOT access the directory

**Screenshot 6:** user2 "Permission denied" when listing directory

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/6_File%20Permissions/user2%20-Permission%20denied-%20when%20listing%20directory.png" alt="user2 Permission denied when listing directory" width="900">
</p>


## TASK 7: Fix permissions to allow user2 to view but NOT modify files

Switch back to user1 (or use sudo):
```bash
$ su - user1
```

OR stay as root:
```bash
$ sudo chmod 755 /tmp/homework
```

Command:
```bash
$ chmod 755 /tmp/homework
```

Explanation of 755:
- 7 (owner): rwx (full access)
- 5 (group): r-x (read + execute/access directory)
- 5 (others): r-x (read + execute/access directory)

Note: For directories:
- r (read) = can list contents
- x (execute) = can access/enter directory
- w (write) = can create/delete files inside

Verify as user2:
```bash
$ sudo -u user2 ls -la /tmp/homework/
```

Output:
```
-r--------. 1 user1 user1  6 Jan 15 10:01 read
-rw-------. 1 user1 user1  0 Jan 15 10:01 write
-rwx------. 1 user1 user1 11 Jan 15 10:01 execute
```

user2 CAN list but CANNOT modify (no write on directory).

Test creating file as user2:
```bash
$ sudo -u user2 touch /tmp/homework/test
```

Output:
```
touch: cannot touch '/tmp/homework/test': Permission denied
```

**Screenshot 7:** chmod 755 and user2 can list but not create files

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/6_File%20Permissions/chmod%20755%20and%20user2%20can%20list%20but%20not%20create%20files.png" alt="chmod 755 and user2 can list but not create files" width="900">
</p>


## TASK 8: Fix permissions to allow user2 to view AND modify files inside

Command:
```bash
$ chmod 757 /tmp/homework
```

OR:
```bash
$ chmod o+w /tmp/homework
```

Explanation of 757:
- 7 (owner): rwx (full access)
- 5 (group): r-x (read + execute)
- 7 (others): rwx (full access including write)

Verify:
```bash
$ ls -la /tmp | grep homework
```

Output:
```
drwxr-xrwx. 2 user1 user1 4096 Jan 15 10:01 homework
```

Now user2 CAN create/delete files in the directory.

Test:
```bash
$ sudo -u user2 touch /tmp/homework/test
$ sudo -u user2 rm /tmp/homework/test
```

(Both commands succeed)

**Screenshot 8:** chmod 757 and user2 can create files

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/6_File%20Permissions/chmod%20757%20and%20user2%20can%20create%20files.png" alt="chmod 757 and user2 can create files" width="900">
</p>


## TASK 9: Fix file permissions to allow user2 actions according to naming

As user1 or root, set permissions:

**For "read" file (user2 should be able to READ):**
```bash
$ chmod 644 /tmp/homework/read
```

OR:
```bash
$ chmod o+r /tmp/homework/read
```

**For "write" file (user2 should be able to WRITE):**
```bash
$ chmod 666 /tmp/homework/write
```

OR:
```bash
$ chmod o+rw /tmp/homework/write
```

**For "execute" file (user2 should be able to EXECUTE):**
```bash
$ chmod 755 /tmp/homework/execute
```

OR:
```bash
$ chmod o+rx /tmp/homework/execute
```

Verify:
```bash
$ ls -la /tmp/homework/
```

Output:
```
-rw-r--r--. 1 user1 user1  6 Jan 15 10:01 read
-rw-rw-rw-. 1 user1 user1  0 Jan 15 10:01 write
-rwxr-xr-x. 1 user1 user1 11 Jan 15 10:01 execute
```

Permission summary:

| File | Octal | Permission | user2 (others) can |
|------|-------|------------|---------------------|
| read | 644 | rw-r--r-- | read (r--) |
| write | 666 | rw-rw-rw- | read+write (rw-) |
| execute | 755 | rwxr-xr-x | read+execute (r-x) |

**Screenshot 9:** chmod 644/666/755 on individual files

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/6_File%20Permissions/chmod%20644-666-755%20on%20individual%20files.png" alt="chmod 644/666/755 on individual files" width="900">
</p>


## TASK 10: Using user2 - read, write, execute the files

Switch to user2:
```bash
$ su - user2
```

**READ the "read" file:**
```bash
$ cat /tmp/homework/read
```

Output:
```
Hello
```


**WRITE to the "write" file:**
```bash
$ echo "Hello" > /tmp/homework/write
```

Verify:
```bash
$ cat /tmp/homework/write
```

Output:
```
Hello
```


**EXECUTE the "execute" file:**
```bash
$ /tmp/homework/execute
```

Output:
```
Hello
```

All operations successful!

**Screenshot 10:** user2 read, write, execute operations (all succeed)

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/6_File%20Permissions/user2%20read,%20write,%20execute%20operations%20(all%20succeed).png" alt="user2 read, write, execute operations (all succeed)" width="900">
</p>


## TASK 11: Create immutable and ownership files (as user1)

Switch to user1:
```bash
$ su - user1
```

Create files:
```bash
$ touch /tmp/homework/immutable
$ touch /tmp/homework/ownership
```

Set permissions to 770:
```bash
$ chmod 770 /tmp/homework/immutable
$ chmod 770 /tmp/homework/ownership
```

Verify:
```bash
$ ls -la /tmp/homework/immutable /tmp/homework/ownership
```

Output:
```
-rwxrwx---. 1 user1 user1 0 Jan 15 10:10 immutable
-rwxrwx---. 1 user1 user1 0 Jan 15 10:10 ownership
```

Explanation of 770:
- 7 (owner): rwx
- 7 (group): rwx
- 0 (others): --- (no access)

**Screenshot 11:** Create immutable and ownership with chmod 770

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/6_File%20Permissions/Create%20immutable%20and%20ownership%20with%20chmod%20770.png" alt="Create immutable and ownership with chmod 770" width="900">
</p>


## TASK 12: Change ownership so user2 can write to ownership file

**Method 1: Change owner to user2**
```bash
$ sudo chown user2 /tmp/homework/ownership
```

**Method 2: Change group to user2's group**
```bash
$ sudo chown :user2 /tmp/homework/ownership
```

**Method 3: Add user2 to user1's group and keep 770**
```bash
$ sudo usermod -aG user1 user2
```

Best approach - change owner:
```bash
$ sudo chown user2 /tmp/homework/ownership
```

Verify:
```bash
$ ls -la /tmp/homework/ownership
```

Output:
```
-rwxrwx---. 1 user2 user1 0 Jan 15 10:10 ownership
```

Test as user2:
```bash
$ sudo -u user2 echo "test" > /tmp/homework/ownership
$ sudo -u user2 cat /tmp/homework/ownership
```

Output:
```
test
```

**Screenshot 12:** chown user2 and user2 write to ownership file

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/6_File%20Permissions/chown%20user2%20and%20user2%20write%20to%20ownership%20file.png" alt="chown user2 and user2 write to ownership file" width="900">
</p>


## TASK 13: Make immutable file, attempt removal, fix and remove

### STEP 1: Make file immutable (requires root)

```bash
$ sudo chattr +i /tmp/homework/immutable
```

Verify immutable attribute:
```bash
$ lsattr /tmp/homework/immutable
```

Output:
```
----i--------------- /tmp/homework/immutable
```

Explanation:
- chattr = change attributes
- +i = add immutable flag
- Immutable files CANNOT be modified, deleted, renamed, or linked (even by root!)


### STEP 2: Attempt to remove (will fail)

```bash
$ rm /tmp/homework/immutable
```

Output:
```
rm: cannot remove '/tmp/homework/immutable': Operation not permitted
```

Even as root:
```bash
$ sudo rm /tmp/homework/immutable
```

Output:
```
rm: cannot remove '/tmp/homework/immutable': Operation not permitted
```


### STEP 3: Fix permissions (remove immutable flag)

```bash
$ sudo chattr -i /tmp/homework/immutable
```

Verify:
```bash
$ lsattr /tmp/homework/immutable
```

Output:
```
-------------------- /tmp/homework/immutable
```

(No 'i' flag anymore)


### STEP 4: Remove the file

```bash
$ rm /tmp/homework/immutable
```

OR:
```bash
$ sudo rm /tmp/homework/immutable
```

Verify:
```bash
$ ls /tmp/homework/immutable
```

Output:
```
ls: cannot access '/tmp/homework/immutable': No such file or directory
```

**Screenshot 13:** chattr +i, failed rm, chattr -i, successful rm

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/6_File%20Permissions/chattr%20+i,%20failed%20rm,%20chattr%20-i,%20successful%20rm.png" alt="chattr +i, failed rm, chattr -i, successful rm" width="900">
</p>


## CLEANUP (Optional)

Remove all created files and users:
```bash
$ sudo rm -rf /tmp/homework
$ sudo userdel -r user1
$ sudo userdel -r user2
```


## COMPLETE COMMAND SEQUENCE

```bash
# Task 2: Create users
sudo useradd user1
sudo useradd user2
sudo passwd user1
sudo passwd user2

# Task 3: Switch to user1
su - user1

# Task 4: Create homework folder
mkdir /tmp/homework
chmod 700 /tmp/homework

# Task 5: Create files with permissions
echo "Hello" > /tmp/homework/read
touch /tmp/homework/write
echo "echo Hello" > /tmp/homework/execute
chmod 400 /tmp/homework/read
chmod 600 /tmp/homework/write
chmod 700 /tmp/homework/execute

# Task 6: Test as user2 (will fail)
sudo -u user2 ls /tmp/homework/

# Task 7: Allow view only
chmod 755 /tmp/homework

# Task 8: Allow view and modify
chmod 757 /tmp/homework

# Task 9: Fix file permissions
chmod 644 /tmp/homework/read
chmod 666 /tmp/homework/write
chmod 755 /tmp/homework/execute

# Task 10: Test as user2
sudo -u user2 cat /tmp/homework/read
sudo -u user2 bash -c 'echo "Hello" > /tmp/homework/write'
sudo -u user2 /tmp/homework/execute

# Task 11: Create more files as user1
touch /tmp/homework/immutable /tmp/homework/ownership
chmod 770 /tmp/homework/immutable /tmp/homework/ownership

# Task 12: Change ownership
sudo chown user2 /tmp/homework/ownership

# Task 13: Immutable file operations
sudo chattr +i /tmp/homework/immutable
rm /tmp/homework/immutable          # FAILS
sudo chattr -i /tmp/homework/immutable
rm /tmp/homework/immutable          # SUCCESS
```


## DIRECTORY vs FILE PERMISSIONS

| Perm | For FILES | For DIRECTORIES |
|------|-----------|-----------------|
| r | Read contents | List contents |
| w | Modify contents | Create/delete files |
| x | Execute as program | Enter directory |

**IMPORTANT FOR DIRECTORIES:**
- To list contents (ls): need r + x
- To enter (cd): need x
- To create/delete files: need w + x
- Without x, you cannot access directory at all!


## SPECIAL ATTRIBUTES (chattr)

| Attr | Description |
|------|-------------|
| i | Immutable - cannot modify/delete |
| a | Append only - can only add data |
| c | Compressed - auto compress on disk |
| s | Secure delete - zero blocks when deleted |
| u | Undeletable - can be undeleted |

Commands:
- chattr +i file  = add immutable
- chattr -i file  = remove immutable
- lsattr file     = list attributes


## PERMISSION QUICK REFERENCE

| Octal | Permission | Typical Use |
|-------|------------|-------------|
| 777 | rwxrwxrwx | Full access for everyone |
| 755 | rwxr-xr-x | Executable, others read |
| 750 | rwxr-x--- | Executable, group only |
| 700 | rwx------ | Private executable |
| 666 | rw-rw-rw- | Writable by everyone |
| 644 | rw-r--r-- | Readable by everyone |
| 640 | rw-r----- | Readable by group only |
| 600 | rw------- | Private file |
| 400 | r-------- | Read-only for owner |


