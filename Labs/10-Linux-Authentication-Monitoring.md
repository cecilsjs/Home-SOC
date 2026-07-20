# Lab 10 – Linux Authentication Monitoring

## Objective

Monitor Linux authentication activity to identify successful SSH key logins, blocked password-only attempts, invalid username attempts, and sudo privilege activity.

## Key Concepts

Authentication monitoring means reviewing login and privilege activity to understand:

- Who accessed a system
- How they authenticated
- Whether the attempt succeeded or failed
- Where the connection came from
- Whether privileged commands were used

## Events Monitored

- Successful SSH public key login
- Blocked password-only SSH attempt
- Invalid username SSH attempt
- Sudo privilege activity

## Commands Practiced

```bash
sudo journalctl -u ssh -n 50 --no-pager
sudo journalctl -u ssh --no-pager | grep "Accepted publickey"
sudo journalctl -u ssh --no-pager | grep "Accepted publickey" | tail -n 5
sudo journalctl -u ssh --no-pager | grep -i "invalid user" | tail -n 5
sudo journalctl -u ssh -n 30 --no-pager
sudo whoami
sudo journalctl --no-pager | grep "sudo" | tail -n 10
```

## PowerShell Test Commands

```powershell
ssh -o PreferredAuthentications=password -o PubkeyAuthentication=no joey@127.0.0.1 -p 2222
ssh fakeuser@127.0.0.1 -p 2222
```

## Successful SSH Key Login

I filtered SSH logs for successful public key authentication:

```bash
sudo journalctl -u ssh --no-pager | grep "Accepted publickey"
```

Finding:

```text
Accepted publickey for joey
```

This confirmed that user `joey` successfully authenticated using SSH key authentication.

## Blocked Password-Only Attempt

I tested password-only authentication using:

```powershell
ssh -o PreferredAuthentications=password -o PubkeyAuthentication=no joey@127.0.0.1 -p 2222
```

The connection failed because password authentication had already been disabled.

The logs showed a connection reset for the authenticating user.

This confirmed that SSH hardening was working.

## Invalid Username Attempt

I tested an invalid username login attempt:

```powershell
ssh fakeuser@127.0.0.1 -p 2222
```

The logs showed:

```text
Connection reset by invalid user fakeuser
```

This means someone attempted to log in with a username that does not exist on the server.

In a real environment, repeated invalid usernames could indicate scanning, brute-force attempts, or automated login attempts.

## Sudo Activity Monitoring

I generated sudo activity:

```bash
sudo whoami
```

Expected output:

```text
root
```

Then I reviewed sudo logs:

```bash
sudo journalctl --no-pager | grep "sudo" | tail -n 10
```

The logs showed user `joey` running:

```text
COMMAND=/usr/bin/whoami
```

This confirmed that sudo activity was being logged.

## Important Log Fields

| Field | Meaning |
|---|---|
| Timestamp | When the event happened |
| Hostname | Which system recorded the event |
| Service/process | Which service generated the event |
| User | Which account was involved |
| Source IP | Where the connection came from |
| Source port | Temporary port used by the client |
| Authentication method | Public key, password, or another method |
| Command | Command executed with sudo |

## Security Lessons Learned

- Successful logins should be reviewed to confirm expected access.
- Invalid usernames can indicate scanning or brute-force attempts.
- Blocked password authentication confirms SSH hardening is working.
- Sudo logs show privilege escalation activity.
- Repeated failed authentication events may indicate attack activity.
- Filtering logs with `grep` helps isolate important security events.
- `journalctl` can be used for SOC-style authentication investigations.

## Interview Summary

In this lab, I monitored Linux authentication activity using `journalctl` and `grep`. I identified successful SSH public key logins, blocked password-only login attempts, invalid username attempts, and sudo privilege activity. I also interpreted important log fields such as username, source IP, authentication method, terminal session, and executed sudo command.
