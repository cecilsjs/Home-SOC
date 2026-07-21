# Lab 12 – UFW Firewall Logging

## Objective

Enable and review UFW firewall logging on Ubuntu, generate a safe blocked traffic event, interpret firewall log fields, and remove the temporary test rule.

## Key Concepts

UFW stands for **Uncomplicated Firewall**.

UFW is Ubuntu's simple firewall management tool.

Firewall logging records allowed or blocked network traffic.

ICMP stands for **Internet Control Message Protocol**.

`ping` uses ICMP echo requests.

Firewall logs can show source IP, destination IP, protocol, packet type, and other packet details.

## Commands Practiced

```bash
sudo ufw status verbose
sudo ufw status numbered
ls -l /var/log/ufw.log
sudo tail -n 20 /var/log/ufw.log
sudo journalctl -k --no-pager | grep UFW | tail -n 20
sudo ufw deny out to 1.1.1.1 comment 'Lab 12 test block'
ping -c 4 1.1.1.1
sudo ufw delete deny out to 1.1.1.1
sudo ufw deny out log to 1.1.1.1 comment 'Lab 12 logged test block'
sudo journalctl -k --no-pager | grep -i "ufw" | tail -n 20
sudo dmesg | grep -i "ufw" | tail -n 20
sudo ufw delete deny out log to 1.1.1.1
```

## Firewall Status

UFW was active.

Logging was on low.

Default incoming traffic was denied.

Default outgoing traffic was allowed.

Routed traffic was disabled.

OpenSSH was allowed so SSH access would continue working.

## Test Performed

I created a temporary outbound deny rule to block traffic to:

```text
1.1.1.1
```

I tested the block using:

```bash
ping -c 4 1.1.1.1
```

The ping test showed:

```text
4 packets transmitted, 0 received
```

This confirmed that the traffic was blocked.

## Firewall Log Review

After enabling a logged deny rule, I checked firewall logs using:

```bash
sudo journalctl -k --no-pager | grep -i "ufw" | tail -n 20
```

and:

```bash
sudo dmesg | grep -i "ufw" | tail -n 20
```

I found a firewall log entry showing traffic from my Ubuntu VM to `1.1.1.1`.

Example fields observed:

```text
SRC=10.0.2.15
DST=1.1.1.1
PROTO=ICMP
TYPE=8
TTL=64
LEN=84
```

## Field Meanings

| Field | Meaning |
|---|---|
| `SRC=10.0.2.15` | Source IP address, my Ubuntu VM |
| `DST=1.1.1.1` | Destination IP address |
| `PROTO=ICMP` | Protocol used by ping |
| `TYPE=8` | ICMP echo request |
| `TTL=64` | Time To Live |
| `LEN=84` | Packet length |

## Rule Cleanup

After reviewing the firewall log, I removed the temporary rule:

```bash
sudo ufw delete deny out log to 1.1.1.1
```

Then I tested connectivity again:

```bash
ping -c 4 1.1.1.1
```

The result was:

```text
4 packets transmitted, 4 received
```

This confirmed that connectivity was restored.

## Security Lessons Learned

- Firewall logs help identify blocked network traffic.
- A blocked ping appears as ICMP traffic in firewall logs.
- `SRC` and `DST` fields show where traffic came from and where it was going.
- `PROTO` shows which protocol was used.
- Temporary test firewall rules should be removed after testing.
- After removing a firewall rule, connectivity should be tested again.
- Firewall logs are useful during SOC investigations because they show allowed or blocked network behavior.

## Interview Summary

In this lab, I enabled and reviewed UFW firewall logging on Ubuntu. I created a temporary outbound deny rule to block ICMP traffic to `1.1.1.1`, confirmed the ping failed, located the firewall log entry, interpreted fields such as `SRC`, `DST`, `PROTO`, `TYPE`, `TTL`, and `LEN`, removed the test rule, and confirmed connectivity was restored.
