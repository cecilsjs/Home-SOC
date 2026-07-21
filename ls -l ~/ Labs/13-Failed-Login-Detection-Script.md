# Lab 13 – Failed Login Detection Script

## Objective

Create a Bash script that monitors Linux authentication activity, counts important SSH and sudo events, and prints basic alert messages.

## Key Concepts

Bash stands for **Bourne Again Shell**.

SSH stands for **Secure Shell**.

SOC stands for **Security Operations Center**.

In this lab, I created a Bash script that monitors Linux authentication activity using `journalctl` and `grep`.

The script counts:

- Successful SSH public key logins
- Invalid username attempts
- SSH connection reset events
- Sudo activity events

The script also prints alert messages when suspicious or important activity is detected.

## Commands Practiced

```bash
mkdir -p ~/soc-scripts
cd ~/soc-scripts
nano auth-monitor.sh
chmod +x auth-monitor.sh
ls -l auth-monitor.sh
sudo ./auth-monitor.sh
sudo ./auth-monitor.sh "1 hour ago"
sudo ~/soc-scripts/auth-monitor.sh
```

## PowerShell Test Command

```powershell
ssh fakeuser@127.0.0.1 -p 2222
```

This generated an invalid username event in the SSH logs.

## Script Created

```bash
#!/bin/bash

# Lab 13 - Linux Authentication Monitoring Script
# Bash means Bourne Again Shell.
# SSH means Secure Shell.
# SOC means Security Operations Center.

SINCE="${1:-today}"

SUCCESSFUL_KEYS=$(journalctl -u ssh --since "$SINCE" --no-pager | grep -c "Accepted publickey")
INVALID_USERS=$(journalctl -u ssh --since "$SINCE" --no-pager | grep -ci "invalid user")
CONNECTION_RESETS=$(journalctl -u ssh --since "$SINCE" --no-pager | grep -ci "connection reset")
SUDO_EVENTS=$(journalctl --since "$SINCE" --no-pager | grep -c "sudo")

echo "======================================"
echo " Linux Authentication Monitoring Report"
echo "======================================"
echo "Time window: $SINCE"
echo ""

echo "[+] Successful SSH public key logins: $SUCCESSFUL_KEYS"
echo "[+] Invalid username attempts: $INVALID_USERS"
echo "[+] Connection reset events: $CONNECTION_RESETS"
echo "[+] Sudo activity events: $SUDO_EVENTS"

echo ""
echo "------------ Alert Summary ------------"

if [ "$INVALID_USERS" -gt 0 ]; then
    echo "[ALERT] Invalid username attempts detected."
else
    echo "[OK] No invalid username attempts detected."
fi

if [ "$CONNECTION_RESETS" -gt 0 ]; then
    echo "[ALERT] SSH connection reset events detected."
else
    echo "[OK] No SSH connection reset events detected."
fi

if [ "$SUDO_EVENTS" -gt 20 ]; then
    echo "[NOTICE] High sudo activity detected. Review sudo logs."
else
    echo "[OK] Sudo activity is within expected range."
fi

echo ""
echo "Recent invalid user events:"
journalctl -u ssh --since "$SINCE" --no-pager | grep -i "invalid user" | tail -n 5

echo ""
echo "Recent successful public key logins:"
journalctl -u ssh --since "$SINCE" --no-pager | grep "Accepted publickey" | tail -n 5

echo ""
echo "Recent sudo activity:"
journalctl --since "$SINCE" --no-pager | grep "sudo" | tail -n 5
```

## Important Script Logic

```bash
SINCE="${1:-today}"
```

This means the script uses the first argument as the time window.

If no argument is provided, it uses:

```text
today
```

Examples:

```bash
sudo ./auth-monitor.sh
sudo ./auth-monitor.sh "1 hour ago"
```

## Variables Used

| Variable | Purpose |
|---|---|
| `SUCCESSFUL_KEYS` | Counts successful SSH public key logins |
| `INVALID_USERS` | Counts invalid username attempts |
| `CONNECTION_RESETS` | Counts SSH connection reset events |
| `SUDO_EVENTS` | Counts sudo activity events |

## Detection Logic

If invalid usernames are greater than `0`, the script prints an alert.

If connection reset events are greater than `0`, the script prints an alert.

If sudo activity is greater than `20`, the script prints a notice to review sudo logs.

## Findings

The script successfully counted SSH authentication activity.

The script detected successful SSH public key logins.

The script detected invalid username attempts.

The script detected high sudo activity.

The `fakeuser` login test created an invalid user event.

The log showed:

```text
invalid user fakeuser from 10.0.2.2
```

## Security Lessons Learned

- Invalid username attempts can indicate scanning, brute-force activity, or attacker enumeration.
- Successful SSH public key logins should be reviewed to confirm expected access.
- High sudo activity should be reviewed because sudo allows elevated/admin commands.
- Automation helps analysts summarize logs more quickly.
- Detection scripts can turn raw log data into useful security alerts.
- `journalctl` can be used to collect logs.
- `grep` can be used to filter and count important security events.

## Interview Summary

In this lab, I created a Bash authentication monitoring script that uses `journalctl` and `grep` to count SSH and sudo events. The script summarizes successful public key logins, invalid username attempts, connection reset events, and sudo activity. I also added alert logic so the script can flag invalid usernames, connection resets, and high sudo activity. This gave me practice with basic Linux log monitoring and detection engineering.
