> **Academic Project Notice**
> 
> This project was developed as part of coursework for the Computer Networks course at the Department of Electronics and Computer Engineering, Pulchowk Campus, Tribhuvan University. It is strictly for academic purposes.
> 
> This work is provided for reference only. You may read and learn from it, but you may **not** copy, reproduce, or submit any part of it as your own work.
> 


---

# Hierarchical Network Design — Pulchowk Campus



## Quick Facts

| Parameter | Value |
|---|---|
| IP Block | `103.5.0.0/22` (1,024 addresses) |
| Allocated | 1,004 addresses |
| Reserved (future) | 20 addresses — `103.5.3.236 – 103.5.3.255` |
| Routing Protocol | OSPF multi-area + BGP (border) |
| Total Routers | 16 (6 transit-only, 10 LAN-connected) |
| OSPF Areas | 5 (Area 0 backbone + Areas 10, 20, 30, 40) |
| IP Allocation | VLSM |
| ISP | Vianet Communications |

---

## Network Architecture

The design follows a three-tier hierarchical model.

**Core Layer** — R-Core in the CIT server room maintains OSPF adjacencies with all four ABRs. R-BorderRouter sits between R-ISP and R-Core, redistributing the BGP default route into OSPF so all campus areas reach the internet.

**Distribution Layer** — Each area has one ABR that exchanges summarised routing information between its area and the backbone.

**Access Layer** — Building-level switches connect end devices to their local router. Area 10 uses 802.1Q trunking and VLANs in the DOECE and CIT buildings.

---

## OSPF Areas

| Area | Zone | Routers | LAN Subnets |
|---|---|---|---|
| Area 0 | Backbone, CIT Server Room | R-Core, R-BorderRouter, R-ISP | `103.5.3.192/28` |
| Area 10 | DOECE, CIT, Campus Facilities | ABR10, R-DOECE/Elec, R-CIT, R-Facilities | `103.5.0.0/24`, `/26`, `/27` ×2, `/28`, `/29` ×2 |
| Area 20 | Engineering & Applied Science | ABR20, R-Civil, R-MechAeroAppFSU | `103.5.1.192/26`, `103.5.2.0/25` |
| Area 30 | Architecture & Administration | ABR30, R-Admin, R-ArchMasters | `103.5.2.160/27`, `103.5.2.192/26`, `103.5.3.0/27` |
| Area 40 | Residential | ABR40, R-BoysHostel, R-GirlsStaff | `103.5.3.40/29`, `103.5.3.64/26`, `103.5.3.128/26` |

> 6 transit-only routers: R-ISP, R-BorderRouter, ABR10, ABR20, ABR30, ABR40.  
> 10 LAN-connected routers: R-Core, R-DOECE/Elec, R-CIT, R-Facilities, R-Civil, R-MechAeroAppFSU, R-Admin, R-ArchMasters, R-BoysHostel, R-GirlsStaff.

---

## IP Address Allocation

### Subnet Size Summary

| Prefix | Block | Usable Hosts | Subnets Used | Allocated To |
|---|---|---|---|---|
| /24 | 256 | 254 | 1 | DOECE/Electrical |
| /25 | 128 | 126 | 1 | Mech + Aero + Applied Science + FSU |
| /26 | 64 | 62 | 5 | CIT, Civil, Admin, Boys Hostel, Girls/Staff |
| /27 | 32 | 30 | 4 | Library, ICTC, Architecture, Masters Hostel |
| /28 | 16 | 14 | 2 | Robotics & Workshop, Server LAN |
| /29 | 8 | 6 | 3 | Upper Canteen, Dept Server LAN, Lower Canteen |
| /30 | 4 | 2 | 22 | All P2P links |

### Area 0 — Backbone

| Index | Name | Subnet | Gateway | Type |
|---|---|---|---|---|
| S29 | Server LAN | `103.5.3.192/28` | `.206` | LAN |
| S30 | R-Core ↔ R-BorderRouter | `103.5.3.208/30` | — | P2P |
| S31 | R-BorderRouter ↔ R-ISP | `103.5.3.212/30` | — | P2P |
| S32 | R-Core ↔ ABR10 | `103.5.3.216/30` | — | P2P backbone |
| S33 | R-Core ↔ ABR20 | `103.5.3.220/30` | — | P2P backbone |
| S34 | R-Core ↔ ABR30 | `103.5.3.224/30` | — | P2P backbone |
| S35 | R-Core ↔ ABR40 | `103.5.3.228/30` | — | P2P backbone |
| S36 | R-BorderRouter ↔ R-ISP | `103.5.3.232/30` | — | P2P to ISP |

