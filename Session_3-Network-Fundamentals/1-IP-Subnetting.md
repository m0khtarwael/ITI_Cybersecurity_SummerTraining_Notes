# 1. What is Subnetting?
- Subnetting is the process of dividing a single large IP network into smaller, logically separated sub-networks known as **Subnets**. 
- The main purpose of subnetting is to provide organization, enhanced security, and operational efficiency in network management, rather than placing all network devices within a single flat broadcast domain.

---
# 2. Key Benefits of Subnetting

### 2.1. Improving Network Performance & Reducing Broadcast Traffic
In traditional unsegmented networks, when a device sends a broadcast frame, every single device on the network receives and processes it.
* **Without Subnetting:** Having 1,000 devices in one network leads to a **Broadcast Storm**, causing extreme congestion and degrading network performance.
* **With Subnetting:** Broadcast traffic is contained strictly within its respective subnet boundaries, maintaining high transmission speeds and minimal latency.
### 2.2. Enhancing Security & Access Control
Dividing a network into subnets allows you to isolate departments and sensitive tiers using Firewalls or Access Control Lists (ACLs):
* Separate employee traffic from guest networks.
* Isolate sensitive infrastructure (e.g., database and application servers) from standard workstations.
* Enforce controlled layer-3 routing between departments for strict monitoring.
### 2.3. Conserving IP Address Space
Using default Classful subnetting often wastes thousands of IP addresses (especially with IPv4):
* Assigning a full **Class B** network (`/16`) without subnetting grants 65,534 IP addresses. If an organization only has 50 devices, over 65,000 IPs are wasted.
* **Subnetting** allows network engineers to customize the Subnet Mask to match exact host requirements (e.g., creating a subnet for 30 hosts and another for 20 hosts).
### 2.4. Simplifying Troubleshooting & Administration
* **Issue Isolation:** If a security breach or network issue (e.g., malware outbreak or loop) occurs in one department (e.g., Sales), that specific subnet can be isolated without disrupting the rest of the enterprise.
* **Logical Addressing:** Hierarchical IP addressing schemes help engineers quickly identify device locations and functions simply by reading their IP address.
---
# 3. Fundamental Subnetting Rules & Classful Defaults

### Core Rules for Subnettings
1. **IP + Subnet Mask Requirement:** A host cannot function on an IP network with an IP address alone; it **must** have a Subnet Mask configured.
2. **Local Communication:** A Default Gateway and DNS IP addresses are only required if devices need to access external networks (Internet/other subnets). For local subnet communication, an IP and Subnet Mask are sufficient.
3. **Router Interfaces:** Every physical or logical interface on a router represents an independent, isolated subnet.
### Default Subnet Masks (Classful Addressing)

| Class       | Default Subnet Mask | Default CIDR | Network / Host Split           |
| :---------- | :------------------ | :----------- | :----------------------------- |
| **Class A** | `255.0.0.0`         | `/8`         | 8 bits Network / 24 bits Host  |
| **Class B** | `255.255.0.0`       | `/16`        | 16 bits Network / 16 bits Host |
| **Class C** | `255.255.255.0`     | `/24`        | 24 bits Network / 8 bits Host  |

---
# 4. CIDR Notation & IP Allocation Mechanics
### How the Subnet Mask Determines Host Capacity
- The zero bits (`0`) in a subnet mask determine the total number of host IP addresses available in that subnet.

$$\text{Total IP Addresses in a subnet} = 2^{\text{Number of Zeros in Subnet Mask}}$$

$$\text{Usable Host IPs} = 2^{\text{Number of Zeros in Subnet Mask}} - 2$$

