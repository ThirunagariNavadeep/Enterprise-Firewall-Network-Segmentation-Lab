# Enterprise Firewall & Network Segmentation Lab

A hands-on network security project built using **Cisco Packet Tracer** and a simulated **Cisco ASA 5506-X firewall**. The lab demonstrates enterprise network segmentation, firewall security policies, access control lists (ACLs), NAT configuration, and connectivity testing across multiple security zones.

## Project Overview

The objective of this project is to design and implement a segmented enterprise network where different departments and network zones communicate through a centralized firewall.

The Cisco ASA firewall separates the network into four security zones:

- **Outside:** External network connectivity.
- **Inside:** Trusted corporate network for administrators and employees.
- **DMZ:** Isolated network hosting publicly accessible services.
- **Guest:** Restricted network for guest devices.

Firewall policies control communication between these zones and restrict unauthorized access to sensitive internal resources.

## Network Architecture

![Network Topology](Screenshots/01_Network-Topology.png)

### Network Components

| Component | Device | Purpose |
|---|---|---|
| Firewall | Cisco ASA 5506-X | Traffic filtering and network security |
| ISP Router | Cisco 2911 | Simulated external network |
| LAN Switch | Cisco 2960 | Connects internal users |
| DMZ Switch | Cisco 2960 | Connects DMZ server |
| Admin PC | PC-PT | Administrator workstation |
| Employee PC | PC-PT | Employee workstation |
| Guest PC | PC-PT | Guest network client |
| DMZ Server | Server-PT | Simulated server in DMZ |

## IP Addressing Scheme

| Network Zone | Network Address | Gateway | Device |
|---|---|---|---|
| Outside | 203.0.113.0/30 | 203.0.113.1 | ISP Router |
| Inside | 192.168.10.0/24 | 192.168.10.1 | Admin / Employee |
| DMZ | 192.168.20.0/24 | 192.168.20.1 | DMZ Server |
| Guest | 192.168.30.0/24 | 192.168.30.1 | Guest PC |

### End-Device IP Configuration

| Device | IP Address | Default Gateway |
|---|---|---|
| Admin PC | 192.168.10.10 | 192.168.10.1 |
| Employee PC | 192.168.10.20 | 192.168.10.1 |
| Guest PC | 192.168.30.30 | 192.168.30.1 |
| DMZ Server | 192.168.20.10 | 192.168.20.1 |

## Firewall Configuration

### ASA Interface Configuration

| Interface | Security Zone | IP Address | Security Level |
|---|---|---|---|
| GigabitEthernet1/1 | Outside | 203.0.113.2/30 | 0 |
| GigabitEthernet1/2 | Inside | 192.168.10.1/24 | 100 |
| GigabitEthernet1/3 | DMZ | 192.168.20.1/24 | 50 |
| GigabitEthernet1/4 | Guest | 192.168.30.1/24 | 25 |

All four configured firewall interfaces were verified as operational.

![ASA Interface Configuration](Screenshots/02_ASA-Interface-Configuration.png)

### Security Policies and ACL

Configured an access control list named `GUEST-POLICY` and applied it inbound on the Guest interface.

| Rule | Source | Destination | Action |
|---|---|---|---|
| 1 | 192.168.30.30 | 192.168.10.10 | Deny |
| 2 | Any | Any | Permit |

The first rule blocks the Guest PC from accessing the Admin PC. The second permits other IP traffic, subject to the remaining firewall policies.

ACL hit counters were verified using:

```cisco
show access-list
```

![ASA ACL Hit Counters](Screenshots/07_ASA-ACL-Hit-Counters.png)

### NAT Configuration

Configured dynamic PAT (Port Address Translation) for the Inside, DMZ, and Guest networks using the outside interface address.

```cisco
object network INSIDE-NET
 subnet 192.168.10.0 255.255.255.0
 nat (inside,outside) dynamic interface

object network DMZ-NET
 subnet 192.168.20.0 255.255.255.0
 nat (dmz,outside) dynamic interface

object network GUEST-NET
 subnet 192.168.30.0 255.255.255.0
 nat (guest,outside) dynamic interface
```

### Routing Configuration

