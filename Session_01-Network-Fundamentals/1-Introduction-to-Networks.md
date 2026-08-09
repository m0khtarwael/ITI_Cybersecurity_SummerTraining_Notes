# What Is a Network?
- A **network** is a group of devices connected together so that data can move between them.
- Examples of network devices: PCs, Laptops, Servers, Printers, Phones
## Why Do We Use Networks?
- The main goal of a network is **sharing**. Networks allow users to share:
	- Data between devices
	- Devices such as printers
	- Internet connections, such as a home router
	- Storage
	- Applications
- Networks are also used for **centralized administration**, which means managing users, devices, resources, and settings from one central location.
## Main Network Components
To build a network, you need three main components:

| Component                     | Description                                                        | Examples                   |
| ----------------------------- | ------------------------------------------------------------------ | -------------------------- |
| **End Devices**               | Devices that send or receive data                                  | PC, laptop, printer, phone |
| **Transmission Medium**       | The method used to move data between devices                       | Wired or wireless          |
| **Intermediate/Core Devices** | Devices that connect and control communication between end devices | Switch, router             |
## Network and Internet

| Situation | Possible? | Explanation |
|---|---|---|
| **Network without Internet** | Yes | Devices can connect together using a switch without having Internet access. |
| **Internet without a Network** | No | The Internet itself is a network of networks. |
## Network Design Classification
- Networks can be classified according to their size and geographical coverage.

| Type    | Full Name                 | Structure                                        | Geographical Area                            | Example                                            |
| ------- | ------------------------- | ------------------------------------------------ | -------------------------------------------- | -------------------------------------------------- |
| **PAN** | Personal Area Network     | A very small network used by one person          | Covers a very short distance around a person | Mobile hotspot                                     |
| **LAN** | Local Area Network        | A group of PCs connected together using a switch | Small office or one building                 | Office network                                     |
| **CAN** | Campus Area Network       | A group of LANs connected together               | University or campus                         | University network                                 |
| **MAN** | Metropolitan Area Network | A group of CANs connected together               | Large organization, government area, or city | Government network                                 |
| **WAN** | Wide Area Network         | A group of MANs connected together               | Large geographical areas                     | A network connecting different cities or countries |
## Storage Connection Types
- Storage can be connected and shared using three main designs:
	- DAS
	- NAS
	- SAN

| Feature                     | DAS                                                                         | NAS                                                                         | SAN                                                                                                 |
| --------------------------- | --------------------------------------------------------------------------- | --------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| **Full Name**               | Direct-Attached Storage                                                     | Network-Attached Storage                                                    | Storage Area Network                                                                                |
| **Connection Type**         | Connected directly to one host using a dedicated cable                      | Connected to a standard LAN using Ethernet or Wi-Fi                         | Connected through a dedicated high-speed storage network                                            |
| **Technologies**            | SATA, SAS, USB, Thunderbolt                                                 | Ethernet, Wi-Fi                                                             | Fibre Channel, iSCSI                                                                                |
| **Main Use**                | Individual PCs, local backups, and high-performance single servers          | Centralized file sharing, media streaming, and small-business backups       | Enterprise data centers, virtual environments, and databases                                        |
| **Sharing**                 | Normally available only to the host directly connected to it                | Shared between multiple devices on the network                              | Provides high-speed storage sharing between multiple cluster servers                                |
| **Performance and Latency** | High performance and very low latency because it uses direct bus access     | Medium latency because performance depends on network bandwidth and traffic | Extremely low latency and high throughput                                                           |
| **Scalability**             | Low scalability because it is limited by physical drive bays and host ports | Moderate scalability because more drives or enclosure units can be added    | Extremely high scalability and can reach petabytes using dedicated storage arrays                   |
| **Cost and Complexity**     | Low cost and simple plug-and-play setup                                     | Moderate cost and easy network configuration                                | High cost with complex deployment and management                                                    |
| **Examples**                | External HDDs, NVMe SSDs, flash drives, and local server HDDs               | Synology NAS, QNAP NAS, and shared network folders                          | Dell PowerStore, NetApp, other enterprise storage arrays, and 15 GB of free storage for Gmail users |
# Network Devices and Their Applications
## 1. Hub
- A **hub** is an old networking device that is not commonly used today.
- The main problem with a hub is the **broadcast problem**.
- When one device sends data to a hub:
	1. The hub receives the data.
	2. It sends the data to every device connected to it.
	3. It cannot send the data only to the required destination device.
- Because of this behavior, hubs create unnecessary traffic and reduce network efficiency.
- A **switch** solves this problem by sending data only to the correct destination device.
## 2. Switch

