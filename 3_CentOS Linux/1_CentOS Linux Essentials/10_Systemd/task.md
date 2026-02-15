# SYSTEMD - TASK

## TASKS

1. Open terminal of your choosing.

2. List unit files available in your system.

3. Install httpd using command `yum install httpd`.

4. Start httpd service.

5. Check httpd service status.

6. Restart httpd service.

7. Enable httpd service. Verify that it is enabled.

8. Create a script in your user's home directory named script.sh with the following content:

   ```bash
   #!/bin/bash

   sleep 1000
   ```

9. Make the script executable.

10. Create unit file called test.service that will be used for starting your script and place it where systemd will be able to use it.

11. Start test service.

12. Verify test service status.

## SELF-REVIEW

After starting and enabling httpd service its status looks like this:

```
httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; vendor preset: disabled)
   Active: active (running) since Tue 2020-12-22 12:43:16 UTC; 26min ago
```

test service status looks like this:

```
test.service - 'test'
   Loaded: loaded (/etc/systemd/system/test.service; static; vendor preset: disabled)
   Active: active (running) since Tue 2020-12-22 13:12:37 UTC; 3s ago
 Main PID: 226 (script.sh)
   CGroup: /docker/.../system.slice/test.service
           ├─226 /bin/bash /root/script.sh
           └─227 sleep 1000

Dec 22 13:12:37 hostname systemd[1]: Started 'test'.
```

## HELPFUL MATERIALS

- [Bash Scripting Tutorial for Beginners](https://linuxconfig.org/bash-scripting-tutorial-for-beginners)
- [Managing Services with Systemd](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/chap-managing_services_with_systemd)
