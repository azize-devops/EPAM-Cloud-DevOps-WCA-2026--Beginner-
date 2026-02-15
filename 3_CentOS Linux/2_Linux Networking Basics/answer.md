# LINUX NETWORKING PRACTICAL TASK - ANSWER

This guide covers advanced Linux networking including multi-interface configuration,
DHCP server setup, routing, SSH, firewall rules, and NAT configuration.


## NETWORK ARCHITECTURE

IP Address Plan (Example):

| Network | Subnet          | Description                    |
|---------|-----------------|--------------------------------|
| Net1    | 192.168.1.0/24  | Home/Bridge network            |
| Net2    | 10.10.2.0/24    | Internal: Server_1 <-> Client_1|
| Net3    | 10.10.3.0/24    | Internal: Server_1 <-> Client_2|
| Net4    | 10.10.4.0/24    | Internal: Client_1 <-> Client_2|

IP Assignments:

| Machine   | Interface | Network | IP Address      |
|-----------|-----------|---------|-----------------|
| Server_1  | Int1      | Net1    | 192.168.1.200   |
| Server_1  | Int2      | Net2    | 10.10.2.1       |
| Server_1  | Int3      | Net3    | 10.10.3.1       |
| Client_1  | Int1      | Net2    | 10.10.2.10 (DHCP)|
| Client_1  | Int2      | Net4    | 10.10.4.10      |
| Client_1  | lo:0      | -       | 10.20.1.1 (IPaddr1)|
| Client_1  | lo:1      | -       | 10.20.2.1 (IPaddr2)|
| Client_2  | Int1      | Net3    | 10.10.3.10 (DHCP)|
| Client_2  | Int2      | Net4    | 10.10.4.20      |


## PRELIMINARY SETUP: Create Virtual Machines

Create 3 VMs in VirtualBox:

Server_1 Configuration:
- OS: CentOS 7/8
- Adapter 1: Bridged Adapter (Net1)
- Adapter 2: Internal Network, name "Net2"
- Adapter 3: Internal Network, name "Net3"

Client_1 Configuration:
- OS: Ubuntu or CentOS
- Adapter 1: Internal Network, name "Net2"
- Adapter 2: Internal Network, name "Net4"

Client_2 Configuration:
- OS: CentOS or Ubuntu (different from Client_1)
- Adapter 1: Internal Network, name "Net3"
- Adapter 2: Internal Network, name "Net4"

+----------------------------------------------------------+
|                    >>> SCREENSHOT 1 <<<                   |
|                                                          |
|  Show: VirtualBox with all 3 VMs listed                  |
|  Expected: Server_1, Client_1, Client_2 visible          |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 2 <<<                   |
|                                                          |
|  Show: Server_1 network settings (3 adapters)            |
|  Expected: Adapter1=Bridged, Adapter2=Net2,              |
|            Adapter3=Net3                                 |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 3 <<<                   |
|                                                          |
|  Show: Client_1 network settings (2 adapters)            |
|  Expected: Adapter1=Net2, Adapter2=Net4                  |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 4 <<<                   |
|                                                          |
|  Show: Client_2 network settings (2 adapters)            |
|  Expected: Adapter1=Net3, Adapter2=Net4                  |
+----------------------------------------------------------+


## TASK 1: Configure static addresses on all interfaces on Server_1

### Step 1: Identify network interfaces on Server_1

```bash
ip link show
# or
nmcli device status
```

Typical interface names:
- enp0s3 (Int1 - Bridged)
- enp0s8 (Int2 - Net2)
- enp0s9 (Int3 - Net3)

+----------------------------------------------------------+
|                    >>> SCREENSHOT 5 <<<                   |
|                                                          |
|  Show: ip link show output on Server_1                   |
|  Expected: Three network interfaces listed               |
+----------------------------------------------------------+

### Step 2: Configure Int1 (Bridged - Net1)

Method 1 - Using nmcli:

```bash
sudo nmcli con mod enp0s3 ipv4.addresses 192.168.1.200/24
sudo nmcli con mod enp0s3 ipv4.gateway 192.168.1.1
sudo nmcli con mod enp0s3 ipv4.dns "8.8.8.8"
sudo nmcli con mod enp0s3 ipv4.method manual
sudo nmcli con up enp0s3
```

