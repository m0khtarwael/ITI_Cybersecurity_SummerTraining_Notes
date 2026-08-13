# 1. Switch Components

| Feature                                         | IOS                                                       | Flash                                    | RAM                                                         | NVRAM                                              |
| ----------------------------------------------- | --------------------------------------------------------- | ---------------------------------------- | ----------------------------------------------------------- | -------------------------------------------------- |
| **Full Name**                                   | Internetwork Operating System                             | Flash Memory (Hard Disk)                 | Random Access Memory                                        | Non-Volatile RAM                                   |
| **Type**                                        | Software (Operating System)                               | Hardware (EEPROM Storage)                | Hardware                                                    | Hardware                                           |
| **Volatile?**                                   | No                                                        | No                                       | Yes                                                         | No                                                 |
| **Main File(s)**                                | Cisco OS image (`.bin`)                                   | Cisco IOS image, `vlan.dat`, system logs | `running-config`, MAC Address Table, ARP Cache, Buffers     | `startup-config`                                   |
| **Main Purpose**                                | Runs the switch, provides the CLI, and manages the system | Stores switch files permanently          | Stores all current working data                             | Stores the saved configuration                     |
| **Boot Role**                                   | Executed by the CPU during boot                           | Bootloader loads the IOS image into RAM  | Holds the running IOS, configuration, and tables after boot | IOS reads `startup-config` and loads it into RAM   |
| **`copy running-config startup-config` Effect** | No effect                                                 | No effect                                | Copies the running configuration from RAM                   | Replaces the old `startup-config` with the new one |
## Connecting to a Switch
To configure a switch:
- Connect a **Console Cable** between the laptop and the switch.
- Cable connection:
	- **Switch:** Console Port
	- **Laptop:** Serial (COM1 / RS-232 in Packet Tracer)
### Starting Configuration
**On a Real Computer**
- Use a terminal program such as **PuTTY**.
- Select **Serial** connection.

**In Packet Tracer**
- **Method 1:** Laptop → Desktop → Terminal
- **Method 2:** Switch → CLI

Both methods open the Cisco CLI so you can start configuring the switch.

---
# 2. Switch Modes
A Cisco switch has four main modes. Each mode has different commands and permissions.
### 1. User EXEC Mode (Default Mode)
#### How to Enter
- This is the default mode after connecting to the switch.
#### What You Can Do
- View limited information.
- Run simple troubleshooting commands.
- You **cannot** configure the switch.
#### Common Commands

```text
Switch>?
Exec commands:
  connect     Open a terminal connection
  disable     Turn off privileged commands
  disconnect  Disconnect an existing network connection
  enable      Turn on privileged commands
  exit        Exit from the EXEC
  logout      Exit from the EXEC
  ping        Send echo messages
  resume      Resume an active network connection
  show        Show running system information
  ssh         Open a secure shell client connection
  telnet      Open a telnet connection
  terminal    Set terminal line parameters
  traceroute  Trace route to destination
```
---
### 2. Privileged EXEC Mode (Enable Mode)
#### How to Enter
```text
Switch> enable
Switch#
```
#### What You Can Do
- View all switch information.
- Manage files.
- Reboot the switch.
- Enter Configuration Mode.
#### Common Commands
```text
Switch#?
Exec commands:
  clear       Reset functions
  clock       Manage the system clock
  configure   Enter configuration mode
  connect     Open a terminal connection
  copy        Copy from one file to another
  debug       Debugging functions (see also 'undebug')
  delete      Delete a file
  dir         List files on a filesystem
  disable     Turn off privileged commands
  disconnect  Disconnect an existing network connection
  enable      Turn on privileged commands
  erase       Erase a filesystem
  exit        Exit from the EXEC
  logout      Exit from the EXEC
  more        Display the contents of a file
  no          Disable debugging informations
  ping        Send echo messages
  reload      Halt and perform a cold restart
  resume      Resume an active network connection
  setup       Run the SETUP command facility
  show        Show running system information
  ssh         Open a secure shell client connection
  telnet      Open a telnet connection
  terminal    Set terminal line parameters
  traceroute  Trace route to destination
  undebug     Disable debugging functions (see also 'debug')
  write       Write running configuration to memory, network, or terminal
```

