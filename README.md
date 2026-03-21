# Secure-MultiSite-Enterprise-Network

## Project Overview
This project features a comprehensive multi-site enterprise network architecture designed using Cisco Packet Tracer. It simulates a realistic corporate infrastructure with a focus on high availability, scalable routing protocols, and a multi-layered security approach (Defense in Depth). The topology includes several departmental buildings, a dedicated server farm, and a secure edge connection to a simulated ISP.

## Network Topology
![Network Topology](NetworkTopology.png)

## Technical Architecture

### Layer 3 Routing & Connectivity
* **Edge Connectivity (BGP):** Implemented **eBGP (AS 65001)** on the `HQ-CORE-R01` router for external peering with the ISP.
* **Internal Routing (IGP):**
    * **OSPF:** Configured as the primary IGP (changed distance) with **MD5 Cryptographic Authentication** for route integrity. 
    * **EIGRP (AS 100):** Utilized for internal backbone routing between core segments.
* **Inter-VLAN Routing:** Performed by the Layer 3 Distribution Switch (`HQ-DIST-L3SW01`) via SVIs, ensuring high-speed internal communication.
* **NAT/PAT:** Network Address Translation with Overload (PAT) is configured on the edge router to provide internet access for all internal private subnets.

### Switching & Redundancy
* **EtherChannel (LACP):** Link aggregation is deployed between the Distribution and Access layers to ensure 2Gbps logical bandwidth and hardware redundancy.
* **Spanning Tree Protocol (PVST+):** `HQ-DIST-L3SW01` is manually assigned as the **Root Bridge** for all active VLANs to ensure a deterministic, loop-free topology.
* **Network Segmentation:** 8 departmental VLANs (Admin, HR, Finance, Business, Sales, Support, Stud-Lab, Servers) are implemented to reduce broadcast domains and improve security.

## Security Implementation

### Layer 2 Security (Access Layer)
| Feature | Implementation Detail |
| :--- | :--- |
| **Port Security** | Hardened access ports with **Sticky MAC addresses** and strict violation restrictions. |
| **DHCP Snooping** | Prevents Rogue DHCP server attacks by validating traffic on untrusted ports. |
| **Dynamic ARP Inspection** | Mitigates Man-in-the-Middle (ARP Spoofing) by verifying ARP packets against the snooping database. |
| **STP Security** | **BPDU Guard** and **PortFast** enabled on edge ports to protect the STP topology. |

### Management & Control Plane Hardening
* **Centralized AAA (TACACS+):** Administrator authentication is offloaded to a dedicated TACACS+ server (`192.168.8.60`) for centralized identity management.
* **Secure Management:** Telnet is disabled. Remote management is restricted to **SSH v2** only.
* **Management ACLs:** Infrastructure access (VTY lines) is restricted via Access Control Lists, permitting only the Administrative subnet (`192.168.1.0/24`) to connect.
* **Routing Security:** MD5 authentication is enforced on all OSPF adjacencies to prevent unauthorized route injection.

## Infrastructure Services
* **DHCP Services:** Centralized IP address allocation via **IP-Helper (Relay Agent)** pointing to the server farm.
* **NTP (Network Time Protocol):** `HQ-CORE-R01` acts as the NTP Master, providing synchronized timestamps for consistent logging.
* **Remote Logging (Syslog):** Critical events are forwarded to a central Syslog server (`192.168.8.50`) for auditing.
* **Server Farm:** Deployment of internal DNS, Web (HTTP), FTP, and Email services within VLAN 80.

## Access Credentials
* **Administrative User:** `admin` / `SuperAdmin`
* **TACACS+ / OSPF Key:** `admin`

---

## Repository Structure
* `/configs` - Detailed `.cfg` files containing running configurations for all key infrastructure devices.
* `Advanced_Enterprise_Network.pkt` - The primary Cisco Packet Tracer source file.
* `NetworkTopology.png` - High-resolution network diagram.

---

### How to Run
1. Open the `.pkt` file using **Cisco Packet Tracer 8.x** or higher.
2. Allow a few moments for STP and OSPF/BGP convergence.
3. Test connectivity by pinging the external DNS server (`8.8.8.8`) from any internal PC.
4. Verify management security by attempting an SSH session from a non-admin VLAN (should be blocked by ACL).
