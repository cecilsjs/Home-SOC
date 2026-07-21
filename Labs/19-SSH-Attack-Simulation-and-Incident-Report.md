# Lab 19 – SSH Attack Simulation and Incident Report

## Objective

Simulate controlled SSH invalid username activity, collect log evidence, run an authentication monitoring script, and create a SOC-style incident report.

## Key Concepts

SSH stands for **Secure Shell**.

SOC stands for **Security Operations Center**.

IoC stands for **Indicator of Compromise**.

An incident report documents what happened, what evidence was collected, what impact occurred, and what response actions were taken.

In this lab, I simulated SSH invalid username activity, collected log evidence, ran my authentication monitoring script, and created a SOC-style incident report.

## Commands Practiced

```bash
mkdir -p ~/soc-evidence/lab19
ls -ld ~/soc-evidence/lab19
```

## Simulated SSH Attempts

From Windows PowerShell, I generated controlled invalid SSH username attempts:

```powershell
ssh admin@127.0.0.1 -p 2222
ssh test@127.0.0.1 -p 2222
ssh backup@127.0.0.1 -p 2222
```

Each login attempt failed as expected.

These usernames are commonly attempted during SSH scanning, brute-force activity, or attacker enumeration.

## Log Investigation Commands

I searched for invalid username activity:

```bash
sudo journalctl -u ssh --since "15 minutes ago" --no-pager | grep -i "invalid user"
```

I searched for connection reset events:

```bash
sudo journalctl -u ssh --since "15 minutes ago" --no-pager | grep -i "connection reset"
```

I counted invalid username events:

```bash
sudo journalctl -u ssh --since "15 minutes ago" --no-pager | grep -ci "invalid user"
```

I counted connection reset events:

```bash
sudo journalctl -u ssh --since "15 minutes ago" --no-pager | grep -ci "connection reset"
```

I ran my authentication monitoring script:

```bash
sudo ~/soc-scripts/auth-monitor.sh "15 minutes ago"
```

## Evidence Collection Commands

I saved invalid user evidence:

```bash
sudo journalctl -u ssh --since "15 minutes ago" --no-pager | grep -i "invalid user" > ~/soc-evidence/lab19/invalid-users-evidence.txt
```

I saved connection reset evidence:

```bash
sudo journalctl -u ssh --since "15 minutes ago" --no-pager | grep -i "connection reset" > ~/soc-evidence/lab19/connection-reset-evidence.txt
```

I saved the authentication monitoring report:

```bash
sudo ~/soc-scripts/auth-monitor.sh "15 minutes ago" > ~/soc-evidence/lab19/auth-monitor-attack-report.txt
```

I created a SOC-style incident report:

```bash
cat > ~/soc-evidence/lab19/incident-report-ssh-attack.md
```

## Evidence Files Created

```text
/home/joey/soc-evidence/lab19/invalid-users-evidence.txt
/home/joey/soc-evidence/lab19/connection-reset-evidence.txt
/home/joey/soc-evidence/lab19/auth-monitor-attack-report.txt
/home/joey/soc-evidence/lab19/incident-report-ssh-attack.md
```

## Simulated Usernames

```text
admin
test
backup
```

## Detection Results

| Event Type | Count |
|---|---:|
| Invalid user events | 6 |
| Connection reset events | 4 |
| Authentication monitoring script alerts | Yes |

## Findings

SSH logs showed invalid username attempts.

SSH logs showed connection reset events.

The invalid usernames included:

```text
admin
test
backup
```

The source IP observed was:

```text
10.0.2.2
```

The target Ubuntu VM IP was:

```text
10.0.2.15
```

The authentication monitoring script detected the suspicious activity.

Evidence files were saved in the Lab 19 evidence folder.

A SOC-style incident report was created.

## Indicators of Compromise

| Indicator | Value |
|---|---|
| Source IP | `10.0.2.2` |
| Target service | SSH |
| Invalid usernames | `admin`, `test`, `backup` |
| Event type | Invalid user login attempt |
| Related activity | Connection reset during pre-authentication |

## Incident Report Summary

A controlled SSH attack simulation was performed against the Ubuntu Server lab system.

Multiple invalid username login attempts were generated from the Windows host through VirtualBox NAT.

The activity was detected in SSH logs and confirmed using the authentication monitoring script.

No unauthorized access occurred.

This was a controlled lab simulation.

## Analysis

The invalid usernames used in this simulation are common usernames that attackers may try during SSH scanning or brute-force activity.

The connection reset events indicate that SSH sessions were closed before authentication completed.

Because password authentication was disabled in a previous hardening lab, the attack attempts did not result in successful password-based access.

## Response Actions

- Reviewed SSH logs using `journalctl`
- Filtered invalid username activity with `grep`
- Counted suspicious events
- Ran the authentication monitoring script
- Saved evidence files
- Created an incident report
- Documented indicators of compromise

## Recommendations

- Keep SSH key authentication enabled
- Keep SSH password authentication disabled
- Continue monitoring invalid username attempts
- Review repeated source IPs
- Consider alerting on repeated invalid users
- Consider blocking suspicious source IPs in a real environment
- Preserve evidence during investigations

## Security Lessons Learned

- Invalid username attempts can indicate SSH scanning, brute-force activity, or attacker enumeration.
- Connection reset events can show blocked or incomplete authentication attempts.
- Authentication monitoring scripts can help summarize suspicious activity.
- Evidence should be saved before writing an incident report.
- Incident reports should include summary, environment, timeline, evidence, indicators, analysis, impact, response actions, and recommendations.
- Disabling SSH password authentication reduces the risk of password brute-force attacks.
- SSH key authentication should remain enabled and tested.

## Interview Summary

In this lab, I simulated an SSH invalid user attack against my Ubuntu Server lab, reviewed SSH logs with `journalctl` and `grep`, counted invalid user and connection reset events, ran my authentication monitoring script, saved evidence files, identified indicators of compromise, and wrote a SOC-style incident report documenting the activity, impact, response actions, and recommendations.
