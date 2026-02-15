# PRACTICAL TASK ON TRAFFIC ANALYSIS - ANSWERS

---

## TASK 1: Install Wireshark packet analyzer and see how it works

**Steps:**
1. Download Wireshark from https://www.wireshark.org/download.html
2. Run the installer and follow the installation wizard
3. Install WinPcap/Npcap when prompted (required for packet capture)
4. Launch Wireshark after installation

**Expected Result:**
Wireshark main window opens showing list of available network interfaces.

> **SCREENSHOT 1**
>
> Wireshark main interface showing:
> - Welcome screen with network interfaces listed
> - Interface names (Ethernet, Wi-Fi, etc.)
> - Traffic activity graphs next to each interface

---

## TASK 2: Run the program and enable packet capture

**Steps:**
1. Open Wireshark
2. Select your active network interface (e.g., Ethernet, Wi-Fi)
3. Click the blue shark fin icon or double-click the interface to start capture

**Expected Result:**
Packets start appearing in the packet list pane.

> **SCREENSHOT 2**
>
> Wireshark capture starting showing:
> - Selected interface highlighted
> - Blue shark fin icon clicked OR interface double-clicked
> - Packets beginning to appear in the list

---

## TASK 3-5: Visit epam.com, navigate, and close browser

**Steps:**
1. Open web browser
2. Navigate to https://epam.com
3. Click on various tabs (Services, Industries, etc.)
4. Close the browser window

**Expected Result:**
Browser displays EPAM website with visible navigation.

> **SCREENSHOT 3**
>
> Browser window showing:
> - URL bar with "epam.com" or "https://www.epam.com"
> - EPAM website homepage loaded
> - Navigation tabs visible (Services, Industries, etc.)

---

## TASK 6: Run ping 8.8.8.8 command

**Steps:**
1. Open Command Prompt (cmd)
2. Type: `ping 8.8.8.8`
3. Wait for the ping to complete (4 packets by default on Windows)

**Command:**
```cmd
ping 8.8.8.8
```

**Expected Output:**
```
Pinging 8.8.8.8 with 32 bytes of data:
Reply from 8.8.8.8: bytes=32 time=15ms TTL=117
Reply from 8.8.8.8: bytes=32 time=14ms TTL=117
Reply from 8.8.8.8: bytes=32 time=16ms TTL=117
Reply from 8.8.8.8: bytes=32 time=15ms TTL=117

Ping statistics for 8.8.8.8:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 14ms, Maximum = 16ms, Average = 15ms
```

> **SCREENSHOT 4**
>
> Command Prompt showing:
> - "ping 8.8.8.8" command typed
> - 4 reply lines from 8.8.8.8
> - Ping statistics (Sent=4, Received=4, Lost=0)

---

## TASK 7: Stop packet capture

**Steps:**
1. Return to Wireshark
2. Click the red square "Stop" button in the toolbar

**Expected Result:**
Packet capture stops, all captured packets visible in list.

> **SCREENSHOT 5**
>
> Wireshark window showing:
> - Red stop button in toolbar
> - List of captured packets (hundreds/thousands of packets)
> - Packet details visible when one is selected

---

## TASK 8: Determine the size of the smallest and largest packet

**Method:**
1. Go to `Statistics > Capture File Properties`
   OR
2. Go to `Statistics > Packet Lengths`

**Look for:**
- Smallest packet: Usually ARP or TCP ACK packets (~54-60 bytes)
- Largest packet: Usually HTTP/HTTPS data packets (up to 1514 bytes for Ethernet)

**Example Answer:**

| Packet Type     | Size       |
|:----------------|:-----------|
| Smallest packet | 54 bytes   |
| Largest packet  | 1514 bytes |

