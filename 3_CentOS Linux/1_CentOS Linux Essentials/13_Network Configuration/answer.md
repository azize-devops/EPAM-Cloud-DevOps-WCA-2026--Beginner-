# NETWORK CONFIGURATION - ANSWER

This guide covers basic network configuration including setting up communication
between two VMs, configuring local DNS resolution using /etc/hosts, and testing
connectivity with curl.


## TASK 1: Setup 2 VMs: VM1 and VM2

Create two CentOS virtual machines in your hypervisor (VirtualBox, VMware, etc.)

VM Configuration Requirements:

VM1 (Web Server):
- Name: VM1 or CentOS-VM1
- Network: Bridged Adapter or Internal Network
- Note the IP address after boot

VM2 (Client):
- Name: VM2 or CentOS-VM2
- Network: Same network as VM1 (Bridged or Internal Network)
- Must be able to reach VM1

Network Setup Options:

Option A - Bridged Adapter:
- Both VMs get IP from your router/DHCP
- VMs can communicate with each other and host

Option B - Internal Network:
- Create internal network named "intnet"
- Assign static IPs manually
- VMs can only communicate with each other

Option C - Host-Only Adapter:
- VMs can communicate with each other and host
- No internet access

**VirtualBox Adapter / Linux Interface Mapping:**
| VirtualBox | Linux Interface |
|------------|-----------------|
| Adapter 1  | enp0s3          |
| Adapter 2  | enp0s8          |
| Adapter 3  | enp0s9          |

![Both VMs running](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/13_Network%20Configuration/Both%20VMs%20(VM1%20and%20VM2)%20are%20running.png)

![Network settings for VM1](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/13_Network%20Configuration/Network%20settings%20for%20VM1.png)

![Network settings for VM2](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/13_Network%20Configuration/Network%20settings%20for%20VM2.png)

After booting both VMs, get IP addresses:

On VM1:
```bash
ip addr show
# or
ip a
```

On VM2:
```bash
ip addr show
# or
ip a
```

Note the IP addresses (e.g., VM1: 192.168.1.100, VM2: 192.168.1.101)

![ip addr show on VM1](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/13_Network%20Configuration/-ip%20addr%20show-%20output%20on%20VM1.png)

![ip addr show on VM2](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/13_Network%20Configuration/-ip%20addr%20show-%20output%20on%20VM2.png)

Test connectivity between VMs:

From VM2, ping VM1:
```bash
ping -c 3 <VM1_IP_ADDRESS>
# Example: ping -c 3 192.168.1.100
```

![Ping from VM2 to VM1](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/13_Network%20Configuration/Ping%20from%20VM2%20to%20VM1.png)


## TASK 2: Install and start httpd service on VM1

On VM1, install and start Apache HTTP Server:

```bash
# Install httpd
sudo yum install httpd -y

# Start httpd service
sudo systemctl start httpd

# Enable httpd to start on boot (optional)
sudo systemctl enable httpd

# Verify httpd is running
sudo systemctl status httpd
```

Expected status output:
```
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; vendor preset: disabled)
   Active: active (running) since ...
```

![yum install httpd on VM1](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/13_Network%20Configuration/yum%20install%20httpd%20-y%20on%20VM1.png)

![systemctl status httpd on VM1](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/13_Network%20Configuration/systemctl%20status%20httpd%20on%20VM1.png)

Configure firewall to allow HTTP traffic:

```bash
# Check if firewalld is running
sudo systemctl status firewalld

# If running, allow HTTP service
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --reload

# Verify
sudo firewall-cmd --list-all
```

Alternative - Disable firewall (for testing only):
```bash
sudo systemctl stop firewalld
sudo systemctl disable firewalld
```

![Firewall status](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/13_Network%20Configuration/Firewall%20status.png)

![Firewall configuration](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/13_Network%20Configuration/Firewall%20configuration.png)


## TASK 3: Create file /var/www/html/index.html on VM1

Create the web page content on VM1:

Method 1 - Using echo:
```bash
echo "Hello" | sudo tee /var/www/html/index.html
```

Method 2 - Using vim/nano:
```bash
sudo vim /var/www/html/index.html
# Press 'i' for insert mode, type: Hello
# Press Esc, then type :wq to save and quit

# Or using nano:
sudo nano /var/www/html/index.html
# Type: Hello
# Press Ctrl+O to save, Ctrl+X to exit
```

Method 3 - Using cat with heredoc:
```bash
sudo cat > /var/www/html/index.html << EOF
Hello
EOF
```

Verify the file content:
```bash
cat /var/www/html/index.html
```

Expected output:
```
Hello
```

![Creating index.html on VM1](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/13_Network%20Configuration/Creating%20index.html%20file%20on%20VM1.png)

Test locally on VM1:
```bash
curl http://localhost
```

Expected output:
```
Hello
```

![curl localhost on VM1](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/13_Network%20Configuration/curl%20http---localhost%20on%20VM1.png)


## TASK 4: Configure VM2 to resolve myserver.local to VM1's IP

