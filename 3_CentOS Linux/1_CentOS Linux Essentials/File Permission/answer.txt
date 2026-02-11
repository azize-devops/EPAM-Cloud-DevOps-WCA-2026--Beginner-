FILE PERMISSION - ANSWERS
==========================

================================================================================
TASK 1: Open terminal of your choosing
================================================================================

Methods:
- Press Ctrl + Alt + T
- Activities > Search "Terminal"
- Right-click on Desktop > Open Terminal

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                          |
|                                                                  |
|  Terminal window showing:                                        |
|  - Empty terminal with command prompt                            |
|  - Username and hostname visible (user@hostname:~$)              |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 2: Create two users: user1, user2
================================================================================

Commands:
$ sudo useradd user1
$ sudo useradd user2

Set passwords:
$ sudo passwd user1
$ sudo passwd user2

Verify:
$ id user1
$ id user2

Output:
uid=1001(user1) gid=1001(user1) groups=1001(user1)
uid=1002(user2) gid=1002(user2) groups=1002(user2)

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                          |
|                                                                  |
|  Terminal showing:                                               |
|  - $ sudo useradd user1                                          |
|  - $ sudo useradd user2                                          |
|  - $ id user1                                                    |
|  - uid=1001(user1) gid=1001(user1) groups=1001(user1)            |
|  - $ id user2                                                    |
|  - uid=1002(user2) gid=1002(user2) groups=1002(user2)            |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 3: Switch to user1
================================================================================

Command:
$ su - user1

OR:
$ sudo su - user1

Verify:
$ whoami

Output:
user1

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                          |
|                                                                  |
|  Terminal showing:                                               |
|  - $ su - user1                                                  |
|  - Password: ********                                            |
|  - $ whoami                                                      |
|  - user1                                                         |
|    ^^^^^                                                         |
|    Confirms switched to user1                                    |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 4: Create folder /tmp/homework with 700 permissions
================================================================================

Commands:
$ mkdir /tmp/homework
$ chmod 700 /tmp/homework

OR in one command:
$ mkdir -m 700 /tmp/homework

Verify:
$ ls -la /tmp | grep homework

Output:
drwx------. 2 user1 user1 4096 Jan 15 10:00 homework

Explanation of 700:
- 7 (owner): rwx (read + write + execute)
- 0 (group): --- (no permissions)
- 0 (others): --- (no permissions)

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                          |
|                                                                  |
|  Terminal showing:                                               |
|  - $ mkdir /tmp/homework                                         |
|  - $ chmod 700 /tmp/homework                                     |
|  - $ ls -la /tmp | grep homework                                 |
|  - drwx------. 2 user1 user1 4096 Jan 15 10:00 homework          |
|     ^^^                                                          |
|     rwx for owner only (700)                                     |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 5: Create 3 files with specific content and permissions
================================================================================

Create files:
$ echo "Hello" > /tmp/homework/read
$ touch /tmp/homework/write
$ echo "echo Hello" > /tmp/homework/execute

Set permissions:
$ chmod 400 /tmp/homework/read
$ chmod 600 /tmp/homework/write
$ chmod 700 /tmp/homework/execute

Verify:
$ ls -la /tmp/homework/

Output:
-r--------. 1 user1 user1  6 Jan 15 10:01 read
-rw-------. 1 user1 user1  0 Jan 15 10:01 write
-rwx------. 1 user1 user1 11 Jan 15 10:01 execute

