# Enterprise-Campus-Network-Design
Network Design of a Campus Area Network made in Packet Tracer.
![Network Topology](Networks_topology.png)

## About the Project
A comprehensive simulation of an Enterprise Network built in Cisco Packet Tracer. The project showcases advanced routing, switching, and network services techniques, simulating a real-world environment of a large company with multiple buildings, a remote branch, and a connection to the global Internet using the BGP protocol.

## Applied Technologies and Protocols
* **External Routing (WAN/Edge):** eBGP (Border Gateway Protocol) with a simulation of 3 Autonomous Systems (AS 65001, AS 65002, AS 65003).
* **Internal Routing (IGP):** Layer 3 redundancy implemented. 
  * **OSPF (Area 0):** Configured as the primary protocol (Administrative Distance changed to 80).
  * **EIGRP (AS 100):** Configured as the backup protocol (AD 90).
* **Switching and L2/L3:** VLAN, 802.1Q Trunking, Inter-VLAN Routing using multilayer switches (SVIs on Cisco 3650).
* **Addressing and Services:** Centralized DHCP server with `ip helper-address` (DHCP Relay) configuration for 10 different subnets, DNS, HTTP, FTP.
* **Security and Network Edge:** NAT (PAT / NAT Overload) for the entire outbound LAN traffic to the Internet.
* **Applications (L7):** Simulation of a functional Email server (SMTP/POP3) located in an external ISP Data Center.

1. **External (INTERNET):** A chain of ISP providers connected via eBGP, hosting a public DNS server (8.8.8.8) and the company's mail server.
2. **Main Campus (Buildings A, B, C):** The network core is based on a `DISTRIBUTION SW` (Multilayer Switch) managing traffic between various VLANs. Building C houses the central **Server Farm**, hosting critical services for the entire company:
   * **Central DHCP Server:** Distributing IP addressing for 10 different VLANs across all buildings (using `ip helper-address`).
   * **Internal DNS Server:** Responsible for resolving local names (e.g., intranet sites and file servers).
   * **WEB & FTP Server:** Hosting the internal company website and a central network drive for employees.
3. **Remote Branch (Building D):** Connected via a routed P2P link to the main network, featuring its own multilayer switch distributing traffic for local VLANs.

## How to Test This Network? (Configuration Verification)
If you downloaded the `.pkt` file, you can test the implemented solutions yourself:

1. **DHCP Relay Test (ip helper-address):**
   * Access any PC (e.g., in the HR department or the remote Building D).
   * In the *IP Configuration* tab, switch from Static to DHCP mode. The computer will flawlessly obtain an IP address from the central server in Building C, traversing routers and L3 switches.
2. **Internal Services Test (DNS, WEB, FTP):**
   * Open the *Web Browser* on an employee's PC and enter the domain name (e.g., corresponding to the WEB server). The internal DNS server will resolve the name to an IP, and the WEB server will display the company page.
   * Open the *Command Prompt* on the PC and type the command `ftp [ftp-server-address]`. Log in with the appropriate credentials and use the `dir` command to list files – proving the FTP protocol works across VLANs.
3. **Internet Access Test (NAT & BGP):** * In the *Command Prompt* on any PC inside the LAN, type the command `ping 8.8.8.8` (Google DNS in the cloud).
   * Packets are masked by NAT on the main router, pass through the ISPs' systems, and return. On the `MAIN ROUTER`, this can be verified with the `show ip nat translations` command.
4. **External Services Test (Email SMTP/POP3):**
   * Use the built-in *Email* client on two different computers to send and receive a message. The message will be routed via BGP to the external Data Center (`EMAIL SERVER`) and back.
5. **Routing Redundancy Test:**
   * On the `MAIN ROUTER`, check the routing table: `show ip route`.
   * The main routes within the company are managed by OSPF (Administrative Distance changed to 80). EIGRP (AD 90) remains on standby as the backup protocol.
