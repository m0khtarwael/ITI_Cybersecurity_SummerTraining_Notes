# Dynamic Routing
- In static routing, the network administrator writes every route by hand.
- In dynamic routing, routers learn routes automatically by using a routing protocol.
- Internet Service Providers, also called ISPs, use dynamic routing because their networks are large and routes can change.
- To use dynamic routing, **we turn on a dynamic routing protocol on each router**.

The router then does the following:
1. Finds its directly connected networks.
2. Advertises, or tells other routers about these networks.
3. Sends routing information through its interfaces.
4. Receives routing information from other routers.
5. Adds the learned routes to its routing table.

After the routers exchange their routing information, each router can learn how to reach networks connected to the other routers.

---
# Routing Protocols
- **Interior routing protocols** are used inside **one organization or one managed network**, these protocols are RIP, EIGRP, OSPF.
- **Exterior routing protocols** are used to exchange routing information **between different Autonomous Systems**, such as between large companies, Internet providers, or organizations.
## Autonomous System
- An **Autonomous System**, also called an **AS**, is a group of routers and networks managed by one organization.
- Each AS has a number value between 1-65535

For example:
- Vodafone can have its own routers and networks.
- A government ministry can have its own routers and networks.
- A large company can manage its network as one Autonomous System.

- **Different Autonomous Systems** communicate with each other by **using an exterior routing protocol such as BGP**.
- The Internet is made of many Autonomous Systems connected together.
---
## Administrative Distance
- **Administrative Distance**, also called **AD**, is a **number used by a router to choose between routes learned from different routing protocols**.
- The Administrative Distance values for routing protocols are:
	- RIP: `120`
	- EIGRP: `90`
	- OSPF: `110`
	
- A lower Administrative Distance is preferred.
- For example, suppose a router learns the same destination network from RIP and OSPF:
	- RIP has an AD of `120`.
	- OSPF has an AD of `110`.
- The router chooses the OSPF route because `110` is lower than `120`.

- Administrative Distance is important when more than one routing protocol is running and the router learns the same destination from different protocols.

---

# 1. RIP — Routing Information Protocol
- RIP is a dynamic routing protocol.
- Routers that use RIP share routing information with each other.
- **RIP sends its routing table to neighboring routers every 30 seconds.**
## RIP Metric
- A **metric** is the value a routing protocol uses to choose the best path.
- Different routing protocols use different ways to choose the best path.
- In RIP, the **metric is hop count**, meaning the number of routers a packet must pass through to reach the destination network.
- One hop means that the packet passes through one router.
- RIP chooses the path with the lowest number of hops.

For example:
- Path 1 has two hops.
- Path 2 has four hops.
RIP chooses Path 1 because two hops are fewer than four hops.

- The largest usable hop count in RIP is `15`.
- A route with a hop count of `16` is treated as unreachable.
- A network that is directly connected to a router has a metric of `0` on that router.
## Equal-Cost Load Balancing
- If RIP finds two or more paths with the same number of hops, it can use the paths together.
- This is called **equal-cost load balancing**.