Method 2 - Edit config file:

```bash
sudo vim /etc/sysconfig/network-scripts/ifcfg-enp0s3
```

Content:

```
TYPE=Ethernet
BOOTPROTO=none
NAME=enp0s3
DEVICE=enp0s3
ONBOOT=yes
IPADDR=192.168.1.200
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
DNS1=8.8.8.8
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 6 <<<                   |
|                                                          |
|  Show: ifcfg-enp0s3 configuration file                   |
|  Expected: Static IP config for Net1 (192.168.1.200)     |
+----------------------------------------------------------+

### Step 3: Configure Int2 (Internal - Net2)

```bash
sudo vim /etc/sysconfig/network-scripts/ifcfg-enp0s8
```

Content:

```
TYPE=Ethernet
BOOTPROTO=none
NAME=enp0s8
DEVICE=enp0s8
ONBOOT=yes
IPADDR=10.10.2.1
NETMASK=255.255.255.0
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 7 <<<                   |
|                                                          |
|  Show: ifcfg-enp0s8 configuration file                   |
|  Expected: Static IP config for Net2 (10.10.2.1)         |
+----------------------------------------------------------+

### Step 4: Configure Int3 (Internal - Net3)

```bash
sudo vim /etc/sysconfig/network-scripts/ifcfg-enp0s9
```

Content:

```
TYPE=Ethernet
BOOTPROTO=none
NAME=enp0s9
DEVICE=enp0s9
ONBOOT=yes
IPADDR=10.10.3.1
NETMASK=255.255.255.0
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 8 <<<                   |
|                                                          |
|  Show: ifcfg-enp0s9 configuration file                   |
|  Expected: Static IP config for Net3 (10.10.3.1)         |
+----------------------------------------------------------+

### Step 5: Restart network and verify

```bash
sudo systemctl restart network
# or
sudo nmcli networking off && sudo nmcli networking on

# Verify all interfaces
ip addr show
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 9 <<<                   |
|                                                          |
|  Show: ip addr show on Server_1 after configuration      |
|  Expected: All 3 interfaces with correct IPs             |
|            192.168.1.200, 10.10.2.1, 10.10.3.1           |
+----------------------------------------------------------+

### Step 6: Enable IP forwarding on Server_1

```bash
# Temporary
sudo sysctl -w net.ipv4.ip_forward=1

# Permanent
echo "net.ipv4.ip_forward = 1" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p

# Verify
cat /proc/sys/net/ipv4/ip_forward
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 10 <<<                  |
|                                                          |
|  Show: IP forwarding enabled                             |
|  Expected: Output shows "1" for ip_forward               |
+----------------------------------------------------------+


## TASK 2: Configure DHCP service on Server_1

### Step 1: Install DHCP server

```bash
sudo yum install dhcp -y
```

### Step 2: Configure DHCP server

```bash
sudo vim /etc/dhcp/dhcpd.conf
```

Content:

```
# DHCP Server Configuration

# Global settings
default-lease-time 600;
max-lease-time 7200;
authoritative;

# Subnet for Net2 (Client_1)
subnet 10.10.2.0 netmask 255.255.255.0 {
    range 10.10.2.10 10.10.2.50;
    option routers 10.10.2.1;
    option subnet-mask 255.255.255.0;
    option domain-name-servers 8.8.8.8, 8.8.4.4;
}

# Subnet for Net3 (Client_2)
subnet 10.10.3.0 netmask 255.255.255.0 {
    range 10.10.3.10 10.10.3.50;
    option routers 10.10.3.1;
    option subnet-mask 255.255.255.0;
    option domain-name-servers 8.8.8.8, 8.8.4.4;
}

# Subnet for Net1 (required declaration, no range)
subnet 192.168.1.0 netmask 255.255.255.0 {
}
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 11 <<<                  |
|                                                          |
|  Show: /etc/dhcp/dhcpd.conf content                      |
|  Expected: DHCP configuration for Net2 and Net3          |
+----------------------------------------------------------+

### Step 3: Start and enable DHCP service

