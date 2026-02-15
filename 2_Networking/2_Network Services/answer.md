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

![Wireshark Main Interface](../../docs/images/2_Networking/2_Network%20Services/Wireshark%20main%20interface%20after%20installation.png)

---

## TASK 2: Run the program and enable packet capture

**Steps:**
1. Open Wireshark
2. Select your active network interface (e.g., Ethernet, Wi-Fi)
3. Click the blue shark fin icon or double-click the interface to start capture

**Expected Result:**
Packets start appearing in the packet list pane.

![Interface Selection and Capture Starting](../../docs/images/2_Networking/2_Network%20Services/Interface%20selection%20and%20capture%20starting.png)

---

## TASK 3-5: Visit epam.com, navigate, and close browser

**Steps:**
1. Open web browser
2. Navigate to https://epam.com
3. Click on various tabs (Services, Industries, etc.)
4. Close the browser window

**Expected Result:**
Browser displays EPAM website with visible navigation.

![Browser Showing EPAM Website](../../docs/images/2_Networking/2_Network%20Services/Browser%20showing%20epam.com%20website.png)

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

![Command Prompt Ping Results](../../docs/images/2_Networking/2_Network%20Services/Command%20Prompt%20with%20ping%208.8.8.8%20results.png)

---

## TASK 7: Stop packet capture

**Steps:**
1. Return to Wireshark
2. Click the red square "Stop" button in the toolbar

**Expected Result:**
Packet capture stops, all captured packets visible in list.

![Wireshark Captured Packets](../../docs/images/2_Networking/2_Network%20Services/Wireshark%20Captured%20Packets.png)

---

## TASK 8: Determine the size of the smallest and largest packet

**Method:**
1. Go to `Statistics > Capture File Properties`
   OR
2. Go to `Statistics > Packet Lengths`

**Look for:**
- Smallest packet: Usually ARP or TCP ACK packets (~54-60 bytes)
- Largest packet: Usually HTTP/HTTPS data packets (up to 1514 bytes for Ethernet)

**Answer:**

| Packet Type     | Size       |
|:----------------|:-----------|
| Smallest packet | 42 bytes   |
| Largest packet  | 65534 bytes|

![Statistics Packet Lengths](../../docs/images/2_Networking/2_Network%20Services/Statistics%20Packet%20Lengths.png)

---

## TASK 9: Determine which packet length is the most common

**Method:**
1. Go to `Statistics > Packet Lengths`
2. Look at the "Count" column to find the most frequent length range

**Answer:**
- Most common packet length: **1280–2559 bytes → %41,62** (TCP ACK and small packets)
- This is typical because TCP acknowledgments are frequent and small

![Statistics Packet Lengths Most Common](../../docs/images/2_Networking/2_Network%20Services/Statistics%20Packet%20Lengths%20Most%20Common.png)

---

## TASK 10: To which public IP address was the most IP traffic transmitted?

**Method:**
1. Go to `Statistics > Endpoints`
2. Select the **IPv4** tab
3. Sort by "Tx Bytes" (transmitted bytes) column - click column header
4. Find the public IP with highest transmitted bytes

**Answer:**
- Most traffic transmitted to: *[34.36.57.103 server]*
- This makes sense because we browsed epam.com website

> **Note:** Ignore private IPs (10.x.x.x, 172.16-31.x.x, 192.168.x.x)

![Statistics Endpoints IPv4 Tx Bytes](../../docs/images/2_Networking/2_Network%20Services/Statistics%20Endpoints%20IPv4%20Tx%20Bytes.png)

---

## TASK 11: From which public IP address was the most IP traffic received?

**Method:**
1. Go to `Statistics > Endpoints`
2. Select the **IPv4** tab
3. Sort by "Rx Bytes" (received bytes) column - click column header
4. Find the public IP with highest received bytes

**Answer:**
- Most traffic received from: *[IP address of 34.36.57.103 server]*
- Web browsing involves downloading more data than uploading

![Statistics Endpoints IPv4 Rx Bytes](../../docs/images/2_Networking/2_Network%20Services/Statistics%20Endpoints%20IPv4%20Rx%20Bytes.png)

---

## TASK 12: Determine the percentage of TCP, UDP, and ICMP packets

**Method:**
1. Go to `Statistics > Protocol Hierarchy`
2. Find TCP, UDP, and ICMP in the list
3. Note the percentage for each

