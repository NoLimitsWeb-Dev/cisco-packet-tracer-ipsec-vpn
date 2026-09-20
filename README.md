# Cisco-packet-tracer-ipsec-vpn
A comprehensive Cisco Packet Tracer lab deploying a production-grade Site-to-Site IPsec VPN using ISR 2911 routers. Includes full CLI configurations and architecture blueprints.
---

### 🏗️ 1. Topology & Network Architecture Blueprint
The network architecture establishes secure, encrypted communication between two geographically isolated private Local Area Networks (LANs) via an untrusted Internet Service Provider (ISP) cloud. 

```
   [ HQ Private LAN ]             [ Simulated Public Internet ]           [ Remote Private LAN ]
    192.168.10.0/24                                                          192.168.20.0/24
          │                                                                        │
    [ HQ PC .10 ]                                                            [ Remote PC .10 ]
          │                                                                        │
    [ HQ Switch ]                                                            [ Remote Switch ]
          │                                                                        │
      (Gi0/0) .1                                                               (Gi0/0) .1
     [ HQ Router ] ─── (Gi0/1) .2 ─── [ ISP Router ] ─── (Gi0/1) .2 ───  [ Remote Router ]
                       203.0.113.0/30              198.51.100.0/30
```

### 🌐 Network Topology Setup
Before typing commands, lets drag and drop the following devices onto the Packet Tracer workspace and connect them:
1. Add Routers and Rename:
* Go to the bottom-left device selection menu and click on Network Devices (the router icon). Select three 2911 Routers model.
* Rename (Router0) to HQ Router: Represents your main office network.
* Rename (Router1) to ISP Router: Represents the public Internet cloud
* Rename (Router2) to Remote Router: Represents the remote office or teleworker node.

2. Add the Switches
Routers generally connect to switches, which then connect to your endpoints.
* Go to the bottom-left device selection menu and click on Network Devices (the router icon), then select Switches from the sub-menu below it.
* Select the 2960 Switch model.
* Drag and drop one switch behind the HQ Router (name it HQ_Switch).
* Drag and drop another switch behind the Remote Router (name it Remote_Switch).

3. Add the End Devices (PCs/Servers)
* Click on End Devices in the bottom-left menu (the desktop computer icon).
* Drag a PC or Server and place it next to HQ_Switch.
* Drag another PC and place it next to Remote_Switch.

4. Cable Everything Together
Because you are connecting different types of devices (Router to Switch, and Switch to PC), you must use a Copper Straight-Through cable (the solid black line icon found under the Connections/Lightning bolt menu).
* HQ LAN Connections:
  * Click the Copper Straight-Through cable.
  * Click your HQ PC, select FastEthernet0, then click HQ_Switch and select      any available port (FastEthernet0/1).
  * Click the cable tool again. Click HQ_Switch (FastEthernet0/24), then click your HQ_Router and plug it into its LAN interface (GigabitEthernet0).
<img width="1142" height="1007" alt="image" src="https://github.com/user-attachments/assets/81a0c905-5c3f-4fe8-b389-b70cecd361a7" />


