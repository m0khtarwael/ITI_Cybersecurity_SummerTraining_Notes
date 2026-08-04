- **VLAN Trunking Protocol (VTP) is a Layer 2 Cisco protocol used to automate the management and synchronization of VLAN configurations across a network of switches.** Instead of manually creating, updating, or deleting VLANs on every individual switch, VTP allows you to make changes on a central switch and automatically propagate those changes to all other connected switches in the same VTP domain.

---
# 1. Prerequisites for VTP
To ensure VTP functions properly across your network switches, the following requirements must be met:
* **Trunk Connections:** Switches must be interconnected via trunk links (e.g., using 802.1Q encapsulation). VTP updates travel strictly over trunk lines.
* **Matching Domain Name:** All participating switches must share the exact same VTP domain name (case-sensitive).
* **Matching Password:** If a VTP password is set, it must match across all switches in the domain.
* **Matching VTP Version:** Switches must run compatible VTP versions (e.g., Version 1 or Version 2).

---
# 2. VTP Operational Modes
VTP operates in one of three primary modes:
### 1. Server Mode
* **Default State:** Switches are in VTP Server mode by default.
* **Functionality:** 
	* Allows administrators to create, modify, and delete VLANs.
	* Advertises its VLAN database to other switches in the domain.
	* Saves the VLAN configuration in NVRAM (`vlan.dat`).
	* Syncs its VLAN configuration with received VTP advertisements if the revision number is higher.
#### Configuration Revision Number
- In VTP, the **Configuration Revision Number** is a 32-bit counter that acts as a version controller for your VLAN database. Every single time you make a change to the VLAN structure on a VTP Server (create a VLAN, delete a VLAN, or rename a VLAN), the revision number **increments by 1**.
- When a switch advertises its VLAN database to neighboring switches over trunk links, it sends this revision number along with the payload.
##### How the Revision Sync Works
Whenever a switch (in Client or Server mode) receives a VTP advertisement from another switch in the same VTP domain, it compares the incoming revision number to its own local revision number:
```
Incoming Revision > Local Revision  ===> OVERWRITE local database with incoming data
Incoming Revision < Local Revision  ===> IGNORE incoming packet (local data is newer)
Incoming Revision = Local Revision  ===> DO NOTHING (databases are already identical)
```
##### The Step-by-Step Mechanism
1. **State 0 (Baseline):** Server A and Client B both have Revision **`5`** with VLANs `10` and `20`.
2. **The Change:** You log into Server A and create **VLAN 30**.
3. **The Increment:** Server A automatically bumps its Configuration Revision Number from **`5`** to **`6`**.
4. **The Broadcast:** Server A sends out a VTP **Summary Advertisement** frame out all its trunk ports containing `Domain: iti.com`, `Revision: 6`, and the updated VLAN payload.
5. **The Synchronization:** Client B receives the frame, checks its local database, and sees:
$$\text{Incoming Revision (6)} > \text{Local Revision (5)}$$
- Because the incoming revision is higher, Client B immediately **wipes its local VLAN table** and replaces it with Server A's database (now containing VLANs `10`, `20`, and `30`). It then updates its own local revision number to **`6`**.
##### Why This Matters (The Security Risk)
- This exact mechanism—blindly trusting any frame with a **higher revision number**—is what makes VTP historically dangerous if not managed properly.
##### The Rogue Switch Scenario:
Imagine you bring an old switch from a storage room that was previously used in another part of the network.
- This old switch happens to have a VTP Revision Number of **`45`**.
- You plug it into your live network without wiping it first.
- It sends out a VTP advertisement with Revision **`45`**.
- Your live switches (currently on Revision **`6`**) see that $45 > 6$.
- **Result:** Every switch in the domain overwrites its operational VLAN database with the old switch's configuration—instantly dropping network traffic across the entire enterprise.
##### How to protect your network from a VTP Revision Number Attack (VTP Bombing)
- **Use VTP Transparent Mode (Modern Best Practice)**
	- Most modern enterprise networks bypass VTP Server/Client sync entirely. Setting switches to `transparent` allows them to pass VTP frames to downstream switches without adopting foreign databases or risking unintended wipes.
```
Switch(config)# vtp mode transparent
```
##### Key Takeaway
> The Configuration Revision Number is **not** tied to a specific switch's role (Server vs. Client). It is simply a counter. **Whichever device broadcasts the highest revision number rules the domain.**
### 2. Client Mode
* **Functionality:** 
	* Receives and applies VLAN updates from the VTP Server.
	* Forwards VTP advertisements out its trunk interfaces to other switches.
	* Syncs its VLAN database dynamically based on the Server's revision number.
* **Limitations:** 
	* Switches in Client mode **cannot** create, modify, or delete VLANs manually.
	* Client configurations are saved in RAM only (lost upon reboot until re-synchronized with the Server).
