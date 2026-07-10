# Lab 03 – Linux Filesystem and Logs

## Objective

Learn how to navigate the Linux filesystem, understand important system directories, and inspect Linux authentication and SSH logs.

## Key Filesystem Concepts

Linux uses a single filesystem tree starting at `/`.

| Directory | Purpose |
|---|---|
| `/` | Root of the entire filesystem |
| `/home` | Normal user home directories |
| `/etc` | System configuration files |
| `/var/log` | System and security logs |
| `/usr` | Installed programs and libraries |
| `/bin` | Essential command binaries |
| `/root` | Root user's home directory |
| `/tmp` | Temporary files |

## Commands Practiced

```bash
pwd
ls
ls -la
cd /
cd /home
cd /var/log
cat /etc/hostname
cat /etc/os-release
grep VERSION /etc/os-release
sudo tail -n 20 /var/log/auth.log
sudo journalctl -u ssh -n 50 --no-pager
sudo journalctl -u ssh --no-pager | grep "Failed password"
sudo journalctl -u ssh --no-pager | grep -i "accepted password"
```

## Log Analysis

I reviewed SSH and authentication logs to identify:

- SSH service startup events
- Failed SSH password attempts
- Successful SSH logins
- Sudo activity
- Source IP addresses
- Source ports
- Usernames
- Terminal sessions

Example successful SSH login:

```text
Accepted password for joey from 10.0.2.2 port 64931 ssh2
```

This means the user `joey` successfully logged in over SSH from source IP `10.0.2.2`.

Example failed SSH login:

```text
Failed password for joey from 10.0.2.2 port 55154 ssh2
```

This means there was an unsuccessful SSH password attempt for user `joey` from source IP `10.0.2.2`.

## Grep Troubleshooting Lesson

I learned that `grep` is case-sensitive by default.

This search did not return the expected result:

```bash
grep "accepted password"
```

because the log contained:

```text
Accepted password
```

with a capital `A`.

To search without caring about capitalization, I used:

```bash
grep -i "accepted password"
```

## Lessons Learned

- `/` is the root of the Linux filesystem.
- `/var/log` stores important system and security logs.
- `grep` searches for text patterns.
- `journalctl` can show logs for specific services.
- SSH logs show successful and failed login attempts.
- Repeated failed passwords could indicate brute force or password spraying.
- Sudo logs show which user ran commands with elevated privileges.
- Linux commands are often case-sensitive.

## Interview Summary

In this lab, I navigated the Linux filesystem, reviewed authentication logs, searched SSH logs using `grep` and `journalctl`, identified failed and successful SSH login attempts, and interpreted sudo activity showing user, terminal, working directory, elevated user, and executed command.
