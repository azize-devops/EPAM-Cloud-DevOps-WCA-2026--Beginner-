# LINUX NETWORKING PRACTICAL TASK

The practical part of the Linux Networking module involves creating a network
using VirtualBox, which is shown in the network diagram below.


## NETWORK TOPOLOGY

```
                            [Internet/Cloud]
                                   |
                                   |
                                [Host]
                                   |
                                 Net1
                                   |
                                 Int1
    [Client_1]               [Server_1]               [Client_2]
      Int1 -------- Net2 ------ Int2                    Int1
      Int2                      Int3 ------ Net3 ------
        |                                                 |
        +----------------- Net4 --------------------------+
                                                        Int2
```


## NETWORK DESCRIPTION

- Host: The computer on which VirtualBox is running.

- Server_1: A virtual machine on which the Linux OS is deployed.
  - Int1 - "Network bridge" mode, connected to Net1 (home network)
  - IP address of Int1 is set statically (e.g., 192.168.1.200/24)
  - Int2 - "Internal network" mode, connected to Net2
  - Int3 - "Internal network" mode, connected to Net3

- Client_1 and Client_2: Virtual machines with different Linux distributions
  (e.g., Ubuntu and CentOS).
  - Interfaces connected in "Internal network" mode to Net2, Net3, and Net4
    as shown in the diagram.

- Address Space Requirements:
  - Net2, Net3, and Net4 must use private IP ranges
  - Must not overlap with the host network address space
  - Same applies to IPaddr1 and IPaddr2


## TASKS

1. Configure static addresses on all interfaces on Server_1.

2. Configure the DHCP service on Server_1, which will configure the Int1
   addresses of Client_1 and Client_2.

3. On the virtual interface lo Client_1, assign two IP addresses IPaddr1 and
   IPaddr2. Configure routing so that traffic from Client_2 to IPaddr1 goes
   through Server_1, and to IPaddr2 through Net4. To check, use traceroute.

4. Configure the SSH service so that Client_1 and Client_2 can connect to
   Server_1 and each other.

5. Configure the firewall on Server_1 as follows:
   - Allowed to connect via SSH from Client_1 and forbidden from Client_2
   - Client_2 may ping IPaddr1, but may not ping IPaddr2

6. On Server_1, configure the NAT service so that Client_1 and Client_2 may
   ping the Internet resources, for example 8.8.8.8


## SELF-REVIEW

- All VMs can communicate according to the network topology
- DHCP assigns IP addresses to Client_1 and Client_2
- Traceroute from Client_2 to IPaddr1 shows path through Server_1
- Traceroute from Client_2 to IPaddr2 shows path through Net4
- SSH works from Client_1 to Server_1
- SSH blocked from Client_2 to Server_1
- Client_2 can ping IPaddr1 but cannot ping IPaddr2
- Both clients can ping 8.8.8.8 (Internet access via NAT)


## HELPFUL MATERIALS

- [IP Command Examples](https://www.tecmint.com/ip-command-examples/)
- [Arch Wiki - dhcpd](https://wiki.archlinux.org/title/dhcpd)
- [DigitalOcean - iptables Essentials](https://www.digitalocean.com/community/tutorials/iptables-essentials-common-firewall-rules-and-commands)
- [Red Hat Networking Guide](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide/)
