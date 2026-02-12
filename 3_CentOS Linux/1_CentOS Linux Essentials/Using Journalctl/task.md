# USING JOURNALCTL - TASK

TASKS:
------

1. Open terminal of your choosing.

2. Install nginx service using commands:
   yum install epel-release
   yum install nginx

3. Start nginx service.

4. Stop nginx service.

5. Check nginx service logs.

6. Introduce error to nginx startup by moving nginx config file using command:
   mv /etc/nginx/nginx.conf ./

7. Attempt to start nginx service.

8. View only error entries in nginx logs.


SELF-REVIEW:
------------

- You are able to view nginx logs.

- After moving config file nginx does not start and you are able to view the error message in the logs.


HELPFUL MATERIALS:
------------------

https://linuxconfig.org/bash-scripting-tutorial-for-beginners
https://www.linode.com/docs/guides/how-to-use-journalctl/

