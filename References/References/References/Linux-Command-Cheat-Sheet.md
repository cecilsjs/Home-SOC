# Linux Command Cheat Sheet

Quick reference for my Home SOC cybersecurity labs.

---

## 1. System Identity

| Goal | Command |
|---|---|
| Current user | `whoami` |
| Hostname/server name | `hostname` |
| UID, GID, groups | `id` |
| User's groups | `groups` |
| Current directory | `pwd` |

---

## 2. Filesystem Navigation

| Goal | Command |
|---|---|
| List files | `ls` |
| List all files with details | `ls -la` |
| Go to root directory | `cd /` |
| Go home | `cd ~` or `cd` |
| Go up one directory | `cd ..` |
| Read file | `cat filename` |
| View long file | `less filename` |
| Last 20 lines | `tail -n 20 filename` |
| First 20 lines | `head -n 20 filename` |

Important directories: `/` = root, `/home` = user folders, `/etc` = configs, `/var/log` = logs, `/usr` = programs/libraries, `/root` = root user home, `/tmp` = temporary files, `/dev` = devices, `/proc` = live system/process info.

---

## 3. Searching Text

| Goal | Command |
|---|---|
| Search file | `grep "word" filename` |
| Search without case sensitivity | `grep -i "word" filename` |
| Search command output | `command | grep "word"` |
| Search SSH failed logins | `sudo journalctl -u ssh --no-pager \| grep "Failed password"` |
| Search SSH accepted logins | `sudo journalctl -u ssh --no-pager \| grep -i "accepted password"` |

Note: `grep` is case-sensitive by default. `accepted password` does not match `Accepted password` unless using `grep -i`.

---

## 4. Logs

| Goal | Command |
|---|---|
| Last auth logs | `sudo tail -n 20 /var/log/auth.log` |
| Last system logs | `sudo journalctl -n 50 --no-pager` |
| SSH logs | `sudo journalctl -u ssh -n 50 --no-pager` |
| SSH socket logs | `sudo journalctl -u ssh.socket -n 50 --no-pager` |
| Getty/login prompt logs | `sudo journalctl -u getty@tty1.service -n 20 --no-pager` |

Log reading pattern: timestamp, hostname, service/process, user, source IP, port, event/action.

---

## 5. Users, Groups, and Sudo

| Goal | Command |
|---|---|
| User account info | `getent passwd joey` |
| Sudo group info | `getent group sudo` |
| Compare passwd/shadow permissions | `ls -l /etc/passwd /etc/shadow` |
| Check sudo privileges | `sudo -l` |

Key files: `/etc/passwd` = user metadata, `/etc/shadow` = protected password hashes, `/etc/group` = group info.

`(ALL : ALL) ALL` means the user can run any command as any user/group with `sudo`.

---

## 6. Permissions

Permission letters: `r` = read, `w` = write, `x` = execute/enter directory.  
Permission numbers: `4` = read, `2` = write, `1` = execute.

| Command | Meaning |
|---|---|
| `chmod 600 file` | Owner read/write only |
| `chmod 644 file` | Owner read/write, group/others read |
| `chmod 700 folder` | Owner full access only |
| `chmod 755 folder` | Owner full access, others can read/enter |
| `ls -l` | Show file permissions |
| `ls -ld folder` | Show folder permissions |

For directories: `r` = list contents, `w` = create/delete inside, `x` = enter/traverse.

---

## 7. Processes

| Goal | Command |
|---|---|
| Current shell PID | `echo $$` |
| Current terminal processes | `ps` |
| All processes | `ps aux` |
| Search processes | `ps aux \| grep ssh` |
| Find SSH PIDs | `pgrep -a ssh` |
| Live process monitor | `top` |
| Start test background process | `sleep 300 &` |
| Find sleep process | `pgrep -a sleep` |
| Stop sleep process | `pkill sleep` |

Process = running program. PID = process ID.

---

## 8. Services

| Goal | Command |
|---|---|
| SSH status | `systemctl status ssh` |
| Is SSH active? | `systemctl is-active ssh` |
| Is SSH enabled? | `systemctl is-enabled ssh` |
| SSH socket status | `systemctl status ssh.socket` |
| Running services | `systemctl list-units --type=service --state=running` |
| Failed services | `systemctl list-units --type=service --state=failed` |
| Local login service | `systemctl status getty@tty1.service` |

Service = background process managed by `systemd`.