```bash
sudo systemctl start dhcpd
sudo systemctl enable dhcpd
sudo systemctl status dhcpd
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 12 <<<                  |
|                                                          |
|  Show: systemctl status dhcpd                            |
|  Expected: Active: active (running)                      |
+----------------------------------------------------------+

### Step 4: Configure Client_1 Int1 for DHCP

On Client_1:

```bash
sudo vim /etc/sysconfig/network-scripts/ifcfg-enp0s3
# or for Ubuntu: sudo vim /etc/netplan/01-netcfg.yaml
```

CentOS content:

```
TYPE=Ethernet
BOOTPROTO=dhcp
NAME=enp0s3
DEVICE=enp0s3
ONBOOT=yes
```

```bash
# Restart network
sudo systemctl restart network

# Verify DHCP lease
ip addr show enp0s3
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 13 <<<                  |
|                                                          |
|  Show: Client_1 receiving IP via DHCP                    |
|  Expected: IP address in range 10.10.2.10-50             |
+----------------------------------------------------------+

### Step 5: Configure Client_2 Int1 for DHCP

On Client_2:

```bash
# Similar configuration as Client_1
sudo vim /etc/sysconfig/network-scripts/ifcfg-enp0s3

# Restart and verify
sudo systemctl restart network
ip addr show enp0s3
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 14 <<<                  |
|                                                          |
|  Show: Client_2 receiving IP via DHCP                    |
|  Expected: IP address in range 10.10.3.10-50             |
+----------------------------------------------------------+

### Step 6: Configure Client_1 and Client_2 Int2 (Net4) - Static

On Client_1 (Int2 for Net4):

```bash
sudo vim /etc/sysconfig/network-scripts/ifcfg-enp0s8
```

Content:

```
TYPE=Ethernet
BOOTPROTO=none
NAME=enp0s8
DEVICE=enp0s8
ONBOOT=yes
IPADDR=10.10.4.10
NETMASK=255.255.255.0
```

On Client_2 (Int2 for Net4):

```bash
sudo vim /etc/sysconfig/network-scripts/ifcfg-enp0s8
```

Content:

```
TYPE=Ethernet
BOOTPROTO=none
NAME=enp0s8
DEVICE=enp0s8
ONBOOT=yes
IPADDR=10.10.4.20
NETMASK=255.255.255.0
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 15 <<<                  |
|                                                          |
|  Show: Client_1 and Client_2 Net4 interface config       |
|  Expected: 10.10.4.10 (Client_1), 10.10.4.20 (Client_2)  |
+----------------------------------------------------------+


## TASK 3: Configure loopback IPs and routing

### Step 1: Add IP addresses to loopback interface on Client_1

```bash
# Add IPaddr1
sudo ip addr add 10.20.1.1/32 dev lo label lo:0

# Add IPaddr2
sudo ip addr add 10.20.2.1/32 dev lo label lo:1

# Verify
ip addr show lo
```

To make permanent, add to /etc/rc.local or create script:

```bash
sudo vim /etc/rc.local
# Add:
ip addr add 10.20.1.1/32 dev lo label lo:0
ip addr add 10.20.2.1/32 dev lo label lo:1
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 16 <<<                  |
|                                                          |
|  Show: ip addr show lo on Client_1                       |
|  Expected: lo:0 with 10.20.1.1, lo:1 with 10.20.2.1      |
+----------------------------------------------------------+

### Step 2: Configure routing on Client_2

Route to IPaddr1 (10.20.1.1) via Server_1:

```bash
sudo ip route add 10.20.1.1/32 via 10.10.3.1
```

Route to IPaddr2 (10.20.2.1) via Net4 (direct):

```bash
sudo ip route add 10.20.2.1/32 via 10.10.4.10
```

Verify routing table:

```bash
ip route show
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 17 <<<                  |
|                                                          |
|  Show: ip route show on Client_2                         |
|  Expected: Route to 10.20.1.1 via 10.10.3.1              |
|            Route to 10.20.2.1 via 10.10.4.10             |
+----------------------------------------------------------+

### Step 3: Configure routing on Server_1

Server_1 needs route to IPaddr1 and IPaddr2:

```bash
sudo ip route add 10.20.1.1/32 via 10.10.2.10
sudo ip route add 10.20.2.1/32 via 10.10.2.10

# Or for the whole 10.20.0.0/16 subnet
sudo ip route add 10.20.0.0/16 via 10.10.2.10
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 18 <<<                  |
|                                                          |
|  Show: ip route show on Server_1                         |
|  Expected: Routes to 10.20.x.x via Client_1              |
+----------------------------------------------------------+

### Step 4: Test with traceroute

Install traceroute:

```bash
sudo yum install traceroute -y
```

From Client_2, trace to IPaddr1 (should go via Server_1):

```bash
traceroute 10.20.1.1
```

Expected output:

```
traceroute to 10.20.1.1 (10.20.1.1), 30 hops max
 1  10.10.3.1 (10.10.3.1)  0.5 ms    <- Server_1
 2  10.10.2.10 (10.10.2.10)  1.0 ms  <- Client_1
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 19 <<<                  |
|                                                          |
|  Show: traceroute 10.20.1.1 from Client_2                |
|  Expected: Path through Server_1 (10.10.3.1)             |
+----------------------------------------------------------+

From Client_2, trace to IPaddr2 (should go via Net4 directly):

```bash
traceroute 10.20.2.1
```

Expected output:

```
traceroute to 10.20.2.1 (10.20.2.1), 30 hops max
 1  10.10.4.10 (10.10.4.10)  0.3 ms  <- Client_1 directly
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 20 <<<                  |
|                                                          |
|  Show: traceroute 10.20.2.1 from Client_2                |
|  Expected: Direct path via Net4 (10.10.4.10)             |
+----------------------------------------------------------+


## TASK 4: Configure SSH service

### Step 1: Install and start SSH on all machines

```bash
# Install openssh-server
sudo yum install openssh-server -y

# Start and enable sshd
sudo systemctl start sshd
sudo systemctl enable sshd
sudo systemctl status sshd
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 21 <<<                  |
|                                                          |
|  Show: sshd status on all three machines                 |
|  Expected: Active: active (running) on each              |
+----------------------------------------------------------+

### Step 2: Test SSH connectivity

From Client_1 to Server_1:

```bash
ssh user@10.10.2.1
```

From Client_2 to Server_1:

```bash
ssh user@10.10.3.1
```

From Client_1 to Client_2:

```bash
ssh user@10.10.4.20
```

From Client_2 to Client_1:

```bash
ssh user@10.10.4.10
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 22 <<<                  |
|                                                          |
|  Show: SSH from Client_1 to Server_1                     |
|  Expected: Successful SSH connection                     |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 23 <<<                  |
|                                                          |
|  Show: SSH between Client_1 and Client_2                 |
|  Expected: Successful SSH connection via Net4            |
+----------------------------------------------------------+


## TASK 5: Configure firewall on Server_1

### Step 1: Enable iptables (or use firewalld)

Using iptables directly:

```bash
# Flush existing rules
sudo iptables -F

# Default policies
sudo iptables -P INPUT ACCEPT
sudo iptables -P FORWARD ACCEPT
sudo iptables -P OUTPUT ACCEPT
```

### Step 2: Allow SSH from Client_1 (Net2) only

```bash
# Allow SSH from Net2 (Client_1)
sudo iptables -A INPUT -p tcp --dport 22 -s 10.10.2.0/24 -j ACCEPT

# Block SSH from Net3 (Client_2)
sudo iptables -A INPUT -p tcp --dport 22 -s 10.10.3.0/24 -j DROP
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 24 <<<                  |
|                                                          |
|  Show: iptables rules for SSH                            |
|  Expected: ACCEPT from 10.10.2.0/24, DROP from 10.10.3.0/24|
+----------------------------------------------------------+

### Step 3: Configure ICMP rules for Client_2

Client_2 can ping IPaddr1 (10.20.1.1) but not IPaddr2 (10.20.2.1):