> **Why subtract 2?**
> 1. **Network ID (N-ID):** The very first IP address in the subnet range reserved to identify the network itself.
> 2. **Broadcast Address (BC):** The very last IP address in the subnet range reserved to send broadcast packets to all hosts in that subnet.
### CIDR (Classless Inter-Domain Routing) Notation
- CIDR notation appends a slash (`/`) followed by a number to the IP address. This number indicates the count of contiguous leading **1-bits** in the subnet mask (representing the network portion).
#### Example:
* **IP + Mask:** `192.168.1.50` with mask `255.255.255.0`
* **Binary Mask:** `11111111.11111111.11111111.00000000` (24 ones)
* **CIDR Format:** `192.168.1.50/24`
#### Unsubnetted Class C Analysis (`192.168.1.0/24`):
* **Subnet Mask:** `255.255.255.0`
* **Total IPs:** $2^8 = 256$ IPs (`192.168.1.0` to `192.168.1.255`)
* **Usable Hosts:** $2^8 - 2 = 254$ hosts (`192.168.1.1` to `192.168.1.254`)
* **Network ID:** `192.168.1.0`
* **Broadcast Address:** `192.168.1.255`

---

# 5. Fixed-Length Subnet Masking (FLSM) Step-by-Step
- FLSM divides a single network space into **==multiple subnets of equal size (equal host capacity)==**.
### How to Create Subnets
1. Start with the default subnet mask.
2. Borrow bits from the host portion (turn leading `0`s into `1`s from left to right in host portion - last 8 bits in IP).
3. **Increasing ones** increases the number of subnets while reducing the number of host addresses per subnet.

$$\text{Number of Subnets Created} = 2^{\text{Borrowed Bits}}$$

$$\text{Total IPs per Subnet} = 2^{\text{Remaining Zero Bits}}$$

---

### FLSM Examples

#### Example 1: Subnetting `192.168.1.0/24` into 2 Subnets (`/25`)
* **Original Mask:** `255.255.255.00000000`
* **Borrow 1 bit:** `255.255.255.10000000` (`255.255.255.128` or `/25`)
* **Subnets Created:** $2^1 = 2$ networks
* **Total IPs per Network:** $2^7 = 128$ IPs
* **Usable Hosts per Network:** $128 - 2 = 126$ hosts

**Network Ranges:**
* **Network 1:** `192.168.1.0` to `192.168.1.127`
	- Network ID: `192.168.1.0`
	- Broadcast IP: `192.168.1.127`
	- Usable IP Range: `192.168.1.1` – `192.168.1.126`
* **Network 2:** `192.168.1.128` to `192.168.1.255`
	* Network ID: `192.168.1.128`
	* Broadcast IP: `192.168.1.255`
	* Usable IP Range: `192.168.1.129` – `192.168.1.254`

> **Communication Note:** If Host A (`192.168.1.1/25`) pings Host B (`192.168.1.200/25`), the ping will fail without a router, as they now reside on two distinct subnets.

---

#### Example 2: Subnetting `192.168.1.0/24` into 4 Subnets (`/26`)
* **Borrow 2 bits:** Subnet mask binary = `11111111.11111111.11111111.11000000`
* **New Subnet Mask:** `255.255.255.192`
* **Subnets Created:** $2^2 = 4$ networks
* **Total IPs per Network:** $2^6 = 64$ IPs ($256 / 4 = 64$)
* **Usable Hosts per Network:** $64 - 2 = 62$ hosts

**Subnet Ranges:**
* **Subnet 1:** `192.168.1.0` to `192.168.1.63`
* **Subnet 2:** `192.168.1.64` to `192.168.1.127`
* **Subnet 3:** `192.168.1.128` to `192.168.1.191`
* **Subnet 4:** `192.168.1.192` to `192.168.1.255`

---

#### Example 3: Subnetting `192.168.1.0/24` into 16 Subnets (`/28`)
* **Borrow 4 bits:** Subnet mask binary = `11111111.11111111.11111111.11110000`
* **New Subnet Mask:** `255.255.255.240`
* **Subnets Created:** $2^4 = 16$ networks
* **Total IPs per Network:** $2^4 = 16$ IPs
* **Usable Hosts per Network:** $16 - 2 = 14$ hosts

