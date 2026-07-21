# Lab 18 – Security Alert Generation and Evidence Collection

## Objective

Generate controlled Linux security events, collect evidence from logs and monitoring scripts, and organize the evidence for SOC-style documentation.

## Key Concepts

SOC stands for **Security Operations Center**.

SSH stands for **Secure Shell**.

FIM stands for **File Integrity Monitoring**.

Evidence collection means saving relevant logs, alerts, and reports so an investigation can be documented.

In this lab, I generated controlled security events and saved evidence files for each event.

## Events Generated

- Invalid SSH username attempt
- File Integrity Monitoring alert
- Sudo/admin activity
- Authentication monitoring report

## Commands Practiced

```bash
mkdir -p ~/soc-evidence/lab18
ls -ld ~/soc-evidence/lab18
```

```bash
ls -l ~/soc-scripts/auth-monitor.sh
ls -l ~/soc-scripts/fim-check.sh
```

```bash
sudo ~/soc-scripts/auth-monitor.sh
~/soc-scripts/fim-check.sh
```

```powershell
ssh fakeuser@127.0.0.1 -p 2222
```

```bash
sudo journalctl -u ssh --since "10 minutes ago" --no-pager | grep -i "invalid user"
```

```bash
cd ~/fim-lab
echo "admin_login=true" > app.conf
~/soc-scripts/fim-check.sh
```

```bash
sudo whoami
sudo journalctl --since "10 minutes ago" --no-pager | grep "sudo" | tail -n 10
```

## Evidence Collection Commands

```bash
sudo journalctl -u ssh --since "30 minutes ago" --no-pager | grep -i "invalid user" > ~/soc-evidence/lab18/ssh-invalid-user-evidence.txt
```

```bash
~/soc-scripts/fim-check.sh > ~/soc-evidence/lab18/fim-alert-evidence.txt
```

```bash
sudo journalctl --since "30 minutes ago" --no-pager | grep "sudo" | tail -n 20 > ~/soc-evidence/lab18/sudo-activity-evidence.txt
```

```bash
sudo ~/soc-scripts/auth-monitor.sh > ~/soc-evidence/lab18/auth-monitor-report.txt
```

## Evidence Files Created

```text
/home/joey/soc-evidence/lab18/ssh-invalid-user-evidence.txt
/home/joey/soc-evidence/lab18/fim-alert-evidence.txt
/home/joey/soc-evidence/lab18/sudo-activity-evidence.txt
/home/joey/soc-evidence/lab18/auth-monitor-report.txt
```

I confirmed all evidence files existed using:

```bash
ls -l ~/soc-evidence/lab18
```

## Invalid SSH Username Event

I generated an invalid SSH login attempt from PowerShell:

```powershell
ssh fakeuser@127.0.0.1 -p 2222
```

The SSH logs showed an invalid user event involving:

```text
fakeuser
```

This simulated an attacker or scanner attempting to log in with a username that does not exist.

## File Integrity Monitoring Alert

I modified the monitored file:

```bash
echo "admin_login=true" > ~/fim-lab/app.conf
```

Then I ran the FIM script:

```bash
~/soc-scripts/fim-check.sh
```

The script detected the file change and produced an alert.

## Sudo/Admin Activity

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
sudo journalctl --since "10 minutes ago" --no-pager | grep "sudo" | tail -n 10
```

The sudo logs showed privileged command activity.

## Authentication Monitoring Report

I generated a full authentication monitoring report using:

```bash
sudo ~/soc-scripts/auth-monitor.sh
```

Then I saved the report to:

```text
/home/joey/soc-evidence/lab18/auth-monitor-report.txt
```

This report summarized SSH and sudo-related activity.

## Cleanup

After collecting evidence, I restored the FIM test file:

```bash
echo "admin_login=false" > ~/fim-lab/app.conf
```

Then I verified the file matched the known-good baseline:

```bash
cd ~/fim-lab
sha256sum -c baseline.sha256
```

Expected result:

```text
app.conf: OK
users.conf: OK
```

I also ran:

```bash
~/soc-scripts/fim-check.sh
```

Expected result:

```text
[OK] All monitored files match the baseline.
```

## Findings

- The invalid SSH username test generated an invalid user `fakeuser` log event.
- The FIM test detected a change to `app.conf`.
- The sudo test logged `joey` running `sudo whoami` as root.
- The authentication monitoring script produced a report showing authentication activity and alerts.
- All 4 evidence files were saved in the Lab 18 evidence folder.
- After evidence collection, `app.conf` was restored to the known-good baseline.

## Security Lessons Learned

- Controlled test events help validate detection logic.
- Evidence should be saved during investigations.
- SSH logs can reveal invalid username attempts.
- FIM alerts can detect file changes.
- Sudo logs can show privileged/admin activity.
- SOC investigations should preserve relevant logs and reports.
- After testing, systems should be restored to a clean state.
- Evidence files make security findings easier to document and explain.

## Interview Summary

In this lab, I generated controlled Linux security events, including an invalid SSH username attempt, a file integrity change, and sudo activity. I collected evidence from `journalctl` logs and my monitoring scripts, saved the evidence into separate files, verified the files existed, and restored the changed file back to the known-good baseline.
