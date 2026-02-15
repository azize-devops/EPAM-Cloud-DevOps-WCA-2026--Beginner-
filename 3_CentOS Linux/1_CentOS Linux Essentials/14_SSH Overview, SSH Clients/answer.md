# SSH OVERVIEW, SSH CLIENTS - ANSWER

This guide covers SSH (Secure Shell) configuration including key-based authentication,
SSH config file setup, and secure file transfer using scp.


## TASK 1: Setup 2 VMs: VM1 and VM2

Create two CentOS virtual machines in your hypervisor.

VM Configuration:

VM1 (SSH Client):
- Name: VM1 or CentOS-VM1
- Network: Bridged Adapter or Internal Network
- Role: Will initiate SSH connections

VM2 (SSH Server):
- Name: VM2 or CentOS-VM2
- Network: Same network as VM1
- Role: Will accept SSH connections

Ensure both VMs are on the same network and can communicate.

+----------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                   |
|                                                          |
|  Show: VirtualBox/VMware with both VMs listed            |
|  Expected: VM1 and VM2 visible in VM manager             |
+----------------------------------------------------------+

Get IP addresses on both machines:

```bash
ip addr show
# or
hostname -I
```

Note: Replace Name_Surname with your actual name (e.g., John_Doe)

+----------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                   |
|                                                          |
|  Show: IP addresses of both VMs                          |
|  Expected: VM1 IP (e.g., 192.168.1.100)                  |
|            VM2 IP (e.g., 192.168.1.101)                  |
+----------------------------------------------------------+


## TASK 2: Create user Name_Surname with home directory on VM2

On VM2, create a new user with home directory:

```bash
# Create user with home directory
sudo useradd -m Name_Surname

# Set password for the user
sudo passwd Name_Surname
# Enter and confirm password when prompted

# Verify user was created
id Name_Surname

# Verify home directory exists
ls -la /home/Name_Surname
```

Expected output from id command:
```
uid=1001(Name_Surname) gid=1001(Name_Surname) groups=1001(Name_Surname)
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                   |
|                                                          |
|  Show: useradd command on VM2                            |
|  Expected: User creation command executed                |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                   |
|                                                          |
|  Show: id Name_Surname and ls /home/Name_Surname         |
|  Expected: User ID info and home directory listing       |
+----------------------------------------------------------+


## TASK 3: Verify sshd service and ssh clients on both machines

Check SSH server (sshd) status on both VMs:

```bash
# Check if sshd is installed
rpm -qa | grep openssh-server

# Check sshd service status
sudo systemctl status sshd

# If not running, start it
sudo systemctl start sshd

# Enable sshd to start on boot
sudo systemctl enable sshd
```

Expected output:
```
● sshd.service - OpenSSH server daemon
   Loaded: loaded (/usr/lib/systemd/system/sshd.service; enabled; vendor preset: enabled)
   Active: active (running) since ...
```

Check SSH client on both VMs:

```bash
# Check if ssh client is installed
rpm -qa | grep openssh-clients

# Verify ssh command is available
which ssh

# Check ssh version
ssh -V
```

Expected output:
```
OpenSSH_7.4p1, OpenSSL 1.0.2k-fips  26 Jan 2017
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                   |
|                                                          |
|  Show: systemctl status sshd on VM1                      |
|  Expected: Active: active (running) status               |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                   |
|                                                          |
|  Show: systemctl status sshd on VM2                      |
|  Expected: Active: active (running) status               |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                   |
|                                                          |
|  Show: ssh -V on both machines                           |
|  Expected: OpenSSH version information                   |
+----------------------------------------------------------+


## TASK 4: Verify ping connectivity from VM1 to VM2

On VM1, ping VM2 to verify network connectivity:

```bash
ping -c 4 <VM2_IP_ADDRESS>
# Example: ping -c 4 192.168.1.101
```

