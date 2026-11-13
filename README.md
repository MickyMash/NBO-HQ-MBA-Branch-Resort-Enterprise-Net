# Enterprise HQ & Branch Network Topology 

## 📌 Project Overview
This project is a Cisco Packet Tracer simulation of a multi-site enterprise network. The goal of this lab was to implement a highly available, secure, and scalable network for a Headquarters and a remote Branch office, incorporating Voice over IP (VoIP) and centralized Wireless LAN management.

![Network Topology](topology-diagram.png)

## 🛠️ Technologies & Protocols Implemented
*   **Layer 2 Switching:** VLANs (Data, Voice, Management), 802.1Q Trunking, EtherChannel (LACP).
*   **High Availability:** HSRP (Hot Standby Router Protocol) for default gateway redundancy.
*   **Routing:** OSPF (Area 0 for HQ, Area 10 for Branch) and Static Routing to the ISP.
*   **Voice Integration:** Configured Cisco IP Phones on dedicated Voice VLANs using `switchport voice vlan`.
*   **Wireless:** Centralized WLC (Wireless LAN Controller) with Lightweight Access Points (LAPs).
*   **Security:** Port Security (Sticky MAC) and DHCP Snooping.
*   **WAN Edge:** NAT/PAT configured for internet access via the ISP Router.

## 🗺️ IP Addressing Summary
*(Update these to match your actual scheme)*
| Site / VLAN | Subnet | Default Gateway (HSRP) |
| :--- | :--- | :--- |
| HQ - Data (VLAN 10) | 192.168.10.0/24 | 192.168.10.1 |
| HQ - Voice (VLAN 150) | 192.168.150.0/24 | 192.168.150.1 |
| Branch - Data (VLAN 20)| 172.31.20.0/24 | 172.31.20.1 |
| WAN Link | 10.1.1.0/30 | N/A |

## 🧠 Key Challenges & Solutions
*   **Challenge:** The IP Phones were not receiving IP addresses from the DHCP server.
    *   **Solution:** I discovered the switch ports were not configured with a Voice VLAN. I applied `switchport voice vlan 150` and configured the `ip helper-address` on the router to forward DHCP requests to the correct server.
*   **Challenge:** HSRP was flapping between the two Multilayer Switches.
    *   **Solution:** The interfaces were coming up at different speeds. I configured `standby 10 preempt` on the primary switch and adjusted the `standby 10 priority` to ensure it always remained the Active router.
*   **Challenge:** Wireless clients could connect to the AP but could not ping the local gateway.
    *   **Solution:** The WLC Management interface was on VLAN 10, but the Dynamic Interface for the wireless clients was on VLAN 20. I had to ensure the trunk link between the switch and the WLC allowed both VLANs.

## 📂 Repository Structure
*   `topology-diagram.png` - The visual layout of the network.
*   `project-file.pkt` - The complete Packet Tracer file.
*   `configs/` - Folder containing the raw CLI configurations for all routers and switches.

## 🚀 How to Test
1. Download and open `project-file.pkt` in Packet Tracer.
2. Wait for STP and OSPF to converge (all link lights should be green).
3. Open a PC at HQ and ping a Server at the Branch to verify end-to-end routing.
4. Use the IP Phone to call another IP Phone to verify Voice VLAN routing.