### Area 10 — DOECE, CIT & Facilities

| Index | Name | Subnet | Gateway | Type |
|---|---|---|---|---|
| S1 | VLAN 1 — DOECE/Elect | `103.5.0.0/24` | `.254` | LAN |
| S2 | VLAN 10 — CIT Building | `103.5.1.0/26` | `.62` | LAN |
| S3 | VLAN 20 — Library | `103.5.1.64/27` | `.94` | LAN |
| S4 | VLAN 30 — ICTC | `103.5.1.96/27` | `.126` | LAN |
| S5 | Robotics & Workshop | `103.5.1.128/28` | `.142` | LAN |
| S6 | Upper Canteen | `103.5.1.144/29` | `.150` | LAN |
| S7 | ABR10 ↔ R-DOECE | `103.5.1.152/30` | — | P2P |
| S8 | ABR10 ↔ R-CIT | `103.5.1.156/30` | — | P2P |
| S9 | ABR10 ↔ R-Facilities | `103.5.1.160/30` | — | P2P |
| S10 | R-DOECE ↔ R-CIT | `103.5.1.164/30` | — | P2P redundant |
| S11 | VLAN 10 — Dept Server LAN | `103.5.1.168/29` | `.174` | LAN |

### Area 20 — Engineering & Applied Science

| Index | Name | Subnet | Gateway | Type |
|---|---|---|---|---|
| S12 | ABR20 ↔ R-Civil | `103.5.1.176/30` | — | P2P |
| S13 | ABR20 ↔ R-MechAeroAppFSU | `103.5.1.180/30` | — | P2P |
| S14 | Civil Engineering | `103.5.1.192/26` | `.254` | LAN |
| S15 | Mech + Aero + Applied Sci + FSU + Incubation | `103.5.2.0/25` | `.126` | LAN |
| S16 | R-Civil ↔ R-MechAeroAppFSU | `103.5.2.128/30` | — | P2P redundant |
| S17 | ABR10 ↔ ABR20 | `103.5.2.136/30` | — | P2P inter-area redundant |

### Area 30 — Architecture & Administration

| Index | Name | Subnet | Gateway | Type |
|---|---|---|---|---|
| S18 | ABR30 ↔ R-Admin | `103.5.2.140/30` | — | P2P |
| S19 | ABR30 ↔ R-ArchMasters | `103.5.2.144/30` | — | P2P |
| S20 | ABR30 ↔ ABR40 | `103.5.2.148/30` | — | P2P inter-area redundant |
| S21 | Architecture Department | `103.5.2.160/27` | `.190` | LAN |
| S22 | Admin & Accounts | `103.5.2.192/26` | `.254` | LAN |
| S23 | Masters Hostel | `103.5.3.0/27` | `.30` | LAN |

### Area 40 — Residential

| Index | Name | Subnet | Gateway | Type |
|---|---|---|---|---|
| S24 | ABR40 ↔ R-BoysHostel | `103.5.3.32/30` | — | P2P |
| S25 | ABR40 ↔ R-GirlsStaff | `103.5.3.36/30` | — | P2P |
| S26 | Lower Canteen | `103.5.3.40/29` | `.46` | LAN |
| S27 | Boys Hostel | `103.5.3.64/26` | `.126` | LAN |
| S28 | Girls Hostel + Staff Quarters | `103.5.3.128/26` | `.190` | LAN |

> `103.5.3.48 – 103.5.3.63` (16 addresses) reserved for future use.

---

## VLAN Design (Area 10 Only)

### R-DOECE/Elec

Switch hierarchy: **SW-FirstFloor** → R-DOECE/Elec (distribution). SW-SecondFloor and SW-ThirdFloor connect to SW-FirstFloor (access). All three switches carry both VLANs.