Permission breakdown:
+----------+------+-------------+-------------------+
| File     | Octal| Permission  | user1 can...      |
+----------+------+-------------+-------------------+
| read     | 400  | r--------   | read only         |
| write    | 600  | rw-------   | read + write      |
| execute  | 700  | rwx------   | read+write+execute|
+----------+------+-------------+-------------------+

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                          |
|                                                                  |
|  Terminal showing:                                               |
|  - $ echo "Hello" > /tmp/homework/read                           |
|  - $ touch /tmp/homework/write                                   |
|  - $ echo "echo Hello" > /tmp/homework/execute                   |
|  - $ chmod 400 /tmp/homework/read                                |
|  - $ chmod 600 /tmp/homework/write                               |
|  - $ chmod 700 /tmp/homework/execute                             |
|  - $ ls -la /tmp/homework/                                       |
|  - -r-------- ... read       (400 = read only)                   |
|  - -rw------- ... write      (600 = read+write)                  |
|  - -rwx------ ... execute    (700 = read+write+execute)          |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 6: Attempt to list /tmp/homework contents using user2
================================================================================

Switch to user2 (open new terminal or exit user1):
$ su - user2

OR:
$ sudo -u user2 ls -la /tmp/homework/

Attempt:
$ ls -la /tmp/homework/

Output:
ls: cannot open directory '/tmp/homework/': Permission denied

Explanation:
- /tmp/homework has 700 permissions
- user2 is NOT the owner and has no permissions (others = 0)
- user2 CANNOT access the directory

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                          |
|                                                                  |
|  Terminal showing:                                               |
|  - $ sudo -u user2 ls -la /tmp/homework/                         |
|  - ls: cannot open directory '/tmp/homework/': Permission denied |
|    ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^  |
|    user2 has NO access to directory (others = 0)                 |
|                                                                  |
|  OR using su:                                                    |
|  - $ su - user2                                                  |
|  - $ ls -la /tmp/homework/                                       |
|  - ls: cannot open directory '/tmp/homework/': Permission denied |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 7: Fix permissions to allow user2 to view but NOT modify files
================================================================================

Switch back to user1 (or use sudo):
$ su - user1

OR stay as root:
$ sudo chmod 755 /tmp/homework

Command:
$ chmod 755 /tmp/homework

Explanation of 755:
- 7 (owner): rwx (full access)
- 5 (group): r-x (read + execute/access directory)
- 5 (others): r-x (read + execute/access directory)

Note: For directories:
- r (read) = can list contents
- x (execute) = can access/enter directory
- w (write) = can create/delete files inside

Verify as user2:
$ sudo -u user2 ls -la /tmp/homework/

Output:
-r--------. 1 user1 user1  6 Jan 15 10:01 read
-rw-------. 1 user1 user1  0 Jan 15 10:01 write
-rwx------. 1 user1 user1 11 Jan 15 10:01 execute

user2 CAN list but CANNOT modify (no write on directory).

Test creating file as user2:
$ sudo -u user2 touch /tmp/homework/test

Output:
touch: cannot touch '/tmp/homework/test': Permission denied

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                          |
|                                                                  |
|  Terminal showing:                                               |
|  - $ chmod 755 /tmp/homework                                     |
|  - $ ls -la /tmp | grep homework                                 |
|  - drwxr-xr-x ... homework   (755 = others can list)             |
|  - $ sudo -u user2 ls -la /tmp/homework/                         |
|  - (shows file listing - SUCCESS)                                |
|  - $ sudo -u user2 touch /tmp/homework/test                      |
|  - touch: cannot touch ... Permission denied                     |
|    ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^                       |
|    user2 CAN list but CANNOT create files                        |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 8: Fix permissions to allow user2 to view AND modify files inside
================================================================================

Command:
$ chmod 757 /tmp/homework

OR:
$ chmod o+w /tmp/homework

Explanation of 757:
- 7 (owner): rwx (full access)
- 5 (group): r-x (read + execute)
- 7 (others): rwx (full access including write)

Verify:
$ ls -la /tmp | grep homework

Output:
drwxr-xrwx. 2 user1 user1 4096 Jan 15 10:01 homework

Now user2 CAN create/delete files in the directory.

Test:
$ sudo -u user2 touch /tmp/homework/test
$ sudo -u user2 rm /tmp/homework/test