### 3. Global Configuration Mode
#### How to Enter
```text
Switch> enable
Switch# configure terminal // you can use conf t
Switch(config)#
```
#### What You Can Do
Configure settings that affect the entire switch, such as:
- Hostname
- Passwords
- Banner
- VLANs
- Interfaces
- IP settings
- Services
#### Common Commands
```text
Switch(config)#?
Configure commands:
  aaa                Authentication, Authorization and Accounting.
  access-list        Add an access list entry
  banner             Define a login banner
  boot               Boot Commands
  cdp                Global CDP configuration subcommands
  clock              Configure time-of-day clock
  crypto             Encryption module
  default            Set a command to its defaults
  do-exec            To run exec commands in config mode
  dot1x              IEEE 802.1X Global Configuration Commands
  enable             Modify enable password parameters
  end                Exit from configure mode
  exit               Exit from configure mode
  hostname           Set system's network name
  interface          Select an interface to configure
  ip                 Global IP configuration subcommands
  line               Configure a terminal line
  lldp               Global LLDP configuration subcommands
  logging            Modify message logging facilities
  mac                MAC configuration
  mls                mls global commands
  monitor            SPAN information and configuration
  no                 Negate a command or set its defaults
  ntp                Configure NTP
  port-channel       EtherChannel configuration
  privilege          Command privilege parameters
  sdm                Switch database management
  service            Modify use of network based services
  snmp-server        Modify SNMP engine parameters
  spanning-tree      Spanning Tree Subsystem
  tacacs-server      Modify TACACS query parameters
  username           Establish User Name Authentication
  vlan               Vlan commands
  vtp                Configure global VTP state
```
### 4. Interface Configuration Mode (Sub-Config)
#### How to Enter
From Global Configuration Mode:
```text
Switch(config)# interface <port_id>
```

Example:
```text
Switch(config)# interface fa0/1
```
#### What You Can Do
Configure one interface only, for example:
- IP address
- Speed
- Duplex
- Switchport mode
- VLAN
- Shutdown / No shutdown
- Description
#### Exit
```text
Switch(config-if)# exit
```
Returns to Global Configuration Mode.

```text
Switch(config-if)# end
```
Returns to Privileged EXEC Mode.
### Switch Modes Summary

| Feature          | User EXEC                                                   | Privileged EXEC                                                          | Global Configuration                                                                              | Interface Configuration                                               |
| ---------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------- |
| **Prompt**       | `Switch>`                                                   | `Switch#`                                                                | `Switch(config)#`                                                                                 | `Switch(config-if)#`                                                  |
| **How to Enter** | Default after login                                         | `enable`                                                                 | `configure terminal` or `conf t`                                                                  | `interface <port_id>`                                                 |
| **How to Exit**  | `exit` or `logout`                                          | `disable` (to User Mode), `exit` (exit session)                          | `exit` (back to Privileged Mode), `end` or `Ctrl+Z`                                               | `exit` (back to Global Config), `end` (back to Privileged Mode)       |
| **Main Purpose** | Basic monitoring and testing (`ping`, `traceroute`, `show`) | Full monitoring, file management, rebooting, and access to configuration | Modify global settings that affect the whole switch (hostname, passwords, banner, static routes). | Configure one interface only (IP, speed, VLAN, switchport mode, etc.) |

---
# 3. Cisco IOS Command Help
Cisco IOS provides built-in help to make finding commands and completing them easier.

| What You Type | What It Does |
|---------------|--------------|
| `?` | Shows all commands available in the current mode. |
| `command ?` | Shows all available parameters for the command. There **must be a space** before `?`. |
| `com?` | Shows all commands that start with the letters you typed. There is **no space** before `?`. |
| `command parm?` | Shows all parameters that begin with the letters you have typed. There is **no space** before `?`. |
| `command <Tab>` | Automatically completes the command if there is only one possible match. |
| `command parm1 ?` | Shows the next available parameters with a short description. There **must be a space** before `?`. |
## Examples
### 1. Show all commands in the current mode
```text
Switch> ?
```
Shows every command you can use in the current mode.
### 2. Show parameters for a command
```text
Switch# show ?
```
- There is a **space** before `?`.
- IOS displays all available options for the `show` command.
### 3. Find commands by their beginning
```text
Switch# co?
```
- There is **no space** before `?`.
- IOS lists every command that starts with **co**.
Example:
```text
configure
connect
copy
```
### 4. Find parameters that start with specific letters
```text
Switch(config)# interface f?
```
- There is **no space** before `?`.
- IOS lists all interface names that start with **f**.
Example:
```text
FastEthernet
```
### 5. Use the Tab key
```text
Switch# conf<Tab>
```
IOS automatically completes the command if there is only one match.

