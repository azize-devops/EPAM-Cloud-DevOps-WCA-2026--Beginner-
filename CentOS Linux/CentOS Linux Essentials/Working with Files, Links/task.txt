WORKING WITH FILES, LINKS - TASK
==================================

TASKS:
------

1. Open terminal of your choosing.

2. Using text editor of your choosing create two files named script and script.sh
   both with the following content:

   #!/bin/bash

   echo Hello

3. Make files executable by running chmod +x script script.sh command.

4. Create a soft link called script_soft for file script.

5. Create a hard link called script_hard for file script.sh.

6. Execute both script_soft and script_hard files.

7. Remove original files script and script.sh.

8. Execute both script_soft and script_hard files.

9. Remove all created files.


SELF-REVIEW:
------------

- When executing files "Hello" message should be printed out into your terminal.

- After removing the original files, trying to execute soft link should print an error.


HELPFUL MATERIALS:
------------------

https://linuxconfig.org/bash-scripting-tutorial-for-beginners
https://medium.com/@307/hard-links-and-symbolic-links-a-comparison-7f2b56864cdd
