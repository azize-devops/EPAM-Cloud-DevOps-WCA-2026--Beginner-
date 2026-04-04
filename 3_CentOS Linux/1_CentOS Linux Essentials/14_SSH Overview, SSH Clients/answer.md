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

![Both VMs listed](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/14_SSH%20Overview,%20SSH%20Clients/VirtualBox-VMware%20with%20both%20VMs%20listed.png)

Get IP addresses on both machines:

```bash
ip addr show
# or
hostname -I
```

Note: Replace Name_Surname with your actual name (e.g., John_Doe)

![IP address VM1](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/14_SSH%20Overview,%20SSH%20Clients/IP%20addresses%20of%20both%20VM1.png)

![IP address VM2](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/14_SSH%20Overview,%20SSH%20Clients/IP%20addresses%20of%20both%20VM2.png)


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

![useradd and id command on VM2](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/14_SSH%20Overview,%20SSH%20Clients/useradd%20com%20-%20id%20Name_Surname%20and%20ls%20-home-Name_Surname%20on%20VM2.png)


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

![systemctl status sshd on VM1](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/14_SSH%20Overview,%20SSH%20Clients/systemctl%20status%20sshd%20on%20VM1.png)

![systemctl status sshd on VM2](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/14_SSH%20Overview,%20SSH%20Clients/systemctl%20status%20sshd%20on%20VM2.png)


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

![ping from VM1 to VM2](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/14_SSH%20Overview,%20SSH%20Clients/ping%20command%20from%20VM1%20to%20VM2.png)

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

![ssh-keygen on VM1](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/14_SSH%20Overview,%20SSH%20Clients/ssh-keygen%20command%20execution%20on%20VM1.png)

### Step 2: View the generated keys

```bash
# List the keys
ls -la ~/.ssh/

# View public key
cat ~/.ssh/id_rsa_namesurname.pub
```

![ls and cat public key](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/14_SSH%20Overview,%20SSH%20Clients/ls%20~-.ssh-%20and%20cat%20public%20key.png)

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

![ssh-copy-id execution](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/14_SSH%20Overview,%20SSH%20Clients/ssh-copy-id%20command%20execution.png)

### Step 4: Verify authorized_keys on VM2

```bash
# On VM2, as Name_Surname or root
cat /home/Name_Surname/.ssh/authorized_keys
```

![authorized_keys on VM2](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/14_SSH%20Overview,%20SSH%20Clients/authorized_keys%20content%20on%20VM2.png)


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

![SSH connection from VM1 to VM2](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/14_SSH%20Overview,%20SSH%20Clients/SSH%20connection%20from%20VM1%20to%20VM2.png)

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

![Creating and viewing SSH config](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/14_SSH%20Overview,%20SSH%20Clients/Creating%20and%20cat%20%20~-.ssh-config%20file.png)

Now test the simplified connection:

```bash
# Instead of: ssh -i ~/.ssh/id_rsa_namesurname Name_Surname@192.168.1.101
# Simply use:
ssh VM2
```

![ssh VM2 using config](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/14_SSH%20Overview,%20SSH%20Clients/ssh%20VM2%20command%20(using%20config).png)


## TASK 8: Copy file from VM1 to VM2 using scp

### Step 1: Create a test file on VM1

```bash
echo "This is a test file from VM1" > ./testfile.txt
cat ./testfile.txt
```

![Creating test file on VM1](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/14_SSH%20Overview,%20SSH%20Clients/Creating%20test%20file%20on%20VM1.png)

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

![scp command execution](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/14_SSH%20Overview,%20SSH%20Clients/scp%20command%20execution.png)

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

![File verification on VM2](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/14_SSH%20Overview,%20SSH%20Clients/File%20verification%20on%20VM2.png)


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