Expected output:
```
PING 192.168.1.101 (192.168.1.101) 56(84) bytes of data.
64 bytes from 192.168.1.101: icmp_seq=1 ttl=64 time=0.523 ms
64 bytes from 192.168.1.101: icmp_seq=2 ttl=64 time=0.412 ms
64 bytes from 192.168.1.101: icmp_seq=3 ttl=64 time=0.398 ms
64 bytes from 192.168.1.101: icmp_seq=4 ttl=64 time=0.445 ms

--- 192.168.1.101 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3002ms
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                   |
|                                                          |
|  Show: ping command from VM1 to VM2                      |
|  Expected: Successful ping with 0% packet loss           |
+----------------------------------------------------------+

If ping fails, check:
- Firewall settings on both VMs
- Network adapter configuration
- IP addresses are on the same subnet


## TASK 5: Configure SSH keys for user Name_Surname on VM2

This task involves generating SSH key pair on VM1 and copying the public key to VM2.

### Step 1: Generate SSH key pair on VM1

```bash
# Generate RSA key pair (on VM1)
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa_namesurname

# When prompted for passphrase, press Enter for no passphrase
# (or set a passphrase for extra security)
```

Expected output:
```
Generating public/private rsa key pair.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/user/.ssh/id_rsa_namesurname.
Your public key has been saved in /home/user/.ssh/id_rsa_namesurname.pub.
The key fingerprint is:
SHA256:... user@VM1
The key's randomart image is:
+---[RSA 4096]----+
|                 |
...
+----[SHA256]-----+
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 9 <<<                   |
|                                                          |
|  Show: ssh-keygen command execution on VM1               |
|  Expected: Key generation with fingerprint displayed     |
+----------------------------------------------------------+

### Step 2: View the generated keys

```bash
# List the keys
ls -la ~/.ssh/

# View public key
cat ~/.ssh/id_rsa_namesurname.pub
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 10 <<<                  |
|                                                          |
|  Show: ls ~/.ssh/ and cat public key                     |
|  Expected: id_rsa_namesurname and id_rsa_namesurname.pub |
+----------------------------------------------------------+

### Step 3: Copy public key to VM2

Method 1 - Using ssh-copy-id (recommended):
```bash
ssh-copy-id -i ~/.ssh/id_rsa_namesurname.pub Name_Surname@<VM2_IP>
# Example: ssh-copy-id -i ~/.ssh/id_rsa_namesurname.pub Name_Surname@192.168.1.101
# Enter Name_Surname's password when prompted
```

Method 2 - Manual copy:
```bash
# Copy public key content
cat ~/.ssh/id_rsa_namesurname.pub

# On VM2, as Name_Surname user:
mkdir -p ~/.ssh
chmod 700 ~/.ssh
# Paste the public key into authorized_keys:
echo "ssh-rsa AAAA..." >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 11 <<<                  |
|                                                          |
|  Show: ssh-copy-id command execution                     |
|  Expected: "Number of key(s) added: 1" message           |
+----------------------------------------------------------+

### Step 4: Verify authorized_keys on VM2

```bash
# On VM2, as Name_Surname or root
cat /home/Name_Surname/.ssh/authorized_keys
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 12 <<<                  |
|                                                          |
|  Show: authorized_keys content on VM2                    |
|  Expected: Public key from VM1 visible                   |
+----------------------------------------------------------+


## TASK 6: SSH to VM2 using SSH keys (no password)

From VM1, connect to VM2 using the SSH key:

```bash
ssh -i ~/.ssh/id_rsa_namesurname Name_Surname@<VM2_IP>
# Example: ssh -i ~/.ssh/id_rsa_namesurname Name_Surname@192.168.1.101
```

You should be logged in without entering a password!

Expected output:
```
Last login: Wed Dec 23 12:00:00 2020 from 192.168.1.100
[Name_Surname@VM2 ~]$
```

Verify you are on VM2:
```bash
hostname
whoami
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 13 <<<                  |
|                                                          |
|  Show: SSH connection from VM1 to VM2                    |
|  Expected: Successful login without password prompt      |
|            Prompt shows Name_Surname@VM2                 |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 14 <<<                  |
|                                                          |
|  Show: hostname and whoami on VM2                        |
|  Expected: VM2 hostname and Name_Surname user            |
+----------------------------------------------------------+