### 2.1 What Is a Switch?
- A **switch** is a networking device that connects devices together to create a **LAN (Local Area Network)**.
- Unlike a hub, a switch sends data only to the correct destination device.
- The switch identifies devices using their **MAC Address**.
### 2.2 MAC Address
**MAC Address** stands for **Media Access Control Address**.
- A **MAC Address** is a unique hexadecimal address assigned to a Network Interface Card (NIC).
- Written as **12 hexadecimal digits** in the format: `XX:XX:XX:XX:XX:XX`
- Example: `00:1A:2B:3C:4D:5E`
- Every device that connects to a network has a NIC.
- The switch uses the MAC address to identify each connected device.
- **First 24 Bits:**
	- OUI (Organizationally Unique Identifier) ,Assigned by **IEEE**, Identifies the NIC manufacturer.
- **Last 24 Bits:**
	- Assigned by the manufacturer, Uniquely identifies the NIC
### 2.3 How Does a Switch Send Data?
- The switch keeps a **MAC Address Table**.
- This table stores:

| Switch Port | Connected MAC Address |
|-------------|----------------------|
| Fa0/1 | aa:bb:cc:dd:ee:ff |
| Fa0/2 | dd:aa:ad:df:ff:ea |
#### When a device sends data:
1. The switch checks the destination MAC address.
2. It searches for that MAC address in its MAC Address Table.
3. It finds the corresponding port.
4. It sends the data only through that port.

- Because of this:
	- Data reaches only the required device.
	- Other devices do not receive unnecessary traffic.
- This solves the broadcast problem found in hubs.
### 2.4 How Does the Switch Learn MAC Addresses?
- The switch builds its MAC Address Table using **ARP requests**.
- An ARP request is similar to a broadcast message that asks devices for their MAC addresses.
- The switch stores these MAC addresses in its table so it can forward future traffic correctly.
- A switch works at: **Layer 2 (Data Link Layer)** of the **OSI Model**.
### 2.5 Switch Ports
- A switch has two main categories of interfaces.
#### 1. Console Port
- Usually only one console port.
- Used for management.
- Used to configure or change switch settings.
#### 2. LAN Ports
- LAN ports are available in different speeds.

| Port Type | Speed | Notes |
|------------|--------|------|
| Ethernet | 10 Mb/s | Basic Ethernet port |
| FastEthernet | 100 Mb/s | A switch usually has **2 FastEthernet ports** |
| GigabitEthernet | 1000 Mb/s | A switch usually has **2 GigabitEthernet ports** |
| 10 GigabitEthernet | 10000 Mb/s | Used in high-speed networks |
##### Why Do We Use GigabitEthernet Ports?
- GigabitEthernet ports are commonly used to connect:
	- Switch to another switch
	- Switch to a router
- These are called **uplink connections**.
Example:
- If 10 devices are sending traffic from one switch to another, all of that traffic passes through the uplink.
- The uplink must support a much higher speed, so GigabitEthernet ports are used.
#### Connecting a PC to a Switch
To connect a PC for normal data communication:
- Connect a **FastEthernet port** on the switch To the **FastEthernet0** interface on the PC's NIC
## 3. Router

### 3.1 What Is a Router?
- A **router** is a networking device used to connect different networks together.
- Unlike a switch, which connects devices inside one LAN, a router connects multiple LANs or connects a network to the Internet.
### 3.2 Main Functions of a Router
A router is used to:
1. Connect different LANs (networks or subnets) together.
2. Connect an entire network to the Internet (like a home router).
3. Perform multiple networking functions such as:
   - Firewall
   - Switch
   - IPS/IDS
   - Unified Communication Server
   - And more
### 3.3 Router Interfaces (Ports)
- A router has three main types of interfaces.

| Port Type | Function |
|------------|----------|
| **Console** | Used for management and configuration |
| **LAN** | Used to connect different networks together (not individual devices) |
| **WAN** | Used to connect the router to the Internet |
#### Notes
- A router has **fewer LAN interfaces** than a switch.
- A router connects **networks**, while a switch connects **devices**.
### 3.4 Home Router
- A typical home router usually has:
	- **4 LAN ports**
	- **1 WAN port**
	- **No console port**
- Instead of using a console cable, you manage it through a web browser:
```
192.168.1.1
```
- This address opens the router's management page.
- A home router is actually a **Home Gateway**.
- It works as a gateway between:
	- Your home network
	- Your Internet Service Provider (ISP)
- Unlike enterprise routers, home routers are managed through a web interface instead of a console port.
## 4. Firewall
### 4.1 What Is a Firewall?
- A **firewall** can be:
	- A hardware device : Better protection but more expensive
	- A software program : Less expensive and installed on operating systems