For example:
- Path 1 has two hops.
- Path 2 also has two hops.
RIP can use both paths because they have the same metric.
---
## Simple RIP Example
Assume that we have three routers:
```text
Network A --- Router0 --- Router1 --- Router2 --- Network B
````
- Router0 knows Network A because Network A is directly connected to it.
- Router2 knows Network B because Network B is directly connected to it.

**After RIP is enabled:**
1. Router0 tells Router1 about Network A.
2. Router2 tells Router1 about Network B.
3. Router1 sends the learned information to the other routers.
4. Router0 learns that Network B can be reached through Router1.
5. Router2 learns that Network A can be reached through Router1.

From Router0, Network B is two hops away:
```text
Router0 -> Router1 -> Router2
```
RIP adds this route to Router0's routing table.

---
# 2. Routing Loops
- A **routing loop** happens when routers keep sending a packet to each other because they have wrong or old routing information.
## Routing Loop Example
Assume that Router0 reaches Network X through Router1:
```text
Router0 ---- Router1 ---- Network X
```
Now suppose Network X goes down:
- Router1 knows that Network X is down, but Router0 may still have the old route for a short time.
- Router0 may tell Router1 that it can reach Network X.
- Router1 may then believe that Router0 has another path to Network X.
- Router1 sends the packet to Router0.
- Router0 sends it back to Router1.
- The packet may continue moving between the two routers until TTL ends.
- This is called a **routing loop**.
## 2.1 Routing Loops Solutions
### 1. Route Poisoning
- **Route poisoning** is used to tell other routers that a route is no longer usable.
- In RIP, the router gives the failed route a metric of `16`.
- RIP accepts routes with a maximum of `15` hops. Because of this, a metric of `16` means that the network is unreachable.
#### Route Poisoning Example
- Assume Router1 is connected to Network X.
- When Network X goes down, Router1 sends an update like this:
```text
Network X: metric 16
```
- The other routers then know that Network X is unreachable.
- They remove the usable route or mark it as unreachable.
- This helps stop routers from sending packets through a broken path.
### 2. Split Horizon
- **Split horizon** means that a router does not advertise a route back through the same interface from which it learned the route. **This helps prevent routing loops.**
#### Split Horizon Example
- Assume Router0 learns about Network X from Router1 through interface `g0/0`.
- Router0 will not advertise Network X back to Router1 through the same `g0/0` interface.
- Router0 learned the route from Router1, so there is no reason to tell Router1 that Router0 can reach the same network.
- Without split horizon, Router1 may think that Router0 has another path to Network X.
- This wrong information can create a routing loop.
### 3. Triggered Update
- RIP normally sends routing updates after a set amount of time.
- A **triggered update** is sent immediately when an important route change happens.
- The router does not wait for the next normal update.
- Triggered updates do not prevent loops by themselves. They mainly make routers learn about failures faster.
#### Triggered Update Example
- Assume Router1 is connected to Network X.
- If Network X goes down, Router1 immediately sends an update saying that Network X is unreachable.
- This allows the other routers to update their routing tables faster.
- Triggered updates reduce the time during which routers may use old routing information.
# 3. Passive Interface
- By default, RIP may send routing updates through all interfaces that are included in RIP.
- Some interfaces may connect to other routers. Other interfaces may connect to switches, laptops, or PCs.
- **There is no need to send RIP updates to laptops, PCs, or normal switches because they do not need routing information.**
- To stop RIP updates from being sent through an interface, we make that interface a **passive interface**.
- A passive interface does not send RIP update messages through that interface.
- However, the connected network can still be advertised to other routers through the router-to-router interface.
- **Using passive interfaces has two main benefits:**
	1. It removes unneeded RIP traffic from user networks.
	2. It improves security because user devices do not receive routing updates.
---
# 4. RIP Configuration
![RIP Network](pics/rip.png)
- The network contains two routers.
- **Router0 is connected to:**
	- Network `192.168.1.0/24`
	- Network `192.168.2.0/24`
	- Network `192.168.3.0/24`
- **Router1 is connected to:**
	- Network `192.168.3.0/24`
	- Network `192.168.4.0/24`
	- Network `192.168.5.0/24`

- Network `192.168.3.0/24` is the connection between Router0 and Router1.
## Step 1: Configure the Interfaces on Router0
```text
Router(config)#int g0/0
Router(config-if)#ip add 192.168.3.1 255.255.255.0
Router(config-if)#no sh
Router(config-if)#exit

Router(config)#int g0/1
Router(config-if)#ip add 192.168.1.1 255.255.255.0
Router(config-if)#no sh
Router(config-if)#exit

Router(config)#int g0/2
Router(config-if)#ip add 192.168.2.1 255.255.255.0
Router(config-if)#no sh
Router(config-if)#exit
```
## Step 2: Configure the Interfaces on Router1
```text
Router(config)#int g0/0
Router(config-if)#ip add 192.168.3.2 255.255.255.0
Router(config-if)#no sh
Router(config-if)#exit

Router(config)#int g0/1
Router(config-if)#ip add 192.168.5.1 255.255.255.0
Router(config-if)#no sh
Router(config-if)#exit

Router(config)#int g0/2
Router(config-if)#ip add 192.168.4.1 255.255.255.0
Router(config-if)#no sh
Router(config-if)#exit
```
## Step 3: Configure RIP
- **In RIP configuration, we write the networks that are directly connected to the router and should use RIP.**
- The router then advertises these connected networks to other RIP routers. 
- **This is different from static routing**. In static routing, we normally write a route to a remote network that the router cannot reach directly.
- In RIP, the `network` command selects directly connected networks that should take part in RIP.
### Configure RIP on Router0
```text
Router(config)#router ?
  bgp    Border Gateway Protocol (BGP)
  eigrp  Enhanced Interior Gateway Routing Protocol (EIGRP)
  ospf   Open Shortest Path First (OSPF)
  rip    Routing Information Protocol (RIP)

