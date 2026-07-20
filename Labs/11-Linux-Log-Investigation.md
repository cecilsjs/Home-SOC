# Lab 11 – Linux Log Investigation with journalctl and grep

## Objective

Investigate Linux system and SSH logs using `journalctl`, `grep`, time filters, severity filters, and event counting.

## Key Concepts

`journalctl` reads the systemd journal.

`grep` filters log output for specific words or patterns.

`tail` can show the most recent matching events.

`--since` narrows logs to a specific time window.

`grep -c` counts matching lines.

`grep -i` ignores capitalization.

`grep -ci` counts matches while ignoring capitalization.

## Commands Practiced

```bash
sudo journalctl -n 20 --no-pager
sudo journalctl -b -n 30 --no-pager
sudo journalctl -u ssh -n 50 --no-pager
sudo journalctl -u ssh --no-pager | grep "Accepted publickey"
sudo journalctl -u ssh --no-pager | grep -i "invalid user"
sudo journalctl -u ssh --no-pager | grep -i "connection reset"
sudo journalctl -u ssh --since "1 hour ago" --no-pager
sudo journalctl -u ssh --since "today" --no-pager
sudo journalctl -u ssh --since "today" --no-pager | grep -c "Accepted publickey"
sudo journalctl -u ssh --since "today" --no-pager | grep -ci "invalid user"
sudo journalctl -u ssh --since "today" --no-pager | grep -ci "connection reset"
sudo journalctl -p warning -n 30 --no-pager
```

## Investigation Findings

| Event Type | Count |
|---|---:|
| Accepted publickey events today | 1 |
| Invalid user events today | 2 |
| Connection reset events today | 2 |
| Warning logs | Mostly normal system messages |

## Interpretation

The `Accepted publickey` event showed a successful SSH key login.

The invalid user events showed login attempts using usernames that do not exist on the server.

The connection reset events showed SSH connections that were closed before authentication completed.

The warning logs did not show obvious critical issues during this review.

## Useful Filters

Successful SSH key logins:

```bash
sudo journalctl -u ssh --no-pager | grep "Accepted publickey"
```

Invalid username attempts:

```bash
sudo journalctl -u ssh --no-pager | grep -i "invalid user"
```

Connection reset events:

```bash
sudo journalctl -u ssh --no-pager | grep -i "connection reset"
```

Count accepted public key logins today:

```bash
sudo journalctl -u ssh --since "today" --no-pager | grep -c "Accepted publickey"
```

Count invalid users today:

```bash
sudo journalctl -u ssh --since "today" --no-pager | grep -ci "invalid user"
```

Count connection resets today:

```bash
sudo journalctl -u ssh --since "today" --no-pager | grep -ci "connection reset"
```

## Security Lessons Learned

- Successful logins should be reviewed to confirm expected access.
- Invalid usernames may indicate scanning, brute-force attempts, or attacker enumeration.
- Connection reset events can show blocked or incomplete authentication attempts.
- Time filters help narrow logs to an investigation window.
- Counting events helps summarize activity quickly.
- Warning and error logs can help identify system problems or suspicious behavior.
- `grep -i` is useful because log capitalization may vary.
- `grep -c` is useful for quickly counting event types.

## Interview Summary

In this lab, I investigated Linux logs using `journalctl`, `grep`, time filters, severity filters, and event counting. I reviewed SSH logs, filtered successful public key logins, invalid usernames, and connection reset events, counted the results for today, and summarized the findings in a SOC-style investigation format.
