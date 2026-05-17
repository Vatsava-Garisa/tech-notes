# IP Addresses (IPv4 and IPv6): Complete DevOps Guide

## 1. Introduction

An **IP Address** is a unique numerical identifier assigned to every device connected to a network. Think of it as a postal address for your computer or server. Just like every house has a unique street address so mail carriers can deliver packages, every device on the internet has a unique IP address so data packets can be delivered to the correct destination.

There are currently two versions:
- **IPv4**: The older, more widely used version (like: 192.168.1.1)
- **IPv6**: The newer version designed for the internet's future (like: 2001:0db8:85a3:0000:0000:8a2e:0370:7334)

### Why You Need to Understand This

As a DevOps Engineer, you'll be:
- Configuring network interfaces on servers
- Setting up cloud infrastructure (AWS, Azure, GCP)
- Managing security groups and firewall rules
- Debugging connectivity issues between services
- Setting up monitoring and alerting
- Managing containerized applications
- Configuring load balancers
- Understanding network architecture

Without solid IP knowledge, you'll struggle with network debugging, cloud infrastructure design, and security configuration.

---

## 2. Why IP Addresses Exist

### Historical Context

In the 1970s, computers were isolated systems. When networking began, researchers faced a fundamental problem: **How do we uniquely identify millions of computers so data can be delivered to the correct machine?**

The solution: Create a hierarchical addressing scheme that:
1. Uniquely identifies every device globally
2. Is logical enough to route data efficiently
3. Is hierarchical (like postal codes) to scale across billions of devices

This is exactly what IP addresses do.

### The Core Problem IP Solves

Imagine you send a data packet from your computer to another server. The packet contains:
- Data you want to send
- Source IP (where it came from)
- Destination IP (where it should go)

Without IP addresses, the network has no way to know:
- Which device should receive this data?
- Where should the response be sent back to?
- How to route data across the internet?

**IP addresses provide this addressing layer.**

---

## 3. Real-World Problem It Solves

### Scenario 1: Node.js Backend Communication

You have a Node.js application running on Server A (IP: 10.0.1.5) that needs to call an API on Server B (IP: 10.0.1.10).

```javascript
// Node.js making an HTTP request
const http = require('http');

const options = {
  hostname: '10.0.1.10',  // This is an IP address
  port: 3000,
  path: '/api/users',
  method: 'GET'
};

const req = http.request(options, (res) => {
  console.log(`STATUS: ${res.statusCode}`);
});

req.end();
```

**Without IP addresses:**
- How does Server A know how to send the data packet?
- How does the network router know which path to use?
- How does Server B know this data is meant for it?

**With IP addresses:**
- Server A sends the packet to 10.0.1.10
- Routers look at the destination IP and forward accordingly
- Server B recognizes 10.0.1.10 as its address and processes it

### Scenario 2: Microservices in Kubernetes

In a Kubernetes cluster, you have:
- Service A at Pod IP: 10.244.1.5
- Service B at Pod IP: 10.244.2.8
- Service C at Pod IP: 10.244.3.12

When Service A needs to call Service B, it uses the IP address to route the request. If there were no IP addresses, the system wouldn't know where to send the traffic.

### Scenario 3: Docker Container Communication

```bash
# Container A tries to communicate with Container B
# Docker assigns each container an IP like 172.17.0.2
# The Docker daemon uses these IPs to enable container-to-container networking
```

---

## 4. Core Concepts

### What is an IP Address?

An IP address is a **32-bit number** (IPv4) or **128-bit number** (IPv6) that identifies a network interface on a device.

#### IPv4 Addresses

An IPv4 address is written as four decimal numbers separated by dots: **a.b.c.d**

- Each number (a, b, c, d) is called an **octet**
- Each octet is a decimal representation of 8 bits
- Range per octet: 0-255
- Total possible addresses: 2^32 = 4,294,967,296

**Example breakdown:**
```
IP Address: 192.168.1.100

Binary breakdown:
192       = 11000000
168       = 10101000
1         = 00000001
100       = 01100100

Full binary: 11000000.10101000.00000001.01100100
```

#### IPv6 Addresses

An IPv6 address is written in hexadecimal with colons: **xxxx:xxxx:xxxx:xxxx:xxxx:xxxx:xxxx:xxxx**

- 128 bits total
- 8 groups of 16 bits (4 hexadecimal digits each)
- Total possible addresses: 2^128 = 340,282,366,920,938,463,463,374,607,431,768,211,456

**Example:**
```
IP Address: 2001:0db8:85a3:0000:0000:8a2e:0370:7334

Hexadecimal breakdown:
2001 = 0010000000000001 (in binary)
0db8 = 0000110110111000 (in binary)
85a3 = 1000010110100011 (in binary)
... and so on
```

### Key Conceptual Difference

| Aspect | IPv4 | IPv6 |
|--------|------|------|
| Address Length | 32 bits | 128 bits |
| Total Addresses | ~4.3 billion | ~340 undecillion |
| Written Format | Decimal dotted (192.168.1.1) | Hexadecimal colon (2001:db8::1) |
| Year Introduced | 1983 | 1995/2017 (widespread) |
| Current Usage | ~95% of internet | Growing (~35% in 2024) |
| Readability | Easy to read | Complex |
| Routing Efficiency | Less efficient | More efficient |

---

## 5. Internal Working

### How IPv4 Works Internally

#### 5.1 Binary Representation and Subnet Masks

Every IP address works with a **subnet mask** that determines which part is the network and which part is the host.

**Example:**

```
IP Address:     192.168.1.100
Subnet Mask:    255.255.255.0

In binary:
IP Address:     11000000.10101000.00000001.01100100
Subnet Mask:    11111111.11111111.11111111.00000000
                ^^^^^^^^^^^^^^^^^^^^^^^^  ^^^^^^^^
                Network portion           Host portion

Network:        192.168.1.0
Broadcast:      192.168.1.255
Usable Hosts:   192.168.1.1 to 192.168.1.254 (254 hosts)
```

**How it works:**
1. Perform bitwise AND operation between IP and subnet mask
2. Bits with 1 in subnet mask = network portion
3. Bits with 0 in subnet mask = host portion

**Why this matters:**

When your Node.js application sends data to 192.168.1.150, the operating system:
1. Performs bitwise AND with subnet mask
2. Determines network is 192.168.1.0
3. Checks: Is 192.168.1.150 on my network?
4. If yes, send directly via ARP (Address Resolution Protocol)
5. If no, send to default gateway/router

#### 5.2 CIDR Notation

Instead of writing two separate numbers (IP + Subnet Mask), we use **CIDR notation**:

```
192.168.1.0/24
  ↑          ↑
  IP         Number of network bits

/24 means: First 24 bits are network, last 8 bits are host
This is equivalent to subnet mask 255.255.255.0
```

**Common CIDR notations:**
```
/8   = 255.0.0.0         (16,777,214 hosts)
/16  = 255.255.0.0       (65,534 hosts)
/24  = 255.255.255.0     (254 hosts)
/25  = 255.255.255.128   (126 hosts)
/30  = 255.255.255.252   (2 hosts) - Used for router-to-router links
/32  = 255.255.255.255   (1 host)  - Single host
```

#### 5.3 Address Resolution Protocol (ARP)

When a device needs to send data to another IP address on the same network, it must first find the **MAC address** (physical address) of that IP. This is where ARP comes in.

**ARP Process:**

```
Device A (192.168.1.100) wants to send data to Device B (192.168.1.150)

1. Device A broadcasts: "Who has 192.168.1.150?"
   ARP Request: Source MAC = A's MAC, Target IP = 192.168.1.150

2. All devices on network receive this broadcast

3. Device B recognizes 192.168.1.150 is its own IP

4. Device B sends back: "I have 192.168.1.150, my MAC is XX:XX:XX:XX:XX:XX"
   ARP Reply: Source MAC = B's MAC, Source IP = 192.168.1.150

5. Device A receives reply and caches: 192.168.1.150 → B's MAC address

6. Now Device A can send data frames directly to B's MAC address
```

**Diagram of ARP:**

```
┌─────────────────────────────────────────────────────────────┐
│ Device A (IP: 192.168.1.100, MAC: AA:AA:AA:AA:AA:AA)       │
│                          │                                   │
│                    "Who has 192.168.1.150?"                 │
│                    (ARP Broadcast)                          │
│                          │                                   │
└──────────────────────────┼───────────────────────────────────┘
                           │
        ┌──────────────────┴──────────────────┬──────────────────┐
        │                                      │                  │
┌───────▼──────────────────┐    ┌─────────────▼─────────┐    ┌──▼────────────┐
│ Device B                 │    │ Device C              │    │ Device D      │
│ IP: 192.168.1.150        │    │ IP: 192.168.1.200     │    │ IP: ...       │
│ MAC: BB:BB:BB:BB:BB:BB   │    │ (Not the target)      │    │ (Not target)  │
│                          │    │ Ignores request       │    │ Ignores req   │
└──────────────────────────┘    └───────────────────────┘    └───────────────┘
        │
        │ Device B recognizes its own IP
        │
        │ "I have 192.168.1.150! Here's my MAC: BB:BB:BB:BB:BB:BB"
        │ (ARP Reply - unicast back to Device A)
        │
┌───────▼──────────────────────────────────────────────────────┐
│ Device A receives ARP reply                                  │
│ Now knows: 192.168.1.150 → BB:BB:BB:BB:BB:BB               │
│ Can send data frames to Device B                            │
└───────────────────────────────────────────────────────────────┘
```

**ARP Cache:**

```bash
# View ARP table on Linux
arp -a
# Output:
# ? (192.168.1.1) at aa:bb:cc:dd:ee:ff [ether] on eth0
# ? (192.168.1.150) at bb:bb:bb:bb:bb:bb [ether] on eth0
```

#### 5.4 IP Routing

