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

+----------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                   |
|                                                          |
|  Show: VirtualBox/VMware with both VMs visible           |
|  Expected: VM1 and VM2 in the VM list                    |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                   |
|                                                          |
|  Show: Network settings for VM1                          |
|  Expected: Adapter type (Bridged/Internal/Host-Only)     |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                   |
|                                                          |
|  Show: Network settings for VM2                          |
|  Expected: Same network type as VM1                      |
+----------------------------------------------------------+

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

+----------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                   |
|                                                          |
|  Show: "ip addr show" output on VM1                      |
|  Expected: IP address visible (e.g., 192.168.1.100)      |
|            Look for inet under eth0/enp0s3               |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                   |
|                                                          |
|  Show: "ip addr show" output on VM2                      |
|  Expected: IP address visible (e.g., 192.168.1.101)      |
+----------------------------------------------------------+

Test connectivity between VMs:

From VM2, ping VM1:
```bash
ping -c 3 <VM1_IP_ADDRESS>
# Example: ping -c 3 192.168.1.100
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                   |
|                                                          |
|  Show: Ping from VM2 to VM1                              |
|  Expected: Successful ping replies (0% packet loss)      |
+----------------------------------------------------------+


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

+----------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                   |
|                                                          |
|  Show: yum install httpd -y on VM1                       |
|  Expected: Installation complete message                 |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                   |
|                                                          |
|  Show: systemctl status httpd on VM1                     |
|  Expected: Active: active (running) status               |
+----------------------------------------------------------+

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

+----------------------------------------------------------+
|                    >>> SCREENSHOT 9 <<<                   |
|                                                          |
|  Show: Firewall configuration or disable                 |
|  Expected: HTTP service added or firewall stopped        |
+----------------------------------------------------------+


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

+----------------------------------------------------------+
|                    >>> SCREENSHOT 10 <<<                  |
|                                                          |
|  Show: Creating index.html file on VM1                   |
|  Expected: echo or vim command execution                 |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 11 <<<                  |
|                                                          |
|  Show: cat /var/www/html/index.html output               |
|  Expected: "Hello" displayed                             |
+----------------------------------------------------------+

Test locally on VM1:
```bash
curl http://localhost
```

Expected output:
```
Hello
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 12 <<<                  |
|                                                          |
|  Show: curl http://localhost on VM1                      |
|  Expected: "Hello" response                              |
+----------------------------------------------------------+


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

+----------------------------------------------------------+
|                    >>> SCREENSHOT 13 <<<                  |
|                                                          |
|  Show: Editing /etc/hosts on VM2                         |
|  Expected: vim or nano with the new entry                |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 14 <<<                  |
|                                                          |
|  Show: cat /etc/hosts output on VM2                      |
|  Expected: myserver.local entry visible with VM1's IP    |
+----------------------------------------------------------+

Test name resolution:
```bash
# Using ping
ping -c 3 myserver.local

# Using getent
getent hosts myserver.local
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 15 <<<                  |
|                                                          |
|  Show: ping myserver.local from VM2                      |
|  Expected: Ping resolves to VM1's IP and succeeds        |
+----------------------------------------------------------+


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

+----------------------------------------------------------+
|                    >>> SCREENSHOT 16 <<<                  |
|                                                          |
|  Show: curl -L myserver.local on VM2                     |
|  Expected: "Hello" response from VM1's httpd server      |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 17 <<<                  |
|                                                          |
|  Show: curl -v myserver.local (verbose) on VM2           |
|  Expected: Connection details and "Hello" response       |
+----------------------------------------------------------+


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


## SELF-REVIEW CHECKLIST

- [ ] Both VMs (VM1 and VM2) are running
- [ ] Both VMs are on the same network and can ping each other
- [ ] VM1 IP address noted (e.g., 192.168.1.100)
- [ ] httpd installed on VM1
- [ ] httpd service started and running on VM1
- [ ] Firewall configured to allow HTTP on VM1
- [ ] /var/www/html/index.html created with content "Hello"
- [ ] curl http://localhost on VM1 returns "Hello"
- [ ] /etc/hosts on VM2 contains: <VM1_IP> myserver.local
- [ ] ping myserver.local from VM2 succeeds
- [ ] curl -L myserver.local from VM2 returns "Hello"
