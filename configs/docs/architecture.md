# Network Architecture

## 1. Overview

NetGuard Lab is a virtual network security environment built with GNS3 and VMware Workstation.

The architecture consists of two IPv4 subnets connected by a Debian Linux router/firewall.

The goal is to demonstrate network segmentation, routing, and controlled access to network services.

## 2. Network Segments

### LAN1 — Employees

**Subnet:** `192.168.10.0/24`

| Device | IP Address | Role |
|---|---|---|
| PC1 | 192.168.10.10 | Employee workstation |
| PC2 | 192.168.10.11 | Employee workstation |
| Windows Host | 192.168.10.254 | Testing workstation |
| Debian Router (ens33) | 192.168.10.1 | Default gateway |

LAN1 is connected through the GNS3 network topology and VMware VMnet2.

### LAN2 — Servers

**Subnet:** `192.168.20.0/24`

| Device | IP Address | Role |
|---|---|---|
| Ubuntu Server | 192.168.20.10 | Apache HTTP server |
| PC3 | 192.168.20.20 | Virtual workstation |
| Debian Router (ens37) | 192.168.20.1 | Default gateway |

LAN2 uses VMware VMnet1.

The Ubuntu Server is connected to VMnet1 through its virtual network adapter. A separate NAT-connected adapter provides management connectivity.

## 3. Routing

The Debian router connects both networks through two interfaces:

- `ens33` → `192.168.10.1/24`
- `ens37` → `192.168.20.1/24`

IPv4 forwarding is enabled to allow traffic to cross between the subnets.

## 4. Firewall

The router uses nftables to enforce a default-deny forwarding policy.

The policy allows new HTTP connections from LAN1 to the Ubuntu Server and permits established or related return traffic.

Other forwarded traffic is dropped.

Dropped packets are logged using the `NETGUARD_DROP` prefix, subject to rate limiting.

## 5. Security Design

The architecture demonstrates:

- Separation of employee and server networks.
- Centralized inter-network routing.
- Stateful firewall filtering.
- Controlled access to the Apache HTTP service.
- Logging of denied traffic.

## 6. Limitations

The current firewall policy applies to forwarded traffic only.

It does not independently secure the Debian router's local INPUT and OUTPUT traffic.

The environment is intended for learning and controlled network security testing.
