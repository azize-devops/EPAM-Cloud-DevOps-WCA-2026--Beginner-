# FILE PERMISSION - TASK

TASKS:
------

1. Open terminal of your choosing.

2. Create two users: user1, user2.

3. Switch to user1.

4. Create folder /tmp/homework with 700 permissions.

5. Create 3 files inside /tmp/homework folder:
   - read (content: Hello, permissions: 400)
   - write (permissions: 600)
   - execute (content: echo Hello, permissions: 700)

6. Attempt to list /tmp/homework contents using user2.

7. Fix /tmp/homework permissions to allow user2 to view but not modify files inside. Verify.

8. Fix /tmp/homework permissions to allow user2 to view and modify files inside.

9. Fix file permissions inside /tmp/homework to allow user2 actions on files
   according to their naming.

10. Using user2 read contents of read file, write Hello inside write file,
    execute execute file.

11. Using user1 create two more files inside /tmp/homework:
    immutable, ownership (permissions: 770).

12. Change ownership of ownership file so that user2 would be able to write to it.

13. Make immutable file immutable. Attempt to remove it. Fix permissions, remove it.


SELF-REVIEW:
------------

- user2 is able to list contents of /tmp/homework folder
- user2 is able to read read file
- user2 is able to write to write file
- user2 is able to execute execute file
- user2 is able to write to ownership file


HELPFUL MATERIALS:
------------------

https://linuxconfig.org/bash-scripting-tutorial-for-beginners
https://wiki.archlinux.org/index.php/File_permissions_and_attributes
https://www.thegeekdiary.com/understanding-basic-file-permissions-and-ownership-in-linux/