> **SCREENSHOT 6**
>
> Statistics > Packet Lengths window showing:
> - Packet length distribution table
> - "Topic / Item" column with ranges (0-19, 20-39, 40-79, etc.)
> - "Count" column showing number of packets in each range
> - Smallest size visible (e.g., 54 bytes)
> - Largest size visible (e.g., 1514 bytes)

---

## TASK 9: Determine which packet length is the most common

**Method:**
1. Go to `Statistics > Packet Lengths`
2. Look at the "Count" column to find the most frequent length range

**Example Answer:**
- Most common packet length: **54-128 bytes** (TCP ACK and small packets)
- This is typical because TCP acknowledgments are frequent and small

> **SCREENSHOT 7**
>
> Statistics > Packet Lengths window showing:
> - Row with highest "Count" value highlighted/marked
> - Most common packet length range visible
> - Percentage column showing highest percentage

---

## TASK 10: To which public IP address was the most IP traffic transmitted?

**Method:**
1. Go to `Statistics > Endpoints`
2. Select the **IPv4** tab
3. Sort by "Tx Bytes" (transmitted bytes) column - click column header
4. Find the public IP with highest transmitted bytes

**Example Answer:**
- Most traffic transmitted to: *[IP address of epam.com server]*
- This makes sense because we browsed epam.com website

> **Note:** Ignore private IPs (10.x.x.x, 172.16-31.x.x, 192.168.x.x)

> **SCREENSHOT 8**
>
> Statistics > Endpoints > IPv4 tab showing:
> - IPv4 tab selected
> - List sorted by "Tx Bytes" column (descending)
> - Public IP with highest Tx Bytes at top
> - Tx Bytes value visible for that IP

---

## TASK 11: From which public IP address was the most IP traffic received?

**Method:**
1. Go to `Statistics > Endpoints`
2. Select the **IPv4** tab
3. Sort by "Rx Bytes" (received bytes) column - click column header
4. Find the public IP with highest received bytes

**Example Answer:**
- Most traffic received from: *[IP address of epam.com server]*
- Web browsing involves downloading more data than uploading

> **SCREENSHOT 9**
>
> Statistics > Endpoints > IPv4 tab showing:
> - IPv4 tab selected
> - List sorted by "Rx Bytes" column (descending)
> - Public IP with highest Rx Bytes at top
> - Rx Bytes value visible for that IP

---

## TASK 12: Determine the percentage of TCP, UDP, and ICMP packets

**Method:**
1. Go to `Statistics > Protocol Hierarchy`
2. Find TCP, UDP, and ICMP in the list
3. Note the percentage for each

**Example Answer:**

| Protocol | Packets | Percentage |
|:--------:|:-------:|:----------:|
| TCP      | XXXX    |  ~85-95%   |
| UDP      | XXX     |  ~3-10%    |
| ICMP     | 8       |  ~0.1-1%   |

**Explanation:**
- **TCP** is highest because HTTP/HTTPS (web browsing) uses TCP
- **UDP** is used for DNS queries and some background services
- **ICMP** is low because we only sent 4 ping requests (8 packets total: 4 request + 4 reply)

> **Note:** HTTPS traffic was encrypted (TLS over TCP 443), therefore application payload was not readable in Wireshark.

> **SCREENSHOT 10**
>
> Statistics > Protocol Hierarchy window showing:
> - Protocol tree expanded
> - TCP row with packet count and percentage visible
> - UDP row with packet count and percentage visible
> - ICMP row with packet count and percentage visible
> - Percentage column clearly showing values

---

## TASK 13: Filter ICMP packets

**Method:**
1. In the display filter bar at the top, type: `icmp`
2. Press Enter

This will show only ICMP packets (ping requests and replies)

**Filter syntax:**
```
icmp
```

**Expected Result:**
Only 8 packets shown (4 Echo Request + 4 Echo Reply)

> **SCREENSHOT 11**
>
> Wireshark main window showing:
> - Filter bar with "icmp" typed
> - Green filter bar (valid filter)
> - Only ICMP packets in the list (~8 packets)
> - "Echo (ping) request" and "Echo (ping) reply" in Info column

