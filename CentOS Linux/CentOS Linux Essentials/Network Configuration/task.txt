NETWORK CONFIGURATION - TASK
============================

TASKS:
------

1. Setup 2 VMs: VM1 and VM2

2. Install and start httpd service on VM1.

3. Create file /var/www/html/index.html with the following content on VM1:
   Hello

4. Configure VM2 to resolve name myserver.local to IP address of VM1.

5. Using command curl -L myserver.local verify that you can access httpd server running on VM1 from VM2.


SELF-REVIEW:
------------

command curl -L myserver.local returns the following response:

Hello


HELPFUL MATERIALS:
------------------

https://linuxconfig.org/bash-scripting-tutorial-for-beginners
https://www.tecmint.com/ip-command-examples/
https://www.tecmint.com/setup-local-dns-using-etc-hosts-file-in-linux/
