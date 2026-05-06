# 🏥 Enterprise Hospital Network Design — Cisco Packet Tracer

A full-scale enterprise network simulation built in **Cisco Packet Tracer**, designed for a hospital environment with two sites (HQ & Branch), complete with inter-VLAN routing, site-to-site VPN, DHCP, OSPF, PAT, and wireless configuration.

---

## 📌 Project Overview

This project simulates a real-world enterprise network for a hospital organization with:
- **HQ (Headquarters)** — 6 departments, multilayer switches, server room
- **Branch** — 6 departments, multilayer switch, connected to HQ via IPSec VPN
- **ISP** — Dual ISP links for redundancy

All configurations are done through Cisco IOS CLI, covering Layer 2 through Layer 7 network services.

---

## 🗺️ Network Topology

![Network Topology](topo.png)

---

## 🏢 HQ Departments & VLANs

| Department | VLAN | Subnet |
|---|---|---|
| MLOCS | 10 | 192.168.100.0/26 |
| MER | 20 | 192.168.100.64/26 |
| MRM | 30 | 192.168.100.128/26 |
| IT | 40 | 192.168.100.192/26 |
| CS | 50 | 192.168.101.0/26 |
| GWA | 60 | 192.168.101.64/26 |
| Server Room | 70 | 192.168.102.64/28 |

## 🏢 Branch Departments & VLANs

| Department | VLAN | Subnet |
|---|---|---|
| NSO | 80 | 192.168.101.128/27 |
| HL | 90 | 192.168.101.160/27 |
| HR | 100 | 192.168.101.192/27 |
| MK | 110 | 192.168.101.224/27 |
| FN | 120 | 192.168.102.0/27 |
| BR-GWA | 130 | 192.168.102.32/27 |

---

## ⚙️ Configuration Steps

### 1. Basic Device Settings
All routers, Layer 3 switches, and Layer 2 switches configured with:
- Hostname, enable password, console password
- `no ip domain-lookup`, banner MOTD
- `service password-encryption`

### 2. SSH Configuration (Routers & L3 Switches)
```
ip domain-name cisco.com
username admin password cisco
crypto key generate rsa (1024-bit)
line vty 0 15 → login local, transport input ssh
```

### 3. VLAN & Port Assignment
- Access ports assigned per department VLAN
- Trunk ports configured between L2 switches → L3 switches → Router
- `switchport trunk encapsulation dot1q` on L3 switches

### 4. Switchport Security (Server Room)
```
switchport port-security
switchport port-security maximum 1
switchport port-security mac-address sticky
switchport port-security violation shutdown
```

### 5. Subnetting & IP Addressing
- Private address space: `192.168.100.0 – 192.168.102.x`
- Public ISP links: `195.136.17.0/30` (dual ISP)
- Point-to-point links: /30 subnets between routers and L3 switches

### 6. OSPF Routing (Area 0)
- Enabled on HQ-Router, BR-Router, ISP, and both L3 switches
- Route summarization:
  - HQ: `192.168.100.0/24` + `192.168.101.0/25`
  - Branch: `192.168.101.128/24`

### 7. Static IP — Server Room Devices
Servers (DHCP, DNS, Email, Web) assigned static IPs within `192.168.102.64/28`

### 8. DHCP Server
Central DHCP server at `192.168.102.67` serving all VLANs

### 9. Inter-VLAN Routing + IP Helper
- SVIs configured on both HQ-MultilayerSW and HQ-MultilayerSW2
- `ip helper-address 192.168.102.67` on each SVI to relay DHCP

### 10. Wireless Network
- Access Points deployed per department
- SSID configured per department VLAN

### 11. Default Static Routes
- HQ-MultilayerSW → HQ-Router (`192.168.102.82`)
- HQ-Router → ISP (primary + floating static with AD 70 for backup)

### 12. Site-to-Site IPSec VPN
```
Phase 1 (ISAKMP):  AES-256, pre-shared key, DH Group 5
Phase 2 (IPSec):   ESP-AES + ESP-SHA-HMAC
Interesting traffic defined by ACL 110
VPN Tunnel: HQ-Router (192.168.102.89) ↔ BR-Router (192.168.102.90)
```

### 13. PAT (Port Address Translation)
- Inside interfaces: G0/0, G0/1, G0/2 on HQ-Router
- Outside interfaces: Serial0/0/0, Serial0/0/1 (dual ISP)
- ACL 1 permits all internal subnets for NAT overload

---

## 🔐 Security Features

| Feature | Details |
|---|---|
| SSH v2 | All routers and L3 switches |
| Port Security | Server-side switch (sticky MAC, shutdown violation) |
| IPSec VPN | AES-256 site-to-site tunnel |
| ACL | PAT access list restricting NAT to internal networks |
| Password Encryption | `service password-encryption` on all devices |

---

## 🛠️ Tools & Technologies

- **Cisco Packet Tracer** — Network simulation
- **Cisco IOS** — Router & switch operating system
- **Protocols:** OSPF, IPSec/IKE, DHCP, SSH, 802.1Q, PAT/NAT
- **Devices:** Cisco 2911 Router, 3560 L3 Switch, 2960 L2 Switch, APs, Servers

---

## ✅ Verified & Tested

- [x] Full OSPF neighbor adjacency HQ ↔ Branch ↔ ISP
- [x] DHCP lease on all department PCs
- [x] Inter-VLAN connectivity across all departments
- [x] PAT working — internal hosts reach ISP IPs
- [x] IPSec VPN tunnel up — HQ PCs ping Branch PCs successfully
- [x] SSH remote access on all routers and L3 switches
- [x] Port security active on server room switch ports

---

## 📁 Files

| File | Description |
|---|---|
| `topo.png` | Full network topology diagram |
| `project-enterprise.docx` | Step-by-step configuration notes |
| `README.md` | This file |

---

## 👤 Author

Built as a hands-on network engineering project to demonstrate enterprise-level Cisco configuration skills.
