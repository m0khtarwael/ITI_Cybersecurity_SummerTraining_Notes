- Instead of console cable, we can connect to switch through network
- **VTY** stands for **Virtual Teletype** (or **Virtual Terminal**).
- It is a virtual command-line interface (CLI) port that allows remote access to a network device—such as a Cisco router, switch, or firewall—over a network connection using protocols like **SSH** or **Telnet**.
# Telnet
- To connect to switch, **switch must have IP** and you can connect with this IP if through the same network (using FastEthernet cable not console cable) or via Routing
- VTY can open **16 lines** (lines 0 through 15) — which means 16 devices can connect to the switch remotely at the same time
- **switch physically can't have IP** , so we assign an IP to its VLAN (By default, switch runs on VLAN 1) ; we will treat this VLAN as an interface and give it IP address 
- all switch ports are in VLAN 1 by default
- You must connect to switch using Ethernet cable not console cable
### How to configure VTY through Packet Tracer?
1. **Assign an IP Address via Switch CLI**
  ```
  Switch>en
  Switch#conf t
  Switch(config)#int
  Switch(config)#interface vlan 1
  Switch(config-if)#no shutdown
  Switch(config-if)#ip address 192.168.1.2 255.255.255.0
  Switch(config-if)#exit
  ```
2. **Configure VTY on switch**
   ```
   Switch(config)#line vty 0
   Switch(config-line)#password 1234
   Switch(config-line)#login
   Switch(config-line)#exit
   ```
	- you can configure VTY login through telnet with user and password:
	  ```
	  Switch(config)#username user1 pass pass1
	  Switch(config)#line vty  0
	  Switch(config-line)#login local 
	  Switch(config-line)#exit
	  ```
3. **Assign an static IP to your laptop**
4. **Click on your laptop and choose Command Prompt from Desktop tab**
   ```
   ping 192.168.1.2 //check connection between switch and laptop
   telnet 192.168.1.2 // manage switch remotely
   Password: 1234 // write your password
   Switch>enable
   % No password set // in VTY , you should set another password for enable mode to avoid unauthorized access
   ```
5. **Return to switch CLI**
   ```
   Switch(config)# enable password 123
   ```
	- `enable password 123` is Type 7 encrypted and it is easy to decrypt, so we use `enable secret 1234` where password is md5 encrypted ; to verify encryption `show running-config`
	- Note that if there is password 123 and secret 1234 , secret will be used not password ; it overrides password
6. **Return to Laptop's Command Prompt**
   ```
   Switch>enable
   Password: 123 // write password here
   Switch#
   ```

- To cancel specific command, you write `no` before the command like `no service password-encryption` for console port encryption, BUT NOTE THAT it isn't decryption ; if you write `show running-config` you will see password encrypted — it only stop encryption new password