# Home Network VLAN Lab

A segmented home network built in Cisco Packet Tracer. Designed as the blueprint for my planned real-world home setup — separating trusted devices, IoT, and guest access with VLANs and ACLs.

## What this project demonstrates

- VLAN creation and access port assignment
- 802.1Q trunking between switch and router
- Router-on-a-stick (inter-VLAN routing with subinterfaces)
- DHCP pools per VLAN with excluded address ranges
- Extended ACLs to enforce segmentation between VLANs
- Wireless segmentation using separate SSIDs mapped to different VLANs

## Topology

![Network topology](homelab/topology.png)

## Network design

| VLAN | Name | Subnet | Gateway | Purpose |
|------|------|--------|---------|---------|
| 10 | Trusted | 192.168.10.0/24 | 192.168.10.1 | Personal PCs, laptop, phones |
| 20 | IoT | 192.168.20.0/24 | 192.168.20.1 | Smart TV (and future smart devices) |
| 30 | Guest | 192.168.30.0/24 | 192.168.30.1 | Visitor devices |

## Security rules (ACLs)

| Source VLAN | Destination | Allowed |
|-------------|-------------|---------|
| Trusted | Anything | ✅ |
| IoT | Trusted | ❌ |
| IoT | Guest / Internet | ✅ |
| Guest | Trusted | ❌ |
| Guest | IoT | ❌ |
| Guest | Internet | ✅ |

The logic: a compromised IoT device (smart TV, future smart plug, etc.) should never be able to reach my personal PCs. A guest on the visitor WiFi should never be able to reach either my internal network or my smart home devices.

## Wireless setup

Packet Tracer's basic AP can only broadcast one SSID per VLAN, so two APs are used in the sim:

- `AP-Home` → SSID `HomeWiFi` → VLAN 10 (Trusted)
- `AP-Guest` → SSID `GuestWiFi` → VLAN 30 (Guest)

In a real-world build, this would collapse into a single capable AP broadcasting multiple SSIDs mapped to different VLANs.

## Verification

**Trusted PC can reach IoT and Guest VLANs:**

![Allowed pings from Trusted VLAN](homelab/allowed-pings.png)

**Guest phone blocked from reaching Trusted VLAN:**

![Guest blocked from Trusted](homelab/blocked-ping.png)

The "Destination host unreachable" response confirms the ACL on the router's Guest subinterfa
