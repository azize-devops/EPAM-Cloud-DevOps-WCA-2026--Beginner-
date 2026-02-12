# LINUX DIRECTORIES LAYOUT - TASK

TASKS:
------

1. Open terminal of your choosing.

2. Create the following folders in your user's home directory: empty, full, final.

3. Create a file file1 in full directory.

4. Copy empty folder to final folder.

5. Move empty folder to final directory under new name empty_moved.

6. Create nested folder inside full directory.

7. Copy all contents of full directory to final folder.

8. Copy full directory to final folder.

9. Move full directory to final folder under new name full_moved.


SELF-REVIEW:
------------

Install tree utility using: yum install tree -y

Output of tree /<your_user_home_directory> command should look like this:

<your_user_home_directory>
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


HELPFUL MATERIALS:
------------------

https://linuxconfig.org/bash-scripting-tutorial-for-beginners

