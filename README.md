<div align="center">

<!-- HEADER BANNER -->
<img src="https://capsule-render.vercel.app/api?type=waving&color=1B3A6B&height=200&section=header&text=Askay%20University%20Network%20Design&fontSize=32&fontColor=ffffff&fontAlignY=40&desc=VLAN%20Segmentation%20%7C%20VLSM%20Subnetting%20%7C%20DHCP%20%7C%20Router-on-a-Stick&descAlignY=62&descSize=15&descColor=a8c8f0" width="100%"/>

<br/>

<!-- BADGES -->
![Cisco Packet Tracer](https://img.shields.io/badge/Cisco%20Packet%20Tracer-1BA0D7?style=for-the-badge&logo=cisco&logoColor=white)
![Networking](https://img.shields.io/badge/Computer%20Networks-1B3A6B?style=for-the-badge&logo=network-wired&logoColor=white)
![VLAN](https://img.shields.io/badge/VLAN%20Segmentation-2E5FA3?style=for-the-badge)
![DHCP](https://img.shields.io/badge/DHCP%20Configuration-3A86C8?style=for-the-badge)
![VLSM](https://img.shields.io/badge/VLSM%20Subnetting-0d6efd?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Complete-2ea44f?style=for-the-badge)

<br/>

# 🏫 Hierarchical Network Design & Implementation
### School of Computing and Information Technology — Askay University of Technology

> *A fully simulated enterprise-grade network infrastructure featuring VLSM-based IP addressing, VLAN segmentation across four departments, Router-on-a-Stick inter-VLAN routing, and centralized DHCP configuration — built and verified in Cisco Packet Tracer.*

<br/>

</div>

---

## 📋 Table of Contents

- [Project Overview](#-project-overview)
- [Network Topology](#-network-topology)
- [IP Addressing Scheme (VLSM)](#-ip-addressing-scheme-vlsm)
- [VLAN Design](#-vlan-design)
- [Hardware Components](#-hardware-components)
- [Configuration Files](#-configuration-files)
- [Implementation Details](#-implementation-details)
  - [Access Layer Switches](#1-access-layer-switches)
  - [Core Switch](#2-core-switch)
  - [Router-on-a-Stick](#3-router-on-a-stick)
  - [DHCP Configuration](#4-dhcp-configuration)
  - [Wireless Access Point](#5-wireless-access-point)
- [Verification & Testing](#-verification--testing)
- [Challenges & Solutions](#-challenges--solutions)
- [Key Learnings](#-key-learnings)
- [Project Structure](#-project-structure)

---

## 🌐 Project Overview

This project designs and implements a **hierarchical network infrastructure** for the School of Computing and Information Technology at **Askay University of Technology**. The network supports four distinct departmental environments with logical isolation, automatic IP assignment, and full inter-departmental routing.

| Attribute | Detail |
|-----------|--------|
| **Institution** | Askay University of Technology |
| **Department** | School of Computing and Information Technology |
| **Simulation Tool** | Cisco Packet Tracer |
| **Base Network** | `192.168.50.0/24` |
| **Subnetting Method** | Variable Length Subnet Masking (VLSM) |
| **Routing Method** | Router-on-a-Stick (802.1Q subinterfaces) |
| **IP Assignment** | DHCP (router-based, per-VLAN pool) |
| **Network Model** | Hierarchical — Core / Distribution / Access |

---

## 🗺 Network Topology

The network follows the **three-layer hierarchical design model**, providing separation of concerns between routing, switching, and end-device access.

```
                    ┌──────────────────────────────────┐
                    │          CORE ROUTER              │
                    │   G0/0.10 → 192.168.50.1/26       │
                    │   G0/0.20 → 192.168.50.65/27      │
                    │   G0/0.30 → 192.168.50.97/28      │
                    │   G0/0.40 → 192.168.50.113/29     │
                    │   [DHCP Server — all 4 VLANs]     │
                    └────────────┬─────────────────────┘
                                 │ G0/0 (802.1Q Trunk)
                    ┌────────────▼─────────────────────┐
                    │           CORE SWITCH             │
                    │   [STP Root — VLANs 10/20/30/40]  │
                    └──┬──────┬──────┬──────┬──────────┘
                       │      │      │      │
                  G0/2 │ G0/3 │ G0/4 │ G0/5 │
          ┌────────────▼─┐ ┌──▼──┐ ┌─▼───┐ ┌▼──────────┐
          │  SW-COMP-LAB │ │SW-  │ │SW-  │ │SW-SERVER  │
          │  VLAN 10     │ │STAFF│ │POST │ │ROOM       │
          │  /26 (62h)   │ │VLAN │ │VLAN │ │VLAN 40    │
          └──────┬───────┘ │ 20  │ │ 30  │ │/29 (6h)   │
                 │         │/27  │ │/28  │ └─────┬──────┘
              [PCs x60]    │(30h)│ │(14h)│    [Servers]
                           └──┬──┘ └──┬──┘
                           [PCs]  [PCs + 📶 AP]
```

### Layer Responsibilities

| Layer | Device | Role |
|-------|--------|------|
| **Distribution** | Core Router (Cisco 1941) | Inter-VLAN routing, DHCP services |
| **Core** | Core Switch (Cisco 3560) | 802.1Q trunk aggregation, STP root |
| **Access** | 4× Departmental Switches (Cisco 2960) | End-device VLAN connectivity |

---

## 📊 IP Addressing Scheme (VLSM)

Subnets were allocated in **descending order of host requirements** from the assigned `192.168.50.0/24` block, minimizing address space waste.

| Department | Subnet | CIDR | Subnet Mask | Usable Range | Broadcast | Hosts |
|------------|--------|------|-------------|--------------|-----------|-------|
| **Computer Lab** | `192.168.50.0` | `/26` | `255.255.255.192` | `.1` – `.62` | `.63` | 62 |
| **Staff Offices** | `192.168.50.64` | `/27` | `255.255.255.224` | `.65` – `.94` | `.95` | 30 |
| **Postgrad Room** | `192.168.50.96` | `/28` | `255.255.255.240` | `.97` – `.110` | `.111` | 14 |
| **Server Room** | `192.168.50.112` | `/29` | `255.255.255.248` | `.113` – `.118` | `.119` | 6 |

> **VLSM Efficiency:** Only 112 addresses consumed out of 254 available — 44% utilisation, with room for future expansion.

---

## 🔀 VLAN Design

Four VLANs provide logical isolation between all departmental segments.

```
┌──────────┬──────────────────┬────────────────────┬──────────────┐
│  VLAN ID │  Name            │  Department        │  Gateway     │
├──────────┼──────────────────┼────────────────────┼──────────────┤
│  VLAN 10 │  COMPUTER_LAB    │  Computer Lab 1    │  .50.1       │
│  VLAN 20 │  STAFF           │  Staff Offices     │  .50.65      │
│  VLAN 30 │  POSTGRAD        │  Postgrad Research │  .50.97      │
│  VLAN 40 │  SERVER_ROOM     │  Server Room       │  .50.113     │
└──────────┴──────────────────┴────────────────────┴──────────────┘
```

**Benefits of VLAN segmentation in this design:**
- 🔒 **Security** — Departments cannot communicate without explicit routing
- 📡 **Broadcast Control** — Each VLAN is its own broadcast domain
- 🛠 **Manageability** — Policies applied per-VLAN on the router
- 📈 **Scalability** — New departments = new VLAN, no physical rewiring

---

## 🔧 Hardware Components

| Device | Model (Packet Tracer) | Qty | Role |
|--------|----------------------|-----|------|
| Router | Cisco 1941 | 1 | Inter-VLAN routing + DHCP |
| Core Switch | Cisco 3560 | 1 | Backbone trunk aggregation |
| Access Switches | Cisco 2960 | 4 | Departmental access layer |
| Wireless AP | WRT300N / Cisco AP | 1 | Wireless in Postgrad Room |
| PCs / Laptops | Generic PT End Devices | Multiple | End-user hosts |
| Servers | Generic PT Servers | 6 | DNS, Web, File services |

---

## 📁 Configuration Files

All configuration files follow a systematic naming and ordering convention matching the network design flow.

```
📦 Askay-network-config/
├── 📄 00_README-CONFIG-INDEX.txt          ← IP reference + apply order
├── 📄 01_ACCESS-SW-COMPUTER-LAB_VLAN10.txt
├── 📄 02_ACCESS-SW-STAFF-OFFICES_VLAN20.txt
├── 📄 03_ACCESS-SW-POSTGRAD-ROOM_VLAN30.txt
├── 📄 04_ACCESS-SW-SERVER-ROOM_VLAN40.txt
├── 📄 05_CORE-SWITCH_ALL-VLANS.txt
├── 📄 06_CORE-ROUTER_INTERVLAN-DHCP.txt
├── 📄 07_WIRELESS-AP_POSTGRAD-ROOM.txt
└── 📄 08_VERIFICATION-AND-TROUBLESHOOTING.txt
```

> **Apply order:** Access switches (01–04) → Core switch (05) → Router (06) → AP (07)

---

## ⚙️ Implementation Details

### 1. Access Layer Switches

Each departmental switch is configured with a **dedicated VLAN**, access ports for end devices, and a **trunk uplink** to the core switch.

**Computer Lab (SW-COMPUTER-LAB — VLAN 10)**

```cisco
! Create VLAN
vlan 10
 name COMPUTER_LAB

! Assign access ports (Fa0/1–24) to VLAN 10
interface range FastEthernet 0/1 - 24
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast

! Configure trunk uplink to Core Switch
interface GigabitEthernet 0/1
 description UPLINK-TO-CORE-SWITCH
 switchport mode trunk
 switchport trunk allowed vlan 10
```

> Same pattern applied to `SW-STAFF-OFFICES` (VLAN 20), `SW-POSTGRAD-ROOM` (VLAN 30), and `SW-SERVER-ROOM` (VLAN 40).

---

### 2. Core Switch

The core switch aggregates all four VLANs and manages **trunk links** to both the router and all access switches. It is also configured as the **STP root bridge** for all VLANs.

```cisco
! Create all VLANs
vlan 10
 name COMPUTER_LAB
vlan 20
 name STAFF
vlan 30
 name POSTGRAD
vlan 40
 name SERVER_ROOM

! Trunk uplink to Router — carries ALL VLANs
interface GigabitEthernet 0/1
 description TRUNK-TO-ROUTER-G0/0
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40

! Set as STP Root for all VLANs
spanning-tree mode rapid-pvst
spanning-tree vlan 10,20,30,40 root primary
```

---

### 3. Router-on-a-Stick

A **single physical interface** (`G0/0`) is divided into four logical **subinterfaces** — one per VLAN — using IEEE 802.1Q encapsulation. Each subinterface serves as the default gateway for its VLAN.

```cisco
! Parent interface — no IP, must be up
interface GigabitEthernet 0/0
 no ip address
 no shutdown

! VLAN 10 — Computer Lab gateway
interface GigabitEthernet 0/0.10
 encapsulation dot1Q 10
 ip address 192.168.50.1 255.255.255.192

! VLAN 20 — Staff Offices gateway
interface GigabitEthernet 0/0.20
 encapsulation dot1Q 20
 ip address 192.168.50.65 255.255.255.224

! VLAN 30 — Postgrad Room gateway
interface GigabitEthernet 0/0.30
 encapsulation dot1Q 30
 ip address 192.168.50.97 255.255.255.240

! VLAN 40 — Server Room gateway
interface GigabitEthernet 0/0.40
 encapsulation dot1Q 40
 ip address 192.168.50.113 255.255.255.248
```

---

### 4. DHCP Configuration

DHCP pools are configured **per VLAN** on the router. Static/gateway addresses are excluded before the pools are declared.

```cisco
! Exclude gateway + management IPs from each pool
ip dhcp excluded-address 192.168.50.1  192.168.50.15
ip dhcp excluded-address 192.168.50.65 192.168.50.70
ip dhcp excluded-address 192.168.50.97 192.168.50.101
ip dhcp excluded-address 192.168.50.113 192.168.50.118  ! Static servers

! DHCP pool — Computer Lab
ip dhcp pool VLAN10-COMPUTER-LAB
 network 192.168.50.0 255.255.255.192
 default-router 192.168.50.1
 dns-server 8.8.8.8 8.8.4.4
 domain-name lab.askay.ac.ke

! DHCP pool — Staff Offices
ip dhcp pool VLAN20-STAFF-OFFICES
 network 192.168.50.64 255.255.255.224
 default-router 192.168.50.65
 dns-server 8.8.8.8 8.8.4.4
```

**DHCP Assignable Ranges:**

| VLAN | Pool | Assignable Range |
|------|------|-----------------|
| 10 | COMPUTER_LAB | `192.168.50.16` – `192.168.50.62` |
| 20 | STAFF | `192.168.50.71` – `192.168.50.94` |
| 30 | POSTGRAD | `192.168.50.102` – `192.168.50.110` |
| 40 | SERVER_ROOM | Static IPs only — DHCP disabled |

---

### 5. Wireless Access Point

A wireless AP is deployed in the Postgraduate Research Room, bridging wireless clients onto **VLAN 30**.

```
SSID      : Askay-POSTGRAD
Security  : WPA2-PSK
VLAN      : 30 (POSTGRAD)
IP Pool   : 192.168.50.102 – 192.168.50.110 (via DHCP)
```

The AP connects to `SW-POSTGRAD-ROOM` on a dedicated access port (Fa0/13) configured for VLAN 30.

---

## ✅ Verification & Testing

After full configuration, the following commands were used to verify correct operation:

### On Switches
```cisco
show vlan brief                        ! Verify VLAN-to-port assignments
show interfaces trunk                  ! Confirm active trunk links
show spanning-tree vlan 10             ! Confirm STP root bridge
```

### On Router
```cisco
show ip interface brief                ! Confirm all subinterfaces up/up
show ip route                          ! Confirm connected routes per VLAN
show ip dhcp binding                   ! Confirm hosts received IPs
show ip dhcp pool                      ! Review pool utilisation
```

### Connectivity Tests
```bash
# From Router — gateway self-test
ping 192.168.50.1     # VLAN 10
ping 192.168.50.65    # VLAN 20
ping 192.168.50.97    # VLAN 30
ping 192.168.50.113   # VLAN 40

# From PC in VLAN 10 — inter-VLAN routing test
ping 192.168.50.71    # PC in VLAN 20 (Staff)
```

All tests confirmed **successful DHCP assignment**, **active trunk links**, and **full inter-VLAN routing**.

---

## 🛠 Challenges & Solutions

| # | Challenge | Root Cause | Resolution |
|---|-----------|-----------|------------|
| 1 | **Spanning Tree PVID mismatch errors** | Inconsistent native VLAN on trunk ends | Applied `switchport trunk native vlan 99` uniformly on all trunk interfaces |
| 2 | **DHCP hosts showing `169.254.x.x`** (APIPA) | Trunk misconfiguration — DHCP Discover not reaching router | Verified trunk allowed VLANs on core switch uplink; corrected `switchport trunk allowed vlan` |
| 3 | **Incorrect port VLAN assignments** | Ports left in default VLAN 1 | Re-applied `switchport access vlan <id>` and confirmed with `show vlan brief` |
| 4 | **Router subinterface down/down** | Parent interface `G0/0` had `shutdown` applied | Added `no shutdown` on physical interface before subinterface config |

---

## 💡 Key Learnings

- **VLSM** eliminates address waste by matching subnet size precisely to host requirements — critical in IPv4 environments
- **Router-on-a-Stick** proves that inter-VLAN routing requires only one physical link when combined with 802.1Q trunking
- **DHCP exclusions must be configured before pool declarations** — order matters in Cisco IOS
- **Trunk native VLAN consistency** across both ends of every trunk link is essential to prevent Spanning Tree errors
- **PortFast** on access ports accelerates host link-up and avoids STP listening/learning delays for end devices
- **Hierarchical design** makes troubleshooting systematic — Access → Core → Router, one layer at a time

---

## 📂 Project Structure

```
askay-university-network-design/
│
├── configs/
│   ├── 00_README-CONFIG-INDEX.txt
│   ├── 01_ACCESS-SW-COMPUTER-LAB_VLAN10.txt
│   ├── 02_ACCESS-SW-STAFF-OFFICES_VLAN20.txt
│   ├── 03_ACCESS-SW-POSTGRAD-ROOM_VLAN30.txt
│   ├── 04_ACCESS-SW-SERVER-ROOM_VLAN40.txt
│   ├── 05_CORE-SWITCH_ALL-VLANS.txt
│   ├── 06_CORE-ROUTER_INTERVLAN-DHCP.txt
│   ├── 07_WIRELESS-AP_POSTGRAD-ROOM.txt
│   └── 08_VERIFICATION-AND-TROUBLESHOOTING.txt
│
├── docs/
│   └── Network_Design_Implementation_Report.docx
│
└── README.md
```

---

<div align="center">

---

**Askay University of Technology**
*School of Computing and Information Technology*

![Cisco](https://img.shields.io/badge/Cisco-Networking-1BA0D7?style=flat-square&logo=cisco&logoColor=white)
![Academic](https://img.shields.io/badge/Academic-Project-1B3A6B?style=flat-square)
![Year](https://img.shields.io/badge/Year-2025-2E5FA3?style=flat-square)

<img src="https://capsule-render.vercel.app/api?type=waving&color=1B3A6B&height=100&section=footer" width="100%"/>

</div>