**Subnet Ranges:**
* **Subnet 1:** `192.168.1.0` to `192.168.1.15` (Usable: `192.168.1.1` – `192.168.1.14`)
* **Subnet 2:** `192.168.1.16` to `192.168.1.31` (Usable: `192.168.1.17` – `192.168.1.30`)
* *...and so on up to Subnet 16 (`192.168.1.240` to `192.168.1.255`).*

---

# 6. Real-World Applications & ARP Behavior
### Internet Service Providers (ISPs) & Public IPs
- Subnetting is heavily utilized by ISPs when selling public IP address blocks. Instead of assigning a full Class C block to a small client, an ISP assigns smaller CIDR blocks (e.g., `/29` or `/30`) based on customer requirements, preventing global IPv4 address exhaustion.
### Broadcasts & ARP Operations
* To perform a local network ping or data exchange, hosts use **ARP (Address Resolution Protocol)** requests to discover destination MAC addresses.
* An ARP Request is a layer-2 broadcast sent to all hosts in the subnet.
* Subnetting reduces ARP broadcast traffic network-wide by limiting ARP broadcasts strictly to local subnets.

---

# 7. How to Determine if Two IPs Belong to the Same Subnet
To verify if two IP addresses can communicate locally without testing them in a simulator (like Cisco Packet Tracer using `ping` command), you can derive their **Network IDs** using bitwise **ANDing**.
### Bitwise AND Rule
$$\text{IP Bit} \mathbf{\text{ AND }} \text{Subnet Mask Bit} = \text{Network Bit}$$
* $1 \mathbf{\text{ AND }} 1 = 1$
* $1 \mathbf{\text{ AND }} 0 = 0$
* $0 \mathbf{\text{ AND }} 0 = 0$

---
### Verification Example
Check if `192.168.1.150/27` and `192.168.1.190/27` are on the same subnet.

#### 1. Subnet Characteristics (`/27`):
* **Mask:** `255.255.255.224` (`11111111.11111111.11111111.11100000`)
* **Number of Subnets:** $2^3 = 8$ networks
* **Number of hosts per subnet (Total IPs per Subnet):** $2^5 = 32$ IPs

---

#### 2. Perform ANDing for IP 1 (`192.168.1.150`):
* `255` = `11111111` , X AND 1 = X ; 192.168.1 AND 255.255.255 = 192.168.1
* `150` in Binary: `10010110`
* `224` in Binary: `11100000`

$$\begin{array}{r@{\quad}l}
  10010110 & \text{(150)} \\
\mathbf{\text{AND }} 11100000 & \text{(224)} \\
\hline
  10000000 & \text{(= 128 in decimal)}
\end{array}$$

* **Network ID for IP 1:** `192.168.1.128`

---
#### 3. Perform ANDing for IP 2 (`192.168.1.190`):
* `190` in Binary: `10111110`
* `224` in Binary: `11100000`

$$\begin{array}{r@{\quad}l}
  10111110 & \text{(190)} \\
\mathbf{\text{AND }} 11100000 & \text{(224)} \\
\hline
  10100000 & \text{(= 160 in decimal)}
\end{array}$$

* **Network ID for IP 2:** `192.168.1.160`

---
#### Conclusion:
* Network ID 1 = `192.168.1.128` (Range: `.128` – `.159`)
* Network ID 2 = `192.168.1.160` (Range: `.160` – `.191`)

Since their Network IDs differ, **`192.168.1.150` and `192.168.1.190` are in different subnets** and cannot communicate directly without a router.

You can write network start and network end like last examples and check if theses IPs are in the same subnet

---

# 8. Intro to Variable Length Subnet Masking (VLSM)
When different company departments require different numbers of host addresses (e.g., HR needs 50 hosts, Sales needs 10 hosts, and WAN links need 2 hosts), **Fixed Subnetting (FLSM)** wastes addresses because every subnet has an identical size.

**VLSM (Variable Length Subnet Masking)** solves this issue by allowing engineers to subnet an already subnetted address block. Instead of using one fixed mask across all subnets, VLSM applies custom mask lengths (`/26`, `/28`, `/30`) tailored to the exact requirements of each individual network segment.