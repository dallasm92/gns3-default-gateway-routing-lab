# Lab Summary

## Scope

This repo isolates one networking concept from a broader GNS3 learning session:

- routed communication between two different IPv4 subnets through a default gateway

## Topology

```text
PC1 ---- Switch1 ---- AlpineRouter ---- Switch2 ---- PC2
```

## IP Plan

- PC1: `192.168.10.10/24`, gateway `192.168.10.1`
- Router `eth0`: `192.168.10.1/24`
- Router `eth1`: `192.168.20.1/24`
- PC2: `192.168.20.10/24`, gateway `192.168.20.1`

## Key Results

- Alpine router template created in GNS3
- Router interfaces configured successfully
- IPv4 forwarding enabled
- PC1 successfully reached:
  - `192.168.10.1`
  - `192.168.20.10`
- PC2 successfully reached:
  - `192.168.20.1`
  - `192.168.10.10`
- ARP tables showed the gateway MAC on each subnet during routed communication

## Best Evidence

- [04-alpine-router-interface-config.png](images/04-alpine-router-interface-config.png)
- [05-pc1-routed-connectivity.png](images/05-pc1-routed-connectivity.png)
- [06-pc2-routed-connectivity.png](images/06-pc2-routed-connectivity.png)
- [07-pc1-arp-gateway-proof.png](images/07-pc1-arp-gateway-proof.png)
- [08-pc2-arp-gateway-proof.png](images/08-pc2-arp-gateway-proof.png)
