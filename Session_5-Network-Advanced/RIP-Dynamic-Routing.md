[ITI - Summer Training 2026 ( Cybersecurity )-20260802_092430-Meeting Recording.mp4](https://itihub-my.sharepoint.com/personal/mosmahmoud_iti_gov_eg/_layouts/15/stream.aspx?id=%2Fpersonal%2Fmosmahmoud%5Fiti%5Fgov%5Feg%2FDocuments%2FRecordings%2Fcyber%2FITI%20%2D%20Summer%20Training%202026%20%28%20Cybersecurity%20%29%2D20260802%5F092430%2DMeeting%20Recording%2Emp4&ga=1&referrer=StreamWebApp%2EWeb&referrerScenario=AddressBarCopied%2Eview%2Ef71cb0bc%2Dc704%2D40bb%2Db020%2D6101ebfed6fd)

[ITI - Summer Training 2026 ( Cybersecurity )-20260802_113055-Meeting Recording.mp4](https://itihub-my.sharepoint.com/personal/mosmahmoud_iti_gov_eg/_layouts/15/stream.aspx?id=%2Fpersonal%2Fmosmahmoud%5Fiti%5Fgov%5Feg%2FDocuments%2FRecordings%2Fcyber%2FITI%20%2D%20Summer%20Training%202026%20%28%20Cybersecurity%20%29%2D20260802%5F113055%2DMeeting%20Recording%2Emp4&ga=1&referrer=StreamWebApp%2EWeb&referrerScenario=AddressBarCopied%2Eview%2Ef7159edb%2Daaf8%2D49a5%2D8715%2D2e426f288125)
- in static you write routes manual , here routes written dynamically
- ISPs use dynamic routing
- here you tell the router to turn on his dynamic protocol : advertisement all its networks and send it across all interfaces, now each router has all routes from other routers 
# interior protocols
- **autonomous system(AS):** single org like Vodafone has router (وزارة) 
- AS communicate with other AS , use exterior protocols like BGP
- internet use exterior protocols

- **Administrative Distance :** number in each protocol 
	- RIP : 120
	- EIGRP : 90
	- OSPF : 110
	- less AD in the network , more protocol priority ; if there more than protocol running at the same time in your entire network 
## 1. RIP(Routing Information Protocol)
- Metric : 
	- you have more than path to your destination router, one has less hops than other
	- metric means number of hops
	- depend on metric, i choose best path to send data
	- each protocol choose with its way
	- RIP choose best path based on number of hops to destination router : less hops -> best path to send data
	- max hops for this protocol : 15 hops
	- if there is same hops number in more than one path : load balance , work on the same paths together
	- network connected directly in any router : metric 0
- GIVE a simple example for RIP 
- poisoning table: explain and give example, solution is 
	- split horizon: explain and give example
	- trigger update:  explain and give example
- we need to remove unuseful traffic to switches, we need to send routing info to only routers (for security) : we define interface connected to switch as passive interface
- RIP works on IP `224.0.0.9` 
### RIP Configuration
![rip](rip.png)
#### First define gateways on router0
```
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
#### second define gateways on router1
```
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
#### configure RIP
- we here write the networks that each router can connect , to advertise them ; على عكس static routing we write networks that each router can't connect
##### in router0
```
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
##### in router1
```
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
- we didn't write subnet mask next to ip address because we use default subnet mask for class c (/24), if we didn't use default subnet mask , we write subnet mask

- RIP has 2 versions : 1, 2
	- you must enter the same rip version on all routers
	- rip version 1 : 
		- classfull , it know IP classes and its default subnet masks
		- it works with no authentication between routers (no password on all routers): EXPLAIN this
	- rip version 1 : classless
- how to choose version:
  ```
  Router(config-router)#version 1
  ```
#### security
- we need to remove unuseful traffic to switches, we need to send routing info to only routers (for security) : we define interface connected to switch as passive interface
- also routers gives info about its networks to its all interfaces, we need to make it advertise its network to interface connected only to the other router
- in the two routers : you do this
```
Router(config-router)#passive-interface g0/1
Router(config-router)#passive-interface g0/2
```

- we here test from router not PC , `show ip route` : you will see R which stand for RIP
- from router0 :
```
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
- you can see that now it can connect to network 4,5 via 192.168.3.2 which is the interface ip in router1 connected to router0
- 120/1 : 120 administrative Distance, 1 is metric

- from router1:
```
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
- you can see that now it can connect to network 1,2 via 192.168.3.1 which is the interface ip in router1 connected to router0

- explain why we put network 3 in each router config ???

- now give laptops ip address and test connection between network, it will success
## 2. EIGRP
## 3. OSPF


# exterior protocol BGP