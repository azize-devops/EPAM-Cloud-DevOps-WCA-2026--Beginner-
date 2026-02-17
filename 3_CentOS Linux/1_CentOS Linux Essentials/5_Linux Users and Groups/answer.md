# LINUX USERS AND GROUPS - ANSWERS

## TASK 1: Open terminal of your choosing

Methods:
- Press Ctrl + Alt + T
- Activities > Search "Terminal"
- Right-click on Desktop > Open Terminal

Note: Most commands require root privileges, so use sudo or switch to root.

**Screenshot 1:** Terminal window opened

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/5_Linux%20Users%20and%20Groups/Terminal%20window%20opened.png" alt="Terminal window opened" width="900">
</p>


## TASK 2: Create three groups: group1, group2, shared (GID 9999)

Commands:
```bash
$ sudo groupadd group1
$ sudo groupadd group2
$ sudo groupadd -g 9999 shared
```

Explanation:
- groupadd = command to create a new group
- -g 9999 = specify Group ID (GID) for shared group

Verify:
```bash
$ cat /etc/group | grep -E "group1|group2|shared"
```

Output:
```
group1:x:10000:
group2:x:10001:
shared:x:9999:
```

Alternative verification:
```bash
$ getent group group1 group2 shared
```

Output:
```
group1:x:10000:
group2:x:10001:
shared:x:9999:
```

**Screenshot 2:** groupadd commands and /etc/group verification (GID 9999)

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/5_Linux%20Users%20and%20Groups/groupadd%20commands%20and%20-etc-group%20verification%20(GID%209999).png" alt="groupadd commands and /etc/group verification (GID 9999)" width="900">
</p>


## TASK 3: Create two users with specific UID and primary groups

Commands:
```bash
$ sudo useradd -u 1001 -g group1 user1
$ sudo useradd -u 1002 -g group2 user2
```

Explanation:
- useradd = command to create a new user
- -u 1001 = specify User ID (UID)
- -g group1 = specify primary group

Set passwords (optional but recommended):
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
uid=1001(user1) gid=10000(group1) groups=10000(group1)
uid=1002(user2) gid=10001(group2) groups=10001(group2)
```

Alternative verification:
```bash
$ cat /etc/passwd | grep -E "user1|user2"
```

Output:
```
user1:x:1001:10000::/home/user1:/bin/bash
user2:x:1002:10001::/home/user2:/bin/bash
```

**Screenshot 3:** useradd commands and id verification (UID 1001, 1002)

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/5_Linux%20Users%20and%20Groups/useradd%20commands%20and%20id%20verification%20(UID%201001,%201002).png" alt="useradd commands and id verification (UID 1001, 1002)" width="900">
</p>


## TASK 4: Add user1 and user2 to shared group

Commands:
```bash
$ sudo usermod -aG shared user1
$ sudo usermod -aG shared user2
```

Explanation:
- usermod = modify user account
- -a = append (don't remove from other groups)
- -G shared = add to supplementary group "shared"
- IMPORTANT: Always use -a with -G to avoid removing user from other groups!

Verify:
```bash
$ id user1
$ id user2
```

Output:
```
uid=1001(user1) gid=10000(group1) groups=10000(group1),9999(shared)
uid=1002(user2) gid=10001(group2) groups=10001(group2),9999(shared)
```

Alternative verification:
```bash
$ cat /etc/group | grep shared
```

Output:
```
shared:x:9999:user1,user2
```

**Screenshot 4:** usermod -aG and id showing shared group membership

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/5_Linux%20Users%20and%20Groups/usermod%20-aG%20and%20id%20showing%20shared%20group%20membership.png" alt="usermod -aG and id showing shared group membership" width="900">
</p>


## TASK 5: Create file /tmp/test containing "Hello" with user1

**Method 1: Using sudo -u**
```bash
$ sudo -u user1 bash -c 'echo "Hello" > /tmp/test'
```

**Method 2: Using su**
```bash
$ su - user1
$ echo "Hello" > /tmp/test
$ exit
```

**Method 3: Using sudo su**
```bash
$ sudo su - user1
$ echo "Hello" > /tmp/test
$ exit
```

Verify:
```bash
$ cat /tmp/test
$ ls -la /tmp/test
```

Output:
```
Hello