When sending data across different networks, routers use the IP address to determine the path:

```
Packet from 192.168.1.100 to 10.0.0.50

1. Device A checks: Is 10.0.0.50 on my network (192.168.1.0/24)?
   Binary AND with subnet mask → Network is 10.0.0.0
   Answer: NO, it's on a different network

2. Device A sends packet to its default gateway (usually 192.168.1.1)
   Gateway = Default exit point from this network

3. Router receives packet, checks its routing table:
   Destination: 10.0.0.50
   Looks for matching routes...
   
   Route 1: 192.168.1.0/24    → Local network
   Route 2: 10.0.0.0/8        → Via router 203.0.113.1
   Route 3: 0.0.0.0/0         → Default route via 203.0.113.254
   
   Match: Route 2 (10.0.0.0/8)
   Next hop: 203.0.113.1

4. Router forwards packet to 203.0.113.1

5. This process repeats at each router until packet reaches 10.0.0.0 network

6. Final router delivers to 10.0.0.50
```

**Linux routing table:**

```bash
route -n
# or
ip route show

# Output example:
# Destination     Gateway         Genmask         Flags Metric Ref Use Iface
# 192.168.1.0     0.0.0.0         255.255.255.0   U     0      0   0   eth0
# 10.0.0.0        192.168.1.1     255.255.0.0     UG    100    0   0   eth0
# 0.0.0.0         192.168.1.1     0.0.0.0         UG    100    0   0   eth0
```

### How IPv6 Works Internally

#### 5.5 IPv6 Address Structure

IPv6 addresses have a hierarchical structure:

```
2001:0db8:0000:0000:0000:0000:0000:0001
└─────┬─────┘ └──────────┬──────────┘ └────────┬────────┘
    Global      Subnet ID       Host/Interface ID
   Routing    (Hierarchical)
   Prefix
```

**IPv6 Address Types:**

1. **Unicast** - Delivered to single host
   ```
   2001:db8::1/64
   ```

2. **Multicast** - Delivered to multiple hosts
   ```
   ff00::/8 (first 8 bits are all 1s)
   ```

3. **Anycast** - Delivered to nearest host
   ```
   Used for load balancing
   ```

#### 5.6 IPv6 Simplification

IPv6 has built-in features that IPv4 requires additions for:

```
IPv4 Approach:
- DHCP for dynamic IP assignment
- ARP for MAC address resolution
- NAT for address translation
- Fragmentation by routers
- Addresses scarce, require careful allocation

IPv6 Approach:
- Stateless Address Autoconfiguration (SLAAC)
- Neighbor Discovery (ND) - replaces ARP
- No NAT (infinite addresses)
- Hosts handle fragmentation
- Addresses abundant
```

**IPv6 Stateless Autoconfiguration:**

```
When an IPv6 host connects to a network:

1. Host generates link-local address automatically
   fe80::1/64 (fe80:: is link-local prefix)

2. Host sends Router Solicitation: "Hello, any routers here?"

3. Router responds with Router Advertisement containing:
   - Network prefix (e.g., 2001:db8::/64)
   - Prefix length
   - Flags and other info

4. Host combines router prefix + own interface ID
   Interface ID: Generated from MAC address (modified EUI-64)
   Example: MAC 00:11:22:33:44:55 → Interface ID ff:fe:33:4455
   Result: 2001:db8::0011:22ff:fe33:4455

5. Host is now configured! No DHCP needed.
```

---

## 6. Step-by-Step Flow

### How a Packet Travels Using IP Addresses

Let's trace a complete flow: Node.js app on Server A making HTTP request to Server B.

#### Scenario Setup

```
Network 1: 192.168.1.0/24
├── Server A (App Server): 192.168.1.10
├── Server B (API Server): 192.168.1.20
└── Router/Gateway: 192.168.1.1

Network 2: 10.0.0.0/24
├── Server C (Database): 10.0.0.50
└── Router/Gateway: 10.0.0.1

Connection between networks via ISP gateway 203.0.113.1
```

#### Scenario: Server A to Server B (Same Network)

**Step 1: Application Layer**

```javascript
// Server A's Node.js code
const http = require('http');

const options = {
  hostname: '192.168.1.20',  // Server B's IP
  port: 3000,
  path: '/api/users',
  method: 'GET'
};

const req = http.request(options, (res) => {
  res.on('data', (chunk) => {
    console.log('Received:', chunk);
  });
});

req.end();
```

**Step 2: Operating System Processes Request**

```
Node.js calls: socket.connect(3000, '192.168.1.20')

OS (Linux kernel):
1. Creates TCP socket
2. Initiates TCP handshake
3. Determines destination IP: 192.168.1.20
4. Checks routing table
5. Sees local network: 192.168.1.0/24
6. Determines: Server B is directly reachable (same network)
```

**Step 3: ARP Resolution**

```
OS: "I need to send to 192.168.1.20, but I need the MAC address"

ARP Process:
1. Checks ARP cache: Is 192.168.1.20 already cached?
   If yes → Use cached MAC, skip to Step 4
   If no → Send ARP request

2. Server A broadcasts:
   "Who has 192.168.1.20? Tell me at 192.168.1.10 (my IP)"

3. Server B recognizes its IP and replies:
   "I have 192.168.1.20. My MAC is BB:BB:BB:BB:BB:BB"

4. Server A caches: 192.168.1.20 → BB:BB:BB:BB:BB:BB

5. ARP entry stays in cache for ~600 seconds (TTL)
```

**Step 4: TCP Handshake (SYN-ACK)**

```
Server A sends:
┌─────────────────────────────────┐
│ Ethernet Frame                  │
├─────────────────────────────────┤
│ Dest MAC: BB:BB:BB:BB:BB:BB     │
│ Src MAC: AA:AA:AA:AA:AA:AA      │
├─────────────────────────────────┤
│ IP Header                       │
├─────────────────────────────────┤
│ Src IP: 192.168.1.10            │
│ Dest IP: 192.168.1.20           │
│ Protocol: TCP (6)               │
├─────────────────────────────────┤
│ TCP Header                      │
├─────────────────────────────────┤
│ Src Port: 54321 (random)        │
│ Dest Port: 3000                 │
│ Flags: SYN                      │
│ Sequence #: 1000                │
└─────────────────────────────────┘

Server B receives frame:
1. Checks destination MAC → Matches its MAC ✓
2. Extracts IP header
3. Checks destination IP → Matches 192.168.1.20 ✓
4. Extracts TCP header
5. Sees SYN flag → Incoming connection request

Server B sends SYN-ACK:
- Src: 192.168.1.20, Dest: 192.168.1.10
- Flags: SYN + ACK
- Acknowledges: 1001
- Own sequence: 2000

Server A sends ACK:
- Src: 192.168.1.10, Dest: 192.168.1.20
- Flags: ACK
- Acknowledges: 2001

Connection established! TCP three-way handshake complete.
```

**Step 5: Data Transfer**

```
Server A sends HTTP GET request:
GET /api/users HTTP/1.1
Host: 192.168.1.20
Connection: close

Wrapped in:
- TCP segment with Src Port 54321, Dest Port 3000
- IP header with Src 192.168.1.10, Dest 192.168.1.20
- Ethernet frame with destination MAC BB:BB:BB:BB:BB:BB

Server B receives:
1. Network card recognizes destination MAC
2. Passes to IP layer
3. IP layer checks destination IP 192.168.1.20 (its IP)
4. Passes to TCP layer
5. TCP sees port 3000 matches listening process
6. Passes to Node.js application
7. Application processes: GET /api/users
```

**Step 6: Response**

```
Server B's Node.js responds:
HTTP/1.1 200 OK
Content-Type: application/json

[{"id": 1, "name": "John"}]

Wrapped with:
- Src IP: 192.168.1.20, Dest IP: 192.168.1.10
- Src Port: 3000, Dest Port: 54321
- Ethernet frame with destination MAC AA:AA:AA:AA:AA:AA
```

**Step 7: Application Receives Data**

```javascript
// Server A's Node.js application
res.on('data', (chunk) => {
  console.log('Received:', chunk);
  // Output: Received: [{"id": 1, "name": "John"}]
});
```

#### Scenario: Server A to Server C (Different Network)

```
Server A: 192.168.1.10 wants to reach Server C: 10.0.0.50
```

**Step 1-2: Same as before** (Application + OS)

**Step 3: Routing Decision**

```
OS checks routing table:
Route 1: 192.168.1.0/24    → Direct (eth0)
Route 2: 10.0.0.0/8        → Via gateway 192.168.1.1
Route 3: 0.0.0.0/0         → Default route 192.168.1.1

Destination: 10.0.0.50
Matches Route 2: 10.0.0.0/8

Decision: Send to gateway 192.168.1.1
```

**Step 4: Gateway Resolution**

```
Server A: "I need to send to 192.168.1.1 (gateway)"

ARP Process:
1. Sends ARP: "Who has 192.168.1.1?"
2. Gateway (192.168.1.1) responds with its MAC: GW:GW:GW:GW:GW:GW
3. Server A now knows: 192.168.1.1 → GW:GW:GW:GW:GW:GW
```

**Step 5: Packet to Gateway**

```
Server A sends:
┌─────────────────────────────────┐
│ Ethernet: Dest MAC = GW MAC     │
├─────────────────────────────────┤
│ IP Header:                      │
│ Src: 192.168.1.10               │
│ Dest: 10.0.0.50                 │ ← Still the final destination!
│ TTL: 64                         │
├─────────────────────────────────┤
│ TCP/Data                        │
└─────────────────────────────────┘

Key point: IP destination doesn't change!
           Only ethernet MAC changes (to gateway's MAC)
```

**Step 6: Gateway Routing**