(Both commands succeed)

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                          |
|                                                                  |
|  Terminal showing:                                               |
|  - $ chmod 757 /tmp/homework                                     |
|  - $ ls -la /tmp | grep homework                                 |
|  - drwxr-xrwx ... homework   (757 = others can write)            |
|        ^^^                                                       |
|        rwx for others                                            |
|  - $ sudo -u user2 touch /tmp/homework/test                      |
|  - (succeeds - no error)                                         |
|  - $ sudo -u user2 rm /tmp/homework/test                         |
|  - (succeeds - no error)                                         |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 9: Fix file permissions to allow user2 actions according to naming
================================================================================

As user1 or root, set permissions:

For "read" file (user2 should be able to READ):
$ chmod 644 /tmp/homework/read

OR:
$ chmod o+r /tmp/homework/read

For "write" file (user2 should be able to WRITE):
$ chmod 666 /tmp/homework/write

OR:
$ chmod o+rw /tmp/homework/write

For "execute" file (user2 should be able to EXECUTE):
$ chmod 755 /tmp/homework/execute

OR:
$ chmod o+rx /tmp/homework/execute

Verify:
$ ls -la /tmp/homework/

Output:
-rw-r--r--. 1 user1 user1  6 Jan 15 10:01 read
-rw-rw-rw-. 1 user1 user1  0 Jan 15 10:01 write
-rwxr-xr-x. 1 user1 user1 11 Jan 15 10:01 execute

Permission summary:
+----------+------+-------------+---------------------+
| File     | Octal| Permission  | user2 (others) can  |
+----------+------+-------------+---------------------+
| read     | 644  | rw-r--r--   | read (r--)          |
| write    | 666  | rw-rw-rw-   | read+write (rw-)    |
| execute  | 755  | rwxr-xr-x   | read+execute (r-x)  |
+----------+------+-------------+---------------------+

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 9 <<<                          |
|                                                                  |
|  Terminal showing:                                               |
|  - $ chmod 644 /tmp/homework/read                                |
|  - $ chmod 666 /tmp/homework/write                               |
|  - $ chmod 755 /tmp/homework/execute                             |
|  - $ ls -la /tmp/homework/                                       |
|  - -rw-r--r-- ... read      (644 = others can read)              |
|  - -rw-rw-rw- ... write     (666 = others can read+write)        |
|  - -rwxr-xr-x ... execute   (755 = others can read+execute)      |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 10: Using user2 - read, write, execute the files
================================================================================

Switch to user2:
$ su - user2

READ the "read" file:
$ cat /tmp/homework/read

Output:
Hello


WRITE to the "write" file:
$ echo "Hello" > /tmp/homework/write

Verify:
$ cat /tmp/homework/write

Output:
Hello


EXECUTE the "execute" file:
$ /tmp/homework/execute

Output:
Hello

All operations successful!

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 10 <<<                         |
|                                                                  |
|  Terminal showing (as user2):                                    |
|  - $ cat /tmp/homework/read                                      |
|  - Hello                                                         |
|    ^^^^^ READ operation SUCCESS                                  |
|                                                                  |
|  - $ echo "Hello" > /tmp/homework/write                          |
|  - $ cat /tmp/homework/write                                     |
|  - Hello                                                         |
|    ^^^^^ WRITE operation SUCCESS                                 |
|                                                                  |
|  - $ /tmp/homework/execute                                       |
|  - Hello                                                         |
|    ^^^^^ EXECUTE operation SUCCESS                               |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 11: Create immutable and ownership files (as user1)
================================================================================

Switch to user1:
$ su - user1

Create files:
$ touch /tmp/homework/immutable
$ touch /tmp/homework/ownership

Set permissions to 770:
$ chmod 770 /tmp/homework/immutable
$ chmod 770 /tmp/homework/ownership

Verify:
$ ls -la /tmp/homework/immutable /tmp/homework/ownership

Output:
-rwxrwx---. 1 user1 user1 0 Jan 15 10:10 immutable
-rwxrwx---. 1 user1 user1 0 Jan 15 10:10 ownership