On VM2, edit the /etc/hosts file to add local DNS resolution:

```bash
sudo vim /etc/hosts

# Or using nano:
sudo nano /etc/hosts
```

Add the following line (replace IP with VM1's actual IP):
```
192.168.1.100   myserver.local
```

The file should look like:
```
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.1.100   myserver.local
```

Alternative using echo:
```bash
echo "192.168.1.100   myserver.local" | sudo tee -a /etc/hosts
```

Verify the entry:
```bash
cat /etc/hosts

# Or grep for the entry
grep myserver /etc/hosts
```

![Editing /etc/hosts on VM2](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/13_Network%20Configuration/Editing%20-etc-hosts%20on%20VM2.png)

Test name resolution:
```bash
# Using ping
ping -c 3 myserver.local

# Using getent
getent hosts myserver.local
```

![ping myserver.local from VM2](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/13_Network%20Configuration/ping%20myserver.local%20from%20VM2.png)


## TASK 5: Verify access using curl from VM2

On VM2, use curl to access the web server on VM1:

```bash
curl -L myserver.local
```

The -L flag follows redirects if any.

Expected output:
```
Hello
```

Alternative curl commands:
```bash
# With verbose output
curl -v myserver.local

# Show response headers
curl -I myserver.local

# With explicit port
curl -L myserver.local:80
```

![curl myserver.local on VM2](https://raw.githubusercontent.com/azize-devops/EPAM-Cloud-DevOps-WCA-2026--Beginner-/main/docs/images/3_CentOS%20Linux/1_CentOS%20Linux%20Essentials/13_Network%20Configuration/curl%20-L,-v%20myserver.local%20on%20VM2.png)


## NETWORK COMMANDS REFERENCE

| Command                        | Description                                 |
|--------------------------------|---------------------------------------------|
| ip addr show                   | Show all network interfaces and IPs         |
| ip a                           | Short form of ip addr show                  |
| ip link show                   | Show network interface status               |
| ip route show                  | Show routing table                          |
| hostname -I                    | Show all IP addresses                       |
| ping <host>                    | Test connectivity to host                   |
| ping -c 3 <host>               | Send 3 ping packets                         |
| curl <url>                     | Fetch content from URL                      |
| curl -L <url>                  | Follow redirects                            |
| curl -v <url>                  | Verbose output                              |
| curl -I <url>                  | Show response headers only                  |
| getent hosts <hostname>        | Query name resolution                       |
| nslookup <hostname>            | DNS lookup                                  |
| dig <hostname>                 | Detailed DNS lookup                         |
| ss -tlnp                       | Show listening TCP ports                    |
| netstat -tlnp                  | Show listening TCP ports (legacy)           |


## /etc/hosts FILE FORMAT

The /etc/hosts file provides local hostname resolution before DNS is queried.

Format:
```
<IP_ADDRESS>    <hostname> [alias1] [alias2] ...
```

Examples:
```
127.0.0.1       localhost
192.168.1.100   myserver.local myserver
192.168.1.101   dbserver.local db
10.0.0.50       webserver www
```

Important notes:
- Changes take effect immediately (no restart needed)
- /etc/hosts is checked before DNS
- Useful for local development and testing
- Each entry should be on a separate line
- Comments start with #


## FIREWALL COMMANDS

| Command                                      | Description                    |
|----------------------------------------------|--------------------------------|
| systemctl status firewalld                   | Check firewall status          |
| firewall-cmd --list-all                      | List all firewall rules        |
| firewall-cmd --add-service=http              | Allow HTTP (temporary)         |
| firewall-cmd --permanent --add-service=http  | Allow HTTP (permanent)         |
| firewall-cmd --reload                        | Reload firewall rules          |
| firewall-cmd --add-port=80/tcp               | Allow port 80 TCP              |
| systemctl stop firewalld                     | Stop firewall                  |
| systemctl disable firewalld                  | Disable firewall on boot       |


## TROUBLESHOOTING

**Problem: Cannot ping VM1 from VM2**

Solutions:
- Verify both VMs are on the same network
- Check firewall settings on VM1
- Verify IP addresses are correct
- Try: `sudo systemctl stop firewalld` (on VM1)

**Problem: curl: (7) Failed to connect**

Solutions:
- Verify httpd is running: `systemctl status httpd`
- Check firewall allows HTTP: `firewall-cmd --list-all`
- Test locally first: `curl http://localhost` (on VM1)

**Problem: curl: (6) Could not resolve host**

Solutions:
- Check /etc/hosts entry on VM2
- Verify correct IP address in /etc/hosts
- Run: `getent hosts myserver.local`

**Problem: Name resolution works but curl fails**

Solutions:
- httpd might not be running
- Firewall blocking port 80
- Check SELinux: `getenforce` (try: `setenforce 0`)

**Problem: VMs cannot communicate**

Solutions:
- Ensure same network adapter type
- For Internal Network: set static IPs manually
- For Bridged: check router DHCP