```
Gateway receives frame:
1. Checks destination MAC → Its own MAC ✓
2. Extracts IP header
3. Checks destination IP → 10.0.0.50 (not its own IP)
4. Checks routing table for 10.0.0.50
5. Route: 10.0.0.0/8 via 10.0.0.1
6. Decrements TTL: 64 → 63
7. Forwards to next gateway at 10.0.0.1
```

**Step 7: Path Through Internet**

```
Packet path:
Server A (192.168.1.10)
    ↓
Router 192.168.1.1 [Network 1]
    ↓
ISP Router 203.0.113.1
    ↓
Internet backbone routers
    ↓
ISP Router 203.0.113.254
    ↓
Router 10.0.0.1 [Network 2]
    ↓
Server C (10.0.0.50)

At each hop:
- IP header and TCP ports stay the same
- Ethernet MAC addresses change
- TTL decrements by 1
- If TTL reaches 0 → Packet dropped, ICMP "Time Exceeded" sent back
```

**Step 8: Server C Receives**

```
Gateway 10.0.0.1 forwards to Server C:

Frame sent with:
- Dest MAC: Server C's MAC (resolved via ARP in 10.0.0.0/24 network)
- Src IP: Still 192.168.1.10
- Dest IP: Still 10.0.0.50
- TCP Port: 3000
- TTL: 62

Server C receives and processes as normal
Response sent back via same path
```

---

## 7. Architecture / Diagram

### Complete IP Networking Architecture

```
╔════════════════════════════════════════════════════════════════════╗
║                     INTERNET ARCHITECTURE WITH IPS                 ║
╚════════════════════════════════════════════════════════════════════╝

                        ┌─────────────────┐
                        │   INTERNET      │
                        │  203.0.113.0/24 │
                        └────────┬────────┘
                                 │
                    ┌────────────┴────────────┐
                    │                        │
        ┌───────────▼─────────────┐  ┌──────▼─────────────┐
        │ ISP Router A            │  │ ISP Router B       │
        │ 203.0.113.1             │  │ 203.0.113.2        │
        │ Routes packets          │  │                    │
        └───────────┬─────────────┘  └────────┬───────────┘
                    │                         │
        ┌───────────▼─────────────┐           │
        │ Corporate Gateway       │           │
        │ 192.168.0.1             │           │
        │ WAN: 203.0.113.254      │           │
        │ Connects 2 networks     │           │
        └───┬────────┬────────┬───┘           │
            │        │        │               │
    ┌───────▼─┐  ┌───▼──┐  ┌─▼────────┐      │
    │ Network │  │Switch│  │  Cloud   │      │
    │   1     │  │ / L2 │  │  Router  │      │
    │ 192.168 │  │ Hub  │  │ (AWS NAT)│      │
    │ 1.0/24  │  └──┬───┘  └─┬────────┘      │
    └────┬────┘     │        │               │
         │          │    ┌───▼───────┐       │
         │      ┌───┴────┤ Cloud VPC │       │
    ┌────▼──────▼───┐   │10.0.0.0/16│       │
    │ Network 1     │   │ - Multiple │       │
    │ 192.168.1.0/24│   │   Subnets │       │
    │               │   │ - Security │       │
    ├─────────────────┤  │   Groups  │       │
    │ Server A        │  └─┬─────────┘       │
    │ 192.168.1.10    │    │                 │
    │ eth0: AA:..     │ ┌──┴──────────┐      │
    │                 │ │ Subnet 1    │      │
    │ Server B        │ │ 10.0.1.0/24 │      │
    │ 192.168.1.20    │ ├─────────────┤      │
    │ eth0: BB:..     │ │ Instance A  │      │
    │                 │ │ 10.0.1.5    │      │
    │ Router/GW       │ │ (Running    │      │
    │ 192.168.1.1     │ │ Node.js API)│      │
    │ eth0: GW:..     │ └─────────────┘      │
    │                 │                     │
    │ Default route:  │ ┌──────────────┐    │
    │ via 192.168.1.1 │ │ Subnet 2     │    │
    │ to 203.0.113.254│ │ 10.0.2.0/24  │    │
    └─────────────────┘ ├──────────────┤    │
                        │ RDS Instance │    │
                        │ 10.0.2.100   │    │
                        │ (Database)   │    │
                        └──────────────┘    │
                                           │
        ┌──────────────────────────────────┘
        │
    ┌───▼──────────────┐
    │ External User    │
    │ IP: Variable     │
    │ (e.g., ISP IP)   │
    │ Makes request to │
    │ public IP of     │
    │ your service     │
    └──────────────────┘
```

### IPv4 Address Space Allocation

```
╔════════════════════════════════════════════════════════════╗
║          FULL IPv4 ADDRESS SPACE (32 BITS)                ║
║        From 0.0.0.0 to 255.255.255.255                    ║
╚════════════════════════════════════════════════════════════╝

0.0.0.0/8           │ This Network (0.0.0.0 - 0.255.255.255)
────────────────────┼────────────────────────────────────────
1.0.0.0/8           │ APNIC (Asia-Pacific) allocations
────────────────────┼────────────────────────────────────────
...                 │ Various regional allocations
────────────────────┼────────────────────────────────────────
10.0.0.0/8          │ PRIVATE (RFC 1918)
                    │ 10.0.0.0 - 10.255.255.255
                    │ ~16.7 million hosts
────────────────────┼────────────────────────────────────────
127.0.0.0/8         │ LOOPBACK (127.0.0.0 - 127.255.255.255)
                    │ Used for localhost (127.0.0.1)
────────────────────┼────────────────────────────────────────
169.254.0.0/16      │ LINK-LOCAL (Automatic Private IP)
────────────────────┼────────────────────────────────────────
172.16.0.0/12       │ PRIVATE (RFC 1918)
                    │ 172.16.0.0 - 172.31.255.255
                    │ ~1 million hosts
────────────────────┼────────────────────────────────────────
192.168.0.0/16      │ PRIVATE (RFC 1918)
                    │ 192.168.0.0 - 192.168.255.255
                    │ ~65,000 hosts
────────────────────┼────────────────────────────────────────
224.0.0.0/4         │ MULTICAST (224.0.0.0 - 239.255.255.255)
────────────────────┼────────────────────────────────────────
240.0.0.0/4         │ Reserved for future use
────────────────────┼────────────────────────────────────────
255.255.255.255/32  │ BROADCAST (limited to local network)
```

### Network Classes (Historical - Still Relevant)

```
CLASS A: First octet 0-127
┌─────────────────────────────┐
│ 1 | Network  | Network | Host│
│   | (7 bits) │ (16 bits) │(8)│
└─────────────────────────────┘
Range: 1.0.0.0 - 126.255.255.255
Subnet Mask: 255.0.0.0 (/8)
Example: 10.0.0.1
Supports: 126 networks, 16M hosts each

CLASS B: First octet 128-191
┌─────────────────────────────┐
│ 10 | Network (14 bits) | Host │
│    │                   |(16) │
└─────────────────────────────┘
Range: 128.0.0.0 - 191.255.255.255
Subnet Mask: 255.255.0.0 (/16)
Example: 172.16.0.1
Supports: 16K networks, 65K hosts each

CLASS C: First octet 192-223
┌─────────────────────────────┐
│ 110 | Network (21 bits) | Host│
│     │                   |(8) │
└─────────────────────────────┘
Range: 192.0.0.0 - 223.255.255.255
Subnet Mask: 255.255.255.0 (/24)
Example: 192.168.1.1
Supports: 2M networks, 254 hosts each

CLASS D: First octet 224-239
Multicast addresses
Example: 224.0.0.5, 239.255.255.255

CLASS E: First octet 240-255
Reserved for future use
```

### Subnetting Example

```
Network: 192.168.1.0/24
Subnet Mask: 255.255.255.0

Full breakdown:
┌─────────────────────────────────────────────┐
│ IP: 192.168.1.0/24                          │
│                                             │
│ Available IPs:                              │
│ 192.168.1.0     → Network address           │
│ 192.168.1.1     → Default Gateway           │
│ 192.168.1.2     ┐                           │
│ 192.168.1.3     ├─ Usable host IPs (252)   │
│ ...             │                          │
│ 192.168.1.254   ┘                           │
│ 192.168.1.255   → Broadcast address         │
└─────────────────────────────────────────────┘

Dividing /24 into smaller subnets (/25):
┌──────────────────────┬──────────────────────┐
│ Subnet 1: /25        │ Subnet 2: /25        │
│ 192.168.1.0/25       │ 192.168.1.128/25     │
│                      │                      │
│ Network: .0          │ Network: .128        │
│ Gateway: .1          │ Gateway: .129        │
│ Hosts: .2 to .126    │ Hosts: .130 to .254  │
│ Broadcast: .127      │ Broadcast: .255      │
│                      │                      │
│ 126 usable hosts     │ 126 usable hosts     │
└──────────────────────┴──────────────────────┘
```

### IPv6 Address Structure

```
2001 : 0db8 : 0000 : 0000 : 0000 : 8a2e : 0370 : 7334
└──┬──┘ └──┬──┘ └──────┬──────┘ └──────────┬───────────┘
   │      │           │                   │
   │      │           │             Host portion
   │      │           │             (usually /64)
   │      │    Subnet ID
   │      │    (usually /16)
   │   Global Routing Prefix
   │   (first 3 sections)
Global Routing Prefix
(usually /48 for organizations)

Equivalent compressed form: 2001:db8::8a2e:370:7334
(consecutive zeros replaced with ::, can be used once)
```

---

## 8. Real Production Example

### Example 1: Multi-Tier AWS Application

