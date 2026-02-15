# PRACTICAL TASK ON IP ADDRESSING AND SUBNETTING - ANSWERS

---

## TASK 1: IP ADDRESS ANALYSIS

### 1.1 & 1.2: Network Class and Category

| IP Address          | Class | Category |
|:-------------------:|:-----:|:--------:|
| 120.156.21.95/12    |   A   |  Public  |
| 172.18.54.141/19    |   B   | Private  |
| 192.167.43.58/27    |   C   |  Public  |

**Explanation:**
- **Class A:** 1-126 (Private range: 10.0.0.0 - 10.255.255.255)
- **Class B:** 128-191 (Private range: 172.16.0.0 - 172.31.255.255)
- **Class C:** 192-223 (Private range: 192.168.0.0 - 192.168.255.255)

---

### 1.3: Table for 120.156.21.95/12

| Parameter                       | 1st octet | 2nd octet | 3rd octet | 4th octet |
|:--------------------------------|:---------:|:---------:|:---------:|:---------:|
| Host Address (decimal)          |    120    |    156    |    21     |    95     |
| Mask (decimal)                  |    255    |    240    |     0     |     0     |
| Network Address (decimal)       |    120    |    144    |     0     |     0     |
| First available host (decimal)  |    120    |    144    |     0     |     1     |
| Last available host (decimal)   |    120    |    159    |    255    |    254    |
| Broadcast address (decimal)     |    120    |    159    |    255    |    255    |
| **Binary Values**               |           |           |           |           |
| Host Address (binary)           | 01111000  | 10011100  | 00010101  | 01011111  |
| Mask (binary)                   | 11111111  | 11110000  | 00000000  | 00000000  |
| Network Address (binary)        | 01111000  | 10010000  | 00000000  | 00000000  |
| First available host (binary)   | 01111000  | 10010000  | 00000000  | 00000001  |
| Last available host (binary)    | 01111000  | 10011111  | 11111111  | 11111110  |
| Broadcast address (binary)      | 01111000  | 10011111  | 11111111  | 11111111  |

> **Available hosts:** 32-12=20 bits, 2^20 - 2 = **1,048,574**

---

### 1.3: Table for 172.18.54.141/19

| Parameter                       | 1st octet | 2nd octet | 3rd octet | 4th octet |
|:--------------------------------|:---------:|:---------:|:---------:|:---------:|
| Host Address (decimal)          |    172    |    18     |    54     |    141    |
| Mask (decimal)                  |    255    |    255    |    224    |     0     |
| Network Address (decimal)       |    172    |    18     |    32     |     0     |
| First available host (decimal)  |    172    |    18     |    32     |     1     |
| Last available host (decimal)   |    172    |    18     |    63     |    254    |
| Broadcast address (decimal)     |    172    |    18     |    63     |    255    |
| **Binary Values**               |           |           |           |           |
| Host Address (binary)           | 10101100  | 00010010  | 00110110  | 10001101  |
| Mask (binary)                   | 11111111  | 11111111  | 11100000  | 00000000  |
| Network Address (binary)        | 10101100  | 00010010  | 00100000  | 00000000  |
| First available host (binary)   | 10101100  | 00010010  | 00100000  | 00000001  |
| Last available host (binary)    | 10101100  | 00010010  | 00111111  | 11111110  |
| Broadcast address (binary)      | 10101100  | 00010010  | 00111111  | 11111111  |

> **Available hosts:** 32-19=13 bits, 2^13 - 2 = **8,190**

---

### 1.3: Table for 192.167.43.58/27

| Parameter                       | 1st octet | 2nd octet | 3rd octet | 4th octet |
|:--------------------------------|:---------:|:---------:|:---------:|:---------:|
| Host Address (decimal)          |    192    |    167    |    43     |    58     |
| Mask (decimal)                  |    255    |    255    |    255    |    224    |
| Network Address (decimal)       |    192    |    167    |    43     |    32     |
| First available host (decimal)  |    192    |    167    |    43     |    33     |
| Last available host (decimal)   |    192    |    167    |    43     |    62     |
| Broadcast address (decimal)     |    192    |    167    |    43     |    63     |
| **Binary Values**               |           |           |           |           |
| Host Address (binary)           | 11000000  | 10100111  | 00101011  | 00111010  |
| Mask (binary)                   | 11111111  | 11111111  | 11111111  | 11100000  |
| Network Address (binary)        | 11000000  | 10100111  | 00101011  | 00100000  |
| First available host (binary)   | 11000000  | 10100111  | 00101011  | 00100001  |
| Last available host (binary)    | 11000000  | 10100111  | 00101011  | 00111110  |
| Broadcast address (binary)      | 11000000  | 10100111  | 00101011  | 00111111  |

> **Available hosts:** 32-27=5 bits, 2^5 - 2 = **30**

---

## TASK 2: SUBNETTING 172.16.0.0/20

**Given Network:** 172.16.0.0/20
**Total addresses:** 2^12 = 4,096
**Usable hosts:** 4,094

### Networks Required:

| Network | Hosts Needed | Purpose      |
|:-------:|:------------:|:-------------|
| Net 1   |     120      | -            |
| Net 2   |      35      | -            |
| Net 3   |      50      | -            |
| Net 4   |      80      | -            |
| Net 5   |      75      | -            |
| Net 6   |      40      | -            |
| Net 7   |      60      | -            |
| Net 8   |      25      | -            |
| Net 9   |       2      | Router link  |
| Net 10  |       2      | Router link  |
| Net 11  |       2      | Router link  |

> **Total hosts needed:** 491

---

