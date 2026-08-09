- Any NIC has 2 addresses:
# 1. MAC Address (Physical Address)
- MAC Address refers to Media Access Control Address, it is an unique address burnt on your device's Network Interface Card (NIC) - Any device connects to internet has NIC.
- It consists of 48 bit = 6 bytes = 12 hexadecimal digits (from 0 to 9 , A to F) -> example : 6C:A0:64:CA:B0:A1
- Each digit represents 4bits
- First 24 bits (3 bytes): OUI (Organizationally Unique Identifier), fixed for the manufacturer.
- Last 24 bits (3 bytes): Vendor-assigned serial number for the device/card.
- you can obtain your MAC Address through
	1. GUI (via Control Panel / Network Settings).
	2. CLI using `getmac` or `ipconfig /all` command 
		- it displays MAC addresses for all active physical and virtual network adapters 
		- you may find more than MAC Address ; one for wired(ethernet) and other for wireless(Wi-Fi) connection , may be there is another MAC Addresses for virtual network adapters like VMware, VirtualBox, Docker ... etc 
- MAC Address in Non-Routable : can't go through Router to another LAN ; it goes only through your switch in your LAN (works in layer 2 only)

# 2. IP address(Logical Address)
- **IP** stands for **Internet Protocol**. It is the foundational set of rules that governs how data packets are addressed and routed across networks so they can reach the correct destination.
- Every device connected to the internet—computers, phones, servers, IoT devices—gets assigned an IP address so other devices know where to send data.
- IPV4 consists of 32 bit = 4 octets (each octet is 8 bits : 00000000 to 11111111 in binary, which means 0 to 255 in decimal) 
- IPV4 example : 192.168.1.1 (11000000.10100000.00000001.00000001)
- Number of IPV4 Addresses in the world = $2^{number-of-bits-in-IPV4}$ = $2^{32}$
## IP Types
| Feature                  | Public IP (Real IP)                                                  | Private IP (Virtual IP)                                                                                                                |
| :----------------------- | :------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------- |
| **Scope & Reachability** | Globally unique; accessible over the public internet.                | Locally unique; restricted to your internal network (LAN).                                                                             |
| **Assignment**           | Assigned by your **Internet Service Provider (ISP)** to your router. | Assigned by your local **Router / DHCP server** to internal devices.                                                                   |
| **Internet Access**      | Routable on the internet.                                            | Non-routable on the internet (requires NAT to access the web).                                                                         |
| **Cost**                 | Paid (provided as part of your ISP plan).                            | Completely free to use within local networks.                                                                                          |
| **Reserved Ranges**      | All valid IPv4 addresses outside private ranges.                     | • Class A: `10.0.0.0` – `10.255.255.255`<br>• Class B: `172.16.0.0` – `172.31.255.255`<br>• Class C: `192.168.0.0` – `192.168.255.255` |
| **Primary Purpose**      | Identifies your network on the global internet.                      | Identifies specific devices (PC, phone, printer) within a local network.                                                               |
## IPv4 classes
- IP class is known from first octet

| Feature / Attribute            | Class A                       | Class B                         | Class C                           | Class D                            | Class E                 |
| :----------------------------- | :---------------------------- | :------------------------------ | :-------------------------------- | :--------------------------------- | :---------------------- |
| **First Octet Range**          | `0` – `127`                   | `128` – `191`                   | `192` – `223`                     | `224` – `239`                      | `240` – `255`           |
| **Default Subnet Mask**        | `255.0.0.0`                   | `255.255.0.0`                   | `255.255.255.0`                   | N/A                                | N/A                     |
| **CIDR Notation**              | `/8`                          | `/16`                           | `/24`                             | N/A                                | N/A                     |
| **Total Networks**             | 128 ($2^7$)                   | 16,384 ($2^{14}$)               | 2,097,152 ($2^{21}$)              | Reserved                           | Reserved                |
| **Usable Hosts**               | 16,777,214 ($2^{24} - 2$)     | 65,534 ($2^{16} - 2$)           | 254 ($2^8 - 2$)                   | Reserved                           | Reserved                |
| **Primary Purpose**            | Very large networks & ISPs    | Medium to large organizations   | Small LANs & home networks        | Multicasting (Advanced Networking) | Experimental / Research |
| **Private IP Range**           | `10.0.0.0` – `10.255.255.255` | `172.16.0.0` – `172.31.255.255` | `192.168.0.0` – `192.168.255.255` |                                    |                         |
| **Total Private IP Addresses** | 16,777,214                    | 1,048,574                       | 65,534                            |                                    |                         |
- The rest of Class IPs are Public IPs

- There are some Real IPs ranges Reserved :

| Range                           | Status                 | Purpose                                                       |
| :------------------------------ | :--------------------- | :------------------------------------------------------------ |
| **0.0.0.0 – 0.255.255.255**     | Reserved               | System identification / "This network" (used in DHCP/Routing) |
| **1.0.0.0 – 126.255.255.255**   | **Assignable Class A** | Usable unicast network addresses                              |
| **127.0.0.0 – 127.255.255.255** | Reserved               | Loopback testing (`127.0.0.1` / `localhost`)                  |
## How to assign an IPV4 address?
1. Static IP (Manual IP)
	- Go to **Settings** then **Network & Internet**
	- Then **Change adapter options** : right click your network , choose properties
	- Double click on **Internet Protocol Version 4 (TCP/IPv4)** then click **Use the following IP address** and write your IP manual

2. Dynamic IP (Auto IP) : DHCP (will be explained later)
	- Go to **Settings** then **Network & Internet**
	- Then **Change adapter options** : right click your network , choose properties
	- Double click on **Internet Protocol Version 4 (TCP/IPv4)** then click **Obtain an IP address automatically**
	- Notice that **Alternative Configuration** tab is appeared
	- if DHCP doesn't assign IP, alternate configuration is APIPI (Automatic Private IP Address)  - it gives an IP from `169.254.0.0` to `169.254.255.255` - it only gives IP and subnet mask , it doesn't give gateway or DNS, so devices that has APIPA can connect together in the same LAN only ; it can't do Routing
	- You can give static IP instead of APIPI by click on **User Configured** in **Alternative Configuration** tab

- You can check all assigned IP addresses across all physical and virtual interfaces using the command line:
  ```bash
  # Windows (PowerShell / Command Prompt)
  ipconfig /all
  
  # Linux / macOS
  ip addr show   # or 'ifconfig'
  ```

- **You can assign multiple IPs to a single NIC** , Common Use Cases:
	1. **Web & Application Hosting:** Hosting multiple websites or SSL/TLS certificates on a single machine, where each site binds to its own distinct IP address.
	2. **Network Migration:** Moving a server from one subnet to another without dropping existing connections. You assign the new IP while keeping the old IP active until all traffic transfers.
	3. **Accessing Multiple Subnets:** Communicating with devices on different IP subnets connected to the same physical switch without needing additional network cables or interfaces.
	4. **Virtualization & Containers:** Assigning dedicated IPs to different virtual machines or containerized services sharing the same physical host interface.
- **How It Works?** 
	- The operating system maps multiple IP addresses to the single MAC address of that physical NIC. When ARP (Address Resolution Protocol) requests come in for _any_ of those IP addresses, the NIC responds with its same MAC address, allowing traffic for all assigned IPs to flow through that single interface.
- **Maximum Number of IP Addresses on the same interface:**
	* The theoretical maximum number of IP addresses you can assign to a single Network Interface Card (NIC) is **virtually unlimited**, bound primarily by the operating system's kernel memory, CPU, routing table capacity, and network stack overhead.