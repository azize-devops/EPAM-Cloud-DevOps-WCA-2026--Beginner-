# SSH OVERVIEW, SSH CLIENTS - TASK

TASKS:
------

1. Setup 2 VMs: VM1 and VM2.

2. Create user Name_Surname with home directory on VM2.

3. Verify that sshd service and ssh clients are present on both machines.

4. Verify that you are able to ping VM2 from VM1.

5. Configure ssh keys for user Name_Surname on VM2.

6. From VM1 ssh to VM2 under user Name_Surname using ssh keys (no password).

7. Create ssh config file on VM1 that will configure VM2 host with user Name_Surname and its ssh key.

8. Copy a file from VM1 to VM2 Name_Surname user's home directory using scp ./file VM2:~ command.


SELF-REVIEW:
------------

- You are able to ssh into VM2 from VM1.

- Copied file is present on VM2 in user's Name_Surname home directory.


HELPFUL MATERIALS:
------------------

https://linuxconfig.org/bash-scripting-tutorial-for-beginners
https://www.tecmint.com/ssh-passwordless-login-using-ssh-keygen-in-5-easy-steps/
https://linuxize.com/post/using-the-ssh-config-file/

