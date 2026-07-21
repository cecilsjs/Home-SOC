# Home SOC Cybersecurity Lab

## Overview

This project is a hands-on Home SOC cybersecurity lab built to develop practical skills in Linux administration, system hardening, log analysis, detection scripting, evidence collection, and incident reporting.

SOC stands for **Security Operations Center**.

The purpose of this lab is to demonstrate job-ready cybersecurity skills through documented technical labs, security monitoring exercises, and investigation workflows.

This lab was built using:

- Ubuntu Server
- VirtualBox
- SSH
- UFW firewall
- Linux logs
- auditd
- Bash scripting
- File Integrity Monitoring
- Wazuh agent inspection
- GitHub documentation

---

## Project Goals

The goals of this project are to:

- Build a cybersecurity lab from scratch
- Learn Linux fundamentals
- Practice system hardening
- Monitor authentication activity
- Investigate logs using command-line tools
- Write basic detection scripts
- Collect security evidence
- Create SOC-style incident documentation
- Build a public cybersecurity portfolio

---

## Lab Environment

| Component | Description |
|---|---|
| Host Machine | Windows laptop |
| Virtualization | VirtualBox |
| Main VM | Ubuntu Server |
| Remote Access | SSH key authentication |
| Firewall | UFW |
| Logs | journalctl, /var/log |
| Monitoring | Bash scripts, auditd, Wazuh agent inspection |
| Documentation | GitHub |

---

## Skills Demonstrated

This project demonstrates practical skills in:

- Linux command-line navigation
- File and directory permissions
- User and group analysis
- Service and process inspection
- Network troubleshooting
- SSH configuration and hardening
- Firewall configuration and logging
- Authentication monitoring
- Log filtering with grep
- Scheduled monitoring with cron
- Bash scripting
- File Integrity Monitoring
- Linux auditing with auditd
- Security alert generation
- Evidence collection
- Incident report writing

---

## Tools and Commands Used

| Tool / Command | Purpose |
|---|---|
| `ssh` | Secure remote access |
| `systemctl` | Service management |
| `journalctl` | Log investigation |
| `grep` | Log filtering |
| `ss` | Port and connection inspection |
| `ip` | Network configuration review |
| `ping` | Connectivity testing |
| `ufw` | Firewall management |
| `chmod` | Permission changes |
| `auditd` | Linux auditing |
| `auditctl` | Audit rule management |
| `ausearch` | Audit log searching |
| `aureport` | Audit reporting |
| `sha256sum` | File integrity hashing |
| `cron` | Scheduled task automation |
| `bash` | Scripting |

---

## Completed Labs

| Lab | Title | Focus |
|---:|---|---|
| 01 | VirtualBox and CyberLab Setup | Lab environment setup |
| 02 | Ubuntu Server Installation and Baseline | Ubuntu installation and baseline checks |
| 03 | Linux Filesystem and Logs | Filesystem navigation and log review |
| 04 | Linux Users, Groups, and Permissions | Account and permission analysis |
| 05 | Linux Processes and Services | Process and service inspection |
| 06 | Linux Networking Basics | IP, routes, DNS, ports, and connections |
| 07 | Linux Hardening Basics | UFW firewall and SSH review |
| 08 | SSH Key Authentication | Public/private key authentication |
| 09 | SSH Hardening: Disable Password Login | Safer SSH configuration |
| 10 | Linux Authentication Monitoring | SSH and sudo log monitoring |
| 11 | Linux Log Investigation | journalctl, grep, time filters, event counting |
| 12 | UFW Firewall Logging | Firewall logging and blocked traffic analysis |
| 13 | Failed Login Detection Script | Bash detection script |
| 14 | Cron Jobs and Scheduled Monitoring | Scheduled security monitoring |
| 15 | Linux Audit Basics with auditd | File auditing and audit log investigation |
| 16 | File Integrity Monitoring | SHA-256 baseline and change detection |
| 17 | Wazuh Agent Local Inspection | Agent installation and troubleshooting |
| 18 | Security Alert Generation and Evidence Collection | Alert generation and evidence preservation |
| 19 | SSH Attack Simulation and Incident Report | Controlled attack simulation and SOC report |
| 20 | Portfolio Polish and Final README | Project organization and final presentation |