```
Production Setup: 3-tier Node.js application on AWS

┌─────────────────────────────────────────────────────────┐
│ AWS VPC: 10.0.0.0/16                                   │
├─────────────────────────────────────────────────────────┤
│                                                         │
│ ┌──────────────────────────────────────┐              │
│ │ Public Subnet 1 (AZ: us-east-1a)     │              │
│ │ 10.0.1.0/24                          │              │
│ │ ├─ Load Balancer: 10.0.1.10          │              │
│ │ │  EIP (Elastic IP): 203.0.113.50    │              │
│ │ │  (Maps to Elastic Network Interface)│             │
│ │ │                                     │              │
│ │ └─ NAT Gateway: 10.0.1.20             │              │
│ │    EIP: 203.0.113.51                 │              │
│ └──────────────────────────────────────┘              │
│                                                         │
│ ┌──────────────────────────────────────┐              │
│ │ Private Subnet 1 (AZ: us-east-1a)    │              │
│ │ 10.0.2.0/24                          │              │
│ │ ├─ App Server 1: 10.0.2.15           │              │
│ │ │  (Node.js, port 3000)              │              │
│ │ │  Can reach internet via NAT         │              │
│ │ │                                     │              │
│ │ └─ App Server 2: 10.0.2.16           │              │
│ │    (Node.js, port 3000)              │              │
│ │    Can reach internet via NAT         │              │
│ └──────────────────────────────────────┘              │
│                                                         │
│ ┌──────────────────────────────────────┐              │
│ │ Private Subnet 2 (AZ: us-east-1b)    │              │
│ │ 10.0.3.0/24                          │              │
│ │ └─ RDS Database: 10.0.3.100          │              │
│ │    PostgreSQL, port 5432             │              │
│ │    (Only accessible from within VPC) │              │
│ └──────────────────────────────────────┘              │
│                                                         │
└─────────────────────────────────────────────────────────┘
        │
        ├─ Internet Gateway (IGW)
        │  Allows public subnet to reach internet
        │
        └─ NAT Gateway
           Allows private subnets to reach internet
           (but not other way around)
```

**Traffic Flow:**

```
External User (203.0.113.100) makes request to your app:
203.0.113.100:53421 → 203.0.113.50:80

            ┌─────────────────────────┐
            │ Internet Gateway        │
            │ Translates:             │
            │ Public IP ↔ Private IP  │
            └────────────┬────────────┘
                         │
                         ▼
         ┌───────────────────────────────┐
         │ Load Balancer: 10.0.1.10:80   │
         │ Routes request to app servers │
         └───────┬───────────┬───────────┘
                 │           │
        ┌────────▼──┐   ┌────▼──────────┐
        │ App 1     │   │ App 2         │
        │ 10.0.2.15 │   │ 10.0.2.16     │
        │ :3000     │   │ :3000         │
        └────────┬──┘   └────┬──────────┘
                 │           │
                 └─────┬─────┘
                       ▼
         ┌──────────────────────────────┐
         │ RDS Database: 10.0.3.100     │
         │ PostgreSQL:5432              │
         │ Apps connect via private IP  │
         └──────────────────────────────┘
```

**Example: Node.js Code for This Setup**

```javascript
const express = require('express');
const { Pool } = require('pg');
const app = express();

// Database connection using private IP (10.0.3.100)
const pool = new Pool({
  user: 'admin',
  password: 'secure_password',
  host: '10.0.3.100',  // Private IP within VPC
  port: 5432,
  database: 'app_db'
});

app.get('/api/users', async (req, res) => {
  try {
    // Query database (uses private IP routing within VPC)
    const result = await pool.query('SELECT * FROM users');
    res.json(result.rows);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

// Server listens on private IP
app.listen(3000, '10.0.2.15', () => {
  console.log('Server listening on 10.0.2.15:3000');
  console.log('Accessible via load balancer at 10.0.1.10:80');
  console.log('External users access via 203.0.113.50:80');
});
```

**IP Address Resolution Chain:**

```
External User Request Flow:

1. User opens browser, types: myapp.example.com
   Browser does DNS lookup

2. DNS returns: 203.0.113.50 (public EIP of load balancer)

3. Browser connects to: 203.0.113.50:80

4. Internet Gateway receives packet:
   - Destination: 203.0.113.50 (public IP)
   - Translates to: 10.0.1.10:80 (private IP)

5. Load Balancer receives packet at 10.0.1.10:80

6. Load Balancer decides to route to 10.0.2.15:3000
   - Checks health of app servers
   - Selects: 10.0.2.15:3000

7. App Server (10.0.2.15) receives request:
   - Processes request
   - Needs database: connects to 10.0.3.100:5432

8. Database responds with data

9. App Server sends response back through load balancer

10. Load Balancer sends response back to Internet Gateway
    - Source: 10.0.1.10:80
    - Destination: User's IP

11. Internet Gateway translates:
    - Source: 203.0.113.50:80 (public IP)
    - Destination: User's IP

12. Response reaches user's browser
```

**Routing Tables**

```
Load Balancer (10.0.1.10) Routing Table:
Destination        Gateway     Interface
────────────────────────────────────────
10.0.0.0/16        Local       eth0
203.0.113.0/32     IGW         eth0

App Server (10.0.2.15) Routing Table:
Destination        Gateway     Interface
────────────────────────────────────────
10.0.0.0/16        Local       eth0
0.0.0.0/0          NAT 10.0.1.20 eth0  (for outbound internet)

Database (10.0.3.100) Routing Table:
Destination        Gateway     Interface
────────────────────────────────────────
10.0.0.0/16        Local       eth0
(No internet access - completely private)
```

---

## 9. How Backend Applications Use This

### 9.1 Node.js Application IP Awareness

**Listening on Specific IPs**

```javascript
const http = require('http');

// Bad: Only accessible locally
http.createServer((req, res) => {
  res.writeHead(200);
  res.end('Hello');
}).listen(3000, 'localhost');  // Only 127.0.0.1
// Accessible: Only from same machine

// Good: Accessible from network
http.createServer((req, res) => {
  res.writeHead(200);
  res.end('Hello');
}).listen(3000, '0.0.0.0');  // All interfaces
// Accessible: From any IP that can reach this machine

// Better: Specific interface (production)
http.createServer((req, res) => {
  res.writeHead(200);
  res.end('Hello');
}).listen(3000, '10.0.2.15');  // Only this IP
// Accessible: Only through 10.0.2.15
```

**Getting Client IP Address**

```javascript
const express = require('express');
const app = express();

// Behind a load balancer, client IP is in X-Forwarded-For
app.get('/api/user', (req, res) => {
  // Direct client IP (unreliable behind proxy)
  console.log('Direct IP:', req.connection.remoteAddress);
  // Output: ::ffff:127.0.0.1 (IPv4-mapped IPv6) or 203.0.113.100

  // Actual client IP (from load balancer)
  const clientIP = req.headers['x-forwarded-for']?.split(',')[0] || req.connection.remoteAddress;
  console.log('Client IP:', clientIP);
  // Output: 203.0.113.100

  res.json({ clientIP });
});
```

### 9.2 Making Outbound Connections

```javascript
const http = require('http');

// Connect to external API
const options = {
  hostname: '203.0.113.200',  // External IP
  port: 80,
  path: '/api/data',
  method: 'GET'
};

const req = http.request(options, (res) => {
  console.log(`STATUS: ${res.statusCode}`);
});

req.on('error', (error) => {
  // Common errors related to IP/routing:
  // ECONNREFUSED - Connection refused (wrong IP or port)
  // ENOTFOUND - DNS resolution failed
  // EHOSTUNREACH - Network unreachable
  // ENETUNREACH - Network unreachable
  // ETIMEDOUT - Connection timed out
  console.error('Connection error:', error.code);
});

req.end();
```

**Within VPC Connections**

```javascript
// App server connecting to database (private IPs)
const { Pool } = require('pg');

const pool = new Pool({
  // Private IP - only works within VPC
  host: '10.0.3.100',
  port: 5432,
  database: 'mydb',
  user: 'admin',
  password: 'secret'
});

pool.query('SELECT * FROM users')
  .then(result => console.log(result.rows))
  .catch(err => console.error('DB Error:', err));

// This works because:
// 1. App server (10.0.2.15) and DB (10.0.3.100) are in same VPC
// 2. Network routing allows private IP communication
// 3. Security group allows port 5432 from 10.0.2.15
```

### 9.3 Docker Container Networking

```bash
# Docker assigns IPs from docker0 bridge network
# Default: 172.17.0.0/16

# View container IP
docker inspect my-container | grep IPAddress

# Output:
# "IPAddress": "172.17.0.2"
```

```javascript
// Inside container A (172.17.0.2)
const http = require('http');

const options = {
  hostname: '172.17.0.3',  // Container B's IP
  port: 3000,
  path: '/api/data',
  method: 'GET'
};

const req = http.request(options, (res) => {
  console.log('Connected to container B');
});

req.end();
```

### 9.4 Kubernetes Pod Networking

```javascript
// Inside Kubernetes pod
// Each pod gets its own IP from cluster CIDR
// Default: 10.244.0.0/16

// Service discovery via hostname (not IP)
const http = require('http');

const options = {
  // Hostname translates to pod IP via cluster DNS
  hostname: 'api-service.default.svc.cluster.local',
  port: 3000,
  path: '/users',
  method: 'GET'
};

const req = http.request(options, (res) => {
  // Works because Kubernetes DNS resolves service name to pod IPs
  console.log('Connected to service');
});

req.end();
```

---

## 10. Security Considerations

### 10.1 Public vs. Private IP Security

**Private IP Addresses:**

```
Ranges (RFC 1918):
- 10.0.0.0/8       (10.0.0.0 to 10.255.255.255)
- 172.16.0.0/12    (172.16.0.0 to 172.31.255.255)
- 192.168.0.0/16   (192.168.0.0 to 192.168.255.255)

Security Properties:
✓ Not routable on the internet
✓ Cannot be accessed from outside the network
✓ Can be reused in different organizations
✓ Suitable for internal services (databases, caches)
✗ Not suitable for public-facing services
```

**Public IP Addresses:**