**Answer:**

| Protocol |  Packets  | Percentage |
|:--------:|:---------:|:----------:|
| TCP      | ≈ 278,089 |  ≈ 53.2%   |
| UDP      | ≈ 244,800 |  ≈ 46.8%   |
| ICMP     | 8         |  ~0.01%    |

**Explanation:**
- **TCP** is highest because HTTP/HTTPS (web browsing) uses TCP
- **UDP** is used for DNS queries and some background services
- **ICMP** is low because we only sent 4 ping requests (8 packets total: 4 request + 4 reply)

> **Note:** HTTPS traffic was encrypted (TLS over TCP 443), therefore application payload was not readable in Wireshark.

![Statistics Protocol Hierarchy](../../docs/images/2_Networking/2_Network%20Services/Statistics%20Protocol%20Hierarchy.png)

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

![Wireshark ICMP Filter](../../docs/images/2_Networking/2_Network%20Services/Wireshark%20with%20icmp%20filter%20applied.png)

---

## TASK 14: Find MAC and IP addresses in ICMP packet header

**Method:**
1. With ICMP filter active, click on any ICMP packet
2. Expand "Ethernet II" section for MAC addresses
3. Expand "Internet Protocol Version 4" section for IP addresses

**Answer:**

### ETHERNET II (Layer 2):

| Field           | Value               |
|:----------------|:--------------------|
| Source MAC      | 80:2d:1a:03:b2:18   |
| Destination MAC | 5a:8d:fe:36:aa:4c   |

> **Note:** Destination MAC is usually your router's MAC (default gateway)

### INTERNET PROTOCOL (Layer 3):

| Field          | Value           |
|:---------------|:----------------|
| Source IP      | 8.8.8.8         |
| Destination IP | 192.168.1.16    |

![Packet Details Ethernet and IPv4](../../docs/images/2_Networking/2_Network%20Services/Packet%20details%20Ethernet%20II%20and%20IPv4%20headers%20expanded.png)

---

## TASK 15: Find Echo Request/Reply pair and explain TTL difference

**Method:**
1. With ICMP filter active, find packets labeled:
   - "Echo (ping) request"
   - "Echo (ping) reply"
2. Click on each and expand "Internet Protocol Version 4"
3. Find the TTL (Time To Live) field

**Answer:**

| Packet Type   | TTL |
|:--------------|:---:|
| Echo Request  | 128 |
| Echo Reply    | 109 |

### EXPLANATION OF TTL DIFFERENCE:

- **Echo Request TTL (128):** This is the initial TTL set by Windows (Windows default = 128, Linux default = 64)
- **Echo Reply TTL (109):** The reply packet started with TTL=128 at Google's server, but each router it passed through decremented the TTL by 1

**Calculation:**
- If reply TTL = 109, and assuming Google uses TTL=128
- Number of hops = 128 - 109 = **19 routers** between you and 8.8.8.8

**Purpose of TTL:**
- Prevents packets from circulating forever in case of routing loops
- Each router decrements TTL by 1
- When TTL reaches 0, the packet is discarded

![Echo Request TTL](../../docs/images/2_Networking/2_Network%20Services/Echo%20Request%20packet%20with%20TTL%20field%20visible.png)

![Echo Reply TTL](../../docs/images/2_Networking/2_Network%20Services/Echo%20Reply%20packet%20with%20TTL%20field%20visible.png)

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
tcp.flags.syn == 1 && tcp.flags.ack == 0
```

![Wireshark TCP SYN Filter](../../docs/images/2_Networking/2_Network%20Services/Wireshark%20TCP%20SYN%20Filter.png)

---

## TASK 17: Find TCP segment fields and explain their purpose

**Method:**
1. With SYN filter active, click on any TCP SYN packet
2. Expand "Transmission Control Protocol" section
3. Find the required fields

**Answer:**

| Field                  | Value  |
|:-----------------------|:--------------:|
| Source Port            | 61790          |
| Destination Port       | 443 (HTTPS)    |
| Window Size            | 65535          |
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
- Window size 65535 means "I can receive up to 65535 bytes"

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

![TCP Header Fields](../../docs/images/2_Networking/2_Network%20Services/TCP%20header%20expanded%20showing%20all%20required%20fields.png)