### 2.1 & 2.2: FLSM (Fixed Length Subnet Mask) Solution

The largest network requires 120 hosts.
**Minimum subnet size:** 2^7 = 128 addresses (126 usable hosts) -> **/25 mask**

Using /25 for ALL subnets (255.255.255.128):

| Network | Subnet Address    | Usable Range                  | Broadcast       | Hosts Needed | Hosts Available | Unused |
|:-------:|:------------------|:------------------------------|:----------------|:------------:|:---------------:|:------:|
| Net 1   | 172.16.0.0/25     | 172.16.0.1 - 172.16.0.126     | 172.16.0.127    |     120      |       126       |    6   |
| Net 2   | 172.16.0.128/25   | 172.16.0.129 - 172.16.0.254   | 172.16.0.255    |      35      |       126       |   91   |
| Net 3   | 172.16.1.0/25     | 172.16.1.1 - 172.16.1.126     | 172.16.1.127    |      50      |       126       |   76   |
| Net 4   | 172.16.1.128/25   | 172.16.1.129 - 172.16.1.254   | 172.16.1.255    |      80      |       126       |   46   |
| Net 5   | 172.16.2.0/25     | 172.16.2.1 - 172.16.2.126     | 172.16.2.127    |      75      |       126       |   51   |
| Net 6   | 172.16.2.128/25   | 172.16.2.129 - 172.16.2.254   | 172.16.2.255    |      40      |       126       |   86   |
| Net 7   | 172.16.3.0/25     | 172.16.3.1 - 172.16.3.126     | 172.16.3.127    |      60      |       126       |   66   |
| Net 8   | 172.16.3.128/25   | 172.16.3.129 - 172.16.3.254   | 172.16.3.255    |      25      |       126       |  101   |
| Net 9   | 172.16.4.0/25     | 172.16.4.1 - 172.16.4.126     | 172.16.4.127    |       2      |       126       |  124   |
| Net 10  | 172.16.4.128/25   | 172.16.4.129 - 172.16.4.254   | 172.16.4.255    |       2      |       126       |  124   |
| Net 11  | 172.16.5.0/25     | 172.16.5.1 - 172.16.5.126     | 172.16.5.127    |       2      |       126       |  124   |

> **FLSM Total addresses used:** 11 x 128 = **1,408**
> **FLSM Total addresses wasted:** **895**

---

### 2.3: VLSM (Variable Length Subnet Mask) Solution - OPTIONAL

Sort networks by size (largest first) and assign appropriate masks:

| Hosts Needed | Minimum Size | Mask | Available Hosts |
|:------------:|:------------:|:----:|:---------------:|
|     120      |     128      | /25  |       126       |
|      80      |     128      | /25  |       126       |
|      75      |     128      | /25  |       126       |
|      60      |      64      | /26  |        62       |
|      50      |      64      | /26  |        62       |
|      40      |      64      | /26  |        62       |
|      35      |      64      | /26  |        62       |
|      25      |      32      | /27  |        30       |
|       2      |       4      | /30  |         2       |
|       2      |       4      | /30  |         2       |
|       2      |       4      | /30  |         2       |

---

#### VLSM Allocation:

| Network | Subnet Address      | Usable Range                    | Broadcast        | Hosts Needed | Hosts Available | Unused |
|:-------:|:--------------------|:--------------------------------|:-----------------|:------------:|:---------------:|:------:|
| Net 1   | 172.16.0.0/25       | 172.16.0.1 - 172.16.0.126       | 172.16.0.127     |     120      |       126       |    6   |
| Net 4   | 172.16.0.128/25     | 172.16.0.129 - 172.16.0.254     | 172.16.0.255     |      80      |       126       |   46   |
| Net 5   | 172.16.1.0/25       | 172.16.1.1 - 172.16.1.126       | 172.16.1.127     |      75      |       126       |   51   |
| Net 7   | 172.16.1.128/26     | 172.16.1.129 - 172.16.1.190     | 172.16.1.191     |      60      |        62       |    2   |
| Net 3   | 172.16.1.192/26     | 172.16.1.193 - 172.16.1.254     | 172.16.1.255     |      50      |        62       |   12   |
| Net 6   | 172.16.2.0/26       | 172.16.2.1 - 172.16.2.62        | 172.16.2.63      |      40      |        62       |   22   |
| Net 2   | 172.16.2.64/26      | 172.16.2.65 - 172.16.2.126      | 172.16.2.127     |      35      |        62       |   27   |
| Net 8   | 172.16.2.128/27     | 172.16.2.129 - 172.16.2.158     | 172.16.2.159     |      25      |        30       |    5   |
| Net 9   | 172.16.2.160/30     | 172.16.2.161 - 172.16.2.162     | 172.16.2.163     |       2      |         2       |    0   |
| Net 10  | 172.16.2.164/30     | 172.16.2.165 - 172.16.2.166     | 172.16.2.167     |       2      |         2       |    0   |
| Net 11  | 172.16.2.168/30     | 172.16.2.169 - 172.16.2.170     | 172.16.2.171     |       2      |         2       |    0   |

> **VLSM Total addresses used:** 3x128 + 4x64 + 1x32 + 3x4 = 384 + 256 + 32 + 12 = **684**
> **VLSM Total addresses wasted:** **171**

---

### COMPARISON: FLSM vs VLSM

| Method | Total Addresses Used | Total Addresses Wasted | Efficiency |
|:------:|:--------------------:|:----------------------:|:----------:|
| FLSM   |        1,408         |          895           |   36.4%    |
| VLSM   |          684         |          171           |   75.0%    |

> **VLSM is more efficient, saving 724 addresses compared to FLSM.**