Result:
```text
configure
```
### 6. Show the next parameter
```text
Switch# copy running-config ?
```
- There is a **space** before `?`.
- IOS shows the next parameter and a short explanation.
Example:
```text
startup-config   Copy to startup configuration
tftp:            Copy to TFTP server
...
```

---
# 4. Cisco IOS Configuration Files
Cisco IOS uses two main configuration files.

| Configuration File | Stored In | Purpose                           | When It Is Used                                                                                |
| ------------------ | --------- | --------------------------------- | ---------------------------------------------------------------------------------------------- |
| **startup-config** | NVRAM     | Stores the saved configuration.   | Used every time the switch starts or reloads.                                                  |
| **running-config** | RAM       | Stores the current configuration. | Used while the switch is running. Changes immediately when configuration commands are entered. |
## Relationship Between the Two Files

```text
Startup
   │
   ▼
startup-config (NVRAM)
   │
   ▼
Loaded into
   │
   ▼
running-config (RAM)

While configuring the switch:
running-config changes immediately.

To save the changes:
running-config ───► startup-config
```
## Saving the Configuration
To save the current configuration:
```text
Switch# copy running-config startup-config
```
or
```text
Switch# write memory
```
### What Happens?
```text
RAM (running-config)
        │
        ▼
NVRAM (startup-config)
```
The current configuration is copied from **RAM** to **NVRAM**, replacing the old `startup-config`.

---
# 5. Basic Configuration
## Using the `do` Command
Normally, commands such as:
- `show`
- `ping`
- `traceroute`
- `write`
can only be used in **Privileged EXEC Mode (`Switch#`)**.

If you are in a configuration mode, you can use the **`do`** keyword to run these commands without leaving the current mode.

Example:
```text
Switch(config)# do show running-config
```
### Notes
- `do` lets you run **Privileged EXEC** commands from **Configuration Mode**.
- **Tab completion** does **not** work after `do`.
- Command help (`?`) also does **not** work for the command after `do`.
- `ping` is used to check the connection between two devices.
## 5.1 Change the Switch Name (Hostname)
### Command
```text
Switch(config)# hostname ITICS
```
- **Purpose :** Changes the switch name.
Example:
- Before:
```text
Switch(config)#
```
- After:
```text
ITICS(config)#
```

## 5.2 Configure the Login Banner
- **A login banner is displayed before a user logs in.**
- It can be used for:
	- Welcome messages
	- Security warnings
	- Company notices
### Command
```text
Switch(config)# banner motd "Welcome to Switch"
```
### Notes
- `motd` means **Message of the Day**.
- The first and last delimiter must be the same.
Example:
```text
banner motd #Welcome to Switch#
```
or
```text
banner motd "Welcome to Switch"
```
## 5.3 Set the Clock
- **The switch clock is important for:**
	- Logs
	- Troubleshooting
	- Security investigations
### Command
```text
Switch(config)# clock set 10:00:00 28 JUL 2026
```
## 5.4 Select an Interface
- **To configure one interface:**
```text
Switch(config)# interface f0/10
```
- You will enter Interface Configuration Mode:
```text
Switch(config-if)#
```
- Available interface commands:
```text
Switch(config-if)#?
  authentication    Auth Manager Interface Configuration Commands
  cdp               Global CDP configuration subcommands
  channel-group     Etherchannel/port bundling configuration
  channel-protocol  Select the channel protocol (LACP, PAgP)
  delay             Specify interface throughput delay
  description       Interface specific description
  dot1x             Interface Config Commands for IEEE 802.1X
  duplex            Configure duplex operation.
  exit              Exit from interface configuration mode
  ip                Interface Internet Protocol config commands
  lldp              LLDP interface subcommands
  mdix              Set Media Dependent Interface with Crossover
  mls               mls interface commands
  no                Negate a command or set its defaults
  shutdown          Shutdown the selected interface
  spanning-tree     Spanning Tree Subsystem
  speed             Configure speed operation.
  storm-control     storm configuration
  switchport        Set switching mode characteristics
  tx-ring-limit     Configure PA level transmit ring limit
```