---

## 9. Networking

| Goal | Command |
|---|---|
| Show IP addresses | `ip addr` |
| Show main interface | `ip addr show enp0s3` |
| Show routes | `ip route` |
| Ping gateway | `ping -c 4 10.0.2.2` |
| Test internet by IP | `ping -c 4 8.8.8.8` |
| Test DNS + internet | `ping -c 4 google.com` |
| DNS status | `resolvectl status` |

Key idea: `ping 8.8.8.8` tests IP connectivity. `ping google.com` tests IP connectivity plus DNS.

---

## 10. Ports and Connections

| Goal | Command |
|---|---|
| Listening ports | `ss -tuln` |
| Listening ports + processes | `sudo ss -tulnp` |
| Check SSH/DNS ports | `sudo ss -tulnp \| grep -E ':22\|:53'` |
| Active TCP connections | `ss -tn` |

Common ports: `22` = SSH, `53` = DNS, `80` = HTTP, `443` = HTTPS.

Address meanings: `127.0.0.1` = local only, `127.0.0.53` = local DNS resolver, `0.0.0.0` = all IPv4 interfaces.

---

## 11. Firewall / UFW

UFW = Uncomplicated Firewall.

| Goal | Command |
|---|---|
| Firewall status | `sudo ufw status verbose` |
| Allow SSH | `sudo ufw allow OpenSSH` |
| Enable firewall | `sudo ufw enable` |
| Numbered rules | `sudo ufw status numbered` |

Important: allow SSH before enabling UFW, or you may lock yourself out.

---

## 12. SSH

| Goal | Command |
|---|---|
| Connect from Windows | `ssh joey@127.0.0.1 -p 2222` |
| Show SSH connection info | `echo $SSH_CONNECTION` |
| Show terminal type | `tty` |
| SSH service status | `systemctl status ssh` |
| SSH logs | `sudo journalctl -u ssh -n 50 --no-pager` |
| Inspect SSH config | `sudo grep -Ei '^(#)?(Port|PermitRootLogin|PasswordAuthentication|PubkeyAuthentication|KbdInteractiveAuthentication|UsePAM)' /etc/ssh/sshd_config` |
| Effective SSH config | `sudo sshd -T \| grep -E 'port\|permitrootlogin\|passwordauthentication\|pubkeyauthentication\|kbdinteractiveauthentication\|usepam'` |

SSH settings: `port 22` = default SSH port, `permitrootlogin prohibit-password` = root password login blocked, `passwordauthentication yes` = password login allowed, `pubkeyauthentication yes` = SSH keys supported, `usepam yes` = uses PAM.

---

## 13. Updates and Shutdown

| Goal | Command |
|---|---|
| Refresh package info | `sudo apt update` |
| List upgrades | `apt list --upgradable` |
| Install upgrades | `sudo apt upgrade` |
| Check reboot required | `test -f /var/run/reboot-required && cat /var/run/reboot-required || echo "No reboot required"` |
| Shutdown | `sudo shutdown now` |
| Reboot | `sudo reboot` |

---

## 14. Troubleshooting Workflow

Ask: What changed? Is the system running? Can I connect? Does it have an IP? Is the service active? Is the port listening? Is the firewall blocking it? What do the logs say?

Core troubleshooting commands: `hostname`, `whoami`, `ip addr`, `ip route`, `systemctl status ssh`, `sudo ufw status verbose`, `sudo ss -tulnp`, `sudo journalctl -u ssh -n 50 --no-pager`.

---

## 15. Interview Phrases

- I used `systemctl` to inspect service status.
- I used `journalctl` to review service logs.
- I used `ss` to identify listening ports and process ownership.
- I used `grep` to filter logs for specific events.
- I used `chmod` to restrict file and directory permissions.
- I enabled UFW safely by allowing OpenSSH before activating the firewall.
- I checked SSH hardening settings with `sshd -T`.
- I confirmed that SSH was listening on port 22 and owned by `sshd`.

---

## 16. Quick Command Map

`whoami` = current user, `id` = UID/GID/groups, `pwd` = current folder, `ls -la` = list files, `cat/less/tail` = read files, `grep` = search text, `journalctl` = logs, `systemctl` = services, `ps/pgrep/top` = processes, `ip` = network info, `ss` = ports/connections, `ufw` = firewall, `chmod` = permissions, `sudo` = admin privileges.
