# NetGuard Lab — Installation Guide

## 1. Prerequisites

NetGuard Lab is a virtual network security environment designed for learning network segmentation, Linux routing, and firewall administration.

### Required Software

| Software | Purpose |
|---|---|
| VMware Workstation | Virtualization platform |
| GNS3 | Network topology design |
| Debian Linux | Router and firewall |
| Ubuntu Server | Apache web server |
| VPCS | Virtual client simulation |
| nftables | Firewall configuration |

A computer with at least 16 GB RAM is recommended for this lab, depending on the virtual machine resource allocation.

## 2. Network Design

Create two isolated virtual networks in VMware:

**VMnet2 — Employees LAN**

- Subnet: `192.168.10.0/24`
- Gateway: `192.168.10.1`

**VMnet1 — Servers LAN**

- Subnet: `192.168.20.0/24`
- Gateway: `192.168.20.1`

Configure the virtual networks without conflicting DHCP services or host routing.

## 3. Debian Router Setup

Create a Debian virtual machine with two network adapters:

| Interface | VMware Network | IP Address |
|---|---|---|
| ens33 | VMnet2 | 192.168.10.1/24 |
| ens37 | VMnet1 | 192.168.20.1/24 |

The interface names may differ depending on the VM configuration.

Verify the interfaces:

```bash
ip -br addr
```

Verify the routing table:

```bash
ip route
```

Enable IPv4 forwarding:

```bash
sudo sysctl -w net.ipv4.ip_forward=1
```

For persistent forwarding, create a configuration file under `/etc/sysctl.d/` containing:

```ini
net.ipv4.ip_forward=1
```

Apply the configuration:

```bash
sudo sysctl --system
```

## 4. Ubuntu Server Setup

Create an Ubuntu Server virtual machine.

Configure its server-network adapter with:

| Setting | Value |
|---|---|
| Network | VMnet1 |
| IP Address | 192.168.20.10/24 |
| Gateway | 192.168.20.1 |

A separate NAT adapter may be used for package installation and management.

Install Apache:

```bash
sudo apt update
sudo apt install apache2 -y
```

Verify the service:

```bash
sudo systemctl status apache2
```

Check that Apache listens on TCP port 80:

```bash
sudo ss -lnt
```

## 5. GNS3 Topology

Create the following devices in GNS3:

- Two Ethernet switches.
- Three VPCS clients.
- Cloud nodes connected to VMware VMnet1 and VMnet2.

Connect:

**LAN1:** PC1 and PC2 to Switch1, then bridge Switch1 to VMnet2.

**LAN2:** PC3 to Switch2, then bridge Switch2 to VMnet1.

The Debian router and Ubuntu Server communicate with the GNS3 topology through their VMware virtual network adapters.

## 6. Client Addressing

Configure the VPCS clients.

### PC1

```text
ip 192.168.10.10/24 192.168.10.1
```

### PC2

```text
ip 192.168.10.11/24 192.168.10.1
```

### PC3

```text
ip 192.168.20.20/24 192.168.20.1
```

Verify the assigned addresses using:

```text
show ip
```

## 7. Firewall Installation

On the Debian router, install nftables:

```bash
sudo apt update
sudo apt install nftables -y
```

The firewall policy is documented in:

`configs/nftables.conf`

Before applying the configuration, review the existing firewall rules and ensure that the management connection will not be disrupted.

Apply the configuration in the controlled lab environment:

```bash
sudo nft -f nftables.conf
```

This command assumes the configuration file has been copied to the current working directory.

Verify the active rules:

```bash
sudo nft list ruleset
```

The configuration uses a default-deny forwarding policy while allowing HTTP connections from LAN1 to the Ubuntu Server.

## 8. Connectivity Testing

### HTTP Test

From a LAN1 workstation:

```bash
curl -I http://192.168.20.10
```

Expected result:

```text
HTTP/1.1 200 OK
```

### ICMP Test

```bash
ping 192.168.20.10
```

Expected result: ICMP Echo traffic is blocked.

### SSH Test

Attempt to establish a TCP connection to port 22 on the Ubuntu Server.

Expected result: The firewall blocks the forwarded connection.

### Firewall Logs

On Debian:

```bash
sudo journalctl -k | grep NETGUARD_DROP
```

The command displays matching kernel log entries, if available.

## 9. Troubleshooting

If HTTP access fails, verify:

- Network interface addressing.
- Default gateways.
- IPv4 forwarding.
- VMware network adapter assignments.
- Apache service status.
- nftables forwarding rules.
- Ubuntu Server routing configuration.

If packet filtering does not behave as expected, inspect the active firewall ruleset and routing tables before making changes.

## 10. Conclusion

NetGuard Lab demonstrates how to build a segmented virtual network, implement Linux routing, and enforce access control using a stateful firewall.

The environment provides a foundation for further experimentation with intrusion detection, network monitoring, and security automation.