- **Its main purpose is to protect the network from attacks.**
### 4.2 Firewall Best Practice
The recommended configuration is:
- Block everything by default.
- Configure the firewall to allow only the required traffic.
### 4.3 Firewall Functions
Some firewall functions include:
- Filtering packets entering and leaving the network.
- Controlling user authentication and authorization.
- Protecting the network from attacks.
In most network designs, the firewall is placed facing the Internet.
### 4.4 Windows Firewall
In Windows:
- You can turn off the **Private Network Firewall**.
- You can turn off the **Public Network Firewall**.
Before doing that, check your network type in Windows Settings.
This tells you which firewall profile you are currently using.
## 5. IPS / IDS
### 5.1 What Are IPS and IDS?
- Both **IDS** and **IPS** are security systems that can be Hardware devices or Software programs
- They may also be built inside a router.
- Their job is to detect or stop network attacks.
### 5.2 IDS (Intrusion Detection System)
#### Function
- An **IDS** monitors packets moving through the network and analyzes them.
- When it detects an attack, it:
	- Sends an alert
	- Creates a log for the SOC (Security Operations Center)
- However, it **does not stop the attack** and **does not drop suspicious packets**.
#### Position in the Network
An IDS works **Out-of-Band**, meaning:
- It does not sit directly in the traffic path.
- It receives a copy of network traffic through a **SPAN** or **TAP** port.
#### Effect on Network Performance
Because it only analyzes copied traffic:
- It has **no effect on network latency**.
### 5.3 IPS (Intrusion Prevention System)

#### Function
- An **IPS** analyzes all incoming and outgoing traffic.
- Every packet passes through the IPS.
- When it detects an attack, it can:
	- Drop malicious packets
	- Close ports
	- Block the attacker's IP address
	- Generate logs
	- Send alerts
- Unlike IDS, IPS actively stops attacks.
#### Position in the Network
An IPS works **Inline**, meaning:
- It is directly in the traffic path.
- All network traffic passes through it.
#### Effect on Network Performance
Since every packet must be inspected before forwarding:
- IPS **may increase network latency**.
### IDS Vs IPS

| Feature | IDS | IPS |
|---------|-----|-----|
| **Full Name** | Intrusion Detection System | Intrusion Prevention System |
| **Main Function** | Detects attacks and generates alerts or logs | Detects attacks and blocks them |
| **Stops the Attack?** | No | Yes |
| **Packet Handling** | Does not drop packets | Can drop malicious packets |
| **Network Position** | Out-of-Band (receives copied traffic) | Inline (all traffic passes through it) |
| **Traffic Source** | SPAN/TAP Port | Direct traffic path |
| **Latency** | No effect on latency | May increase latency |

---

## 6. ACL (Access Control List)
- An **ACL (Access Control List)** is used to define permissions on a network.
- It allows or blocks specific users or devices.
### Example
On a home router, you can:
- Allow certain MAC addresses.
- Block all other MAC addresses.
In simple words:
- ACL determines who is allowed to access your network and what permissions they have.
## 7. Access Point (AP)

### 7.1 Network Interface Card (NIC)
- A **Network Interface Card (NIC)** allows a device to connect to a network.
- There are two main types of NICs.
#### 1. Internal NIC
- Built inside the device.
- It can be:
	- **Wired NIC** (such as the Ethernet port on a PC)
	- **Wireless NIC** (such as the Wi-Fi card in a laptop)
#### 2. External NIC
- Connected from outside the device.
Example:
- USB Wi-Fi Adapter (USB Antenna)
### 7.2 Notes About NICs
- A mobile phone usually has **one Wireless NIC**.
- A laptop usually has:
	- One Wired NIC
	- One Wireless NIC

To check the MAC addresses of your NICs in Windows:
1. Open **Command Prompt (CMD)**.
2. Run:
```bash
getmac
```
If your laptop has both wired and wireless NICs, the command usually displays **two MAC addresses**.
### 7.3 What Is an Access Point?
- An **Access Point (AP)** is a networking device whose main function is to **Convert a wired network (Ethernet) into a wireless network (Wi-Fi).**
- This allows wireless devices to connect to the existing network.
- One common use of an Access Point is allowing users who do not have a wired NIC (Ethernet port) to connect to the network wirelessly.
### 7.4 How Does an Access Point Work?
The Access Point is connected to:
- A switch
- Or the main router
using an **Ethernet cable**.

After receiving the wired connection, it broadcasts a **Wi-Fi signal** to nearby devices.

The AP acts as a gateway that allows wireless devices to access the rest of the network.
### 7.5 Common Uses of Access Points
#### 1. Enterprise and Large Buildings
Large buildings usually use multiple Access Points instead of relying on one router.

