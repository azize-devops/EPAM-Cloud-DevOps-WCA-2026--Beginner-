LINUX USERS AND GROUPS - TASK
==============================

TASKS:
------

1. Open terminal of your choosing.

2. Create three groups: group1, group2, shared (GID 9999).

3. Create two users: user1 (UID: 1001, primary group: group1),
   user2 (UID: 1002, primary group: group2).

4. Add user1 and user2 to shared group.

5. With the help of sudo or su commands create file /tmp/test containing
   word "Hello" with user1.

6. Remove public read permissions on the file using chmod 640 /tmp/test command.

7. Using user2 attempt to read /tmp/test file contents.

8. Change ownership of file using chown user1:shared /tmp/test command.

9. Using user2 attempt to read /tmp/test file contents.

10. Remove created users and groups.


SELF-REVIEW:
------------

- user2 is able to read /tmp/test file contents after ownership change.

- id user1 command returns the following output (ignore group1 gid):
  uid=1001(user1) gid=10000(group1) groups=10000(group1),9999(shared)

- id user2 command returns the following output (ignore group2 gid):
  uid=1002(user2) gid=10001(group2) groups=10001(group2),9999(shared)


HELPFUL MATERIALS:
------------------

https://linuxconfig.org/bash-scripting-tutorial-for-beginners
https://www.tecmint.com/manage-users-and-groups-in-linux/