Exit from VM2:
```bash
exit
```


## TASK 7: Create SSH config file on VM1

Create/edit SSH config file on VM1 to simplify connections:

```bash
vim ~/.ssh/config
# or
nano ~/.ssh/config
```

Add the following content:

```
Host VM2
    HostName 192.168.1.101
    User Name_Surname
    IdentityFile ~/.ssh/id_rsa_namesurname
    Port 22
```

Save and set correct permissions:
```bash
chmod 600 ~/.ssh/config
```

Verify the config:
```bash
cat ~/.ssh/config
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 15 <<<                  |
|                                                          |
|  Show: Creating ~/.ssh/config file                       |
|  Expected: Config content with Host, HostName, User,     |
|            IdentityFile settings                         |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 16 <<<                  |
|                                                          |
|  Show: cat ~/.ssh/config output                          |
|  Expected: Complete SSH config displayed                 |
+----------------------------------------------------------+

Now test the simplified connection:

```bash
# Instead of: ssh -i ~/.ssh/id_rsa_namesurname Name_Surname@192.168.1.101
# Simply use:
ssh VM2
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 17 <<<                  |
|                                                          |
|  Show: ssh VM2 command (using config)                    |
|  Expected: Successful login using simplified command     |
+----------------------------------------------------------+


## TASK 8: Copy file from VM1 to VM2 using scp

### Step 1: Create a test file on VM1

```bash
echo "This is a test file from VM1" > ./testfile.txt
cat ./testfile.txt
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 18 <<<                  |
|                                                          |
|  Show: Creating test file on VM1                         |
|  Expected: File created with content                     |
+----------------------------------------------------------+

### Step 2: Copy the file to VM2 using scp

Using SSH config (simplified):
```bash
scp ./testfile.txt VM2:~
```

Without SSH config:
```bash
scp -i ~/.ssh/id_rsa_namesurname ./testfile.txt Name_Surname@192.168.1.101:~
```

Expected output:
```
testfile.txt                                  100%   30     0.0KB/s   00:00
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 19 <<<                  |
|                                                          |
|  Show: scp command execution                             |
|  Expected: File transfer with 100% progress              |
+----------------------------------------------------------+

### Step 3: Verify file on VM2

```bash
# SSH to VM2
ssh VM2

# Check file in home directory
ls -la ~
cat ~/testfile.txt
```

Expected output:
```
This is a test file from VM1
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 20 <<<                  |
|                                                          |
|  Show: File verification on VM2                          |
|  Expected: testfile.txt present in Name_Surname home     |
|            with correct content                          |
+----------------------------------------------------------+


## SSH COMMANDS REFERENCE

| Command                                    | Description                    |
|--------------------------------------------|--------------------------------|
| ssh user@host                              | Connect to remote host         |
| ssh -i keyfile user@host                   | Connect using specific key     |
| ssh -p port user@host                      | Connect to specific port       |
| ssh -v user@host                           | Verbose mode (debugging)       |
| ssh-keygen -t rsa -b 4096                  | Generate RSA key pair          |
| ssh-keygen -t ed25519                      | Generate Ed25519 key pair      |
| ssh-copy-id user@host                      | Copy public key to remote      |
| ssh-copy-id -i key.pub user@host           | Copy specific key to remote    |
| scp file user@host:path                    | Copy file to remote            |
| scp user@host:file localpath               | Copy file from remote          |
| scp -r dir user@host:path                  | Copy directory recursively     |
| sftp user@host                             | Interactive secure FTP         |


## SSH CONFIG FILE OPTIONS

~/.ssh/config file options:

```
Host <alias>                    # Short name for the connection
    HostName <ip_or_hostname>   # Actual IP or hostname
    User <username>             # Username to connect as
    Port <port_number>          # SSH port (default 22)
    IdentityFile <path_to_key>  # Private key file path
    ForwardAgent yes            # Forward SSH agent
    StrictHostKeyChecking no    # Skip host key verification
    UserKnownHostsFile /dev/null # Don't save known hosts