Explanation of 770:
- 7 (owner): rwx
- 7 (group): rwx
- 0 (others): --- (no access)

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 11 <<<                         |
|                                                                  |
|  Terminal showing:                                               |
|  - $ touch /tmp/homework/immutable                               |
|  - $ touch /tmp/homework/ownership                               |
|  - $ chmod 770 /tmp/homework/immutable                           |
|  - $ chmod 770 /tmp/homework/ownership                           |
|  - $ ls -la /tmp/homework/immutable /tmp/homework/ownership      |
|  - -rwxrwx--- ... immutable   (770)                              |
|  - -rwxrwx--- ... ownership   (770)                              |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 12: Change ownership so user2 can write to ownership file
================================================================================

Method 1: Change owner to user2
$ sudo chown user2 /tmp/homework/ownership

Method 2: Change group to user2's group
$ sudo chown :user2 /tmp/homework/ownership

Method 3: Add user2 to user1's group and keep 770
$ sudo usermod -aG user1 user2

Best approach - change owner:
$ sudo chown user2 /tmp/homework/ownership

Verify:
$ ls -la /tmp/homework/ownership

Output:
-rwxrwx---. 1 user2 user1 0 Jan 15 10:10 ownership

Test as user2:
$ sudo -u user2 echo "test" > /tmp/homework/ownership
$ sudo -u user2 cat /tmp/homework/ownership

Output:
test

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 12 <<<                         |
|                                                                  |
|  Terminal showing:                                               |
|  - $ sudo chown user2 /tmp/homework/ownership                    |
|  - $ ls -la /tmp/homework/ownership                              |
|  - -rwxrwx---. 1 user2 user1 0 Jan 15 10:10 ownership            |
|                  ^^^^^                                           |
|                  owner changed to user2                          |
|  - $ sudo -u user2 bash -c 'echo "test" > /tmp/homework/ownership'
|  - $ sudo -u user2 cat /tmp/homework/ownership                   |
|  - test                                                          |
|    ^^^^ user2 can write - SUCCESS                                |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
TASK 13: Make immutable file, attempt removal, fix and remove
================================================================================

STEP 1: Make file immutable (requires root)
$ sudo chattr +i /tmp/homework/immutable

Verify immutable attribute:
$ lsattr /tmp/homework/immutable

Output:
----i--------------- /tmp/homework/immutable

Explanation:
- chattr = change attributes
- +i = add immutable flag
- Immutable files CANNOT be modified, deleted, renamed, or linked (even by root!)


STEP 2: Attempt to remove (will fail)
$ rm /tmp/homework/immutable

Output:
rm: cannot remove '/tmp/homework/immutable': Operation not permitted

Even as root:
$ sudo rm /tmp/homework/immutable

Output:
rm: cannot remove '/tmp/homework/immutable': Operation not permitted


STEP 3: Fix permissions (remove immutable flag)
$ sudo chattr -i /tmp/homework/immutable

Verify:
$ lsattr /tmp/homework/immutable

Output:
-------------------- /tmp/homework/immutable

(No 'i' flag anymore)


STEP 4: Remove the file
$ rm /tmp/homework/immutable

OR:
$ sudo rm /tmp/homework/immutable

Verify:
$ ls /tmp/homework/immutable

Output:
ls: cannot access '/tmp/homework/immutable': No such file or directory

+------------------------------------------------------------------+
|                    >>> SCREENSHOT 13 <<<                         |
|                                                                  |
|  Terminal showing:                                               |
|  - $ sudo chattr +i /tmp/homework/immutable                      |
|  - $ lsattr /tmp/homework/immutable                              |
|  - ----i--------------- /tmp/homework/immutable                  |
|       ^                                                          |
|       immutable flag set                                         |
|                                                                  |
|  - $ rm /tmp/homework/immutable                                  |
|  - rm: cannot remove ... Operation not permitted                 |
|    ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^                 |
|    CANNOT remove - even root cannot delete!                      |
|                                                                  |
|  - $ sudo chattr -i /tmp/homework/immutable                      |
|  - $ lsattr /tmp/homework/immutable                              |
|  - -------------------- /tmp/homework/immutable                  |
|    (no i flag)                                                   |
|                                                                  |
|  - $ rm /tmp/homework/immutable                                  |
|  - (succeeds - no error)                                         |
|  - $ ls /tmp/homework/immutable                                  |
|  - ls: cannot access ... No such file or directory               |
|    ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^                  |
|    File deleted successfully                                     |
|                                                                  |
+------------------------------------------------------------------+