```
All other IPs (not in RFC 1918 ranges)

Security Properties:
✓ Globally unique
✓ Routable on the internet
✓ Can be accessed from anywhere
✗ Need protection (firewalls, security groups)
✗ Target for port scans and attacks
```

### 10.2 IP-Based Access Control

**Bad: No IP filtering**

```javascript
// INSECURE: Accessible from anywhere
app.listen(3000, '0.0.0.0');  // All IPs can connect
```

**Good: Firewall rules**

```bash
# Linux iptables - Allow only specific IP
sudo iptables -A INPUT -p tcp --dport 3000 -s 203.0.113.0/24 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 3000 -j DROP

# AWS Security Group
# Inbound Rule: TCP port 3000, Source: 203.0.113.0/24
```

**Better: Behind load balancer**

```
Public subnet:
└─ Load Balancer: 0.0.0.0:443
   (Public facing, accepts any IP)

Private subnet:
└─ App Server: Only accepts from LB (10.0.1.10)
   Security group: Allow TCP 3000 from 10.0.1.10

Requests: External IP → LB → App Server
App server never exposes to internet directly
```

### 10.3 IP Spoofing

**Attack:**

```
Attacker sends packet:
Source IP: 10.0.1.1 (forged, pretending to be internal server)
Destination IP: 10.0.3.100 (database)
Port: 5432

Database receives packet and trusts the IP
Executes commands thinking it's from trusted internal server
```

**Defense:**

```
1. Unicast Reverse Path Forwarding (uRPF)
   - Router checks: "Is source IP reachable on incoming interface?"
   - If not → Drop packet (likely spoofed)

2. Firewalls with stateful inspection
   - Track connection state
   - Only allow responses to outgoing connections

3. VPCs with strict routing
   - Only route within VPC
   - Prevent external packets with internal IPs

4. Security Groups / Network ACLs
   - Whitelist specific sources
   - Deny everything else
```

### 10.4 IP Enumeration / Reconnaissance

**Attacker's reconnaissance:**

```bash
# Ping sweep to find live hosts
for i in {1..254}; do
  ping -c 1 -W 1 192.168.1.$i &
done

# Port scanning
nmap 192.168.1.0/24

# Finding services
nmap -sV 10.0.0.0/8

# Results: Attacker maps your infrastructure
```

**Defense:**

```bash
# 1. Don't respond to ICMP (ping)
sudo iptables -A INPUT -p icmp --icmp-type echo-request -j DROP

# 2. Rate limit responses
sudo iptables -A INPUT -p tcp --dport 22 -m limit --limit 5/min -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 22 -j DROP

# 3. Hide unnecessary services
# Only expose what's needed on public IPs

# 4. Use security groups to deny all by default
# AWS Security Group: Explicit DENY, whitelist specific sources
```

### 10.5 CIDR Block Overlap

**Problem in multi-VPC environment:**

```
VPC 1 CIDR: 10.0.0.0/16
VPC 2 CIDR: 10.0.0.0/16  (SAME!)

When VPC 1 tries to send to 10.0.0.50:
- Belongs to VPC 1
- But also matches VPC 2's CIDR
- Routing becomes ambiguous
- Packets may go to wrong VPC
```

**Solution:**

```
VPC 1 CIDR: 10.0.0.0/16
VPC 2 CIDR: 10.1.0.0/16  (Different!)
VPC 3 CIDR: 10.2.0.0/16

Rule: Use RFC 1918 ranges wisely
- Plan IP ranges before creating infrastructure
- Document CIDR blocks
- No overlaps between networks you'll peer/connect
```

### 10.6 IPv6 Unique Local Addresses (ULA)

**IPv6 equivalent of private IPs:**

```
Prefix: fc00::/7  or  fd00::/7
Example: fd00:1234:5678::1

More secure than IPv4 private IPs:
- Cryptographically generated
- Unique even without coordination
- Not routable globally
- Can be used across organizations safely
```

---

## 11. Performance Considerations

### 11.1 MTU and Fragmentation

**Maximum Transmission Unit (MTU):**

```
MTU = Maximum size of data in a single frame

Typical values:
- Ethernet: 1500 bytes
- PPP: 1492 bytes
- Jumbo frames: 9000 bytes

Packet larger than MTU:
IPv4: Routers fragment the packet
IPv6: Hosts fragment, routers must drop

Example (IPv4):
Application sends 3000 bytes
├─ IP header: 20 bytes
├─ TCP header: 20 bytes
├─ Payload: 3000 bytes
Total: 3040 bytes

Too large for 1500 MTU!
Router fragments into:
- Fragment 1: 1500 bytes (IP header + 1460 bytes data)
- Fragment 2: 1540 bytes (IP header + 1480 bytes data)

Destination reassembles fragments back to original packet
```

**Performance Impact:**

```
Fragmentation = Extra processing = Slower

If MTU is 1500:
- 1000 byte packets: No fragmentation ✓
- 1500 byte packets: No fragmentation ✓
- 2000 byte packets: Fragmented ✗ (slower)
- 3000 byte packets: Fragmented ✗ (slower)

Solution: Path MTU Discovery (PMTUD)
- Find smallest MTU along path
- Size packets accordingly
- Avoid unnecessary fragmentation
```

**Node.js Context:**

```javascript
const net = require('net');

const server = net.createServer((socket) => {
  // Large response (3000 bytes)
  const largeData = Buffer.alloc(3000);
  
  socket.write(largeData);  // May be fragmented
  socket.end();
});

server.listen(3000);

// Better: Use appropriate buffer sizes
const socket = net.createConnection(3000, '192.168.1.10');

// Write multiple smaller chunks (respects MTU)
for (let i = 0; i < 10; i++) {
  socket.write(Buffer.alloc(1400));  // Fits in one frame
}
```

### 11.2 Subnetting and Broadcast Domains

**Broadcast Domain:**

```
Network: 192.168.1.0/24

Broadcast address: 192.168.1.255
When Host A broadcasts to 192.168.1.255:
- All hosts on 192.168.1.0/24 receive it
- Hosts on 192.168.2.0/24 don't receive it

This is good:
- Limits broadcast traffic
- Each subnet is isolated
- Reduces network congestion

Larger subnets = more broadcast traffic
Example:
/24 = 254 hosts (small broadcast domain)
/16 = 65,534 hosts (huge broadcast domain, lots of broadcasts)
```

**Performance impact:**

```
Scenario: ARP broadcast in /16 network
- One device searches for an IP address
- Broadcasts to all 65,534 hosts
- Each host must process and respond
- Significant network traffic

Same scenario in /24:
- Broadcasts to 254 hosts
- Much less traffic

Best practice: Use appropriate subnet sizes
- Too large: Broadcast storms
- Too small: Inefficient routing
```

### 11.3 Routing Efficiency

**Longest Prefix Match:**

```
Routing table:
0.0.0.0/0           → via ISP (slowest, most general)
10.0.0.0/8          → via local gateway
10.0.1.0/24         → via local gateway (more specific)
10.0.1.128/25       → via different gateway (most specific)

Routing algorithm: Use MOST SPECIFIC match

Packet to 10.0.1.150:
1. Matches 0.0.0.0/0? YES, use ISP
2. Matches 10.0.0.0/8? YES, prefer this
3. Matches 10.0.1.0/24? YES, prefer this
4. Matches 10.0.1.128/25? YES - MOST SPECIFIC
   Use this route → Fastest path

Why it matters:
- More specific routes = better performance
- Direct local routes faster than ISP
- Well-designed routing = optimized traffic
```

### 11.4 IP Address Exhaustion in Subnets

```
Scenario: /25 subnet (126 usable hosts)
Actually running 200 containers

Problem:
- Only 126 IP addresses available
- 74 containers can't get IPs
- Deployment fails

Solution:
- Use larger subnet: /24 (254 hosts)
- Or split across multiple subnets
- Plan growth: Use /22 instead of /24

Kubernetes example:
- Cluster CIDR: 10.0.0.0/16 (65,534 hosts)
- Per-node subnet: /24 (254 pods per node)
- Can support: 65,534 / 254 = 257 nodes
- Growth plan: Use /17 or /18 for larger clusters
```

### 11.5 DNS vs Direct IP Connection

```javascript
// Connection via hostname (involves DNS)
const https = require('https');

https.get('https://api.example.com/users', (res) => {
  console.log(res.statusCode);
});

Process:
1. DNS lookup: api.example.com → 203.0.113.50 (~50ms)
2. TCP connection to 203.0.113.50 (~20ms)
3. TLS handshake (~30ms)
4. HTTP request (~10ms)
Total: ~110ms

// Connection via IP (skips DNS)
https.get('https://203.0.113.50/users', (res) => {
  console.log(res.statusCode);
});

Process:
1. TCP connection to 203.0.113.50 (~20ms)
2. TLS handshake (~30ms)
3. HTTP request (~10ms)
Total: ~60ms

Performance improvement: ~50ms saved per request
Matters for high-traffic applications
```

**DNS Caching:**

```javascript
// After first lookup, subsequent requests use cached IP
// DNS TTL (Time To Live): typically 300 seconds

// Force DNS refresh
dns.lookup('api.example.com', (err, address, family) => {
  console.log('IP:', address);  // Bypasses OS cache
});

// DNS resolver with caching
const dns = require('dns');
const resolver = new dns.Resolver();
resolver.resolve4('api.example.com', (err, addresses) => {
  console.log('IPs:', addresses);
});
```

---

## 12. Common Mistakes

### 12.1 Hardcoding IPs in Configuration

**WRONG:**

```javascript
const DATABASE_HOST = '10.0.3.100';
const API_SERVER = '203.0.113.50';
const CACHE_SERVER = '10.0.4.20';

// Problem: If IP changes, must recompile and redeploy
```

**RIGHT:**

