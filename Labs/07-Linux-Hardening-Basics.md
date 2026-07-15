# Lab 07 – Linux Hardening Basics

## Objective

Learn basic Linux hardening by enabling a firewall, allowing SSH safely, checking listening ports, and reviewing SSH server security settings.

## Key Concepts

Hardening means reducing unnecessary risk by limiting access, enabling protections, and checking system exposure.

A firewall controls allowed and denied network traffic.

UFW stands for **Uncomplicated Firewall**.

SSH must be allowed before enabling the firewall to avoid locking out remote access.

`sshd -T` shows the effective SSH server configuration.

## Commands Practiced

```bash
sudo ufw status verbose
sudo ufw allow OpenSSH
sudo ufw enable
sudo ufw status numbered
sudo ss -tulnp
sudo ss -tulnp | grep -E ':22|:53'
ls -l /etc/ssh/
sudo grep -Ei '^(#)?(Port|PermitRootLogin|PasswordAuthentication|PubkeyAuthentication|KbdInteractiveAuthentication|UsePAM)' /etc/ssh/sshd_config
sudo sshd -T | grep -E 'port|permitrootlogin|passwordauthentication|pubkeyauthentication|kbdinteractiveauthentication|usepam'
```

## Firewall Setup

I checked the firewall status using:

```bash
sudo ufw status verbose
```

UFW was initially inactive.

Before enabling the firewall, I allowed OpenSSH:

```bash
sudo ufw allow OpenSSH
```

This was important because I was connected remotely through SSH. If SSH was not allowed before enabling the firewall, I could have locked myself out of the server.

Then I enabled UFW:

```bash
sudo ufw enable
```

After enabling it, I confirmed the firewall was active:

```bash
sudo ufw status verbose
```

The firewall showed rules allowing OpenSSH:

```text
OpenSSH      ALLOW IN    Anywhere
OpenSSH (v6) ALLOW IN    Anywhere (v6)
```

## SSH Safety Test

After enabling UFW, I opened a second SSH session from Windows Terminal:

```bash
ssh joey@127.0.0.1 -p 2222
```

The second SSH session worked, confirming that the firewall rule allowed SSH correctly and I was not locked out.

## Open Port Check

I reviewed listening ports using:

```bash
sudo ss -tulnp
```

I also filtered for SSH and DNS:

```bash
sudo ss -tulnp | grep -E ':22|:53'
```

Findings:

- Port `22` was used for SSH.
- Port `53` was used locally for DNS resolution.

## SSH Configuration Review

I inspected the SSH configuration file using:

```bash
sudo grep -Ei '^(#)?(Port|PermitRootLogin|PasswordAuthentication|PubkeyAuthentication|KbdInteractiveAuthentication|UsePAM)' /etc/ssh/sshd_config
```

Some settings appeared commented out with `#`, meaning they were not directly set in the file and may be using defaults.

To view the effective SSH configuration, I used:

```bash
sudo sshd -T | grep -E 'port|permitrootlogin|passwordauthentication|pubkeyauthentication|kbdinteractiveauthentication|usepam'
```

## SSH Findings

The effective SSH configuration showed:

```text
port 22
usepam yes
permitrootlogin prohibit-password
pubkeyauthentication yes
passwordauthentication yes
kbdinteractiveauthentication no
```

## What These Settings Mean

| Setting | Meaning |
|---|---|
| `port 22` | SSH is listening on the default SSH port |
| `permitrootlogin prohibit-password` | Root password login is restricted |
| `pubkeyauthentication yes` | SSH key authentication is supported |
| `passwordauthentication yes` | Password login is still allowed |
| `kbdinteractiveauthentication no` | Keyboard-interactive authentication is disabled |
| `usepam yes` | SSH uses Linux PAM authentication rules |

## Security Lessons Learned

- Always allow SSH before enabling a firewall on a remote server.
- Confirm firewall rules after enabling UFW.
- Test a second SSH session before closing the first terminal.
- Use `ss` to verify listening ports.
- Use `sshd -T` to view the effective SSH configuration.
- Do not disable password authentication until SSH keys are configured and tested.
- Root password login should be restricted.
- Listening ports should be reviewed after firewall changes.

## Interview Summary

In this lab, I hardened my Ubuntu Server by enabling UFW, allowing OpenSSH safely, confirming SSH access still worked, checking listening ports, and reviewing SSH security settings. I verified that the firewall was active, SSH was allowed, root password login was restricted, public key authentication was enabled, and password authentication remained enabled until SSH keys are configured.