* **Important Note on Port Assignment:** VTP only synchronizes **VLAN IDs and names**, not port assignments. Because different switches have varying numbers of ports and module configurations, switch ports must still be assigned to their respective VLANs manually on each individual switch.
### 3. Transparent Mode
- In **VTP Transparent Mode**, a switch does **not** participate in the automated VTP synchronization of VLAN databases. It acts as an independent entity regarding its local VLAN configurations, but continues to act as a bridge for VTP traffic traveling across the network.
* **Functionality:** 
	* Does **not** synchronize its VLAN database with VTP Server advertisements.
	* Does **not** advertise its local VLAN modifications to the rest of the VTP domain.
	* When a Transparent switch receives VTP summary advertisements on a trunk port, it **forwards** them out its other trunk ports to downstream switches. This ensures that Client and Server switches further down the line can still synchronize with each other.
	* Unlike VTP Version 1 and 2 in Server/Client mode (which only support Normal Range VLANs 1–1005), Transparent mode supports **Extended Range VLANs (IDs 1006–4094) without needing VTP Version 3** because the local VLAN database is stored in the switch configuration file (`running-config` / `startup-config`) rather than the VTP database file (`vlan.dat`).
	* **Complete Protection Against VTP Attacks:** Immune to accidental database wipes caused by rogue switches with higher Revision Numbers.
### Summary
| Feature / Function                 | Server Mode        | Client Mode       | Transparent Mode         |
| :--------------------------------- | :----------------- | :---------------- | :----------------------- |
| **Create / Modify / Delete VLANs** | Yes                | No                | Yes (Local switch only)  |
| **Syncs Database with Server**     | Yes                | Yes               | No                       |
| **Advertises Local VLAN Changes**  | Yes                | No                | No                       |
| **Forwards VTP Advertisements**    | Yes                | Yes               | Yes (v2 & v3)            |
| **VLAN Storage Location**          | NVRAM (`vlan.dat`) | RAM (Dynamic)     | NVRAM (`running-config`) |
| **Extended VLANs (1006–4094)**     | No (in VTP v1/v2)  | No (in VTP v1/v2) | Yes                      |

---
# 3. Security Consideration: VTP Domain & Re-configuration Vulnerability #########
* By default, a factory-fresh switch has a domain name set to `NULL`.
* If a switch with a `NULL` domain receives a VTP advertisement, it will automatically adopt the domain name contained within that advertisement.
* **Security Risk (Revision Number Attack):** If an old or newly introduced switch with a higher **Configuration Revision Number** is connected to an existing VTP domain, it can overwrite the entire domain's VLAN database—potentially wiping out operational VLANs across all switches.
* **Mitigation:**
  1. Always set a strong **VTP Password**.
  2. Reset a new switch's Revision Number to `0` before inserting it into a live network (e.g., by changing its domain to a temporary name and back, or changing its mode to Transparent and back).

---
# 4. Step-by-Step Configuration Example
Below is the CLI configuration sequence to set up VTP between a **Server Switch (Switch0)** and a **Client Switch (Switch1)** over a trunk link.
### 1. Configure the VTP Server (Switch0)
```cli
Switch0# configure terminal
! Create VLANs on the server
Switch0(config)# vlan 10
Switch0(config-vlan)# exit
Switch0(config)# vlan 20
Switch0(config-vlan)# exit

! Set VTP Mode to Server (default)
Switch0(config)# vtp mode server

! Set VTP Domain Name
Switch0(config)# vtp domain iti.com

! Set VTP Password
Switch0(config)# vtp password 123
Switch0(config)# exit
```
### 2. Configure the VTP Client (Switch1)
```
Switch1# configure terminal
! Set VTP Mode to Client
Switch1(config)# vtp mode client

! Match the VTP Domain Name and Password
Switch1(config)# vtp domain iti.com
Switch1(config)# vtp password 123
Switch1(config)# exit
```
### 3. Configure the Trunk Link Between Switches
- Trunking must be explicitly configured on the connecting interfaces so VTP frames can travel between switches.
```
! On Switch0 (Server)
Switch0# configure terminal
Switch0(config)# interface gigabitethernet 0/1
Switch0(config-if)# switchport mode trunk
Switch0(config-if)# exit

! On Switch1 (Client)
Switch1# configure terminal
Switch1(config)# interface gigabitethernet 0/1
Switch1(config-if)# switchport mode trunk
Switch1(config-if)# exit
```

- **Operational Note:** If VLANs created on the server before establishing the VTP/Trunk connection do not immediately reflect on client switches when executing `show vlan brief`, triggering a minor database change on the Server (e.g., adding a temporary VLAN or renaming one) forces a VTP database update broadcast to bring all clients fully in sync.