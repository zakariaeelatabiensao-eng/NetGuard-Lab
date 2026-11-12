# Evidence screenshots

These ten PNG files were captured during the NetGuard Lab v1 walkthrough and document the actual virtual network configuration and test results.

| File | Evidence |
|---|---|
| `01-network-topology.png` | Annotated GNS3 topology |
| `02-debian-interfaces.png` | Debian IPv4 interfaces |
| `03-debian-routing.png` | Debian directly connected routes |
| `04-ip-forwarding.png` | IPv4 forwarding enabled |
| `05-ubuntu-interfaces.png` | Ubuntu NIC addresses |
| `06-firewall-rules.png` | nftables forwarding rules |
| `07-http-allowed.png` | HTTP 200 from Windows |
| `08-icmp-blocked.png` | Ping timeout |
| `09-ssh-blocked.png` | TCP/22 connection unsuccessful |
| `10-firewall-logs.png` | Kernel logs for denied packets |

Note: `Test-NetConnection` failing alone does not prove the service is running; the firewall logs include dropped TCP/22 packets. Screenshots may include local usernames and private IPs; review before public posting.
