# CASE STATEMENT - TASK

GOALS:
------

- Learn to use case statement


TASKS:
------

1. Create a script called my_service.sh which does the following:
   - Accepts start, stop, restart arguments:
     - start: outputs "Service started" message and sleeps for 9999
     - stop: gets the PID of my_service.sh process and terminates it,
             then outputs "Service stopped" message
     - restart: stops and then starts the service
     - any other argument: outputs script usage information

2. Add your service to PATH.

3. Run your script with start and restart arguments in background.

4. Run your script with stop and help arguments.


SELF-REVIEW:
------------

- Script executions return the following:

  start:
  [1] 45043
  Service is started

  stop:
  Service is stopped
  [2]  + 44996 terminated  ./my_script.sh start

  restart:
  [1] 45863
  Service is stopped
  Service is started

  help:
  usage: my_service.sh [start|stop|restart]


HELPFUL MATERIALS:
------------------

https://linuxize.com/post/bash-case-statement/
https://devhints.io/bash

