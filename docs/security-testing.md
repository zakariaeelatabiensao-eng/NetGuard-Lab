# Security Testing and Validation

## 1. Objective

The objective of these tests is to validate the nftables firewall policy implemented in NetGuard Lab.

Testing was performed from the LAN1 network toward the Ubuntu Server in LAN2.

**Source network:** `192.168.10.0/24`

**Target server:** `192.168.20.10`

## 2. HTTP Connectivity Test

### Objective

Verify that authorized HTTP traffic can reach the Apache web server.

### Command

```bash
curl -I http://192.168.20.10
```

### Observed Result

```text
HTTP/1.1 200 OK
```

**Status: PASS**

The firewall successfully permits HTTP connections on TCP port 80.

## 3. ICMP Filtering Test

### Objective

Verify that ICMP Echo Requests from LAN1 to the Ubuntu Server are blocked.

### Command

```bash
ping 192.168.20.10
```

### Observed Result

ICMP Echo Requests were blocked by the firewall.

**Status: PASS**

## 4. SSH Filtering Test

### Objective

Verify that TCP port 22 cannot be accessed from LAN1 through the firewall.

### Target

`192.168.20.10:22`

### Observed Result

SSH connection attempts were blocked by the firewall.

**Status: PASS**

## 5. Firewall Logging

### Objective

Verify that dropped packets generate log entries.

### Command

```bash
sudo journalctl -k | grep NETGUARD_DROP
```

### Observed Result

Firewall logs contained entries using the `NETGUARD_DROP` prefix.

**Status: PASS**

## 6. Results Summary

| Test | Expected Behavior | Observed Behavior | Status |
|---|---|---|---|
| HTTP TCP/80 | Allow | HTTP 200 OK | PASS |
| ICMP Echo | Block | Blocked | PASS |
| SSH TCP/22 | Block | Blocked | PASS |
| Firewall Logging | Record drops | Log entries observed | PASS |

## 7. Conclusion

The tests confirm that the implemented firewall policy enforces the intended access restrictions between the employee and server networks.

HTTP access is permitted, while unauthorized ICMP and SSH traffic is blocked.

The logging mechanism provides visibility into denied forwarded packets.

These results validate the core network security objectives of NetGuard Lab V1.