The APs are installed in different locations such as:
- Hallways
- Rooms
- Offices

All Access Points are connected back to a central switch.
#### 2. Seamless Roaming
Multiple Access Points can be configured with the same **SSID** (network name).

As users move through the building:
- Their devices automatically connect to the nearest AP.
- The wireless connection continues without interruption.
#### 3. Eliminating Dead Zones
Access Points help extend Wi-Fi coverage in places where the main router's signal is weak.

Examples:
- Large homes
- Large buildings
- Buildings with thick walls
### 7.6 Router vs Access Point

| Feature | Router | Access Point |
|---------|--------|--------------|
| **Primary Function** | Connects different networks, assigns IP addresses using DHCP, and manages NAT and routing | Converts a wired network into a wireless network to expand Wi-Fi coverage |
| **Coverage** | Covers a small apartment or office | Multiple APs can cover large buildings, hotels, or enterprises |
| **Internet Connection** | Connects directly to the ISP through the WAN interface | Does not connect directly to the Internet; it requires an upstream router |
### 7.7 Access Point Operating Modes

Most Access Points support multiple operating modes.
#### 1. Access Point Mode (Default)
- Receives a wired Ethernet connection.
- Broadcasts a Wi-Fi signal.
#### 2. Repeater / Extender Mode
- Receives an existing wireless signal.
- Re-broadcasts it to increase Wi-Fi coverage.
- Does **not** require an Ethernet cable.
#### 3. Client / Bridge Mode
- Receives a Wi-Fi signal.
- Converts it into a wired Ethernet connection.

Useful for devices that do not have built-in Wi-Fi, such as:
- Older PCs
- Smart TVs
### Why Is PoE (Power over Ethernet) Important for Access Points?
In enterprise networks, Access Points are usually installed in places such as:
- Ceilings
- Hallways
- Outdoor walls

These locations usually do **not** have nearby electrical power outlets.
### What Is PoE?
**PoE (Power over Ethernet)** allows a single Ethernet cable to carry:
- Network data
- Electrical power
at the same time.

This means there is no need to:
- Install separate electrical wiring.
- Use a separate power adapter for every Access Point.

A single Cat5e or Cat6 Ethernet cable can both power the Access Point and connect it to the network.

## 8. Servers
### 8.1 What Is a Server?
A **server** is a high-performance computer or software system designed to:
- Process requests
- Manage resources
- Deliver data or services

It provides these services to other computers, called **clients**, over:
- LAN (Local Area Network)
- WAN (Wide Area Network)

Unlike a normal PC, a server is designed for:
- Continuous operation
- High reliability
- Multiple users
- Heavy workloads
### 8.2 Main Functions of a Server
A server acts as a central resource supplier for clients.

It can provide many services, such as:
- Web hosting (Web Server)
- Email services (Mail Server)
- IP address assignment (DHCP Server)
- Database services
- File storage

Servers can provide these services to clients on:
- The same LAN
- Different networks over a WAN
### 8.3 Client-Server Architecture
Servers use the **Client-Server Architecture**.

The communication follows these steps:
1. The client sends a request.
2. The server processes the request.
3. The server sends the required response back to the client.

Example:
- A web browser requests a webpage.
- The web server processes the request.
- The webpage is returned to the browser.
### 8.4 High Availability and Reliability
Enterprise servers are designed to reduce downtime.

They may include:
- Dual power supplies
- RAID (hot-swappable hard drives)
- ECC (Error-Correcting Code) memory

These components help keep the server running even if hardware problems occur.
### 8.5 Concurrency
Unlike a normal PC, a server is built to:
- Handle thousands of users at the same time.
- Run many background processes efficiently.
### 8.6 Types of Server Deployment
There are three main types of servers.

| Type | Description |
|------|-------------|
| **Hardware Server (Physical)** | A physical machine with powerful processors, large RAM, and enterprise hardware (such as Cisco UCS rack or blade servers). |
| **Software Server** | An application or operating system that listens for network requests, such as Apache, Nginx, or Microsoft Exchange. |
| **Virtual Server** | A software-based server running on physical hardware using a hypervisor such as VMware or Hyper-V. Multiple virtual servers can run on one physical machine. |
## 9. IP Phone

### 9.1 What Is an IP Phone?
- An **IP Phone** is a telephone that communicates over an IP network instead of a traditional telephone network.
- It uses **VoIP (Voice over IP)** technology.
### 9.2 Functions of an IP Phone
An IP Phone:
1. Uses **VoIP (Voice over IP)** technology for voice communication.
2. Is widely used in:
   - Companies
   - Banks
   - Schools