5. Assign IP Addresses to the PCs
Let's give the computers their IP configurations so they can communicate through the routers.
* On the HQ PC:
  * Click the PC -> Go to the Desktop tab -> Click IP Configuration.
  * IP Address: 192.168.10.10
  * Subnet Mask: 255.255.255.0
  * Default Gateway: 192.168.10.1 (NB: This must match the exact IP to configure later on the HQ_Router's LAN interface).
<img width="1131" height="680" alt="image" src="https://github.com/user-attachments/assets/45d9dd9a-89a7-4416-8f90-4e7645dd14a9" />


* On the Remote PC:
  * Click the PC -> Go to the Desktop tab -> Click IP Configuration.
  * IP Address: 192.168.20.10
  * Subnet Mask: 255.255.255.0
  * Default Gateway: 192.168.20.1 (NB: This must match the exact IP you configure on the Remote_Router's LAN interface).
<img width="1145" height="677" alt="image" src="https://github.com/user-attachments/assets/6c530ce5-91a4-499f-8ca7-37fb9b455a33" />

---

To ensure the PCs can communicate with their routers, I must configure and activate the LAN interfaces. let's follow these steps to configure the gateways.

### 1. Configure the HQ Router LAN Interface
Click on HQ Router, open the CLI tab, press Enter to see the prompt, and type the following commands:
```
HQ_Router> enable
HQ_Router# configure terminal
HQ_Router(config)# interface GigabitEthernet0/0
HQ_Router(config-if)# ip address 192.168.10.1 255.255.255.0
HQ_Router(config-if)# no shutdown
HQ_Router(config-if)# exit
```
<img width="1146" height="1022" alt="image" src="https://github.com/user-attachments/assets/e78152e3-d025-486d-be66-d855c5510667" />

### 2. Configure the Remote Router LAN Interface
Click on your Remote Router, go to the CLI tab, and enter these commands:
```
Remote_Router> enable
Remote_Router# configure terminal
Remote_Router(config)# interface GigabitEthernet0/1
Remote_Router(config-if)# ip address 192.168.20.1 255.255.255.0
Remote_Router(config-if)# no shutdown
Remote_Router(config-if)# exit
```
<img width="1147" height="1001" alt="image" src="https://github.com/user-attachments/assets/0b944c64-41f7-48d4-937a-f0cab3550b04" />

---

### 🔍 Quick Check
Once you type no shutdown, the link indicators (triangle arrows) between your routers and switches in Packet Tracer should turn from Red to Green/Orange.

To verify everything is configured correctly before setting up the VPN tunnel:
1. Open the HQ PC -> Go to the Desktop tab -> Open Command Prompt.
2. Type ping 192.168.10.1 and press Enter. It should get successful replies from your gateway
<img width="1125" height="405" alt="image" src="https://github.com/user-attachments/assets/d8b1fad4-0fbb-4619-bdaa-93e75dc0e754" />

3. Let's repeat this step on the Remote PC by typing ping 192.168.20.1.
<img width="1104" height="283" alt="image" src="https://github.com/user-attachments/assets/496239a4-9463-4eb7-9474-55e13a94e8a6" />

Since both LANs are verified and replying successfully, let's interconnect the HQ Router, ISP Router, and Remote Router to create our simulated internet cloud.

### Let's choose Cable to Use?
Because am connecting routers directly to other routers, I have two options in Packet Tracer:

* Option A (Modern Ethernet Ports): Use a Copper Cross-Over cable (the dashed black line icon under the Connections menu). This connects the built-in GigabitEthernet ports together.
* Option B (Traditional Serial Ports): Use a Serial DCE cable (the red lightning bolt with a clock icon). This requires adding a hardware expansion card (HWIC-2T) into each 2911 router first.

To keep things straightforward and fast without shutting down my routers to insert hardware modules,I will use Option A (GigabitEthernet Cross-Over cables).
---

### Option A:

**1. Cable the Routers Together**
Go to the bottom-left menu, click the Connections icon (the lightning bolt), and select the Copper Cross-Over cable (dashed black line).
* HQ to ISP Connection:
  * Click the HQ Router and select GigabitEthernet0/1.
  * Drag the cable to the ISP Router and select GigabitEthernet0/0.
* Remote to ISP Connection:
  * Click the Remote Router and select GigabitEthernet0/1.
  * Drag the cable to the ISP Router and select GigabitEthernet0/1.
<img width="1142" height="1017" alt="image" src="https://github.com/user-attachments/assets/0dc2cbc2-9699-43bc-969b-97a65f0907fc" />

---

**2. Assign Public WAN IP Addresses**
Right now, the connection links between the routers look Red because the interfaces are shut down by default. Let's turn them on and assign public IP addresses.

**Configure the HQ Router WAN Interface**
Click the HQ Router, open the CLI tab, and enter:
```
HQ_Router> enable
HQ_Router# configure terminal
HQ_Router(config)# interface GigabitEthernet0/0
HQ_Router(config-if)# ip address 203.0.113.2 255.255.255.252
HQ_Router(config-if)# no shutdown
```
**Configure the ISP Router Interfaces**
Click the center ISP Router, open its CLI tab, and configuration both public-facing interfaces:
```
Router> enable
Router# configure terminal
Router(config)# hostname ISP_Router
ISP_Router(config)# interface GigabitEthernet0/0
ISP_Router(config-if)# ip address 203.0.113.1 255.255.255.252
ISP_Router(config-if)# no shutdown
ISP_Router(config-if)# exit

ISP_Router(config)# interface GigabitEthernet0/1
ISP_Router(config-if)# ip address 198.51.100.1 255.255.255.252
ISP_Router(config-if)# no shutdown
```
**Configure the Remote Router WAN Interface**
Click the Remote Router, open its CLI tab, and enter:
```
Remote_Router> enable
Remote_Router# configure terminal
Remote_Router(config)# interface GigabitEthernet0/0
Remote_Router(config-if)# ip address 198.51.100.2 255.255.255.252
Remote_Router(config-if)# no shutdown
```
<img width="1139" height="1004" alt="image" src="https://github.com/user-attachments/assets/dc78aeee-f234-4804-9183-6ed9695ebfb7" />

### 🔍 Verification Check
Once all interfaces are configured, all lines connecting the three routers should turn Green.

Let's test connectivity over the "Internet" links before I add routing:
1. Let's open the CLI on HQ Router and type ping 203.0.113.1 (The result below shows success rate of 100%).
<img width="836" height="195" alt="image" src="https://github.com/user-attachments/assets/23556d36-c72f-46ab-93af-9f64143f9ae6" />

2. Let's open the CLI on Remote Router and type ping 198.51.100.1 (The result below shows success rate of 80%).
<img width="691" height="149" alt="image" src="https://github.com/user-attachments/assets/dc11aaa7-0cdf-433d-9c14-a5dea4059317" />
---

### 🔑 Advanced Device Feature Unlocking (Licensing)
* Goal: Activating the Cisco IOS Security Technology Package (securityk9).
* Purpose: Unlocking the router's hardware-accelerated encryption engine so it can understand and execute complex cryptographic commands.

*NB: Before we type any crypto commands, I must unlock the feature set on both endpoints. If I skip this, the routers will reject the crypto syntax.
I will be running this on BOTH the HQ Router and Remote Router:*
```
Router> enable
Router# configure terminal
Router(config)# license boot module c2900 technology-package securityk9
```
*Type yes when prompted to accept the agreement, then save and reboot them:*
```
Router(config)# exit
Router# write memory
Router# reload
```
*(Press Enter to confirm the reload. Let both routers reboot completely and return to green link status before moving on).*
<img width="1132" height="983" alt="image" src="https://github.com/user-attachments/assets/292d3138-6973-4202-8f46-17a5c206e8f8" />

---

### 🏢 Clean HQ Router VPN Configuration
Once the HQ Router reboots, open its CLI and paste this streamlined script to configure your VPN tunnel over your internet port (GigabitEthernet0/1):
```
HQ_Router> enable
HQ_Router# configure terminal

! --- 1. Set up the Phase 1 Handshake (Packet Tracer compatible) ---
HQ_Router(config)# crypto isakmp policy 10
HQ_Router(config-isakmp)# encryption aes 256
HQ_Router(config-isakmp)# hash sha
HQ_Router(config-isakmp)# authentication pre-share
HQ_Router(config-isakmp)# group 2
HQ_Router(config-isakmp)# exit
HQ_Router(config)# crypto isakmp key vpn_p@ssword address 198.51.100.2

! --- 2. Set up the Phase 2 Transform Set ---
HQ_Router(config)# crypto ipsec transform-set HQ-SET esp-aes esp-sha-hmac

! --- 3. Define Traffic Rules & Peer Map ---
HQ_Router(config)# access-list 100 permit ip 192.168.10.0 0.0.0.255 192.168.20.0 0.0.0.255

HQ_Router(config)# crypto map HQ-MAP 10 ipsec-isakmp
HQ_Router(config-crypto-map)# set peer 198.51.100.2
HQ_Router(config-crypto-map)# set transform-set HQ-SET
HQ_Router(config-crypto-map)# match address 100
HQ_Router(config-crypto-map)# exit

! --- 4. Bind Crypto to the Public Port & Add Routing Paths ---
HQ_Router(config)# interface GigabitEthernet0/1
HQ_Router(config-if)# crypto map HQ-MAP
HQ_Router(config-if)# exit

HQ_Router(config)# ip route 0.0.0.0 0.0.0.0 203.0.113.1 
HQ_Router(config)# ip route 198.51.100.2 255.255.255.255 203.0.113.1 
HQ_Router(config)# end

HQ_Router# write memory
```