================================================================================
CLEANUP (Optional)
================================================================================

Remove all created files and users:
$ sudo rm -rf /tmp/homework
$ sudo userdel -r user1
$ sudo userdel -r user2


================================================================================
COMPLETE COMMAND SEQUENCE
================================================================================

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


================================================================================
DIRECTORY vs FILE PERMISSIONS
================================================================================

+------+-------------------+-------------------+
| Perm | For FILES         | For DIRECTORIES   |
+------+-------------------+-------------------+
|  r   | Read contents     | List contents     |
|  w   | Modify contents   | Create/delete files|
|  x   | Execute as program| Enter directory   |
+------+-------------------+-------------------+

IMPORTANT FOR DIRECTORIES:
- To list contents (ls): need r + x
- To enter (cd): need x
- To create/delete files: need w + x
- Without x, you cannot access directory at all!


================================================================================
SPECIAL ATTRIBUTES (chattr)
================================================================================

+------+------------------------------------------+
| Attr | Description                              |
+------+------------------------------------------+
|  i   | Immutable - cannot modify/delete         |
|  a   | Append only - can only add data          |
|  c   | Compressed - auto compress on disk       |
|  s   | Secure delete - zero blocks when deleted |
|  u   | Undeletable - can be undeleted           |
+------+------------------------------------------+

Commands:
- chattr +i file  = add immutable
- chattr -i file  = remove immutable
- lsattr file     = list attributes


================================================================================
PERMISSION QUICK REFERENCE
================================================================================

+------+-------------+---------------------------+
| Octal| Permission  | Typical Use               |
+------+-------------+---------------------------+
| 777  | rwxrwxrwx   | Full access for everyone  |
| 755  | rwxr-xr-x   | Executable, others read   |
| 750  | rwxr-x---   | Executable, group only    |
| 700  | rwx------   | Private executable        |
| 666  | rw-rw-rw-   | Writable by everyone      |
| 644  | rw-r--r--   | Readable by everyone      |
| 640  | rw-r-----   | Readable by group only    |
| 600  | rw-------   | Private file              |
| 400  | r--------   | Read-only for owner       |
+------+-------------+---------------------------+


================================================================================
SCREENSHOTS CHECKLIST
================================================================================

Print this checklist and mark each screenshot as you take it:

[ ] SCREENSHOT 1  - Terminal window opened
[ ] SCREENSHOT 2  - useradd commands and id verification
[ ] SCREENSHOT 3  - Switch to user1 (su - user1) and whoami
[ ] SCREENSHOT 4  - mkdir and chmod 700 for /tmp/homework (drwx------)
[ ] SCREENSHOT 5  - File creation and ls -la showing 400/600/700 permissions
[ ] SCREENSHOT 6  - user2 "Permission denied" when listing directory
[ ] SCREENSHOT 7  - chmod 755 and user2 can list but not create files
[ ] SCREENSHOT 8  - chmod 757 and user2 can create files
[ ] SCREENSHOT 9  - chmod 644/666/755 on individual files
[ ] SCREENSHOT 10 - user2 read, write, execute operations (all succeed)
[ ] SCREENSHOT 11 - Create immutable and ownership with chmod 770
[ ] SCREENSHOT 12 - chown user2 and user2 write to ownership file
[ ] SCREENSHOT 13 - chattr +i, failed rm, chattr -i, successful rm

TOTAL: 13 Screenshots Required
