# SPECIAL VARIABLES - TASK

GOALS:
------

- Using reserved variables


TASKS:
------

1. Create a script that does the following:
   - Prints the name of the script
   - Prints all arguments
   - Prints the number of arguments passed to the script
   - Prints 2nd and 4th argument
   - Prints the exit code of -eq operation on 1st and 2nd arguments (use [[]])

2. Execute your script like this: ./my_script.sh 1 2 hello world

3. Change the script arguments so that the result of the last operation (-eq)
   would be 0.

4. Run the script again with new arguments.


SELF-REVIEW:
------------

- First script run results:

  ./my_script.sh
  1 2 hello world
  4
  2 world
  1

- Second script run results contain 0 as the last output.


HELPFUL MATERIALS:
------------------

https://tldp.org/LDP/abs/html/internalvariables.html
https://devhints.io/bash

