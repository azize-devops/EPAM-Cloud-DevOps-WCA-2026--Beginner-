DEPLOY SIMPLE CORPORATE NETWORK ARCHITECTURE - TASK
===================================================

GOALS:
------
- Learn to deploy a corporate network infrastructure using Windows Server 2019
- Configure DHCP service for multiple network segments
- Implement routing between different network segments
- Apply network traffic filtering rules between segments
- Configure NAT for Internet access

TASKS:
------
1. Create VirtualBox Virtual Machines:
   - Main Server VM (LAB1SRV1):
     * OS: Windows Server 2019
     * 3 Network interfaces:
       - NIC 1: NAT Network (Internet access)
       - NIC 2: Host-only network (Accounting)
       - NIC 3: Host-only network (Business)

   - Client VM 1 (LAB1ACC1):
     * OS: Windows Server 2019
     * 1 Network interface: Host-only (Accounting network)

   - Client VM 2 (LAB1BUS1):
     * OS: Windows Server 2019
     * 1 Network interface: Host-only (Business network)

2. Install and Configure Required Roles on LAB1SRV1:
   - Install DHCP Server role
   - Install Remote Access role (Routing)

3. Configure DHCP Service:
   - Accounting network scope: 192.168.1.0/24
   - Business network scope: 192.168.2.0/24
   - Both client VMs should obtain IP addresses dynamically

4. Configure Routing and Remote Access:
   - Enable IP routing between all network interfaces
   - Configure NAT for Internet access from both networks

5. Configure Network Traffic Filtering:
   - ALLOW all traffic from Business network (192.168.2.0/24) to Accounting network (192.168.1.0/24)
   - DENY all traffic from Accounting network (192.168.1.0/24) to Business network (192.168.2.0/24)

6. Verify Configuration:
   - Test DHCP on both client VMs
   - Test connectivity between networks (with traffic filtering rules)
   - Test Internet access from both client VMs

NETWORK DIAGRAM:
----------------
                         +------------------+
                         |     INTERNET     |
                         +--------+---------+
                                  |
                           [NAT Network]
                                  |
                         +--------+---------+
                         |    LAB1SRV1      |
                         | Windows Server   |
                         |      2019        |
                         |                  |
                         | DHCP + Routing   |
                         +--+------------+--+
                            |            |
              [Host-only 1] |            | [Host-only 2]
            192.168.1.0/24  |            |  192.168.2.0/24
                            |            |
                   +--------+--+      +--+--------+
                   |  LAB1ACC1 |      | LAB1BUS1  |
                   | Accounting|      | Business  |
                   |   Client  |      |  Client   |
                   +-----------+      +-----------+

Traffic Rules:
- Business -> Accounting: ALLOWED
- Accounting -> Business: DENIED
- Both networks -> Internet: ALLOWED (via NAT)

SELF-REVIEW:
------------
- All VMs are created with correct network adapter configurations
- DHCP service is running and distributing IP addresses to both networks
- Client VMs receive IP addresses in the correct subnet ranges
- Routing is enabled between network segments
- Traffic from Business to Accounting is allowed
- Traffic from Accounting to Business is blocked
- Both client VMs can access the Internet through NAT
- "ipconfig /all" shows correct IP configuration on all machines
- "route print" shows correct routing tables
- "tracert" commands demonstrate proper routing and traffic filtering

HOMETASK RESULTS REQUIREMENTS:
------------------------------
All screenshots should contain:
- System DateTime of your host OS
- Output for "ipconfig /all" and "route print" commands for each server
- Output for "tracert -d 192.168.X.Y" (IP of opposite VM) for each server
- Relevant DHCP service configuration pages
- Relevant Routing and network access restriction configuration pages
- Other screenshots to prove your solution

HELPFUL MATERIALS:
------------------
https://docs.microsoft.com/en-us/windows-server/networking/technologies/dhcp/dhcp-top
https://docs.microsoft.com/en-us/windows-server/remote/remote-access/remote-access
https://docs.microsoft.com/en-us/windows-server/networking/technologies/network-policy-server/network-policy-server
https://www.virtualbox.org/manual/ch06.html
https://docs.microsoft.com/en-us/windows-server/networking/technologies/ipam/ipam-top