---

## Featured Security Workflows

### SSH Hardening

I configured SSH key authentication and disabled password-based SSH login after verifying key login worked safely.

Key results:

- SSH key login worked successfully
- Password-only login failed
- Root password login was restricted
- SSH logs confirmed public key authentication

---

### Authentication Monitoring

I monitored SSH and sudo activity using `journalctl` and `grep`.

Events detected:

- Successful SSH public key logins
- Invalid username attempts
- Connection reset events
- Sudo/admin activity

---

### Detection Scripting

I created a Bash authentication monitoring script that counts important SSH and sudo events.

The script reports:

- Successful SSH public key logins
- Invalid username attempts
- Connection reset events
- Sudo activity events
- Alert messages for suspicious activity

---

### Scheduled Monitoring

I scheduled the authentication monitoring script with cron so it runs automatically every 5 minutes and saves output to a report file.

Cron job used:

```cron
*/5 * * * * /home/joey/soc-scripts/auth-monitor.sh > /home/joey/soc-reports/auth-report-cron.txt 2>&1
```

---

### File Integrity Monitoring

I created a File Integrity Monitoring lab using SHA-256 hashes.

FIM stands for **File Integrity Monitoring**.

The lab detected when a monitored configuration file changed from:

```text
admin_login=false
```

to:

```text
admin_login=true
```

The script generated:

```text
[ALERT] File integrity change detected.
```

---

### Linux Auditing

I installed and used `auditd`, the Linux Audit Daemon, to monitor file activity.

I created an audit rule to monitor:

```text
/home/joey/audit-lab/important.conf
```

The rule tracked:

- Read events
- Write events
- Execute events
- Attribute changes

---

### Firewall Logging

I enabled UFW firewall logging and created a controlled blocked traffic event.

UFW stands for **Uncomplicated Firewall**.

I blocked outbound ICMP traffic to:

```text
1.1.1.1
```

ICMP stands for **Internet Control Message Protocol**.

The firewall logs showed fields such as:

- Source IP
- Destination IP
- Protocol
- ICMP type
- Time To Live
- Packet length

---

### Incident Report

I simulated an SSH invalid username attack using usernames such as:

```text
admin
test
backup
```

I collected evidence, counted events, identified indicators of compromise, and wrote a SOC-style incident report.

IoC stands for **Indicator of Compromise**.

Example indicators:

| Indicator | Value |
|---|---|
| Source IP | `10.0.2.2` |
| Target Service | SSH |
| Invalid Usernames | `admin`, `test`, `backup` |
| Activity | Invalid user login attempts |
| Related Event | Connection reset during pre-authentication |

---

## Key Security Lessons

- SSH should be hardened with key-based authentication.
- Password authentication should only be disabled after SSH keys are tested.
- Logs are critical for detecting suspicious activity.
- Invalid usernames may indicate scanning or brute-force attempts.
- Sudo activity should be reviewed because it shows privileged commands.
- Firewall logs help identify blocked network behavior.
- File integrity monitoring can detect unauthorized file changes.
- auditd can monitor sensitive files and system activity.
- Detection scripts help turn raw logs into useful alerts.
- Evidence should be saved before writing incident reports.
- Incident reports should include findings, evidence, impact, response actions, and recommendations.

---

## Interview Summary

I built a hands-on Home SOC cybersecurity lab using Ubuntu Server, VirtualBox, SSH, UFW, auditd, Bash scripting, cron, and GitHub. I hardened SSH, enabled firewall logging, monitored authentication activity, wrote detection scripts, scheduled automated reports, created file integrity monitoring checks, inspected Wazuh agent behavior, generated controlled security alerts, collected evidence, and wrote a SOC-style incident report.

This project demonstrates practical entry-level cybersecurity skills in Linux administration, security monitoring, log analysis, detection engineering, and incident response documentation.

---

## Next Steps

Future improvements may include:

- Add screenshots to each lab
- Add a network diagram
- Add Splunk or Wazuh manager when hardware allows
- Add Windows Event Viewer labs later
- Add more detection scripts
- Add MITRE ATT&CK mapping
- Create a final portfolio summary for LinkedIn
