# Lab 06 – Linux Networking Basics

## Objective

Learn how to inspect Linux network configuration, test connectivity, verify DNS, identify listening ports, and inspect active network connections.

## Key Concepts

Linux networking includes:

- Network interfaces
- IP addresses
- Routes
- Gateways
- DNS
- Ports
- Listening services
- Active connections

An interface is a network adapter, such as `enp0s3`.

An IP address identifies a system on a network.

A route tells Linux where to send traffic.

DNS translates domain names into IP addresses.

A listening port means a service is waiting for connections.

An established connection means two systems are actively communicating.

## Commands Practiced

```bash
ip addr
ip addr show enp0s3
ip route
ping -c 4 10.0.2.2
ping -c 4 8.8.8.8
ping -c 4 google.com
resolvectl status
ss -tuln
sudo ss -tulnp
sudo ss -tulnp | grep -E ':22|:53'
ss -tn
```

## IP Address and Interface

I inspected my main network interface using:

```bash
ip addr show enp0s3
```

The Ubuntu VM had the IP address:

```text
10.0.2.15/24
```

This means the VM’s IPv4 address was `10.0.2.15`, and `/24` represents the subnet size.

## Routing

I inspected the routing table using:

```bash
ip route
```

The default route showed that traffic leaving the VM was sent through the VirtualBox NAT gateway.

In this lab:

```text
Ubuntu VM: 10.0.2.15
VirtualBox NAT Gateway: 10.0.2.2
```

## Connectivity Testing

I tested connectivity to the VirtualBox NAT gateway:

```bash
ping -c 4 10.0.2.2
```

This worked, confirming the VM could reach its gateway.

I tested internet connectivity by IP:

```bash
ping -c 4 8.8.8.8
```

This worked, confirming the VM had internet connectivity by IP address.

I tested DNS resolution:

```bash
ping -c 4 google.com
```

This worked, confirming DNS was functioning.

## DNS

I inspected DNS configuration using:

```bash
resolvectl status
```

DNS is important because it translates domain names like `google.com` into IP addresses.

In cybersecurity, DNS is valuable because DNS activity can help identify suspicious domains, malware communication, phishing activity, and command-and-control traffic.

## Listening Ports

I inspected listening TCP and UDP ports using:

```bash
ss -tuln
```

I saw ports including:

```text
22
53
```

Port `22` is used for SSH.

Port `53` is used for DNS.

## Process Ownership of Ports

I used:

```bash
sudo ss -tulnp | grep -E ':22|:53'
```

to identify which processes owned ports `22` and `53`.

Findings:

- Port `22` was associated with `sshd` and `systemd`.
- Port `53` was associated with `systemd-resolve`.

The `sshd` process handles SSH connections.

The `systemd-resolve` process handles local DNS resolution.

## Local vs Network Listening

I learned that the address a service binds to matters.

Examples:

```text
127.0.0.1
```

means local-only.

```text
127.0.0.53
```

is a local DNS resolver address.

```text
0.0.0.0
```

means listening on all IPv4 interfaces.

This is important because a service listening on `0.0.0.0` may be reachable from the network, while a service listening on `127.0.0.1` is only reachable locally.

## Active SSH Connection

I inspected active TCP connections using:

```bash
ss -tn
```

I identified my active SSH session involving:

```text
10.0.2.15:22
```

This showed that the Ubuntu VM was communicating over SSH on port `22`.

## Security Lessons Learned

- Always identify the system’s IP address.
- Always check the default route and gateway.
- Test IP connectivity separately from DNS.
- `ping 8.8.8.8` tests IP connectivity.
- `ping google.com` tests both IP connectivity and DNS.
- Listening ports should be tied to expected services.
- Unexpected listening ports may indicate misconfiguration or compromise.
- Process ownership matters when investigating open ports.
- DNS activity can be useful in security investigations.
- Active connections can show who a system is communicating with.

## Interview Summary

In this lab, I inspected Linux network configuration using `ip`, `ping`, `resolvectl`, and `ss`. I confirmed my VM IP address, default route, DNS functionality, listening ports, process ownership of ports, and my active SSH connection. I identified SSH on port `22` and local DNS resolution on port `53`.