-rw-r--r--. 1 user1 group1 6 Jan 15 10:10 /tmp/test
```

Explanation:
- File is owned by user1
- Group ownership is group1 (user1's primary group)
- Default permissions: 644 (rw-r--r--)

**Screenshot 5:** File creation as user1 and ls -la showing ownership

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/5_Linux%20Users%20and%20Groups/File%20creation%20as%20user1%20and%20ls%20-la%20showing%20ownership.png" alt="File creation as user1 and ls -la showing ownership" width="900">
</p>


## TASK 6: Remove public read permissions using chmod 640

Command:
```bash
$ sudo chmod 640 /tmp/test
```

Verify:
```bash
$ ls -la /tmp/test
```

Output:
```
-rw-r-----. 1 user1 group1 6 Jan 15 10:10 /tmp/test
```

Explanation of permissions 640:
- 6 (owner/user1): rw- (read + write)
- 4 (group/group1): r-- (read only)
- 0 (others/public): --- (no permissions)

Permission breakdown:

| Octal | Binary | Perms | Meaning |
|-------|--------|-------|---------|
| 6 | 110 | rw- | read+write |
| 4 | 100 | r-- | read only |
| 0 | 000 | --- | none |

**Screenshot 6:** chmod 640 and ls -la showing rw-r----- permissions

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/5_Linux%20Users%20and%20Groups/chmod%20640%20and%20ls%20-la%20showing%20rw-r-----%20permissions.png" alt="chmod 640 and ls -la showing rw-r----- permissions" width="900">
</p>


## TASK 7: Using user2, attempt to read /tmp/test file contents

Command:
```bash
$ sudo -u user2 cat /tmp/test
```

OR:
```bash
$ su - user2
$ cat /tmp/test
```

Output:
```
cat: /tmp/test: Permission denied
```

Explanation:
- user2 is NOT the owner (user1 is)
- user2 is NOT in group1 (user2's primary group is group2)
- user2 is in "others" category which has NO permissions (0)
- Therefore, user2 CANNOT read the file

Access check:

| Category | User/Group | Can Read? |
|----------|------------|-----------|
| Owner | user1 | YES (rw-) |
| Group | group1 | YES (r--) |
| Others | user2 | NO (---) |

**Screenshot 7:** user2 cat attempt showing "Permission denied"

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/5_Linux%20Users%20and%20Groups/user2%20cat%20attempt%20showing%20-Permission%20denied-.png" alt="user2 cat attempt showing Permission denied" width="900">
</p>


## TASK 8: Change ownership using chown user1:shared

Command:
```bash
$ sudo chown user1:shared /tmp/test
```

Explanation:
- chown = change owner
- user1:shared = set owner to user1, group to shared
- Format: chown [owner]:[group] file

Verify:
```bash
$ ls -la /tmp/test
```

Output:
```
-rw-r-----. 1 user1 shared 6 Jan 15 10:10 /tmp/test
```

Now:
- Owner: user1
- Group: shared (changed from group1)
- Permissions: still 640

**Screenshot 8:** chown user1:shared and ls -la showing new group

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/5_Linux%20Users%20and%20Groups/chown%20user1-shared%20and%20ls%20-la%20showing%20new%20group.png" alt="chown user1:shared and ls -la showing new group" width="900">
</p>


## TASK 9: Using user2, attempt to read /tmp/test file contents (again)

Command:
```bash
$ sudo -u user2 cat /tmp/test
```

OR:
```bash
$ su - user2
$ cat /tmp/test
```

Output:
```
Hello
```

Explanation:
- File group is now "shared"
- user2 IS a member of "shared" group
- Group permissions are r-- (read)
- Therefore, user2 CAN now read the file!

Access check after chown:

| Category | User/Group | Can Read? |
|----------|------------|-----------|
| Owner | user1 | YES (rw-) |
| Group | shared | YES (r--) |
| Others | everyone | NO (---) |

user2 is in "shared" group -> matches Group category -> CAN READ!

**Screenshot 9:** user2 cat showing "Hello" (success)

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/5_Linux%20Users%20and%20Groups/user2%20cat%20showing%20-Hello-%20(success).png" alt="user2 cat showing Hello (success)" width="900">
</p>


## TASK 10: Remove created users and groups

**Step 1: Remove users**
```bash
$ sudo userdel -r user1
$ sudo userdel -r user2
```

Explanation:
- userdel = delete user
- -r = remove home directory and mail spool

**Step 2: Remove groups**
```bash
$ sudo groupdel group1
$ sudo groupdel group2
$ sudo groupdel shared
```

**Step 3: Remove test file**
```bash
$ sudo rm /tmp/test
```

Verify:
```bash
$ id user1
$ id user2
$ getent group group1 group2 shared
```

Output:
```
id: 'user1': no such user
id: 'user2': no such user
(no output for getent - groups don't exist)
```

**Screenshot 10:** Cleanup commands and verification

<p align="center">
  <img src="https://github.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/blob/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/5_Linux%20Users%20and%20Groups/Cleanup%20commands%20and%20verification.png" alt="Cleanup commands and verification" width="900">
</p>


## COMPLETE COMMAND SEQUENCE

```bash
# Task 1: Open terminal
# (Use Ctrl+Alt+T)

# Task 2: Create groups
sudo groupadd group1
sudo groupadd group2
sudo groupadd -g 9999 shared

# Task 3: Create users
sudo useradd -u 1001 -g group1 user1
sudo useradd -u 1002 -g group2 user2

# Task 4: Add users to shared group
sudo usermod -aG shared user1
sudo usermod -aG shared user2

# Verify users
id user1
id user2

# Task 5: Create file as user1
sudo -u user1 bash -c 'echo "Hello" > /tmp/test'

# Task 6: Remove public read permissions
sudo chmod 640 /tmp/test

# Task 7: Try to read as user2 (will fail)
sudo -u user2 cat /tmp/test

# Task 8: Change ownership to shared group
sudo chown user1:shared /tmp/test

# Task 9: Try to read as user2 (will succeed)
sudo -u user2 cat /tmp/test

# Task 10: Cleanup
sudo userdel -r user1
sudo userdel -r user2
sudo groupdel group1
sudo groupdel group2
sudo groupdel shared
sudo rm /tmp/test
```


## FILE PERMISSIONS EXPLAINED

### PERMISSION FORMAT: -rwxrwxrwx

| Pos | Category | Description |
|-----|----------|-------------|
| 1 | Type | - file, d dir, l link |
| 2-4 | Owner | User permissions |
| 5-7 | Group | Group permissions |
| 8-10 | Others | Public permissions |

### PERMISSION VALUES:

| Perm | Binary | Octal |
|------|--------|-------|
| --- | 000 | 0 |
| --x | 001 | 1 |
| -w- | 010 | 2 |
| -wx | 011 | 3 |
| r-- | 100 | 4 |
| r-x | 101 | 5 |
| rw- | 110 | 6 |
| rwx | 111 | 7 |

### COMMON PERMISSION SETS:

| Octal | Permission | Typical Use |
|-------|------------|-------------|
| 755 | rwxr-xr-x | Directories, scripts |
| 644 | rw-r--r-- | Regular files |
| 640 | rw-r----- | Private group files |
| 600 | rw------- | Private files |
| 700 | rwx------ | Private directories |


## USER AND GROUP COMMANDS REFERENCE

### USER COMMANDS:

| Command | Description |
|---------|-------------|
| useradd username | Create new user |
| useradd -u UID username | Create user with specific UID |
| useradd -g group username | Create user with primary group |
| userdel username | Delete user |
| userdel -r username | Delete user + home directory |
| usermod -aG group user | Add user to supplementary group |
| passwd username | Set/change password |
| id username | Show user UID, GID, groups |

### GROUP COMMANDS:

| Command | Description |
|---------|-------------|
| groupadd groupname | Create new group |
| groupadd -g GID groupname | Create group with specific GID |
| groupdel groupname | Delete group |
| groups username | Show groups user belongs to |
| getent group groupname | Get group information |

### PERMISSION COMMANDS:

| Command | Description |
|---------|-------------|
| chmod 640 file | Set permissions using octal |
| chmod u+x file | Add execute for user |
| chmod g-w file | Remove write for group |
| chmod o= file | Remove all permissions for others |
| chown user file | Change owner |
| chown user:group file | Change owner and group |
| chgrp group file | Change group only |

### SWITCH USER COMMANDS:

| Command | Description |
|---------|-------------|
| su - username | Switch to user (login shell) |
| su username | Switch to user (non-login shell) |
| sudo -u username command | Run command as specific user |
| sudo su - username | Switch to user using sudo |


## IMPORTANT FILES

**/etc/passwd** - User account information
Format: username:x:UID:GID:comment:home:shell
Example: user1:x:1001:10000::/home/user1:/bin/bash

**/etc/shadow** - Encrypted passwords (root only)
Format: username:encrypted_password:...

**/etc/group** - Group information
Format: groupname:x:GID:members
Example: shared:x:9999:user1,user2


