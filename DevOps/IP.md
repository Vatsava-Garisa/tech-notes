
The IP addresses are like phone numbers for the computers an other devices that connect to the internet. Each device needs its own IP address to connect to the other devices online.

## IP Address Formats 
### Classful IP Address
- The IPv4 address is made of the 32 bits shown as the four numbers from 0 to 255 separated by dots.
- There are five classes but only three classes are used to assign to the client and remaining two class are reserved for future purpose.

1. **Class A** 
	- Uses 8 bits for the network part. Example: 44.0.0.1
	- Here, 44 is the network and 0.0.1 is the device.
2. **Class B** 
	- Uses 16 bits for the network part. Example: 128.16.0.2
	- Here, 128.16 is the network part and 0.2 is the device.
3. **Class C** 
	- Uses 24 bits for the network part. Example: 192.168.1.100
	- Here, 192.168.1 is the network and 100 is the device.

### Limitations of Classful IP 
- In the old system, IP addresses came in fixed sizes. This led to wasting IP addresses.
	1. Class A: 16,777,214 devices
	2. Class B: 65,534 devices
	3. Class C: 254 devices

**Example 1:** 
- A company had 300 devices, they couldn't use Class C (too small). 
- They had to use Class B, which was very much bigger than they needed. 
- This left many IP addresses unused.

**Example 2:** 
- The Classful IP addresses made it tough to join the networks together. 
- For instance, 192.168.1.0 and 192.168.0.0 are **Class C** networks. In the old system, you couldn't combine them because they had a fixed size. 
- This made it hard for network managers to create the setups they wanted.

### Classless IP Address 
- These are called the (CIDR) **Classless Inter-Domain Routing** addresses.
- They use a more flexible way to split the network and device parts.
- This lets network managers create smaller sub networks of different sizes.
- These CIDR addresses adds the number at the end to show that how many bits are used for the network part.
- Example: 192.0.2.1/24 - Here, 192.0.2 is the network and 1 is the device.

**CIDR Block** 
A CIDR block is a group of IP addresses that share the same start and size. Big blocks have more IP addresses. Big internet groups give out large CIDR blocks to smaller groups. These smaller groups then give them to companies. If you're at home, you get your CIDR block from your internet company.

## Benefits of CIDR 
1. Saves IP Addresses
	- CIDR lets you give out just the right number of IP addresses for each network.
	- This mean fewer IP addresses are wasted.
	- It also makes routing data between devices easier and faster.
2. Sends Data Faster
	- CIDR helps routers organize IP addresses into smaller groups called subnets.
	- This means data can go straight to where it needs to, without taking long routes.
3. Helps Make Private Cloud Spaces
	- A VPC is a private area in the cloud for a company's work.
	- VPCs use CIDR to send data between devices safely.
4. Makes Big Networks Easily
	- CIDR lets you join smaller networks into bigger ones (called supernets) easily. 
	- Take 192.168.1/23 and 192.168.0/23. This joins two networks into one big network.
	- It makes it simpler for routers to send data between devices in these networks.

---
## IP Address Breakdowns 

1. **192.168.1.100/24** 
```
IP: 192.168.1.100

192 = 11000000
168 = 10101000
1 = 00000001
100 = 01100100

Full Binary:
11000000.10101000.00000001.01100100

Decimal: 3232235876

Subnet Mask: 255.255.255.0
11111111.11111111.11111111.00000000

CIDR Notation: /24

Network Bits: 24 | Host Bits: 8

Network Address: 192.168.1.0 (all host bits = 0)
Broadcast Address: 192.168.1.255 (all host bits = 1)
Total Addresses: 256
First Usable Host: 192.168.1.1
Last Usable Host: 192.168.1.254
Usable Hosts: 254
```

2. **192.168.1.100/12** 
```
IP: 192.168.1.100

192 = 11000000
168 = 10101000
1 = 00000001
100 = 01100100

Full Binary:
11000000.10101000.00000001.01100100

Decimal: 3232235876

Subnet Mask: 255.240.0.0
11111111.11110000.00000000.00000000

CIDR Notation: /12

Network Bits: 12 | Host Bits: 20

Network Address: 192.160.0.0 (all host bits = 0)
Broadcast Address: 192.175.255.255 (all host bits = 1)
Total Addresses: 1048576
First Usable Host: 192.160.0.1
Last Usable Host: 192.175.255.254
Usable Hosts: 1048574
```

3. **192.168.1.100/16** 
```
IP: 192.168.1.100

192 = 11000000
168 = 10101000
1 = 00000001
100 = 01100100

Full Binary:
11000000.10101000.00000001.01100100

Decimal: 3232235876

Subnet Mask: 255.255.0.0
11111111.11111111.00000000.00000000

CIDR Notation: /16

Network Bits: 16 | Host Bits: 16
Network Address: 192.168.0.0 (all host bits = 0)
Broadcast Address: 192.168.255.255 (all host bits = 1)
Total Addresses: 65536
First Usable Host: 192.168.0.1
Last Usable Host: 192.168.255.254
Usable Hosts: 65534
```
---
## **The Three Critical Ideas About IPs:**

1. **Hierarchical Addressing** 
    - Network portion identifies the network
    - Host portion identifies the device within that network
    - Subnet mask separates them
    - Enables routers to efficiently forward packets
2. **Two-Layer Communication** 
    - **Local Network (Layer 2)**: MAC addresses, ARP resolution, direct delivery
    - **Remote Networks (Layer 3)**: IP addresses, routing tables, gateway forwarding
    - Your application doesn't care - the OS handles both
3. **IP Is Just Addressing, Not Transport**
    - IP gets packets _to_ the right machine
    - TCP/UDP get data _to_ the right application
    - DNS translates names _to_ IP addresses
    - These layers work together seamlessly
---
## Network Communication
![[Pasted image 20260517190327.png|817]]
## IPv4 vs IPv6
![[Pasted image 20260517190844.png|621]]