```

Example with multiple hosts:

```
Host VM2
    HostName 192.168.1.101
    User Name_Surname
    IdentityFile ~/.ssh/id_rsa_namesurname

Host production
    HostName prod.example.com
    User admin
    Port 2222
    IdentityFile ~/.ssh/prod_key

Host *
    ServerAliveInterval 60
    ServerAliveCountMax 3
```


## SSH KEY TYPES

| Key Type    | Command                          | Notes                        |
|-------------|----------------------------------|------------------------------|
| RSA         | ssh-keygen -t rsa -b 4096        | Most compatible, use 4096+   |
| Ed25519     | ssh-keygen -t ed25519            | Modern, faster, recommended  |
| ECDSA       | ssh-keygen -t ecdsa -b 521       | Good security, less common   |
| DSA         | ssh-keygen -t dsa                | Deprecated, avoid            |


## IMPORTANT SSH FILES

| File                        | Location           | Purpose                     |
|-----------------------------|--------------------|-----------------------------|
| id_rsa                      | ~/.ssh/            | Private key (keep secret!)  |
| id_rsa.pub                  | ~/.ssh/            | Public key (share this)     |
| authorized_keys             | ~/.ssh/            | Allowed public keys         |
| known_hosts                 | ~/.ssh/            | Trusted host fingerprints   |
| config                      | ~/.ssh/            | SSH client configuration    |
| sshd_config                 | /etc/ssh/          | SSH server configuration    |

Permissions:
- ~/.ssh directory: 700 (drwx------)
- Private keys: 600 (-rw-------)
- Public keys: 644 (-rw-r--r--)
- authorized_keys: 600 (-rw-------)
- config: 600 (-rw-------)


## TROUBLESHOOTING

**Problem: Permission denied (publickey)**

Solutions:
- Check key permissions: `chmod 600 ~/.ssh/id_rsa*`
- Verify public key is in authorized_keys
- Check ~/.ssh directory permissions: `chmod 700 ~/.ssh`
- Use -v flag for debugging: `ssh -v user@host`

**Problem: Host key verification failed**

Solutions:
- Remove old entry: `ssh-keygen -R <hostname>`
- Accept new key when prompted

**Problem: Connection refused**

Solutions:
- Verify sshd is running: `systemctl status sshd`
- Check firewall: `firewall-cmd --list-all`
- Verify port 22 is open: `ss -tlnp | grep :22`

**Problem: ssh-copy-id fails**

Solutions:
- Ensure password authentication is enabled on server
- Try manual key copy method
- Check sshd_config allows password auth temporarily

**Problem: scp fails with "No such file or directory"**

Solutions:
- Verify source file exists
- Check destination path is correct
- Ensure target directory is writable


## SELF-REVIEW CHECKLIST

- [ ] Both VMs (VM1 and VM2) are running and accessible
- [ ] User Name_Surname created on VM2 with home directory
- [ ] sshd service running on both VMs
- [ ] ssh client installed on both VMs (ssh -V works)
- [ ] Ping from VM1 to VM2 successful
- [ ] SSH key pair generated on VM1
- [ ] Public key copied to VM2's authorized_keys
- [ ] SSH from VM1 to VM2 works without password
- [ ] SSH config file created on VM1 (~/.ssh/config)
- [ ] ssh VM2 command works (using config file)
- [ ] Test file created on VM1
- [ ] scp ./file VM2:~ copies file successfully
- [ ] File present in Name_Surname's home directory on VM2
