# Network Fundamentals

**Platform:** LetsDefend | **Path:** Career Switch | **Status:** Completed
**Goal:** Understand how data moves across a network, because every SOC alert is built on network activity.

---

## 1. Introduction
A network is two or more devices connected so they can share data. As a SOC analyst, almost everything you investigate (phishing, malware callbacks, brute force) leaves a trail in network traffic. If you understand the normal flow, the abnormal stands out.

## 2. Types of Networks
| Type | Full name | Scope | Example |
|------|-----------|-------|---------|
| PAN | Personal Area Network | A few meters | Phone to Bluetooth earbuds |
| LAN | Local Area Network | One building | Office or home network |
| WLAN | Wireless LAN | One building, wireless | Office Wi-Fi |
| MAN | Metropolitan Area Network | A city | City-wide ISP network |
| WAN | Wide Area Network | Countries or global | The Internet |

**Why it matters:** Attacks usually cross from a WAN (the Internet) into a LAN (the company). The boundary between them is where firewalls sit.

## 3. Network Topologies
The topology is the *layout* of how devices connect.
- **Bus:** all devices share one cable. Cheap, but one break kills everything.
- **Star:** every device connects to a central switch. The most common today. Easy to manage, but the switch is a single point of failure.
- **Ring:** devices form a loop. Rare today.
- **Mesh:** devices connect to many others. Very reliable, expensive.
- **Tree / Hybrid:** combinations of the above, common in larger organizations.

## 4. OSI Reference Model
A 7-layer model that describes how data travels from one computer to another. Memory trick (top to bottom): **A**ll **P**eople **S**eem **T**o **N**eed **D**ata **P**rocessing.

| # | Layer | What it does | Data unit | Examples |
|---|-------|--------------|-----------|----------|
| 7 | Application | Services the user interacts with | Data | HTTP, DNS, SMTP |
| 6 | Presentation | Formatting, encryption, compression | Data | TLS, JPEG |
| 5 | Session | Opens, maintains, closes sessions | Data | NetBIOS, RPC |
| 4 | Transport | Reliable delivery, ports | Segment | TCP, UDP |
| 3 | Network | Addressing and routing | Packet | IP, ICMP |
| 2 | Data Link | Delivery within the local network (MAC) | Frame | Ethernet, ARP |
| 1 | Physical | Raw bits over cable or air | Bits | Cables, Wi-Fi signals |

**Why it matters:** It lets you pinpoint *where* a problem or attack happens, for example ARP spoofing (Layer 2), IP spoofing (Layer 3), SYN flood (Layer 4), SQL injection and phishing (Layer 7).

## 5. Network Devices
- **Hub:** repeats data to every port (dumb, insecure, outdated).
- **Switch:** sends data only to the right device using **MAC addresses** (Layer 2).
- **Router:** connects different networks and forwards using **IP addresses** (Layer 3).
- **Firewall:** allows or blocks traffic based on rules.
- **Access Point:** lets wireless devices join the wired network.
- **IDS/IPS:** detects (IDS) or detects and blocks (IPS) suspicious traffic.
- **Proxy / Load balancer:** sits between users and servers to filter or distribute traffic.

## 6. TCP/IP Model
A simpler, real-world version of OSI with 4 layers:

| TCP/IP layer | Matches OSI layers |
|--------------|--------------------|
| Application | 5, 6, 7 |
| Transport | 4 |
| Internet | 3 |
| Network Access (Link) | 1, 2 |

**TCP vs UDP**
- **TCP:** reliable, checks that data arrived (web, email, SSH). Starts with the **three-way handshake**: SYN, then SYN-ACK, then ACK.
- **UDP:** fast, no delivery guarantee (DNS, video streaming).

**Why it matters:** Port scans and SYN floods abuse the handshake. Knowing the normal handshake helps you spot the abnormal one in logs.

## 7. IP Addressing
- **IPv4:** 32-bit, written like `192.168.1.10`. About 4.3 billion addresses, which ran short.
- **IPv6:** 128-bit, far more addresses.
- **Private ranges** (not routable on the Internet): `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`.
- **Subnet mask / CIDR:** says which part of the address is the *network* and which is the *host* (`/24` = `255.255.255.0`).
- **Default gateway:** the router a device uses to leave its network.
- **MAC vs IP:** MAC is the hardware address (local delivery); IP is the logical address (routing).

**Why it matters:** In an alert, instantly knowing whether an IP is internal (private) or external (public) tells you the direction of the traffic.

## 8. Network Address Translation (NAT)
NAT lets many private devices share one public IP by rewriting addresses at the router or firewall.
- **Static NAT:** one private IP maps to one public IP.
- **Dynamic NAT:** private IPs map to a pool of public IPs.
- **PAT (NAT overload):** many private IPs share one public IP, separated by port numbers. The most common.

**Why it matters:** Outside logs show only the firewall's public IP. To find which internal machine did something, you need the NAT/firewall logs.

---

## My own takeaways
- Knowing the OSI layers is less about memorizing them and more about asking "at which layer is this happening?" It tells me which device, log, or attack I'm dealing with.
- The TCP three-way handshake (SYN, SYN-ACK, ACK) is the baseline. A flood of SYNs with no ACK back is not normal traffic.
- The first thing to check on any alert is whether an IP is private (`10.x`, `172.16-31.x`, `192.168.x`) or public. That tells me if traffic is internal or coming from outside.
- NAT hides internal machines behind one public IP, so I need the firewall/NAT logs to find which internal host actually made a connection.
- Switches use MAC addresses, routers use IP addresses, and firewalls decide what is allowed. Knowing which device logs what tells me where to look.
- Most real attacks cross from a WAN (the Internet) into a LAN, so the network edge is where I expect to find the first evidence.

**Quiz score:** _(add yours)_
**What confused me / what I want to revisit:** _(add yours, for example subnetting or CIDR)_

## Related notes
- [Windows Fundamentals](02-windows-fundamentals.md)
