# Northgate Logistics: Network Addressing Plan

Northgate Logistics operates a single-site logistics and distribution facility for receiving, storing and dispatching goods.

## Core Architecture Overview

- **Address block:** 192.168.10.0/24
- **Gateway convention:** First usable IP address in each subnet
- **Switch type:** 24-port managed switches

## Subnet & VLAN Specifications

### VLAN 30 : Warehouse Floor

- **Network:** 192.168.10.0/25
- **Subnet mask:** 255.255.255.128
- **Usable range:** 192.168.10.1 - 192.168.10.126
- **Broadcast:** 192.168.10.127
- **Gateway:** 192.168.10.1
- **Devices:** 52 + 20% growth (11) = 63 planned devices. Including the gateway, 64 IP addresses are required.

### VLAN 40: Drivers & Yard

- **Network:** 192.168.10.128/26
- **Subnet mask:** 255.255.255.192
- **Usable range:** 192.168.10.129 - 192.168.10.190
- **Broadcast:** 192.168.10.191
- **Gateway:** 192.168.10.129
- **Devices:** 25 ePOD scanners + 20% growth (5) = 30 planned devices
- **Note:** Telematics units are excluded because they use mobile data, not the LAN.

### VLAN 10: Office & Planning

- **Network:** 192.168.10.192/27
- **Subnet mask:** 255.255.255.224
- **Usable range:** 192.168.10.193 - 192.168.10.222
- **Broadcast:** 192.168.10.223
- **Gateway:** 192.168.10.193
- **Devices:** 10 PCs + 10 desk phones + 3 network printers = 23 devices
- **Growth allowance:** 20% (5)
- **Total planned devices:** 28
- **Note:** Desk phones remain in the Office VLAN to simplify the initial design.

### VLAN 20 - Supervision & Inventory

- **Network:** 192.168.10.224/28
- **Subnet mask:** 255.255.255.240
- **Usable range:** 192.168.10.225 - 192.168.10.238
- **Broadcast:** 192.168.10.239
- **Gateway:** 192.168.10.225
- **Devices:** 4 shared PCs + 6 audit tablets = 10 devices
- **Growth allowance:** 20% (2)
- **Total planned devices:** 12

### VLAN 99 - Network Management

- **Network:** 192.168.10.240/28
- **Subnet mask:** 255.255.255.240
- **Usable range:** 192.168.10.241 - 192.168.10.254
- **Broadcast:** 192.168.10.255
- **Gateway:** 192.168.10.241
- **Devices:** 9 switches + 1 local server = 10 devices, plus 1 gateway
- **Total assigned IP addresses:** 11
- **Spare usable addresses:** 3

## Infrastructure & Switch Management

### Switch Allocation

| Location | Assigned Switches |
|---|---|
| Warehouse Floor | Switches 1, 2 and 3 |
| Drivers & Yard | Switches 4 and 5 |
| Office & Planning | Switches 6 and 7 |
| Supervision & Inventory | Switch 8 |
| Central Communications Room | Switch 9 (Core Switch) |

### Switch Management IP Assignments

| Device | IP Address | Subnet |
|---|---|---|
| Switch 1 - Warehouse Floor | 192.168.10.242 | Management (VLAN 99) |
| Switch 2 - Warehouse Floor | 192.168.10.243 | Management (VLAN 99) |
| Switch 3 - Warehouse Floor | 192.168.10.244 | Management (VLAN 99) |
| Switch 4 - Drivers & Yard | 192.168.10.245 | Management (VLAN 99) |
| Switch 5 - Drivers & Yard | 192.168.10.246 | Management (VLAN 99) |
| Switch 6 - Office & Planning | 192.168.10.247 | Management (VLAN 99) |
| Switch 7 - Office & Planning | 192.168.10.248 | Management (VLAN 99) |
| Switch 8 - Supervision & Inventory | 192.168.10.249 | Management (VLAN 99) |
| Switch 9 - Core Switch | 192.168.10.250 | Management (VLAN 99) |
| Local Server | 192.168.10.251 | Management (VLAN 99) |

## Architecture Decisions & Topology

### Design Rationale

- **Subnet alignment:** The address block is divided into correctly aligned, consecutive subnet ranges. Allocating larger subnets first ensures smaller subnets fit into the remaining address space without overlapping.
- **VLAN 30 allocation (/25):** A /26 provides only 62 usable addresses, which is insufficient for 63 planned devices plus the gateway. A /25 provides 126 usable addresses, consuming half of the 192.168.10.0/24 address block.
- **Logical separation:** VLANs separate departments logically, while switches provide physical network connectivity.
- **Desk phones:** Remain in VLAN 10 initially to simplify the design. A separate voice VLAN can be introduced in Phase 2.
- **ePOD scanners:** Included because they require on-site network connectivity to synchronise.
- **Telematics:** Excluded because they communicate using cellular/mobile data rather than the site LAN.
- **Native VLAN:** VLAN 999 is configured as an unused native VLAN on trunk links rather than using a standard user or management VLAN.
- **Address space limitation:** The current /24 is fully allocated. Additional address space will be required for future expansion.

### Phase 2 Considerations

Additional address space will be evaluated for:

- Wireless client traffic
- Access point (AP) management
- Dedicated voice VLAN (VoIP)

### Network Topology Specifications

- One router connects to the core switch through a single physical interface configured as an 802.1Q trunk (router-on-a-stick).
- The core switch connects to the eight access switches through trunk links.
- VLANs 10, 20, 30, 40 and 99 are carried across the required trunk links.
- Router subinterfaces provide default gateways for all VLANs.
- Access ports assign end devices directly to their designated VLANs.