```bash
# Allow ICMP to IPaddr1 from Client_2
sudo iptables -A FORWARD -p icmp -s 10.10.3.0/24 -d 10.20.1.1 -j ACCEPT

# Block ICMP to IPaddr2 from Client_2
sudo iptables -A FORWARD -p icmp -s 10.10.3.0/24 -d 10.20.2.1 -j DROP
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 25 <<<                  |
|                                                          |
|  Show: iptables rules for ICMP                           |
|  Expected: ACCEPT ICMP to 10.20.1.1, DROP to 10.20.2.1   |
+----------------------------------------------------------+

### Step 4: Save iptables rules

```bash
sudo iptables-save > /etc/sysconfig/iptables
# or
sudo service iptables save
```

### Step 5: Verify firewall rules

```bash
sudo iptables -L -n -v
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 26 <<<                  |
|                                                          |
|  Show: iptables -L -n -v output                          |
|  Expected: All configured rules visible                  |
+----------------------------------------------------------+

### Step 6: Test firewall rules

Test SSH from Client_1 (should work):

```bash
ssh user@10.10.2.1
```

Test SSH from Client_2 (should be blocked):

```bash
ssh user@10.10.3.1
# Expected: Connection timeout or refused
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 27 <<<                  |
|                                                          |
|  Show: SSH from Client_1 succeeds                        |
|  Expected: Successful login                              |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 28 <<<                  |
|                                                          |
|  Show: SSH from Client_2 blocked                         |
|  Expected: Connection timeout/refused                    |
+----------------------------------------------------------+

Test ping from Client_2:

```bash
ping 10.20.1.1   # Should work
ping 10.20.2.1   # Should be blocked
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 29 <<<                  |
|                                                          |
|  Show: ping 10.20.1.1 from Client_2                      |
|  Expected: Ping successful                               |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 30 <<<                  |
|                                                          |
|  Show: ping 10.20.2.1 from Client_2                      |
|  Expected: Ping blocked (no response)                    |
+----------------------------------------------------------+


## TASK 6: Configure NAT on Server_1

### Step 1: Enable IP forwarding (if not already done)

```bash
echo "net.ipv4.ip_forward = 1" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

### Step 2: Configure NAT with iptables

```bash
# MASQUERADE for traffic from internal networks going to Internet
sudo iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE

# Alternative: SNAT if you have static external IP
# sudo iptables -t nat -A POSTROUTING -s 10.10.2.0/24 -o enp0s3 -j SNAT --to-source 192.168.1.200
# sudo iptables -t nat -A POSTROUTING -s 10.10.3.0/24 -o enp0s3 -j SNAT --to-source 192.168.1.200

# Allow forwarding from internal to external
sudo iptables -A FORWARD -i enp0s8 -o enp0s3 -j ACCEPT
sudo iptables -A FORWARD -i enp0s9 -o enp0s3 -j ACCEPT

# Allow established connections back
sudo iptables -A FORWARD -m state --state ESTABLISHED,RELATED -j ACCEPT
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 31 <<<                  |
|                                                          |
|  Show: iptables NAT rules                                |
|  Expected: MASQUERADE rule in nat table                  |
+----------------------------------------------------------+

### Step 3: Verify NAT configuration

```bash
sudo iptables -t nat -L -n -v
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 32 <<<                  |
|                                                          |
|  Show: iptables -t nat -L -n -v output                   |
|  Expected: POSTROUTING chain with MASQUERADE             |
+----------------------------------------------------------+

### Step 4: Save iptables rules

```bash
sudo iptables-save > /etc/sysconfig/iptables
```

### Step 5: Set default gateway on clients

On Client_1:

```bash
sudo ip route add default via 10.10.2.1
```

On Client_2:

```bash
sudo ip route add default via 10.10.3.1
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 33 <<<                  |
|                                                          |
|  Show: Default route on Client_1 and Client_2            |
|  Expected: default via Server_1's interface IP           |
+----------------------------------------------------------+

### Step 6: Test Internet connectivity

From Client_1:

```bash
ping -c 4 8.8.8.8
```

From Client_2:

```bash
ping -c 4 8.8.8.8
```

+----------------------------------------------------------+
|                    >>> SCREENSHOT 34 <<<                  |
|                                                          |
|  Show: ping 8.8.8.8 from Client_1                        |
|  Expected: Successful ping to Google DNS                 |
+----------------------------------------------------------+

+----------------------------------------------------------+
|                    >>> SCREENSHOT 35 <<<                  |
|                                                          |
|  Show: ping 8.8.8.8 from Client_2                        |
|  Expected: Successful ping to Google DNS                 |
+----------------------------------------------------------+