---

## TASK 14: Find MAC and IP addresses in ICMP packet header

**Method:**
1. With ICMP filter active, click on any ICMP packet
2. Expand "Ethernet II" section for MAC addresses
3. Expand "Internet Protocol Version 4" section for IP addresses

**Example Answer:**

### ETHERNET II (Layer 2):

| Field           | Example Value       |
|:----------------|:--------------------|
| Source MAC      | aa:bb:cc:dd:ee:ff   |
| Destination MAC | 11:22:33:44:55:66   |

> **Note:** Destination MAC is usually your router's MAC (default gateway)

### INTERNET PROTOCOL (Layer 3):

| Field          | Example Value   |
|:---------------|:----------------|
| Source IP      | 192.168.1.100   |
| Destination IP | 8.8.8.8         |

> **SCREENSHOT 12**
>
> Wireshark packet details pane showing:
> - ICMP packet selected in top pane
> - "Ethernet II" section EXPANDED showing:
>   - Source: [your MAC address]
>   - Destination: [router MAC address]
> - "Internet Protocol Version 4" section EXPANDED showing:
>   - Source: [your IP address]
>   - Destination: 8.8.8.8

---

## TASK 15: Find Echo Request/Reply pair and explain TTL difference

**Method:**
1. With ICMP filter active, find packets labeled:
   - "Echo (ping) request"
   - "Echo (ping) reply"
2. Click on each and expand "Internet Protocol Version 4"
3. Find the TTL (Time To Live) field

**Example Answer:**

| Packet Type   | TTL |
|:--------------|:---:|
| Echo Request  | 128 |
| Echo Reply    | 115 |

### EXPLANATION OF TTL DIFFERENCE:

- **Echo Request TTL (128):** This is the initial TTL set by Windows (Windows default = 128, Linux default = 64)
- **Echo Reply TTL (115):** The reply packet started with TTL=128 at Google's server, but each router it passed through decremented the TTL by 1

**Calculation:**
- If reply TTL = 115, and assuming Google uses TTL=128
- Number of hops = 128 - 115 = **13 routers** between you and 8.8.8.8

**Purpose of TTL:**
- Prevents packets from circulating forever in case of routing loops
- Each router decrements TTL by 1
- When TTL reaches 0, the packet is discarded

> **SCREENSHOT 13a**
>
> Echo REQUEST packet showing:
> - "Echo (ping) request" selected in packet list
> - IPv4 section expanded in details pane
> - "Time to Live: 128" field visible and highlighted

> **SCREENSHOT 13b**
>
> Echo REPLY packet showing:
> - "Echo (ping) reply" selected in packet list
> - IPv4 section expanded in details pane
> - "Time to Live: 115" (or similar) field visible and highlighted
> - Note the DIFFERENT TTL value compared to request

---

## TASK 16: Filter TCP segments with SYN flag

**Method:**
1. Clear the current filter
2. In the display filter bar, type: `tcp.flags.syn == 1`
   OR for only SYN (not SYN-ACK): `tcp.flags.syn == 1 && tcp.flags.ack == 0`
3. Press Enter

This shows TCP packets used to initiate connections (3-way handshake)

**Filter syntax:**
```
tcp.flags.syn == 1
```

> **SCREENSHOT 14**
>
> Wireshark main window showing:
> - Filter bar with "tcp.flags.syn == 1" typed
> - Green filter bar (valid filter)
> - Only TCP SYN packets in the list
> - "[SYN]" visible in Info column for packets

---

## TASK 17: Find TCP segment fields and explain their purpose

**Method:**
1. With SYN filter active, click on any TCP SYN packet
2. Expand "Transmission Control Protocol" section
3. Find the required fields

**Example Answer:**