| VLAN | Subnet | Gateway | Purpose |
|---|---|---|---|
| 1 | `103.5.0.0/24` | `103.5.0.254` | DOECE/Elect user hosts |
| 10 | `103.5.1.168/29` | `103.5.1.174` | Departmental servers (DNS2, Web2, DHCP2) |

### R-CIT

Switch hierarchy: **SW-CIT-ICTC-Lib-2** → R-CIT (distribution). SW-CIT-ICTC-Lib-1 and SW-CIT-ICTC-Lib-3 connect to Lib-2 (access). All three switches carry VLANs 10, 20, and 30.

| VLAN | Subnet | Gateway | Purpose |
|---|---|---|---|
| 10 | `103.5.1.0/26` | `103.5.1.62` | CIT Building hosts |
| 20 | `103.5.1.64/27` | `103.5.1.94` | Library hosts |
| 30 | `103.5.1.96/27` | `103.5.1.126` | ICTC hosts |

---

## Servers

| Server | IP | Subnet | Role |
|---|---|---|---|
| DNS1 | `103.5.3.193` | `103.5.3.192/28` | Primary DNS — authoritative for `pulchowk.edu.np`; upstream: ISP DNS |
| Web1 | `103.5.3.194` | `103.5.3.192/28` | Campus website — `pcampus.edu.np` and exam portal |
| DHCP1 | `103.5.3.195` | `103.5.3.192/28` | Central DHCP — serves all areas except DOECE/Elec |
| DNS2 | `103.5.1.169` | `103.5.1.168/29` | Dept DNS — resolves `departments.pcampus.edu.np` |
| Web2 | `103.5.1.170` | `103.5.1.168/29` | Dept website — hosts DOECE department pages |
| DHCP2 | `103.5.1.171` | `103.5.1.168/29` | DHCP for VLAN 1 (DOECE/Elec subnet) |
| R-ISP-DNS | `8.8.8.8` | ISP Network | External/upstream DNS |
| R-ISP-Web | `8.8.8.9` | ISP Network | `vianet.com.np` |

---

## Redundancy

| Link | Subnet | Type |
|---|---|---|
| R-DOECE ↔ R-CIT | `103.5.1.164/30` | Intra-area redundant P2P |
| R-Civil ↔ R-MechAeroAppFSU | `103.5.2.128/30` | Intra-area redundant P2P |
| ABR10 ↔ ABR20 | `103.5.2.136/30` | Inter-area redundant P2P |
| ABR30 ↔ ABR40 | `103.5.2.148/30` | Inter-area redundant P2P |

---

## Switch Topology

| Router | Distribution Switch | Access Switches |
|---|---|---|
| R-DOECE/Elec | SW-FirstFloor | SW-SecondFloor, SW-ThirdFloor |
| R-CIT | SW-CIT-ICTC-Lib-2 | SW-CIT-ICTC-Lib-1, SW-CIT-ICTC-Lib-3 |
| R-Facilities | SW-Robotics, SW-Canteen | — |
| R-Civil | SW-Civil | — |
| R-MechAeroAppFSU | SW-Distribution | SW-Aerospace, SW-Mech, SW-AppliedScience, SW-FSU, SW-Incubation |
| R-Admin | SW-Admin | SW-Finance |
| R-ArchMasters | SW-ArchDept, SW-MastersHostel | — |
| R-BoysHostel | SW-Block A | SW-Block B, SW-Block C, SW-LCanteen |
| R-GirlsStaff | SW-GirlsHostel, SW-StaffQuarter | — |

---

## Verification

Tested in Cisco Packet Tracer:

- **Ping** across areas (e.g. to `103.5.1.153`) — 0% packet loss
- **Tracert** from Area 10 to Area 20 (`103.5.2.6`) — confirmed OSPF inter-area routing through backbone
- **Departmental website** — `http://departments.pcampus.edu.np` resolved and served by DNS2/Web2
- **Campus website** — `http://pcampus.edu.np` and `/exam.html` served by central servers in Area 0
- **ISP connectivity** — `http://vianet.com.np` reachable via R-BorderRouter through R-ISP

---

## Conclusion

The hierarchical network design for Pulchowk Campus fulfils all project requirements. VLSM ensures efficient IP utilisation, VLANs provide logical segmentation in high-density buildings, and redundant P2P links improve resilience. A 20-address reserve block supports future expansion without renumbering existing subnets.