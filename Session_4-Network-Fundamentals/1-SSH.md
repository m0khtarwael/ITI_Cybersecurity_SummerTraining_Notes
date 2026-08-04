- Normally, we configure a switch using a **console cable**.
- However, instead of using a console cable every time, we can configure and manage the switch **through the network** using **VTY (Virtual Terminal) lines**.
---
# What is VTY?
- **VTY** stands for **Virtual Teletype** (also called **Virtual Terminal**).
- It is a virtual CLI (Command Line Interface) that allows you to remotely access a network device (such as a Cisco switch or router) over the network.
- VTY supports remote access protocols such as:
	- **Telnet**
	- **SSH** (recommended because it is encrypted and more secure)

---
# Step 1: Configure VTY
Before you can remotely connect to a switch, you must configure the VTY lines.
### 1. Assign an IP address to the switch
- A Layer 2 switch needs a management IP address so that devices on the network can reach it.
- Configure the IP address on the management VLAN (usually VLAN 1).
```
Switch> enable
Switch# configure terminal

Switch(config)# interface vlan 1
Switch(config-if)# no shutdown
Switch(config-if)# ip address 192.168.1.2 255.255.255.0
Switch(config-if)# exit
```

---
### 2. Configure the VTY lines
- First, create a local username and password.
- Then configure the VTY lines to use the local user database for authentication.
```
Switch(config)# username user1 password 123

Switch(config)# line vty 0 4
Switch(config-line)# login local
Switch(config-line)# exit
```
#### Notes
- `username user1 password 123`
	- Creates a local user named **user1** with password **123**.
- `line vty 0 4`
	- Configures VTY lines **0 through 4**.
	- This allows up to **5 simultaneous remote sessions**.
- `login local`
  - Tells the switch to authenticate using the local username and password.

> If you use `login local`, the command `password 1234` under the VTY line is **not needed** because authentication uses the local username database instead.
>
> A VTY line password is only used with the `login` command (without `local`).

- Example of the old method (not recommended when using local users):
```
Switch(config)# line vty 0 4
Switch(config-line)# password 1234
Switch(config-line)# login
```
- Recommended method:
```
Switch(config)# username user1 password 123
Switch(config)# line vty 0 4
Switch(config-line)# login local
```

---
# Step 2: Configure SSH
- SSH is the secure way to remotely access a switch.
- Unlike Telnet, SSH encrypts all traffic, including usernames and passwords.
- Before SSH can work, several requirements must be met.
- **SSH** stands for **Secure Shell**.
## SSH Requirements
### 1. Configure VTY
VTY must already be configured.

---
### 2. Change the default hostname
- The hostname **cannot remain the default (`Switch`)**.
Example:
```
Switch(config)# hostname ITI
```

---
### 3. Configure a domain name
- A domain name is required before generating RSA keys.
Example:
```
ITI(config)# ip domain-name iti.com
```
- The domain name usually represents the company or organization.
---
### 4. Generate RSA keys
- SSH requires RSA encryption keys.
```
ITI(config)# crypto key generate rsa

The name for the keys will be: ITI.iti.com

Choose the size of the key modulus in the range of 360 to 4096.

How many bits in the modulus [512]: 1024

% Generating 1024 bit RSA keys, keys will be non-exportable...[OK]
```
### Notes
- Larger key sizes provide **better security**.
- Larger keys also require **more processing time**.
- In modern networks, **2048 bits or higher** is recommended. Packet Tracer labs often use **1024 bits** because it generates faster.
---
### 5. Configure the Enable Secret
- SSH must protect privileged EXEC mode with an enable secret.
```
ITI(config)# enable secret 123
```
- This password is required when entering **enable mode** after logging in remotely.
Example:
```
ITI> enable
Password: 123
ITI#
```

---
# Connecting to the Switch Using SSH
From a PC or laptop command prompt:
```
ssh -l user1 192.168.1.2
```
or
```bash
ssh user1@192.168.1.2
```
You will be asked for the user's password:
```text
Password: 123
ITI>
```
### Why use SSH?
- Encrypts all communication.
- Protects usernames and passwords.
- Provides accountability because every user logs in with their own username.
---
# Telnet vs SSH

## Telnet
- Not encrypted.
- Usernames and passwords travel as plain text.
- Less secure.
- Should only be used in lab environments.
---
## SSH
- Encrypted.
- Much more secure.
- Recommended for production networks.
- Supports user authentication and accountability.
---
# By Default
- If SSH is configured and VTY lines allow all protocols, **both SSH and Telnet can work**.
You can verify the available transport protocols:
```
ITI(config)# line vty 0 4
ITI(config-line)# transport input ?
  all     All protocols
  none    No protocols
  ssh     TCP/IP SSH protocol
  telnet  TCP/IP Telnet protocol
```

---
# Allow Only SSH (Disable Telnet)
To improve security, disable Telnet and allow only SSH.
```
ITI(config)# line vty 0 4
ITI(config-line)# transport input ssh
ITI(config-line)# exit
ITI(config)# exit
ITI# exit
```

Now, if someone tries Telnet:
```bash
telnet 192.168.1.2
```
The connection will immediately close because Telnet is no longer allowed.

Example:
```text
Trying 192.168.1.2...
Open

[Connection to 192.168.1.2 closed by foreign host]
```

- SSH will continue to work normally.
---
# Complete Configuration Example
```
enable
configure terminal

hostname ITI

interface vlan 1
no shutdown
ip address 192.168.1.2 255.255.255.0
exit

username user1 password 123

enable secret 123

ip domain-name iti.com

crypto key generate rsa
1024

line vty 0 4
login local
transport input ssh
exit

end
```

---
# Summary
- A switch must have a management IP address before remote access is possible.
- VTY lines provide remote CLI access.
- `login local` uses the local username database.
- `password` under the VTY line is only needed when using `login`, **not** `login local`.
- SSH requires:
	- A non-default hostname.
	- A configured domain name.
	- RSA keys.
- `enable secret` protects privileged EXEC mode.
- Telnet is insecure because it sends data unencrypted.
- SSH is encrypted and is the recommended method for remote management.
- `transport input ssh` disables Telnet and allows only SSH connections.