Router(config)#router rip
Router(config-router)#network 192.168.1.0
Router(config-router)#network 192.168.2.0
Router(config-router)#network 192.168.3.0
Router(config-router)#ex
Router(config)#
```
- **Router0 advertises these networks:**
	- `192.168.1.0`
	- `192.168.2.0`
	- `192.168.3.0`
### Configure RIP on Router1
```text
Router(config)#router ?
  bgp    Border Gateway Protocol (BGP)
  eigrp  Enhanced Interior Gateway Routing Protocol (EIGRP)
  ospf   Open Shortest Path First (OSPF)
  rip    Routing Information Protocol (RIP)

Router(config)#router rip
Router(config-router)#network 192.168.3.0
Router(config-router)#network 192.168.4.0
Router(config-router)#network 192.168.5.0
Router(config-router)#ex
Router(config)#
```
- **Router1 advertises these networks:**
	- `192.168.3.0`
	- `192.168.4.0`
	- `192.168.5.0`
### Why Do We Add Network 192.168.3.0 on Both Routers?
- Network `192.168.3.0/24` is directly connected to both Router0 and Router1.
- Router0 uses this address:
```text
192.168.3.1
```
- Router1 uses this address:
```text
192.168.3.2
```
- This network is the link between the two routers.
- It allows Router0 and Router1 to communicate with each other.
- It also allows them to exchange RIP routing updates.
- We add this command on Router0:
```text
network 192.168.3.0
```
- We also add it on Router1:
```text
network 192.168.3.0
```
- **This tells both routers to run RIP on the interfaces connected to Network 3.**
- **Without Network 3 in the RIP configuration, the two routers may not exchange RIP updates through this link.**

---

## Step 4: RIP Security Using Passive Interfaces
- In this network:
	- `g0/0` connects Router0 to Router1.
	- `g0/1` and `g0/2` connect to local networks and switches.
- Because of this, we make `g0/1` and `g0/2` passive interfaces.
- Run these commands **on both routers**:
```text
Router(config-router)#passive-interface g0/1
Router(config-router)#passive-interface g0/2
```
- After these commands:
	- RIP does not send updates through `g0/1`.
	- RIP does not send updates through `g0/2`.
	- RIP can still advertise the networks connected to these interfaces.
	- RIP updates are still sent through `g0/0` to the other router.
- This removes unneeded traffic and gives better security.

---

## Step 5: Configure the Laptops and Test the Connection
- After the routers and RIP are configured, give each laptop an IP address.
- Each laptop must have:
	1. An IP address from its local network.
	2. The correct subnet mask.
	3. The router interface as its default gateway.
- To test the connection, send a ping from one laptop to a laptop in another network.
# 5. Subnet Mask Note
- The RIP `network` command does not include a subnet mask in this configuration.
- For an interface IP address, we write the IP address and the subnet mask:
```text
ip address 192.168.3.1 255.255.255.0
```
- For a RIP network command, we write the network address:
```text
network 192.168.3.0
```
- The networks in this example use `/24`.

- The subnet mask was not written **because the default Class C mask was used**.
- In this example, all networks use the Class C default mask, which is `/24`.
# 6. RIP Versions
- RIP has two main versions:
	1. RIP version 1
	2. RIP version 2
- **Routers that exchange RIP routes should use the same version.**
- The RIP version can be selected with this command:
```text
Router(config-router)#version 1
```
- To use RIP version 2, use:
```text
Router(config-router)#version 2
```
## RIP Version 1
- RIP version 1 is **classful**. This means **it does not send subnet mask information inside its routing updates**.
- Because it does not send the subnet mask, **it can't work with different subnet sizes**.
- It depends on the old IP address classes and their default subnet masks.
- The default masks are:
	- Class A: `/8`
	- Class B: `/16`
	- Class C: `/24`
- RIP version 1 does not support authentication between routers.
### What Does “No Authentication” Mean?
- **Authentication means checking that a routing update came from a trusted router.**
- With authentication, routers can use a shared password or key.
- RIP version 1 does not have this password check.
- This means a device could send a false RIP version 1 update, and the router would not have a built-in RIP password check to confirm whether the sender is trusted.
- Because of this, **RIP version 1 is less secure.**
## RIP Version 2
- RIP version 2 is **classless**. This means **it sends subnet mask information with its routing updates**.
- Because it sends the subnet mask, **it can work with different subnet sizes**.
- RIP version 2 also **supports authentication** between routers.
- Authentication allows routers to use a shared password or key to check routing updates.
- This helps prevent unknown devices from sending false RIP updates.
- RIP version 2 also uses the multicast IP address:
```text
224.0.0.9
```
- This address is used to send RIP version 2 updates to RIP routers on the local network.

---

# 7. Testing RIP
- **To check the routing table from a router**, use:
```text
show ip route
```
- **Routes learned through RIP start with the letter**:
```text
R
```
- The letter `R` means that the route was learned through RIP.
## Router0 Routing Table
```text
     192.168.1.0/24 is variably subnetted, 2 subnets, 2 masks
