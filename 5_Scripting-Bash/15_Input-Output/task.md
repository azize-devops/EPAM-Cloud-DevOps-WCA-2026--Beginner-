# INPUT/OUTPUT - TASK

GOALS:
------

- Learn to utilize data streams


TASKS:
------

1. Create a script that does the following:
   - Asks the user to input a filename
   - Writes the following poem to the file specified by user:

     An old silent pond...
     A frog jumps into the pond,
     splash! Silence again.

     Autumn moonlight-
     a worm digs silently
     into the chestnut.

     In the twilight rain
     these brilliant-hued hibiscus -
     A lovely sunset.

   - Outputs the poem to the terminal
   - Outputs the message "Task finished" to stderr

2. Run your script:
   - Specify "output" as the file to write the poem to
   - Redirect stdout to /dev/null
   - Redirect stderr to "stderr" file


SELF-REVIEW:
------------

- Script returns no output (stdout redirected)

- cat output command returns:
  An old silent pond...
  A frog jumps into the pond,
  splash! Silence again.

  Autumn moonlight-
  a worm digs silently
  into the chestnut.

  In the twilight rain
  these brilliant-hued hibiscus -
  A lovely sunset.

- cat stderr command returns:
  Task finished


HELPFUL MATERIALS:
------------------

https://linuxize.com/post/bash-redirect-stderr-stdout/
https://devhints.io/bash