```javascript
// Environment variables or config files
const DATABASE_HOST = process.env.DB_HOST || 'db.internal';
const API_SERVER = process.env.API_SERVER || 'api.example.com';
const CACHE_SERVER = process.env.CACHE_SERVER || 'cache.internal';

// Or use service discovery
const consul = require('consul');
const client = new consul();

client.health.service({
  service: 'database',
  passing: true
}, (err, result) => {
  const dbServer = result[0].Service.Address;
  // Automatically discovers current IP
});
```

### 12.2 Forgetting About IPv4-Mapped IPv6 Addresses

```javascript
const net = require('net');

const server = net.createServer((socket) => {
  // Client IP (WRONG)
  console.log(socket.remoteAddress);
  // Output: ::ffff:192.168.1.100
  // This is an IPv4 address mapped into IPv6 format!

  // Client IP (RIGHT)
  const clientIP = socket.remoteAddress.replace('::ffff:', '');
  console.log('Real IP:', clientIP);
  // Output: 192.168.1.100
});

server.listen(3000);
```

### 12.3 Not Accounting for NAT

```javascript
// Problem: Application tries to connect back to client
const net = require('net');

const server = net.createServer((socket) => {
  // Try to connect back to client
  const clientIP = socket.remoteAddress;
  console.log('Connecting back to:', clientIP);
  
  // If client is behind NAT:
  // clientIP = 10.0.1.100 (internal IP)
  // But we can't connect to 10.0.1.100 from outside
  // Because it's not routable!
});

server.listen(3000);

// Solution: Use callback channel the client established
socket.write('Response data');  // Send back through existing connection
```

### 12.4 Wrong Subnet Mask

```
Given:
Network: 192.168.1.0
Mask: 255.255.255.0 (/24)

Wrong assumption:
"Servers on 192.168.1.0/25 can talk to servers on 192.168.1.128/25"

Reality:
- 192.168.1.0/25: Range 192.168.1.0 - 192.168.1.127
- 192.168.1.128/25: Range 192.168.1.128 - 192.168.1.255
- They share infrastructure but are separate subnets
- May require routing/gateway to communicate
- Security group rules apply separately
```

### 12.5 Default Gateway Not Set

```bash
# Linux server with no default route
route -n

Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref Use Iface
192.168.1.0     0.0.0.0         255.255.255.0   U     0      0   0 eth0

# No default route! (0.0.0.0/0)

# Application tries to reach 10.0.0.50
# Packet goes to 192.168.1.1 gateway
# Gateway doesn't know route to 10.0.0.0
# Packet dropped!

# FIX:
route add default gw 192.168.1.1 eth0

# Now:
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref Use Iface
192.168.1.0     0.0.0.0         255.255.255.0   U     0      0   0 eth0
0.0.0.0         192.168.1.1     0.0.0.0         UG    100    0   0 eth0

# Works now!
```

### 12.6 Assuming All 255 Addresses are Usable

```
Network: 192.168.1.0/24

Total addresses: 256 (0-255)
BUT:

192.168.1.0     = Network address (not usable)
192.168.1.255   = Broadcast address (not usable)

Usable addresses: 256 - 2 = 254

Common mistake:
```
Think: "I'll allocate 100 servers in this /24"
Actually allocate: 192.168.1.1 to 192.168.1.100
That's only 100 IPs, leaving 154 unused! Wasteful.

Better: Use /25 for 126 hosts, /26 for 62 hosts
```

### 12.7 Mixing Up Private and Public in Config

```javascript
// WRONG: Using private IP in public-facing config
const config = {
  api_server: '10.0.2.15:3000',  // Only works internally!
  // External users can't access 10.0.2.15
};

// RIGHT: Use public IP/domain for external clients
const config = {
  api_server_internal: '10.0.2.15:3000',  // For pods/servers in VPC
  api_server_external: 'api.example.com:443',  // For external users
};

// In Docker:
// External users → Load Balancer (public IP) → Your app (private IP)
```

---

## 13. Debugging and Troubleshooting

### 13.1 Connection Refused Errors

```javascript
// Node.js error
const http = require('http');

http.get('http://10.0.2.20:3000/api', (res) => {
  console.log('Success');
}).on('error', (err) => {
  console.error('Error:', err.code);
  // ECONNREFUSED: Connection refused
});
```

**Diagnosis steps:**

```bash
# 1. Is the server running?
ps aux | grep node
# Look for process listening on port 3000

# 2. Check listening ports
netstat -tlnp | grep 3000
# Output: tcp  0  0 10.0.2.20:3000   0.0.0.0:*  LISTEN  1234/node

# If nothing shows: Server isn't listening on 3000

# 3. Check if listening on all interfaces (0.0.0.0)
netstat -tlnp | grep node
# tcp  0  0 0.0.0.0:3000  0.0.0.0:*  LISTEN
# Good: Listening on all interfaces

# tcp  0  0 127.0.0.1:3000  0.0.0.0:*  LISTEN
# Bad: Only localhost, can't connect from other IPs

# 4. Check firewall
iptables -L -n | grep 3000
# Should NOT have "REJECT" or "DROP" rules for port 3000

# 5. Check security groups (AWS)
# Inbound rules must allow:
# Protocol: TCP
# Port: 3000
# Source: Your IP or 0.0.0.0/0

# 6. Can you reach the server's network?
ping 10.0.2.20
# If timeout: Network unreachable

# 7. Is there a NAT or gateway issue?
traceroute 10.0.2.20
# Shows path to destination
# Helps identify where connection fails
```

### 13.2 Network Unreachable

```bash
# Attempting to connect to different subnet
# Your server: 192.168.1.10
# Target server: 10.0.0.50

ping 10.0.0.50
# ping: connect: Network is unreachable

# Diagnosis:
route -n

Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref Use Iface
192.168.1.0     0.0.0.0         255.255.255.0   U     0      0  0 eth0
# Missing route to 10.0.0.0!

# Fix:
route add -net 10.0.0.0 netmask 255.255.0.0 gw 192.168.1.1 eth0

# Now try again:
ping 10.0.0.50
# Works!

# Permanent fix (Linux):
# Edit /etc/network/interfaces or /etc/sysconfig/network
```

### 13.3 Wrong IP Assignment

```bash
# Server supposed to have IP 10.0.2.15
# But actually has 10.0.2.20

# Check current IP:
ip addr show
# or
ifconfig

# For static IP assignment (Linux):
# Edit /etc/network/interfaces
auto eth0
iface eth0 inet static
  address 10.0.2.15
  netmask 255.255.255.0
  gateway 10.0.2.1

# Apply changes:
systemctl restart networking

# For DHCP (auto-assigned):
# Edit /etc/network/interfaces
auto eth0
iface eth0 inet dhcp

# Restart:
systemctl restart networking

# Check what you got:
dhclient -v eth0
# Shows DHCP lease information
```

### 13.4 DNS vs IP Issues

```bash
# Can you ping the IP but not the hostname?
ping 203.0.113.50  # Works
ping api.example.com  # Fails

# It's a DNS issue, not network issue

# Check DNS resolution:
nslookup api.example.com
# Output:
# Server: 8.8.8.8
# Name: api.example.com
# Address: 203.0.113.50  OR  (server failed)

# Check /etc/resolv.conf
cat /etc/resolv.conf
# Should have: nameserver 8.8.8.8

# Force DNS refresh:
sudo systemctl restart systemd-resolved

# Or directly query DNS:
dig api.example.com
host api.example.com
```

### 13.5 ARP Issues

```bash
# Server not responding even though ping works
# Likely ARP cache issue

# View ARP cache:
arp -a
# 192.168.1.100 at aa:bb:cc:dd:ee:ff

# Clear ARP cache (temporary):
sudo arp -d 192.168.1.100

# On reboot it clears anyway

# Issue: If MAC address changed but ARP cached old one
# Solution: Clear cache and ARP will re-learn
```

### 13.6 Packet Tracing

```bash
# Install tcpdump first
sudo apt-get install tcpdump

# Capture all traffic on eth0
sudo tcpdump -i eth0

# Capture specific protocol
sudo tcpdump -i eth0 icmp  # Only ping (ICMP)
sudo tcpdump -i eth0 tcp port 3000  # Only TCP port 3000

# More detailed:
sudo tcpdump -i eth0 -vvv -n

# Save to file:
sudo tcpdump -i eth0 -w capture.pcap

# Analyze with Wireshark (GUI):
wireshark capture.pcap

# From tcpdump output, you can see:
# IP addresses involved
# Ports being used
# TCP flags (SYN, ACK, RST)
# Packet sizes
# MTU issues
```

**Example tcpdump analysis:**

```
17:45:23.456789 IP 192.168.1.10.54321 > 192.168.1.20.3000: Flags [S]
  ^timestamp    ^src IP and port       ^dst IP and port  ^SYN flag
  (Client initiating TCP connection)

17:45:23.456890 IP 192.168.1.20.3000 > 192.168.1.10.54321: Flags [S.]
  (Server responding with SYN-ACK)

17:45:23.456891 IP 192.168.1.10.54321 > 192.168.1.20.3000: Flags [.]
  (Client sending ACK - connection established)

17:45:23.456892 IP 192.168.1.10.54321 > 192.168.1.20.3000: Flags [P.]
  (Client sending data - P flag = PUSH)

17:45:23.456893 IP 192.168.1.20.3000 > 192.168.1.10.54321: Flags [P.]
  (Server responding with data)
```

---

## 14. Best Practices

### 14.1 IP Address Planning

```
Before deploying infrastructure, create IP plan:

Network:        10.0.0.0/16
├── VPC 1       10.0.0.0/16
│   ├── Public Subnet AZ-1a: 10.0.1.0/24 (254 IPs)
│   ├── Public Subnet AZ-1b: 10.0.2.0/24 (254 IPs)
│   ├── Private Subnet AZ-1a: 10.0.11.0/24 (254 IPs)
│   ├── Private Subnet AZ-1b: 10.0.12.0/24 (254 IPs)
│   └── Database Subnet: 10.0.21.0/24 (254 IPs)
│
└── VPC 2       10.1.0.0/16 (for future expansion)