- Configured a default route on the ASA pointing to the ISP router.
- Configured static return routes on the ISP router for the Inside, DMZ, and Guest networks.
- Used the ISP router loopback address `198.51.100.1/32` as a simulated external test destination.

### ICMP Inspection

Enabled ICMP inspection through the global service policy to support ICMP request and reply traffic across security zones.

```cisco
configure terminal
policy-map global_policy
 class inspection_default
  inspect icmp
exit
exit
write memory
```

## Connectivity and Security Testing

The following tests were performed to validate network segmentation and firewall behavior.

| Test Case | Source | Destination | Expected Result | Observed Result |
|---|---|---|---|---|
| 1 | Admin PC | DMZ Server | Allow | Successful |
| 2 | Admin PC | Guest PC | Allow | Successful |
| 3 | Guest PC | Admin PC | Deny | Blocked |
| 4 | Guest PC | Employee PC | Allow | Successful |
| 5 | Guest PC | DMZ Server | Allow | Successful |
| 6 | ASA Firewall | ISP Router | Allow | Successful |
| 7 | ASA Firewall | DMZ Server | Allow | Successful |

### Test Evidence

**Guest-to-Admin: Blocked**

The Guest PC cannot access the Admin PC because of the configured ACL rule.

![Guest to Admin Blocked](Screenshots/05_Guest-to-Admin-Blocked-and-DMZ-Allowed.png)

**Guest-to-DMZ: Allowed**

The Guest PC can communicate with the DMZ server.

![Guest to DMZ Allowed](Screenshots/05_Guest-to-Admin-Blocked-and-DMZ-Allowed.png)

**Admin-to-DMZ: Allowed**

The Admin PC can communicate with the DMZ server.

![Admin to DMZ Allowed](Screenshots/06_Admin-to-Guest-and-DMZ-Allowed.png)

**Firewall Interface and Inspection Status**

![ASA Inspection and Interface Status](Screenshots/03_ASA-Inspection-and-Interface-Status.png)

## Project Structure

```text
Enterprise-Network-Security-Lab/
│
├── Firewall-Rules/
├── Incident-Reports/
├── Packet-Tracer/
├── Screenshots/
│   ├── 01_Network-Topology.png
│   ├── 02_ASA-Interface-Configuration.png
│   ├── 03_ASA-Inspection-and-Interface-Status.png
│   ├── 04_Guest-to-Employee-and-Admin-Tests.png
│   ├── 05_Guest-to-Admin-Blocked-and-DMZ-Allowed.png
│   ├── 06_Admin-to-Guest-and-DMZ-Allowed.png
│   └── 07_ASA-ACL-Hit-Counters.png
│
├── Wireshark/
├── Enterprise-Firewall-Lab.pkt
└── README.md
```

## Tools and Technologies

- Cisco Packet Tracer
- Cisco ASA 5506-X (simulated)
- Cisco IOS Router
- VLANs and network segmentation
- Access Control Lists (ACLs)
- Network Address Translation (NAT/PAT)
- Static and default routing
- ICMP inspection
- Ping-based connectivity testing

## Key Learning Outcomes

- Designed a segmented enterprise network with multiple security zones.
- Configured and verified Cisco ASA firewall interfaces.
- Implemented access control policies using ACLs.
- Configured dynamic PAT for multiple internal networks.
- Implemented routing between internal networks and a simulated ISP.
- Validated allowed and denied traffic using connectivity tests.
- Used ACL hit counters to verify firewall policy enforcement.

## Known Limitations

- This project uses Cisco Packet Tracer simulation rather than physical firewall hardware.
- Guest-to-external-network connectivity through NAT was not fully verified. NAT rule matching was observed, but the Guest-to-ISP loopback test did not succeed.
- The project focuses on firewall fundamentals, segmentation, and traffic filtering rather than production deployment.

## Conclusion

This project demonstrates practical understanding of enterprise firewall configuration, network segmentation, access control, routing, and NAT. It provides a foundation for further learning in network security engineering and firewall administration.

**Author:** Navadeep Thirunagari

**GitHub:** [ThirunagariNavadeep](https://github.com/ThirunagariNavadeep)