# Lab 05 – Linux Processes and Services

## Objective

Learn how to inspect Linux processes, understand process IDs, check service status, view service logs, and identify whether the system is healthy.

## Key Concepts

A **process** is a running program.

A **service** is a managed background process controlled by `systemd`.

Every process has a **PID**, which stands for Process ID.

`systemctl` is used to inspect and manage services.

`journalctl` is used to view system logs.

## Commands Practiced

```bash
echo $$
ps
ps aux
ps aux | grep ssh
pgrep -a ssh
systemctl status ssh
systemctl is-active ssh
systemctl is-enabled ssh
systemctl status ssh.socket
journalctl -u ssh -n 20 --no-pager
journalctl -u ssh.socket -n 20 --no-pager
top
sleep 300 &
pgrep -a sleep
pkill sleep
systemctl list-units --type=service --state=running
systemctl list-units --type=service --state=failed
systemctl status getty@tty1.service
journalctl -u getty@tty1.service -n 20 --no-pager
```

## Process Inspection

I used:

```bash
echo $$
```

to identify the PID of my current shell.

I used:

```bash
ps
```

to view processes attached to my current terminal session.

I used:

```bash
ps aux
```

to view all running processes on the system.

I searched for SSH-related processes with:

```bash
ps aux | grep ssh
pgrep -a ssh
```

Example SSH process output:

```text
sshd: /usr/sbin/sshd -D [listener]
sshd-session: joey [priv]
sshd-session: joey@pts/0
```

This showed:

- The main SSH daemon/listener
- The privileged SSH session process
- My active SSH pseudo-terminal session

## SSH Service Inspection

I checked SSH service status with:

```bash
systemctl status ssh
```

The service was:

```text
active (running)
```

I also checked:

```bash
systemctl is-active ssh
systemctl is-enabled ssh
systemctl status ssh.socket
```

Findings:

- `ssh.service` was active.
- `ssh.service` was disabled for direct startup.
- `ssh.socket` was active and listening.

This means SSH was using **systemd socket activation**.

## Socket Activation

In this setup, `ssh.socket` listens for incoming SSH connections and triggers the SSH service when needed.

Flow:

```text
Incoming SSH connection
        ↓
ssh.socket listens on port 22
        ↓
ssh.service handles the session
        ↓
User connects through SSH
```

## Service Logs

I reviewed SSH service logs using:

```bash
journalctl -u ssh -n 20 --no-pager
```

I also reviewed SSH socket logs using:

```bash
journalctl -u ssh.socket -n 20 --no-pager
```

These commands help investigate service startup, shutdown, errors, and connection-related activity.

## Live Process Monitoring

I used:

```bash
top
```

to inspect live system activity.

I reviewed:

- Load average
- Number of tasks
- CPU usage
- Memory usage
- Swap usage
- Running processes

The system showed low CPU usage, low memory usage, and no swap usage, indicating a healthy system.

## Background Process Practice

I created a harmless background process:

```bash
sleep 300 &
```

Then identified it:

```bash
pgrep -a sleep
```

Then stopped it:

```bash
pkill sleep
```

After running:

```bash
pgrep -a sleep
```

there was no output, confirming the process had stopped.

## Running and Failed Services

I listed running services with:

```bash
systemctl list-units --type=service --state=running
```

Examples of services observed:

- `getty`
- `ModemManager`
- `multipathd`

I checked for failed services with:

```bash
systemctl list-units --type=service --state=failed
```

The system showed:

```text
0 failed services
```

This indicated no managed services were currently failing.

## Getty Service

I inspected:

```bash
systemctl status getty@tty1.service
```

This service was loaded and active.

`getty@tty1.service` manages the local console login prompt, such as the login screen shown in the VirtualBox console.

## Security Lessons Learned

- Processes show what is currently running on a Linux system.
- Services are managed background processes controlled by `systemd`.
- `pgrep` can identify processes by name.
- `top` can help detect unusual CPU or memory usage.
- `systemctl` can confirm whether services are active, inactive, enabled, disabled, or failed.
- `journalctl` can investigate logs for specific services.
- Failed services may indicate crashes, misconfiguration, or suspicious activity.
- Unexpected processes can be a sign of compromise or misconfiguration.

## Interview Summary

In this lab, I inspected Linux processes and services using `ps`, `pgrep`, `top`, `systemctl`, and `journalctl`. I identified SSH-related processes, confirmed the SSH service and socket status, reviewed service logs, created and terminated a test background process, checked live system resource usage, and confirmed that there were no failed services.