C       192.168.1.0/24 is directly connected, GigabitEthernet0/1
L       192.168.1.1/32 is directly connected, GigabitEthernet0/1
     192.168.2.0/24 is variably subnetted, 2 subnets, 2 masks
C       192.168.2.0/24 is directly connected, GigabitEthernet0/2
L       192.168.2.1/32 is directly connected, GigabitEthernet0/2
     192.168.3.0/24 is variably subnetted, 2 subnets, 2 masks
C       192.168.3.0/24 is directly connected, GigabitEthernet0/0
L       192.168.3.1/32 is directly connected, GigabitEthernet0/0
R    192.168.4.0/24 [120/1] via 192.168.3.2, 00:00:01, GigabitEthernet0/0
R    192.168.5.0/24 [120/1] via 192.168.3.2, 00:00:01, GigabitEthernet0/0
```
### Router0 Routing Table Explanation
- Router0 can now reach Network 4 and Network 5.
- It reaches both networks through:
```text
192.168.3.2
```
- The address `192.168.3.2` belongs to Router1's interface connected to Router0.

- This line:
```text
R 192.168.4.0/24 [120/1] via 192.168.3.2, 00:00:01, GigabitEthernet0/0
```
- means:
	- `R` means the route was learned using RIP.
	- `192.168.4.0/24` is the destination network.
	- `120` is the Administrative Distance of RIP.
	- `1` is the RIP metric - hop count . (In this network, packet must pass through only one router to reach the destination network.)
	- `192.168.3.2` is the next router.
	- `GigabitEthernet0/0` is the interface used to send the packet.
## Router1 Routing Table
```text
R    192.168.1.0/24 [120/1] via 192.168.3.1, 00:00:21, GigabitEthernet0/0
R    192.168.2.0/24 [120/1] via 192.168.3.1, 00:00:21, GigabitEthernet0/0
     192.168.3.0/24 is variably subnetted, 2 subnets, 2 masks
C       192.168.3.0/24 is directly connected, GigabitEthernet0/0
L       192.168.3.2/32 is directly connected, GigabitEthernet0/0
     192.168.4.0/24 is variably subnetted, 2 subnets, 2 masks
C       192.168.4.0/24 is directly connected, GigabitEthernet0/2
L       192.168.4.1/32 is directly connected, GigabitEthernet0/2
     192.168.5.0/24 is variably subnetted, 2 subnets, 2 masks
C       192.168.5.0/24 is directly connected, GigabitEthernet0/1
L       192.168.5.1/32 is directly connected, GigabitEthernet0/1
```
### Router1 Routing Table Explanation
- Router1 can now reach Network 1 and Network 2.
- It reaches both networks through:
```text
192.168.3.1
```
- The address `192.168.3.1` belongs to Router0's interface connected to Router1.

- This line:
```text
R    192.168.1.0/24 [120/1] via 192.168.3.1, 00:00:21, GigabitEthernet0/0
```
- means:
	- `R` means the route was learned using RIP.
	- `192.168.1.0/24` is the destination network.
	- `120` is the Administrative Distance of RIP.
	- `1` is the Router metric - hop count . (In this network, packet must pass through only one router to reach the destination network.)
	- `192.168.3.1` is the next router.
	- `GigabitEthernet0/0` is the interface used to send the packet.