## COMPLETE IPTABLES RULES SUMMARY

Server_1 Complete iptables configuration:

```bash
# Flush all rules
iptables -F
iptables -t nat -F

# Default policies
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT

# Allow loopback
iptables -A INPUT -i lo -j ACCEPT

# SSH Rules
iptables -A INPUT -p tcp --dport 22 -s 10.10.2.0/24 -j ACCEPT
iptables -A INPUT -p tcp --dport 22 -s 10.10.3.0/24 -j DROP

# ICMP Rules for Client_2
iptables -A FORWARD -p icmp -s 10.10.3.0/24 -d 10.20.1.1 -j ACCEPT
iptables -A FORWARD -p icmp -s 10.10.3.0/24 -d 10.20.2.1 -j DROP

# NAT
iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE

# Forwarding
iptables -A FORWARD -i enp0s8 -o enp0s3 -j ACCEPT
iptables -A FORWARD -i enp0s9 -o enp0s3 -j ACCEPT
iptables -A FORWARD -m state --state ESTABLISHED,RELATED -j ACCEPT

# Save
iptables-save > /etc/sysconfig/iptables
```


## NETWORKING COMMANDS REFERENCE

| Command                              | Description                          |
|--------------------------------------|--------------------------------------|
| ip addr show                         | Show all IP addresses                |
| ip link show                         | Show network interfaces              |
| ip route show                        | Show routing table                   |
| ip addr add IP/MASK dev IFACE        | Add IP to interface                  |
| ip route add NET via GW              | Add static route                     |
| nmcli device status                  | Show device status (NetworkManager)  |
| nmcli con show                       | Show connections                     |
| systemctl restart network            | Restart networking                   |
| sysctl net.ipv4.ip_forward           | Check IP forwarding status           |
| iptables -L -n -v                    | List firewall rules                  |
| iptables -t nat -L -n -v             | List NAT rules                       |
| traceroute HOST                      | Trace route to host                  |


## TROUBLESHOOTING

**Problem: DHCP not assigning IPs**

Solutions:
- Check dhcpd.conf syntax: `dhcpd -t`
- Verify dhcpd is running: `systemctl status dhcpd`
- Check firewall allows DHCP (ports 67, 68)
- Ensure interfaces are UP: `ip link set enp0s8 up`

**Problem: Routing not working**

Solutions:
- Verify IP forwarding: `cat /proc/sys/net/ipv4/ip_forward`
- Check routes: `ip route show`
- Verify interfaces have correct IPs
- Test connectivity step by step with ping

**Problem: NAT not working**

Solutions:
- Verify MASQUERADE rule: `iptables -t nat -L`
- Check IP forwarding is enabled
- Verify default gateway on clients
- Test: `tcpdump -i enp0s3` to see traffic

**Problem: Firewall blocking unintended traffic**

Solutions:
- List all rules: `iptables -L -n -v --line-numbers`
- Check rule order (first match wins)
- Temporarily flush: `iptables -F` (for testing)


## SELF-REVIEW CHECKLIST

- [ ] All 3 VMs created with correct network adapters
- [ ] Server_1 has static IPs on all 3 interfaces
- [ ] IP forwarding enabled on Server_1
- [ ] DHCP server running on Server_1
- [ ] Client_1 receives IP via DHCP on Net2
- [ ] Client_2 receives IP via DHCP on Net3
- [ ] Client_1 and Client_2 have static IPs on Net4
- [ ] Loopback IPs configured on Client_1 (10.20.1.1, 10.20.2.1)
- [ ] Routing configured for IPaddr1 via Server_1
- [ ] Routing configured for IPaddr2 via Net4
- [ ] traceroute 10.20.1.1 from Client_2 shows Server_1 in path
- [ ] traceroute 10.20.2.1 from Client_2 shows direct path via Net4
- [ ] SSH works from Client_1 to Server_1
- [ ] SSH blocked from Client_2 to Server_1
- [ ] Client_2 can ping IPaddr1 (10.20.1.1)
- [ ] Client_2 cannot ping IPaddr2 (10.20.2.1)
- [ ] NAT configured on Server_1
- [ ] Client_1 can ping 8.8.8.8
- [ ] Client_2 can ping 8.8.8.8
