# DEPLOY SIMPLE CORPORATE NETWORK ARCHITECTURE - ANSWER

This guide walks you through deploying a corporate network infrastructure
using Windows Server 2019 with VirtualBox. You will create a main server
with DHCP and Routing services, and two client VMs in separate network
segments with traffic filtering rules.

## PART 1: VIRTUALBOX NETWORK CONFIGURATION

Before creating VMs, we need to configure VirtualBox networks.

STEP 1.1: CREATE HOST-ONLY NETWORKS
-----------------------------------

1. Open VirtualBox Manager
2. Go to File -> Host Network Manager (or press Ctrl+H)
3. Create first Host-only Network (Accounting):
   - Click "Create" button
   - Select the new adapter (e.g., VirtualBox Host-Only Ethernet Adapter)
   - Configure Adapter:
     * IPv4 Address: 192.168.1.1
     * IPv4 Network Mask: 255.255.255.0
   - DHCP Server tab: Disable (we will use Windows DHCP)

4. Create second Host-only Network (Business):
   - Click "Create" button again
   - Select the new adapter (e.g., VirtualBox Host-Only Ethernet Adapter #2)
   - Configure Adapter:
     * IPv4 Address: 192.168.2.1
     * IPv4 Network Mask: 255.255.255.0
   - DHCP Server tab: Disable

5. Click "Apply" and "Close"

NOTE: Take note of the adapter names, you'll need them when configuring VMs.

STEP 1.2: CREATE NAT NETWORK (OPTIONAL)
---------------------------------------

If you want a dedicated NAT network instead of the default NAT:

1. Go to File -> Preferences -> Network
2. Click "+" to add a NAT Network
3. Configure:
   - Network Name: NatNetwork
   - Network CIDR: 10.0.2.0/24
   - Enable Network
4. Click OK

## PART 2: CREATE VIRTUAL MACHINES

STEP 2.1: CREATE MAIN SERVER VM (LAB1SRV1)
------------------------------------------

1. Click "New" in VirtualBox Manager
2. Configure VM:
   - Name: LAB1SRV1
   - Type: Microsoft Windows
   - Version: Windows 2019 (64-bit)
   - Memory: 4096 MB (minimum 2048 MB)
   - Hard disk: Create a virtual hard disk now
     * VDI format
     * Dynamically allocated
     * Size: 50 GB

3. Configure Network Adapters (Settings -> Network):

   Adapter 1 (Internet - NAT):
   - Enable Network Adapter: Checked
   - Attached to: NAT (or NAT Network if you created one)

   Adapter 2 (Accounting Network):
   - Enable Network Adapter: Checked
   - Attached to: Host-only Adapter
   - Name: VirtualBox Host-Only Ethernet Adapter (first one created)

   Adapter 3 (Business Network):
   - Enable Network Adapter: Checked
   - Attached to: Host-only Adapter
   - Name: VirtualBox Host-Only Ethernet Adapter #2 (second one created)

4. Install Windows Server 2019:
   - Mount ISO to optical drive
   - Start VM and follow installation wizard
   - Select "Windows Server 2019 Standard (Desktop Experience)"
   - Set Administrator password

STEP 2.2: CREATE CLIENT VM 1 (LAB1ACC1 - ACCOUNTING)
----------------------------------------------------

1. Click "New" in VirtualBox Manager
2. Configure VM:
   - Name: LAB1ACC1
   - Type: Microsoft Windows
   - Version: Windows 2019 (64-bit)
   - Memory: 2048 MB
   - Hard disk: 40 GB (dynamically allocated)

3. Configure Network (Settings -> Network):

   Adapter 1 (Accounting Network):
   - Enable Network Adapter: Checked
   - Attached to: Host-only Adapter
   - Name: VirtualBox Host-Only Ethernet Adapter (Accounting)

4. Install Windows Server 2019 (same process as above)

STEP 2.3: CREATE CLIENT VM 2 (LAB1BUS1 - BUSINESS)
--------------------------------------------------

1. Click "New" in VirtualBox Manager
2. Configure VM:
   - Name: LAB1BUS1
   - Type: Microsoft Windows
   - Version: Windows 2019 (64-bit)
   - Memory: 2048 MB
   - Hard disk: 40 GB (dynamically allocated)

3. Configure Network (Settings -> Network):

   Adapter 1 (Business Network):
   - Enable Network Adapter: Checked
   - Attached to: Host-only Adapter
   - Name: VirtualBox Host-Only Ethernet Adapter #2 (Business)

4. Install Windows Server 2019

## PART 3: CONFIGURE MAIN SERVER (LAB1SRV1)

STEP 3.1: RENAME COMPUTER AND CONFIGURE STATIC IPs
--------------------------------------------------

1. Rename Computer:
   - Open Server Manager
   - Click on "Local Server"
   - Click on Computer name
   - Click "Change..." button
   - Enter: LAB1SRV1
   - Click OK and restart

2. After restart, configure Network Adapters:
   - Open "Network and Sharing Center"
   - Click "Change adapter settings"

   Rename adapters for clarity:
   - Ethernet (NAT) -> Right-click -> Rename -> "Internet"
   - Ethernet 2 -> Right-click -> Rename -> "Accounting"
   - Ethernet 3 -> Right-click -> Rename -> "Business"

3. Configure Static IPs:

   Internet Adapter (NAT):
   - Leave as DHCP (gets IP from VirtualBox NAT)
   - Or configure static if using NAT Network

   Accounting Adapter:
   - Right-click -> Properties -> Internet Protocol Version 4 (TCP/IPv4)
   - Use the following IP address:
     * IP address: 192.168.1.1
     * Subnet mask: 255.255.255.0
     * Default gateway: (leave empty)
   - DNS: Leave empty or set to 127.0.0.1

   Business Adapter:
   - Right-click -> Properties -> Internet Protocol Version 4 (TCP/IPv4)
   - Use the following IP address:
     * IP address: 192.168.2.1
     * Subnet mask: 255.255.255.0
     * Default gateway: (leave empty)
   - DNS: Leave empty or set to 127.0.0.1

STEP 3.2: INSTALL REQUIRED ROLES
--------------------------------

Using Server Manager:

1. Open Server Manager
2. Click "Manage" -> "Add Roles and Features"
3. Click Next through the wizard until "Server Roles"

4. Select the following roles:
   [X] DHCP Server
   [X] Remote Access

5. Click Next until "Role Services" for Remote Access:
   [X] Routing
   (This will also select DirectAccess and VPN)

6. Click Next and Install
7. Wait for installation to complete
8. Click "Complete DHCP configuration" link that appears
   - Click Next, then Commit, then Close

Using PowerShell (Alternative):

```powershell
Install-WindowsFeature -Name DHCP -IncludeManagementTools
Install-WindowsFeature -Name Routing -IncludeManagementTools
Install-WindowsFeature -Name RSAT-RemoteAccess-Mgmt
```

## PART 4: CONFIGURE DHCP SERVICE

STEP 4.1: OPEN DHCP MANAGEMENT CONSOLE
--------------------------------------

1. Open Server Manager
2. Click Tools -> DHCP
   OR
   Run: dhcpmgmt.msc

STEP 4.2: AUTHORIZE DHCP SERVER
-------------------------------

1. In DHCP console, right-click on server name (lab1srv1.domain or lab1srv1)
2. Click "Authorize"
3. Press F5 to refresh - green checkmarks should appear

NOTE: If not in a domain, authorization might happen automatically.

STEP 4.3: CREATE ACCOUNTING NETWORK SCOPE (192.168.1.0/24)
----------------------------------------------------------

1. Right-click on "IPv4" -> "New Scope..."
2. Click Next

3. Scope Name:
   - Name: Accounting Network
   - Description: DHCP scope for Accounting department
   - Click Next

4. IP Address Range:
   - Start IP address: 192.168.1.100
   - End IP address: 192.168.1.200
   - Subnet mask: 255.255.255.0 (Length: 24)
   - Click Next

5. Add Exclusions (Optional):
   - Exclude: 192.168.1.1 to 192.168.1.10 (for servers/static IPs)
   - Click Next

6. Lease Duration:
   - Set to 8 days (or adjust as needed)
   - Click Next

7. Configure DHCP Options: Yes, I want to configure these options now
   - Click Next

8. Router (Default Gateway):
   - IP address: 192.168.1.1
   - Click Add, then Next

9. Domain Name and DNS Servers:
   - Parent domain: (leave empty or enter your domain)
   - DNS Server: 192.168.1.1 (or external DNS like 8.8.8.8)
   - Click Next

10. WINS Servers:
    - Skip (Click Next)

11. Activate Scope: Yes, I want to activate this scope now
    - Click Next, then Finish

STEP 4.4: CREATE BUSINESS NETWORK SCOPE (192.168.2.0/24)
--------------------------------------------------------

1. Right-click on "IPv4" -> "New Scope..."
2. Click Next

3. Scope Name:
   - Name: Business Network
   - Description: DHCP scope for Business department
   - Click Next

4. IP Address Range:
   - Start IP address: 192.168.2.100
   - End IP address: 192.168.2.200
   - Subnet mask: 255.255.255.0 (Length: 24)
   - Click Next

5. Add Exclusions (Optional):
   - Exclude: 192.168.2.1 to 192.168.2.10
   - Click Next

6. Lease Duration:
   - Set to 8 days
   - Click Next

7. Configure DHCP Options: Yes
   - Click Next

8. Router (Default Gateway):
   - IP address: 192.168.2.1
   - Click Add, then Next

9. Domain Name and DNS Servers:
   - DNS Server: 192.168.2.1 (or 8.8.8.8)
   - Click Next

10. WINS Servers: Skip

11. Activate Scope: Yes
    - Click Next, then Finish

STEP 4.5: VERIFY DHCP CONFIGURATION
-----------------------------------

In DHCP console, you should see:
- IPv4
  - Scope [192.168.1.0] Accounting Network (Active)
    - Address Pool
    - Address Leases
    - Reservations
    - Scope Options (Router: 192.168.1.1)
  - Scope [192.168.2.0] Business Network (Active)
    - Address Pool
    - Address Leases
    - Reservations
    - Scope Options (Router: 192.168.2.1)

PowerShell verification:

```powershell
Get-DhcpServerv4Scope
```

Expected output:

```
ScopeId       SubnetMask      Name              State
-------       ----------      ----              -----
192.168.1.0   255.255.255.0   Accounting Network Active
192.168.2.0   255.255.255.0   Business Network   Active
```

## PART 5: CONFIGURE ROUTING AND REMOTE ACCESS

STEP 5.1: ENABLE ROUTING AND REMOTE ACCESS
------------------------------------------

1. Open Server Manager
2. Click Tools -> Routing and Remote Access
   OR
   Run: rrasmgmt.msc

3. Right-click on server name (LAB1SRV1) -> "Configure and Enable Routing and Remote Access"

4. Configuration Wizard:
   - Click Next
   - Select: "Network address translation (NAT)"
   - Click Next

5. NAT Internet Connection:
   - Select the interface connected to Internet (the NAT adapter)
   - Usually shows as "Ethernet" or "Internet" (the one with public/NAT IP)
   - Click Next

6. Click Finish

7. Wait for service to start (green arrow appears on server icon)

STEP 5.2: VERIFY NAT CONFIGURATION
----------------------------------

1. In Routing and Remote Access console:
2. Expand: LAB1SRV1 -> IPv4 -> NAT

3. You should see your interfaces listed:
   - Internet (Public Interface - NAT enabled)
   - Accounting (Private Interface)
   - Business (Private Interface)

If interfaces are missing, add them:
1. Right-click on NAT -> "New Interface..."
2. Select the interface and configure:
   - For Internet: "Public interface connected to the Internet"
     [X] Enable NAT on this interface
   - For Accounting/Business: "Private interface connected to private network"

STEP 5.3: ENABLE IP ROUTING (IF NOT ALREADY)
--------------------------------------------

1. In Routing and Remote Access console
2. Right-click on LAB1SRV1 -> Properties
3. IPv4 tab:
   [X] Enable IPv4 Forwarding
4. Click OK

PowerShell alternative:

```powershell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters" -Name "IPEnableRouter" -Value 1
Restart-Service RemoteAccess
```

## PART 6: CONFIGURE TRAFFIC FILTERING (ACCESS RULES)

This is the critical part - allowing Business->Accounting but denying Accounting->Business.

METHOD 1: USING ROUTING AND REMOTE ACCESS PACKET FILTERS
--------------------------------------------------------

STEP 6.1: Configure Input Filter on Accounting Interface
(Block traffic FROM Accounting TO Business)

1. In Routing and Remote Access console
2. Navigate to: LAB1SRV1 -> IPv4 -> General
3. Right-click on "Accounting" interface -> Properties
4. Click "Inbound Filters" button

5. Configure:
   - Select: "Drop all packets except those that meet the criteria below"

6. Add allowed traffic (everything EXCEPT to Business network):

   Click "New..."
   - Source network:
     [X] Any
   - Destination network:
     IP address: 192.168.1.0
     Subnet mask: 255.255.255.0
   - Protocol: Any
   Click OK

   Click "New..."
   - Source network:
     [X] Any
   - Destination network:
     [X] Any (for Internet access via NAT)
   - Protocol: Any

   ALTERNATIVE APPROACH - Create DENY rule specifically:

   Select: "Receive all packets except those that meet the criteria below"

   Click "New..."
   - Source network:
     IP address: 192.168.1.0
     Subnet mask: 255.255.255.0
   - Destination network:
     IP address: 192.168.2.0
     Subnet mask: 255.255.255.0
   - Protocol: Any
   Click OK

7. Click OK to apply

METHOD 2: USING WINDOWS FIREWALL WITH ADVANCED SECURITY (RECOMMENDED)
---------------------------------------------------------------------

This method provides more granular control and is easier to manage.

STEP 6.2: Open Windows Firewall with Advanced Security

1. Run: wf.msc
   OR
   Server Manager -> Tools -> Windows Firewall with Advanced Security

STEP 6.3: Create Rule to BLOCK Accounting to Business Traffic

1. Click on "Inbound Rules" in left panel
2. Click "New Rule..." in right panel

3. Rule Type: Custom
   Click Next

4. Program: All programs
   Click Next

5. Protocol and Ports:
   - Protocol type: Any
   Click Next

6. Scope:
   - Which local IP addresses: These IP addresses
     Click Add -> This IP address or subnet: 192.168.2.0/24
     (This is the DESTINATION - Business network)

   - Which remote IP addresses: These IP addresses
     Click Add -> This IP address or subnet: 192.168.1.0/24
     (This is the SOURCE - Accounting network)
   Click Next

7. Action: Block the connection
   Click Next

8. Profile: Select all (Domain, Private, Public)
   Click Next

9. Name: Block Accounting to Business
   Description: Deny all traffic from Accounting (192.168.1.0/24) to Business (192.168.2.0/24)
   Click Finish

STEP 6.4: Create Rule to ALLOW Business to Accounting Traffic

1. Click on "Inbound Rules"
2. Click "New Rule..."

3. Rule Type: Custom
   Click Next

4. Program: All programs
   Click Next

5. Protocol and Ports: Any
   Click Next

6. Scope:
   - Local IP addresses: These IP addresses
     Add: 192.168.1.0/24 (Destination - Accounting)

   - Remote IP addresses: These IP addresses
     Add: 192.168.2.0/24 (Source - Business)
   Click Next

7. Action: Allow the connection
   Click Next

8. Profile: All
   Click Next

9. Name: Allow Business to Accounting
   Click Finish

STEP 6.5: Ensure Rule Priority

In Windows Firewall, Block rules take precedence over Allow rules by default.
Make sure the rules are applied correctly:

1. The "Block Accounting to Business" rule blocks traffic from 192.168.1.0/24 to 192.168.2.0/24
2. The "Allow Business to Accounting" rule allows traffic from 192.168.2.0/24 to 192.168.1.0/24
3. NAT handles Internet access automatically

PowerShell Commands for Firewall Rules:

```powershell
# Block Accounting to Business
New-NetFirewallRule -DisplayName "Block Accounting to Business" `
    -Direction Inbound `
    -LocalAddress 192.168.2.0/24 `
    -RemoteAddress 192.168.1.0/24 `
    -Action Block `
    -Profile Any

# Allow Business to Accounting
New-NetFirewallRule -DisplayName "Allow Business to Accounting" `
    -Direction Inbound `
    -LocalAddress 192.168.1.0/24 `
    -RemoteAddress 192.168.2.0/24 `
    -Action Allow `
    -Profile Any
```

METHOD 3: USING NETSH ROUTING FILTERS (ALTERNATIVE)
---------------------------------------------------

```batch
# Add filter to block Accounting -> Business on the routing level
netsh routing ip add filter name="Accounting" filtertype=input ^
    srcaddr=192.168.1.0 srcmask=255.255.255.0 ^
    dstaddr=192.168.2.0 dstmask=255.255.255.0 ^
    proto=any action=drop
```

## PART 7: CONFIGURE CLIENT VMs

STEP 7.1: CONFIGURE LAB1ACC1 (ACCOUNTING CLIENT)
------------------------------------------------

1. Start LAB1ACC1 VM
2. Rename computer:
   - Server Manager -> Local Server -> Computer name
   - Change to: LAB1ACC1
   - Restart

3. Configure Network:
   - Open Network Connections
   - Right-click adapter -> Properties -> IPv4
   - Select: "Obtain an IP address automatically"
   - Select: "Obtain DNS server address automatically"
   - Click OK

4. Verify DHCP:
   - Open Command Prompt as Administrator
   - Run: ipconfig /release
   - Run: ipconfig /renew
   - Run: ipconfig /all

Expected output:

```
Windows IP Configuration

Ethernet adapter Ethernet:
   Connection-specific DNS Suffix  . :
   IPv4 Address. . . . . . . . . . . : 192.168.1.100-200 (from DHCP range)
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . : 192.168.1.1
   DHCP Server . . . . . . . . . . . : 192.168.1.1
```

STEP 7.2: CONFIGURE LAB1BUS1 (BUSINESS CLIENT)
----------------------------------------------

1. Start LAB1BUS1 VM
2. Rename computer to: LAB1BUS1
3. Restart

4. Configure Network:
   - Set to obtain IP automatically (DHCP)

5. Verify DHCP:
   - ipconfig /release
   - ipconfig /renew
   - ipconfig /all

Expected output:

```
Ethernet adapter Ethernet:
   IPv4 Address. . . . . . . . . . . : 192.168.2.100-200 (from DHCP range)
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . : 192.168.2.1
   DHCP Server . . . . . . . . . . . : 192.168.2.1
```

## PART 8: VERIFICATION AND TESTING

STEP 8.1: VERIFY CONFIGURATION ON LAB1SRV1 (MAIN SERVER)
--------------------------------------------------------

Command: ipconfig /all

Expected output (3 adapters):

```
Windows IP Configuration

Host Name . . . . . . . . . . . . : LAB1SRV1

Ethernet adapter Internet:
   IPv4 Address. . . . . . . . . . . : 10.0.2.15 (NAT IP)
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . : 10.0.2.2

Ethernet adapter Accounting:
   IPv4 Address. . . . . . . . . . . : 192.168.1.1
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . :

Ethernet adapter Business:
   IPv4 Address. . . . . . . . . . . : 192.168.2.1
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . :
```

Command: route print

Expected output shows routes to all three networks:

```
Network Destination    Netmask          Gateway       Interface
0.0.0.0                0.0.0.0          10.0.2.2      10.0.2.15
10.0.2.0               255.255.255.0    On-link       10.0.2.15
192.168.1.0            255.255.255.0    On-link       192.168.1.1
192.168.2.0            255.255.255.0    On-link       192.168.2.1
```

STEP 8.2: TEST FROM LAB1ACC1 (ACCOUNTING)
-----------------------------------------

1. Check IP configuration:

```batch
ipconfig /all
```

Expected: IP in 192.168.1.100-200 range

2. Check routing table:

```batch
route print
```

Expected: Default gateway 192.168.1.1

3. Test connectivity to gateway:

```batch
ping 192.168.1.1
```

Expected: Reply from 192.168.1.1

4. Test connectivity to Business network (SHOULD FAIL):

```batch
ping 192.168.2.1
```

Expected: Request timed out (or Destination unreachable)

```batch
tracert -d 192.168.2.100
```

Expected: Packets blocked, trace fails

5. Test Internet connectivity (SHOULD WORK):

```batch
ping 8.8.8.8
```

Expected: Reply from 8.8.8.8

STEP 8.3: TEST FROM LAB1BUS1 (BUSINESS)
---------------------------------------

1. Check IP configuration:

```batch
ipconfig /all
```

Expected: IP in 192.168.2.100-200 range

2. Check routing table:

```batch
route print
```

Expected: Default gateway 192.168.2.1

3. Test connectivity to Accounting network (SHOULD WORK):

```batch
ping 192.168.1.1
```

Expected: Reply from 192.168.1.1

```batch
tracert -d 192.168.1.100
```

Expected: Trace shows path through 192.168.2.1 to 192.168.1.x

4. Test Internet connectivity (SHOULD WORK):

```batch
ping 8.8.8.8
```

Expected: Reply from 8.8.8.8

STEP 8.4: VERIFY DHCP LEASES ON SERVER
--------------------------------------

1. Open DHCP console on LAB1SRV1
2. Navigate to each scope -> Address Leases
3. You should see active leases for both clients

PowerShell:

```powershell
Get-DhcpServerv4Lease -ScopeId 192.168.1.0
Get-DhcpServerv4Lease -ScopeId 192.168.2.0
```

## PART 9: SCREENSHOTS FOR SUBMISSION

Capture the following screenshots (with host DateTime visible):

1. VirtualBox Network Configuration:
   - Host Network Manager showing both Host-only adapters

2. For LAB1SRV1:
   - ipconfig /all output
   - route print output
   - Server Manager showing installed roles
   - DHCP console showing both scopes
   - DHCP Scope Options for each scope
   - Routing and Remote Access console
   - NAT configuration
   - Windows Firewall rules (or RRAS packet filters)

3. For LAB1ACC1:
   - ipconfig /all output
   - route print output
   - tracert -d 192.168.2.x (Business VM IP) - showing BLOCKED
   - ping 8.8.8.8 - showing Internet access works

4. For LAB1BUS1:
   - ipconfig /all output
   - route print output
   - tracert -d 192.168.1.x (Accounting VM IP) - showing SUCCESS
   - ping 8.8.8.8 - showing Internet access works

5. Additional proof:
   - DHCP Address Leases showing both clients
   - ping tests between networks showing traffic rules work

## TROUBLESHOOTING

PROBLEM: DHCP not giving out addresses
--------------------------------------
Solutions:
1. Verify DHCP Server service is running:

```powershell
Get-Service DHCPServer
```

2. Authorize DHCP server (required in domain environments)

3. Check scope is activated

4. Verify network adapter bindings:
   - DHCP console -> Right-click server -> Add/Remove Bindings
   - Ensure Accounting and Business interfaces are checked

PROBLEM: Clients can't reach Internet
-------------------------------------
Solutions:
1. Verify NAT is configured correctly:
   - RRAS console -> NAT -> Verify public interface

2. Check IP forwarding is enabled:

```powershell
Get-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters" -Name IPEnableRouter
```

3. Verify default gateway on clients points to server

4. Check Windows Firewall isn't blocking forwarded traffic

PROBLEM: Traffic filtering not working
--------------------------------------
Solutions:
1. Verify firewall rules are enabled and in correct order

2. Check rule scope (Local vs Remote addresses)

3. Test with firewall temporarily disabled to isolate issue

4. Use:

```batch
netsh advfirewall firewall show rule name=all
```

PROBLEM: Routing not working between networks
---------------------------------------------
Solutions:
1. Enable IP routing:

```powershell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters" -Name "IPEnableRouter" -Value 1
```

2. Restart Routing and Remote Access service

3. Verify all interfaces are added to RRAS

## SUMMARY

> **Network Architecture Diagram**
>
> ```
> +------------------+     +------------------+     +------------------+
> |    LAB1ACC1      |     |    LAB1SRV1      |     |    LAB1BUS1      |
> |   (Accounting)   |     |   (Main Server)  |     |   (Business)     |
> |                  |     |                  |     |                  |
> | 192.168.1.x/24   |<--->| 192.168.1.1      |     | 192.168.2.x/24   |
> |                  |     | 192.168.2.1      |<--->|                  |
> |                  |     | 10.0.2.x (NAT)   |     |                  |
> +------------------+     +--------+---------+     +------------------+
>                                  |
>                                  v
>                          +-------+--------+
>                          |   INTERNET     |
>                          +----------------+
> ```

Traffic Rules:
- Business (192.168.2.0/24) -> Accounting (192.168.1.0/24): ALLOWED
- Accounting (192.168.1.0/24) -> Business (192.168.2.0/24): BLOCKED
- Both networks -> Internet: ALLOWED (via NAT)

Services on LAB1SRV1:
- DHCP Server (two scopes)
- Routing and Remote Access (NAT + Routing)
- Windows Firewall (traffic filtering)

Key Verification Commands:
- ipconfig /all (check IP configuration)
- route print (check routing table)
- tracert -d <IP> (trace route to destination)
- ping <IP> (test connectivity)
- Get-DhcpServerv4Scope (verify DHCP scopes)
- Get-DhcpServerv4Lease -ScopeId <scope> (check leases)