| Field                  | Example Value  |
|:-----------------------|:--------------:|
| Source Port            | 52431          |
| Destination Port       | 443 (HTTPS)    |
| Window Size            | 64240          |
| Sequence Number        | 0 (relative)   |
| Acknowledgment Number  | 0              |

### EXPLANATION OF FIELDS:

#### 1. SOURCE PORT (16 bits)
- Identifies the sending application/process
- Ephemeral port (49152-65535) assigned by OS for client connections
- Used by server to send responses back to correct application

#### 2. DESTINATION PORT (16 bits)
- Identifies the receiving application/service
- Well-known ports: 80 (HTTP), 443 (HTTPS), 22 (SSH), etc.
- Tells the server which service the client wants to connect to

#### 3. WINDOW SIZE (16 bits)
- Flow control mechanism
- Indicates how many bytes the sender can receive before needing acknowledgment
- Prevents fast sender from overwhelming slow receiver
- Example: Window size 64240 means "I can receive up to 64240 bytes"

#### 4. SEQUENCE NUMBER (32 bits)
- Identifies the position of data in the byte stream
- Used to reassemble data in correct order
- In SYN packet: Initial Sequence Number (ISN), randomly generated
- Subsequent packets: ISN + number of bytes sent

#### 5. ACKNOWLEDGMENT NUMBER (32 bits)
- Indicates the next sequence number the sender expects to receive
- Confirms receipt of all bytes up to this number - 1
- In SYN packet: Usually 0 (no data acknowledged yet)
- In SYN-ACK: Set to client's ISN + 1

### TCP 3-WAY HANDSHAKE:

| Step | Direction         | Flags   | Details           |
|:----:|:------------------|:--------|:------------------|
| 1    | Client -> Server  | SYN     | Seq=X             |
| 2    | Server -> Client  | SYN-ACK | Seq=Y, Ack=X+1    |
| 3    | Client -> Server  | ACK     | Seq=X+1, Ack=Y+1  |

> **SCREENSHOT 15**
>
> Wireshark packet details showing:
> - TCP SYN packet selected in top pane
> - "Transmission Control Protocol" section EXPANDED showing:
>   - Source Port: [port number]
>   - Destination Port: [port number, e.g., 443]
>   - Sequence Number: [number]
>   - Acknowledgment Number: [number]
>   - Window size: [number]
> - All fields clearly visible

---

## SCREENSHOTS CHECKLIST

Print this checklist and mark each screenshot as you take it:

| #    | Screenshot Description                                        | Done |
|:----:|:--------------------------------------------------------------|:----:|
| 1    | Wireshark main interface after installation                   | [ ]  |
| 2    | Interface selection and capture starting                      | [ ]  |
| 3    | Browser showing epam.com website                              | [ ]  |
| 4    | Command Prompt with ping 8.8.8.8 results                      | [ ]  |
| 5    | Wireshark with captured packets (after stop)                  | [ ]  |
| 6    | Statistics > Packet Lengths (smallest/largest)                | [ ]  |
| 7    | Statistics > Packet Lengths (most common - can combine with 6)| [ ]  |
| 8    | Statistics > Endpoints > IPv4 (sorted by Tx Bytes)            | [ ]  |
| 9    | Statistics > Endpoints > IPv4 (sorted by Rx Bytes)            | [ ]  |
| 10   | Statistics > Protocol Hierarchy (TCP, UDP, ICMP percentages)  | [ ]  |
| 11   | Wireshark with "icmp" filter applied                          | [ ]  |
| 12   | Packet details: Ethernet II and IPv4 headers expanded         | [ ]  |
| 13a  | Echo Request packet with TTL field visible                    | [ ]  |
| 13b  | Echo Reply packet with TTL field visible                      | [ ]  |
| 14   | Wireshark with "tcp.flags.syn == 1" filter applied            | [ ]  |
| 15   | TCP header expanded showing all required fields               | [ ]  |

> **TOTAL: 15-16 Screenshots Required**
