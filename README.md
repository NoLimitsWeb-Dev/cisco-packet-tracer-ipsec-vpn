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
---