- **To Configure Multiple Consecutive Interfaces:**
```text
Switch(config)# interface range f0/1-10
Switch(config-if-range)#
```
Configures interfaces **FastEthernet 0/1** through **FastEthernet 0/10**.

- **To Configure Multiple Interface Ranges:**
```text
Switch(config)# interface range f0/1-10, f0/20
Switch(config-if-range)#
```
Configures:
- FastEthernet 0/1 → FastEthernet 0/10
- FastEthernet 0/20
at the same time.
---
# 6. Security and User Management
This section covers:
- Setting a password for the console port.
- Viewing the running configuration.
- Encrypting passwords.
- Creating local users.
## 1. Set a Password for the Console Port
- The switch has **one console port**.
- Setting a console password helps prevent unauthorized access through the console connection.
### Step 1: Enter Console Line Configuration Mode

```text
Switch(config)# line console 0
```
- You will enter:
```text
Switch(config-line)#
```
- Available commands:
```text
Switch(config-line)#?
Line configuration commands:
  access-class  Filter connections based on an IP access list
  accounting    Accounting parameters
  databits      Set number of data bits per character
  default       Set a command to its defaults
  exec-timeout  Set the EXEC timeout
  exit          Exit from line configuration mode
  flowcontrol   Set the flow control
  history       Enable and control the command history function
  logging       Modify message logging facilities
  login         Enable password checking
  motd-banner   Enable the display of the MOTD banner
  no            Negate a command or set its defaults
  parity        Set terminal parity
  password      Set a password
  privilege     Change privilege level for line
  speed         Set the transmit and receive speeds
  stopbits      Set async line stop bits
  transport     Define transport protocols for line
```
### Step 2: Set the Password
```text
Switch(config-line)# password 123
```
### Step 3: Enable Password Checking
```text
Switch(config-line)# login
```
### Result
When someone connects through the console:
- The login banner is displayed (if configured).
- The user must enter the console password before accessing the switch.
## 2. Show the Running Configuration
- The running configuration is stored in **RAM**.
- To display it:
```text
Switch# show running-config
```
- Example output:
```text
Building configuration...

Current configuration : 1101 bytes
!
version 15.0
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname ITICS
!
!
!
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!
interface FastEthernet0/1
!
interface FastEthernet0/2
!
interface FastEthernet0/3
!
interface FastEthernet0/4
!
interface FastEthernet0/5
!
interface FastEthernet0/6
!
interface FastEthernet0/7
!
interface FastEthernet0/8
!
interface FastEthernet0/9
!
interface FastEthernet0/10
!
interface FastEthernet0/11
!
interface FastEthernet0/12
!
interface FastEthernet0/13
!
interface FastEthernet0/14
!
interface FastEthernet0/15
!
interface FastEthernet0/16
!
interface FastEthernet0/17
!
interface FastEthernet0/18
!
interface FastEthernet0/19
!
interface FastEthernet0/20
!
interface FastEthernet0/21
!
interface FastEthernet0/22
!
interface FastEthernet0/23
!
interface FastEthernet0/24
!
interface GigabitEthernet0/1
!
interface GigabitEthernet0/2
!
interface Vlan1
 no ip address
 shutdown
!
!
!
!
line con 0
 password 123
 login
!
line vty 0 4
 login
line vty 5 15
 login
!
!
!
!
end
```
- **Purpose:** Use this command to view the current active configuration stored in RAM.
## 3. Encrypt the Console Password
- By default, the console password is stored as **plain text**.
- To encrypt it:
```text
Switch(config)# service password-encryption
```
### Notes
- The default encryption used by the switch is **Type 7 encryption**.
- **Type 7 encryption is easy to decrypt**, so stronger encryption methods (such as SHA-256) are preferred when available.
## 4. Create a Local User
- Create a username and password:
```text
Switch(config)# username user1 password 123
```
- Enable local authentication:
```text
Switch(config)# login local
```
### Difference Between `login` and `login local`

| Command       | Purpose                                                               |
| ------------- | --------------------------------------------------------------------- |
| `login`       | Uses only the configured line password for authentication.            |
| `login local` | Uses the locally configured username and password for authentication. |