Document:
- Reserved IPs in each subnet
- Gateway IPs
- NAT Gateway IPs
- Future growth plans
```

### 14.2 Using DNS Instead of IPs

```javascript
// WRONG: Hardcoded IPs
const DB_HOST = '10.0.3.100';
const API_HOST = '203.0.113.50';

// RIGHT: Use DNS names
const DB_HOST = 'database.internal';
const API_HOST = 'api.example.com';

// Benefits:
// 1. Can change IP without code change
// 2. Load balancing via DNS
// 3. High availability
// 4. Easier migrations
// 5. Better security (hide real IPs)
```

### 14.3 Always Use CIDR Notation

```bash
# WRONG: Unclear
10.0.0.0 with 255.255.0.0

# RIGHT: CIDR notation
10.0.0.0/16

# Easier to:
# - Understand network size
# - Calculate subnets
# - Configure firewalls
# - Document infrastructure
```

### 14.4 Separate Public and Private Subnets

```
DO:
Public Subnet
├── Load Balancer: 0.0.0.0:443
├── NAT Gateway

Private Subnet
└── App Servers: Only allow from LB
└── Databases: Only allow from App Servers

DON'T:
- Expose databases on public IPs
- Allow app servers directly from internet
- Put everything in same subnet
```

### 14.5 Use Service Discovery

```javascript
// Instead of managing IPs manually
// Use service discovery like Consul, Kubernetes, AWS Elastic Service Discovery

// Kubernetes example:
const Database = new require('pg').Pool({
  host: 'database-service',  // Kubernetes discovers IP
  port: 5432
});

// Consul example:
const Consul = require('consul');
const consul = new Consul();

consul.health.service({
  service: 'api-server',
  passing: true
}, (err, result) => {
  const servers = result.map(r => r.Service.Address);
  // Use one of the discovered IPs
});
```

### 14.6 Document Your Network

```
README.md:

## Network Architecture

### IP Ranges
- VPC CIDR: 10.0.0.0/16
- Public Subnets: 10.0.0.0/24, 10.0.1.0/24
- Private Subnets: 10.0.11.0/24, 10.0.12.0/24

### Important IPs
- Database: 10.0.12.100
- Redis Cache: 10.0.12.101
- Load Balancer: EIP 203.0.113.50

### Security Groups
- Web: Allow 80, 443 from 0.0.0.0/0
- App: Allow 3000 from 10.0.0.0/16
- Database: Allow 5432 from 10.0.0.0/16

### Diagram
[Include ASCII or image of network]
```

---

## 15. Commands and Practical Usage

### 15.1 IPv4 Network Commands

```bash
# View IP configuration
ip addr show
ifconfig -a
hostname -I

# View routing table
ip route show
route -n
netstat -r

# View ARP cache
arp -a
ip neigh show

# View listening ports
netstat -tlnp
ss -tlnp
lsof -i -P -n | grep LISTEN

# Network statistics
netstat -s
ss -s

# DNS lookup
nslookup example.com
dig example.com
host example.com

# Ping (ICMP)
ping -c 4 192.168.1.1
ping6 2001:db8::1  # IPv6

# Traceroute
traceroute 8.8.8.8
tracert 8.8.8.8  # Windows

# Test connectivity
curl -I http://203.0.113.50:3000
telnet 203.0.113.50 3000

# View network interfaces
ip link show
ifconfig

# Configure IP (temporary)
sudo ip addr add 192.168.1.100/24 dev eth0
sudo ip addr del 192.168.1.100/24 dev eth0

# Configure IP (permanent)
# Edit /etc/network/interfaces or netplan

# Bring interface up/down
sudo ip link set eth0 up
sudo ip link set eth0 down

# Set default gateway
sudo ip route add default via 192.168.1.1 dev eth0

# Check connectivity
mtr 8.8.8.8  # Realtime traceroute

# Network performance
iperf3 -s  # Server
iperf3 -c 192.168.1.100  # Client
```

### 15.2 IPv6 Commands

```bash
# View IPv6 addresses
ip -6 addr show
ifconfig | grep inet6

# IPv6 routing
ip -6 route show
ip -6 route add 2001:db8:1::/64 via 2001:db8::1

# IPv6 ping
ping6 2001:db8::1
ip -6 route add 2001:db8::1/128 via fe80::1 dev eth0

# View IPv6 neighbors (ARP equivalent)
ip -6 neigh show

# Configure IPv6
sudo ip -6 addr add 2001:db8::1/64 dev eth0
sudo ip -6 addr del 2001:db8::1/64 dev eth0

# Enable IPv6 on interface
sudo sysctl net.ipv6.conf.eth0.disable_ipv6=0

# Disable IPv6
sudo sysctl net.ipv6.conf.all.disable_ipv6=1
```

### 15.3 AWS IP/Network Commands

```bash
# List VPC CIDR blocks
aws ec2 describe-vpcs --query 'Vpcs[*].[VpcId,CidrBlock]'

# List subnets with IP info
aws ec2 describe-subnets --query 'Subnets[*].[SubnetId,CidrBlock,AvailableIpAddressCount]'

# List instances with IPs
aws ec2 describe-instances --query 'Reservations[*].Instances[*].[InstanceId,PrivateIpAddress,PublicIpAddress,SubnetId]'

# View network interfaces
aws ec2 describe-network-interfaces --query 'NetworkInterfaces[*].[NetworkInterfaceId,PrivateIpAddresses,SubnetId]'

# View route tables
aws ec2 describe-route-tables --query 'RouteTables[*].[RouteTableId,VpcId,Routes[*].[DestinationCidrBlock,GatewayId,InstanceId,State]]'

# View security groups
aws ec2 describe-security-groups --query 'SecurityGroups[*].[GroupId,GroupName,IpPermissions]'

# View NAT gateway public IPs
aws ec2 describe-nat-gateways --query 'NatGateways[*].[NatGatewayId,PublicIp,PrivateIp,SubnetId]'

# Check VPC peering connections
aws ec2 describe-vpc-peering-connections --query 'VpcPeeringConnections[*].[VpcPeeringConnectionId,RequesterVpcInfo.CidrBlock,AccepterVpcInfo.CidrBlock]'

# List Elastic IPs
aws ec2 describe-addresses --query 'Addresses[*].[PublicIp,PrivateIpAddress,InstanceId,AllocationId]'
```

### 15.4 Docker IP Commands

```bash
# View Docker networks
docker network ls

# Inspect network (shows CIDR and connected containers)
docker network inspect bridge

# Get container IP
docker inspect my-container | grep IPAddress

# Connect container to specific IP
docker run -it --network mynet --ip 172.20.0.5 ubuntu

# Create custom network with specific CIDR
docker network create --driver bridge --subnet 172.20.0.0/16 mynet

# View container network stats
docker stats my-container  # Includes network I/O

# Check container networking
docker exec my-container ip addr show
docker exec my-container ip route show
```

### 15.5 Kubernetes IP Commands

```bash
# View cluster CIDR
kubectl cluster-info dump | grep -i cidr

# Get pod IPs
kubectl get pods -o wide

# Get pod IP for specific pod
kubectl get pod my-pod -o jsonpath='{.status.podIP}'

# View service IPs
kubectl get svc

# Get service cluster IP
kubectl get svc my-service -o jsonpath='{.spec.clusterIP}'

# View network policies
kubectl get networkpolicies

# Check pod connectivity
kubectl exec my-pod -- ping 10.0.0.10  # If pod is in another IP range

# Get node IPs
kubectl get nodes -o wide

# View CNI (network plugin) info
kubectl get daemonset -n kube-system  # Shows network plugins like weave, calico

# Test DNS within cluster
kubectl exec my-pod -- nslookup my-service
kubectl exec my-pod -- nslookup my-service.default.svc.cluster.local
```

### 15.6 Node.js Network Testing

```javascript
// File: test-network.js

const net = require('net');
const dns = require('dns');
const http = require('http');

// DNS lookup
dns.lookup('example.com', (err, address, family) => {
  console.log('IP:', address, 'Family:', family);
});

// Direct IP connection test
const socket = net.createConnection({
  host: '192.168.1.1',
  port: 22
}, () => {
  console.log('Connected!');
  socket.end();
});

socket.on('error', (err) => {
  console.error('Error:', err.code);
  // Possible errors:
  // ECONNREFUSED - Port closed
  // ENOTFOUND - DNS failed
  // EHOSTUNREACH - Host unreachable
  // ENETUNREACH - Network unreachable
});

// HTTP request with IP
http.get('http://203.0.113.50:3000/health', (res) => {
  console.log('Status:', res.statusCode);
}).on('error', (err) => {
  console.error('HTTP Error:', err);
});

