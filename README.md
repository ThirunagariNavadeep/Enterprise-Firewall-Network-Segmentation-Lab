Enterprise Firewall & Network Segmentation Lab
A hands-on enterprise network security lab built with Cisco Packet
Tracer and a simulated Cisco ASA 5506-X firewall. The project
demonstrates security-zone segmentation, firewall interface
configuration, access control lists (ACLs), dynamic NAT/PAT, routing,
and connectivity validation.
Project Objectives
- Design an enterprise network with separate Inside, Guest, DMZ, and
  Outside zones.
- Configure Cisco ASA interfaces and security levels.
- Restrict Guest access to a protected administrative workstation
  using an ACL.
- Configure dynamic PAT for internal networks.
- Configure routing between the firewall and a simulated ISP router.
- Validate permitted and denied traffic with connectivity tests and
  ACL hit counters.
Network Topology

Devices
  Device                Role
  Cisco ASA 5506-X      Firewall between the network zones
Cisco 2911 router     Simulated ISP router
Cisco 2960 switches   LAN and DMZ connectivity
Admin PC              Administrative workstation
Employee PC           Employee workstation
Guest PC              Guest network client
DMZ Server            Server hosted in the DMZ
IP Addressing
  Zone / Device   Network or IP address   Default gateway
  Outside         203.0.113.0/30          ISP router: 203.0.113.1
Inside          192.168.10.0/24         192.168.10.1
DMZ             192.168.20.0/24         192.168.20.1
Guest           192.168.30.0/24         192.168.30.1
Admin PC        192.168.10.10/24        192.168.10.1
Employee PC     192.168.10.20/24        192.168.10.1
Guest PC        192.168.30.30/24        192.168.30.1
DMZ Server      192.168.20.10/24        192.168.20.1
ASA Firewall Configuration
Interface and security-zone assignments
  ASA interface        Nameif / zone          IP address   Security level
  GigabitEthernet1/1   Outside            203.0.113.2/30                0
GigabitEthernet1/2   Inside            192.168.10.1/24              100
GigabitEthernet1/3   DMZ               192.168.20.1/24               50
GigabitEthernet1/4   Guest             192.168.30.1/24               25

All four configured ASA interfaces were verified as up/up.

Guest access-control policy
An ACL named GUEST-POLICY was applied inbound on the Guest interface.

  Order   Source          Destination     Action
  1       192.168.30.30   192.168.10.10   Deny
2       Any             Any             Permit

The first rule blocks the Guest PC from reaching the Admin PC. The
second rule permits other IP traffic matching this ACL; traffic is still
subject to the ASA's other policies and stateful inspection.

The ACL was checked with:
show access-list
The captured output shows hit counters on both ACL entries, confirming
that traffic matched the deny and permit rules.

Dynamic NAT/PAT
Dynamic PAT was configured for the Inside, DMZ, and Guest networks using
the ASA outside interface address.
object network INSIDE-NET
 subnet 192.168.10.0 255.255.255.0
 nat (inside,outside) dynamic interface

object network DMZ-NET
 subnet 192.168.20.0 255.255.255.0
 nat (dmz,outside) dynamic interface

object network GUEST-NET
 subnet 192.168.30.0 255.255.255.0
 nat (guest,outside) dynamic interface
Routing
- The ASA default route points to the ISP router at 203.0.113.1.
- The ISP router has static return routes for the Inside, DMZ, and
  Guest subnets via 203.0.113.2.
- The ISP router loopback 198.51.100.1/32 was used as a simulated
  external test destination.
ICMP inspection
ICMP inspection was enabled under the global inspection policy to
support ICMP request/reply traffic across zones.
configure terminal
policy-map global_policy
 class inspection_default
  inspect icmp
exit
exit
write memory
The inspection policy and interface status are shown below.

Connectivity and Security Validation
The following tests were performed in Packet Tracer.
  Test           Source         Destination    Expected       Observed result
behavior       
  1              Admin PC       DMZ Server     Permit         Successful

  2              Admin PC       Guest PC       Permit         Successful

  3              Guest PC       Admin PC       Deny by ACL    Blocked

  4              Guest PC       Employee PC    Permit         Successful

  5              Guest PC       DMZ Server     Permit         Successful

  6              ASA            ISP router     Permit         Successful

  7              ASA            DMZ Server     Permit         Successful
8              Guest PC       ISP router     External test  Unsuccessful;
loopback                      not verified
Guest network tests
The Guest PC successfully reached the Employee PC and DMZ Server, while
access to the Admin PC was blocked by the ACL.

Admin network tests
The Admin PC successfully reached both the Guest PC and the DMZ Server.

Project Screenshots
  Screenshot                                        Evidence
  01_Network-Topology.png                         Overall network design

  02_ASA-Interface-Configuration.png              ASA zone interfaces and IP
addressing

  03_ASA-Inspection-and-Interface-Status.png      ICMP inspection and interface
status

  04_Guest-to-Employee-and-Admin-Tests.png        Guest connectivity tests

  05_Guest-to-Admin-Blocked-and-DMZ-Allowed.png   ACL deny and Guest-to-DMZ test

  06_Admin-to-Guest-and-DMZ-Allowed.png           Admin connectivity tests
07_ASA-ACL-Hit-Counters.png                     ACL rules and hit counters
Repository Structure
Enterprise-Network-Security-Lab/
├── Screenshots/
│   ├── 01_Network-Topology.png
│   ├── 02_ASA-Interface-Configuration.png
│   ├── 03_ASA-Inspection-and-Interface-Status.png
│   ├── 04_Guest-to-Employee-and-Admin-Tests.png
│   ├── 05_Guest-to-Admin-Blocked-and-DMZ-Allowed.png
│   ├── 06_Admin-to-Guest-and-DMZ-Allowed.png
│   └── 07_ASA-ACL-Hit-Counters.png
├── Enterprise-Firewall-Lab.pkt
└── README.md
Tools and Technologies
- Cisco Packet Tracer
- Cisco ASA 5506-X (simulated)
- Cisco IOS router
- Network segmentation and security zones
- Access Control Lists (ACLs)
- Network Address Translation (NAT/PAT)
- Static and default routing
- ICMP inspection
- Ping-based connectivity testing
Key Learning Outcomes
- Designed a multi-zone enterprise network.
- Configured and verified ASA firewall interfaces.
- Applied an inbound ACL to restrict Guest-to-Admin traffic.
- Configured dynamic PAT for multiple internal networks.
- Configured static return routes and an ASA default route.
- Validated allowed and denied traffic using connectivity tests.
- Used ACL hit counters to confirm policy matches.
Known Limitations
- The lab is a Packet Tracer simulation, not a deployment on physical
  firewall hardware.
- Guest-to-external-network connectivity through NAT was not
  successfully verified. Although NAT rule matches were observed, the
  Guest-to-ISP loopback test failed.
- Results reflect the behavior of this simulated topology and should
  not be treated as a production security assessment.
Conclusion
This lab demonstrates practical firewall fundamentals, network
segmentation, access-control policy enforcement, NAT/PAT, routing, and
connectivity validation in a simulated enterprise environment.

This project demonstrates practical understanding of enterprise firewall configuration, network segmentation, access control, routing, and NAT. It provides a foundation for further learning in network security engineering and firewall administration.

**Author:** Navadeep Thirunagari

**GitHub:** [ThirunagariNavadeep](https://github.com/ThirunagariNavadeep)
