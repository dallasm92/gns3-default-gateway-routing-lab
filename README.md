# GNS3 Default Gateway and Basic Routing Lab

Last reviewed: May 5, 2026

This repo is a focused follow-up lab built from the routed portion of a larger GNS3 learning session. It isolates one core networking concept:

- devices on different subnets need a default gateway and a router to communicate

Instead of burying that inside a broader setup repo, this project keeps the scope tight and evidence-driven.

Social preview asset:

- [assets/social-preview.png](assets/social-preview.png)

## Start Here

If you only review three things in this repo, use these:

1. [03b-routed-topology-final.png](images/03b-routed-topology-final.png) for the clean two-subnet layout
2. [04-alpine-router-interface-config.png](images/04-alpine-router-interface-config.png) for the router interfaces and forwarding state
3. [07-pc1-arp-gateway-proof.png](images/07-pc1-arp-gateway-proof.png) for the clearest gateway-versus-destination ARP proof

## Objective

Demonstrate basic inter-subnet routing in GNS3 by placing two hosts on different IPv4 networks and forwarding traffic between them with a lightweight Linux router.

Key concepts covered:

- same subnet vs different subnet behavior
- default gateway configuration
- basic router interface addressing
- IPv4 forwarding
- ARP behavior for routed traffic
- using TTL as supporting evidence of a routed hop

## Topology

```text
PC1 ---- Switch1 ---- AlpineRouter ---- Switch2 ---- PC2
```

IP plan:

- PC1: `192.168.10.10/24`
- PC1 gateway: `192.168.10.1`
- AlpineRouter `eth0`: `192.168.10.1/24`
- AlpineRouter `eth1`: `192.168.20.1/24`
- PC2: `192.168.20.10/24`
- PC2 gateway: `192.168.20.1`

## Environment

- GNS3 running on Windows 11 Pro
- Hyper-V-backed GNS3 VM
- VPCS endpoints
- Alpine Linux Docker container used as a simple router

## Hiring Manager Quick View

| Review area | Evidence |
|---|---|
| Network design basics | Two-subnet topology with explicit gateway plan |
| Troubleshooting | Compute-placement error and first-pass failed connectivity captured before final success |
| Linux networking | Router interfaces and IPv4 forwarding configured manually |
| Routing validation | Cross-subnet pings succeed with TTL reduced by one hop |
| Protocol understanding | ARP table shows the default gateway MAC during routed traffic |

## Evidence Set

Screenshots are stored in [`images/`](images/).

1. [01-alpine-router-template-start-command.png](images/01-alpine-router-template-start-command.png) - Alpine router template created in GNS3
2. [02-cross-compute-link-error.png](images/02-cross-compute-link-error.png) - failed attempt to connect nodes across incompatible compute targets
3. [03-initial-routing-failure-pc2.png](images/03-initial-routing-failure-pc2.png) - PC2 cannot reach the gateway or remote subnet before the final working state
4. [03b-routed-topology-final.png](images/03b-routed-topology-final.png) - clean final topology showing one router between two IPv4 subnets
5. [04-alpine-router-interface-config.png](images/04-alpine-router-interface-config.png) - router interfaces and forwarding configured
6. [05-pc1-routed-connectivity.png](images/05-pc1-routed-connectivity.png) - PC1 reaches both its gateway and the remote host
7. [07-pc1-arp-gateway-proof.png](images/07-pc1-arp-gateway-proof.png) - PC1 ARP table shows the default gateway MAC for routed traffic

## Lab Steps

### 1. Build the router template

No packaged router was available in the default node list, so an Alpine Docker container was created as a small router appliance with two network adapters.

Evidence:

- [01-alpine-router-template-start-command.png](images/01-alpine-router-template-start-command.png)

### 2. Correct the compute-placement mistake

The first topology attempt failed because the router and the VPCS nodes were not placed on compatible compute backends.

This is valuable evidence because it shows that platform architecture matters in GNS3, not just IP addressing.

Evidence:

- [02-cross-compute-link-error.png](images/02-cross-compute-link-error.png)

### 3. Observe the failed state first

Before the final configuration was complete, PC2 could not reach its expected gateway or the remote host. That is the right failure to capture because it makes the later success more credible.

Evidence:

- [03-initial-routing-failure-pc2.png](images/03-initial-routing-failure-pc2.png)

### 4. Lock in the final topology

Before validating traffic, the topology was reduced to the exact shape needed for the concept:

```text
PC1 ---- Switch1 ---- AlpineRouter ---- Switch2 ---- PC2
```

That matters because it keeps the story focused on gateway logic, not on extra GNS3 clutter.

Evidence:

- [03b-routed-topology-final.png](images/03b-routed-topology-final.png)

### 5. Configure the Linux router

The router was configured with one interface in each network and IPv4 forwarding enabled.

Commands used:

```sh
ip addr add 192.168.10.1/24 dev eth0
ip addr add 192.168.20.1/24 dev eth1
ip link set eth0 up
ip link set eth1 up
sysctl -w net.ipv4.ip_forward=1
```

Evidence:

- [04-alpine-router-interface-config.png](images/04-alpine-router-interface-config.png)

### 6. Configure endpoint gateways and validate routing

PC1 was configured with:

```text
192.168.10.10/24
gateway 192.168.10.1
```

PC2 was configured with:

```text
192.168.20.10/24
gateway 192.168.20.1
```

Successful validation:

- PC1 could ping `192.168.10.1`
- PC1 could ping `192.168.20.10`

TTL in the successful cross-subnet ping dropped to `63`, which is consistent with one routed hop.

Evidence:

- [05-pc1-routed-connectivity.png](images/05-pc1-routed-connectivity.png)

### 7. Prove gateway behavior with ARP

The ARP tables are one of the most useful parts of this lab.

When PC1 sends traffic to a host in another subnet, it does not ARP for the remote host directly. It ARPs for the MAC address of its default gateway instead.

Evidence:

- [07-pc1-arp-gateway-proof.png](images/07-pc1-arp-gateway-proof.png)

## What This Lab Proves

- A switch alone is not enough for communication between different IPv4 networks.
- Endpoints use their subnet mask to decide whether traffic is local or remote.
- When a destination is remote, the host sends traffic to the default gateway.
- A router with one interface in each subnet and IPv4 forwarding enabled can move traffic between those networks.
- ARP behavior changes depending on whether the destination is local or remote.

## What I Learned

- Routing labs are more credible when the failed state is documented first.
- GNS3 compute placement can create lab problems that look like network problems until the architecture is corrected.
- A small Linux container is enough to demonstrate core routing concepts when a packaged router image is not available.

## Outcome

This repo now stands on its own as a compact networking case study focused on one concept: default gateway and inter-subnet routing behavior.

That makes it useful for interviews where I want to show direct evidence of practical networking knowledge without making the reviewer parse a larger platform-setup narrative first.