// Get local IP
const os = require('os');
const interfaces = os.networkInterfaces();
for (const name of Object.keys(interfaces)) {
  for (const iface of interfaces[name]) {
    if (iface.family === 'IPv4' && !iface.internal) {
      console.log('Local IP:', iface.address);
    }
  }
}
```

---

## 16. Interview Questions

### Conceptual Questions

1. **What is an IP address and why do we need it?**
   - Answer should include: Unique identifier for devices, enables routing, hierarchical structure for internet scalability

2. **What's the difference between IPv4 and IPv6?**
   - IPv4: 32-bit, ~4.3B addresses, dotted decimal
   - IPv6: 128-bit, ~340 undecillion addresses, hexadecimal with colons
   - IPv6: No NAT, no DHCP needed (SLAAC), better routing

3. **Explain what a subnet mask does and give an example.**
   - Separates network portion from host portion
   - Example: 192.168.1.100/24 means first 24 bits = network, last 8 bits = host
   - Binary AND with IP to get network address

4. **What happens when you send a packet to a server on a different network?**
   - Packet sent to default gateway
   - Router reads destination IP
   - Looks in routing table for match
   - Forwards to next hop
   - Process repeats at each router until destination network reached

5. **Explain ARP and why it's needed.**
   - Resolves IP addresses to MAC addresses
   - Needed because network frames require MAC addresses, not IPs
   - ARP broadcast on local network
   - Host recognizes its IP and responds

### Practical Questions

6. **Your Node.js application can't reach a database server at 10.0.3.100. How would you troubleshoot?**
   - Check if server is running and listening: `netstat -tlnp | grep 5432`
   - Check firewall rules: `iptables -L -n`
   - Verify routing: `route -n` (do you have route to 10.0.0.0?)
   - Check security groups (if AWS)
   - Use ping, traceroute to find where connection fails
   - Use tcpdump to capture packets

7. **In AWS, you created two VPCs with CIDR 10.0.0.0/16 each. What problem could arise?**
   - IP overlap when trying to peer or connect them
   - Packets meant for VPC A might go to VPC B
   - Solution: Use non-overlapping CIDRs (10.0.0.0/16 and 10.1.0.0/16)

8. **Explain how a Docker container communicates with your Node.js host machine.**
   - Container gets IP from docker0 bridge (default 172.17.0.0/16)
   - Host has IP on eth0 (e.g., 192.168.1.10)
   - Container's packets go to docker0 bridge
   - Bridge routes to host's eth0
   - Applications communicate via published ports or bridge network

9. **What's the difference between 192.168.1.0/24 and 192.168.1.0/25?**
   - /24: 256 total addresses, 254 usable (network and broadcast reserved)
   - /25: 128 total addresses, 126 usable
   - /25 is half the size, useful for smaller subnets

10. **How does NAT (Network Address Translation) work in AWS?**
    - NAT Gateway sits in public subnet
    - Private subnet instances send traffic through NAT
    - NAT changes source IP from private to Elastic IP (public)
    - Responses come back to NAT, which translates back to private IP
    - Allows private instances to reach internet without being exposed

### Deep Dive Questions

11. **Explain the three-way TCP handshake and the role of IP addresses in it.**
    - SYN: Client (IP A) sends to Server (IP B), flags [SYN], seq=1000
    - SYN-ACK: Server responds, flags [SYN,ACK], ack=1001, seq=2000
    - ACK: Client responds, flags [ACK], ack=2001
    - IP addresses identify source and destination throughout

12. **In a /24 subnet with 254 usable hosts, why might you still run out of IPs?**
    - Network and broadcast addresses reserved (not usable)
    - Gateway IP reserved
    - Reserved for future expansion
    - Every container/VM needs an IP
    - If you have 300 containers in /24, you'll run out

13. **Explain IP fragmentation and when it occurs.**
    - Packet larger than MTU (usually 1500 bytes) is fragmented
    - IPv4: Routers fragment; IPv6: Hosts fragment
    - Each fragment has same IP header (except offset field)
    - Destination reassembles fragments
    - Fragments increase processing overhead

14. **How would you optimize network performance for a high-traffic Node.js application?**
    - Keep packet sizes under MTU to avoid fragmentation
    - Use appropriate subnet sizes (not too large/small)
    - Use local routing over public internet
    - Use load balancing with DNS round-robin
    - Monitor MTU with Path MTU Discovery

15. **What's the purpose of the loopback address (127.0.0.1) and when would you use it?**
    - Local testing without network involvement
    - Binding to 127.0.0.1 makes server only accessible locally
    - Used for: localhost development, inter-process communication
    - 127.0.0.0/8 is reserved for loopback

---

## 17. Advanced Concepts

### 17.1 Subnetting Deep Dive

**Variable Length Subnet Masking (VLSM):**

```
Network: 10.0.0.0/16

Allocate:
- Site A: 500 hosts   → Use /23 (510 hosts)    10.0.0.0/23
- Site B: 100 hosts   → Use /25 (126 hosts)    10.0.2.0/25
- Site C: 50 hosts    → Use /26 (62 hosts)     10.0.2.128/26
- Site D: 10 hosts    → Use /28 (14 hosts)     10.0.2.192/28

More efficient than giving each site /24 (254 hosts each)
```

### 17.2 IP Routing Protocols

**Static vs. Dynamic Routing:**

```
Static Routing:
- Administrator manually configures routes
- Small networks
- Predictable, secure
- More management overhead

Dynamic Routing:
- Routers learn routes automatically
- Large networks (internet scale)
- Adapts to failures automatically
- OSPF, BGP, RIP, EIGRP

Example BGP (Border Gateway Protocol):
AS 64512 advertises: 203.0.113.0/24
AS 64513 advertises: 203.0.114.0/24
Routers exchange routes dynamically
If path fails, automatically reroutes
```

### 17.3 IP Multicast

**One-to-many communication:**

```
Standard unicast: 1 sender → 1 recipient
Multicast: 1 sender → multiple recipients (group)

Address range: 224.0.0.0/4 (224.0.0.0 - 239.255.255.255)

Example:
- 224.0.0.5: OSPF routers
- 224.0.0.6: OSPF designated routers
- 238.1.1.1: Custom multicast group

Use case:
- Live streaming
- Gaming (one server updates multiple clients)
- Cluster announcements
- Real-time data feeds

Linux example:
# Join multicast group and listen
socat UDP-RECV:5000,ip-add-membership=238.1.1.1:0.0.0.0 -

# Send to multicast
echo "message" | socat - UDP-DATAGRAM:238.1.1.1:5000
```

### 17.4 Anycast Addressing

**Multiple servers, one address:**

```
Anycast IP: 203.0.113.1

Server A: 203.0.113.1
Server B: 203.0.113.1
Server C: 203.0.113.1

When client sends to 203.0.113.1:
Routing protocol determines "closest" or "best" server
Client reaches nearest server automatically

Use cases:
- DNS (root servers use anycast)
- CDN (edge servers use anycast)
- Load distribution
- Failover without client configuration
```

### 17.5 IPv6 Transition Mechanisms

**Dual-stack approach:**

```
Host runs both IPv4 and IPv6 simultaneously
Communicates with both IPv4 and IPv6 servers

Example:
Device A:
- IPv4: 192.168.1.100
- IPv6: 2001:db8::100

Can connect to:
- IPv4 servers: 203.0.113.50
- IPv6 servers: 2001:db8::50:1
```

**IPv4-mapped IPv6:**

```
IPv4 address inside IPv6 format:
::ffff:192.168.1.100

Allows IPv6 socket to accept IPv4 connections
Bridges the transition period
```

### 17.6 Network Virtualization

**Virtual IPs (floating IPs):**

```
HA Pair setup:
Server A: 192.168.1.10
Server B: 192.168.1.20
Virtual IP: 192.168.1.30 (active on Server A)

Client connects to 192.168.1.30

If Server A fails:
Virtual IP automatically moves to Server B
Clients automatically reconnect

Used in:
- High availability database clusters
- Load balancer failover
- Kubernetes VIP
```

### 17.7 IP Geolocation

```javascript
// Determine user's country/location from IP
const geoip = require('geoip-lite');

const lookup = geoip.lookup('203.0.113.50');
console.log(lookup);
// {
//   range: [3419001856, 3419002111],
//   country: 'US',
//   region: 'CA',
//   eu: false,
//   timezone: 'America/Los_Angeles',
//   city: 'San Francisco',
//   ll: [37.7749, -122.4194],
//   metro: 807,
//   area: 50
// }
```

---

## 18. Summary

### Key Takeaways

**IPv4 Addresses:**
- 32-bit identifiers (2^32 = ~4.3 billion addresses)
- Written in dotted decimal (192.168.1.1)
- Uses subnet masks to separate network from host
- CIDR notation (/24, /16, etc.) is the standard
- Private ranges: 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16
- ARP translates IPs to MAC addresses for local networks
- Routing directs packets across different networks

**IPv6 Addresses:**
- 128-bit identifiers (2^128 = ~340 undecillion addresses)
- Written in hexadecimal with colons (2001:db8::1)
- Solves IPv4 exhaustion
- Built-in features (no NAT, SLAAC, ND)
- Gradually replacing IPv4

**Network Architecture:**
- Subnetting allows efficient IP allocation
- Public IPs for internet-facing services
- Private IPs for internal communication
- NAT enables private networks to access internet
- Routing tables determine packet paths
- Security groups/firewalls control IP-based access

**DevOps Perspective:**
- IP planning is critical for infrastructure design
- Use DNS instead of hardcoding IPs
- Understand how your applications communicate via IPs
- Troubleshoot connectivity issues systematically
- Design networks for scalability and security

### Important Commands Summary

```bash
# View/Configure IPs
ip addr show
ip link set eth0 up
ip addr add 192.168.1.100/24 dev eth0

# Routing
route -n
ip route show
ip route add default via 192.168.1.1

# DNS
nslookup example.com
dig example.com

# Connectivity Testing
ping -c 4 192.168.1.1
traceroute 8.8.8.8
telnet 203.0.113.50 3000

# Network Statistics
netstat -tlnp
ss -tlnp
arp -a

# Packet Analysis
sudo tcpdump -i eth0 tcp port 3000
```

### Interview Preparation

You should now be able to answer:
- How IP addresses enable network communication
- Difference between IPv4 and IPv6
- How subnetting and subnet masks work
- The role of ARP in local network communication
- How packets are routed across networks
- Common troubleshooting for connectivity issues
- Best practices for IP address planning
- How backend applications interact with IP addresses

### Next Steps

With solid IP knowledge, you're ready to learn:
1. **Routers** - How they forward packets using IPs
2. **Public vs Private IPs** - AWS/Cloud perspective
3. **Firewalls** - How they control IP-based traffic
4. **VPCs and Subnets** - Cloud infrastructure
5. **NAT Gateways** - Network address translation in cloud
6. **Security Groups** - IP-based access control

---

