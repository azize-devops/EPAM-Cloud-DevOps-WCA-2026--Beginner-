PRACTICAL TASK ON IP ADDRESSING AND SUBNETTING
===============================================

1. For each IP address 120.156.21.95/12, 172.18.54.141/19, 192.167.43.58/27:

   1.1. Determine the network class (A, B, C);

   1.2. Determine which category the address belongs to (private, public);

   1.3. Complete Table 1 instead of example. (Table 1 shows an example for 184.35.72.95/20)

   Table 1 Example (184.35.72.95/20):
   +---------------------------------+----------+----------+----------+----------+
   |                                 | 1st octet| 2nd octet| 3rd octet| 4th octet|
   +---------------------------------+----------+----------+----------+----------+
   | Host Address (decimal)          |   184    |    35    |    72    |    95    |
   | Mask (decimal)                  |   255    |   255    |   240    |     0    |
   | Network Address (decimal)       |   184    |    35    |    64    |     0    |
   | First available host (decimal)  |   184    |    35    |    64    |     1    |
   | Last available host (decimal)   |   184    |    35    |    79    |   254    |
   | Broadcast address (decimal)     |   184    |    35    |    79    |   255    |
   +---------------------------------+----------+----------+----------+----------+
   | Host Address (binary)           | 10111000 | 00100011 | 01001000 | 01011111 |
   | Mask (binary)                   | 11111111 | 11111111 | 11110000 | 00000000 |
   | Network Address (binary)        | 10111000 | 00100011 | 01000000 | 00000000 |
   | First available host (binary)   | 10111000 | 00100011 | 01000000 | 00000001 |
   | Last available host (binary)    | 10111000 | 00100011 | 01001111 | 11111110 |
   | Broadcast address (binary)      | 10111000 | 00100011 | 01001111 | 11111111 |
   +---------------------------------+----------+----------+----------+----------+
   | Available number of addresses for hosts: 32-20=12, 2^12 - 2 = 4094        |
   +---------------------------------------------------------------------------+


2. For the network shown in Figure 1, the address 172.16.0.0/20 is assigned.
   The figure shows the number of hosts in each of the networks.
   The networks between the routers consist of two hosts (the addresses of the router interfaces).

   Network Topology (Fig.1):

                    [Net 7: 60]     [Net 8: 25]
                         \            /
                          \          /
                           [Router]
                          /          \
                    [Net 9]          [Net 10]
                        |              |
   [Net 1: 120]---[Router]---[Net 11]---[Router]---[Net 6: 40]
                    /    \              /    \
              [Net 2: 35] [Net 3: 50] [Net 4: 80] [Net 5: 75]

   You must:

   2.1. Divide the network into subnets based on the number of hosts in each of
        the subnets and using a fixed length subnet mask (FLSM).

   2.2. Calculate how many addresses are left unused in each of the subnets.

   2.3. Optional extra task: perform steps 2.1 to 2.2 using a variable length
        subnet mask (VLSM).
