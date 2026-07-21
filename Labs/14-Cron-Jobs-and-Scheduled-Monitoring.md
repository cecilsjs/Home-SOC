# Lab 14 – Cron Jobs and Scheduled Monitoring

## Objective

Schedule a Linux authentication monitoring script to run automatically using cron and save its output to a report file.

## Key Concepts

Cron is the Linux job scheduler.

Cron can automatically run commands or scripts at specific times.

Bash stands for **Bourne Again Shell**.

SSH stands for **Secure Shell**.

SOC stands for **Security Operations Center**.

In this lab, I scheduled my authentication monitoring script to run automatically and save its output to a report file.

## Commands Practiced

```bash
ls -l ~/soc-scripts/auth-monitor.sh
sudo ~/soc-scripts/auth-monitor.sh
mkdir -p ~/soc-reports
sudo ~/soc-scripts/auth-monitor.sh > ~/soc-reports/auth-report-test.txt
cat ~/soc-reports/auth-report-test.txt
crontab -e
crontab -l
crontab -r
sudo crontab -e
sudo crontab -l
ls -l ~/soc-reports
cat ~/soc-reports/auth-report-cron.txt
ls -l ~/soc-reports/auth-report-cron.txt
```

## Important Folders

```text
/home/joey/soc-scripts
/home/joey/soc-reports
```

## Important Files

```text
/home/joey/soc-scripts/auth-monitor.sh
/home/joey/soc-reports/auth-report-test.txt
/home/joey/soc-reports/auth-report-cron.txt
```

## Cron Job Used

```cron
*/5 * * * * /home/joey/soc-scripts/auth-monitor.sh > /home/joey/soc-reports/auth-report-cron.txt 2>&1
```

## Cron Schedule Meaning

| Field | Value | Meaning |
|---|---|---|
| Minute | `*/5` | Every 5 minutes |
| Hour | `*` | Every hour |
| Day of month | `*` | Every day of the month |
| Month | `*` | Every month |
| Day of week | `*` | Every day of the week |

## Output Redirection

```bash
>
```

Sends normal output to a file.

```bash
2>&1
```

Sends errors to the same file as normal output.

This matters because if the scheduled job fails, the error can be captured in the report file.

## Why Root Cron Was Used

A user crontab running `sudo` may fail because cron cannot type a sudo password.

Using:

```bash
sudo crontab -e
```

creates a root cron job.

Root cron runs with root privileges, so `sudo` is not needed inside the cron command.

## Scheduled Report

The scheduled cron report was saved to:

```text
/home/joey/soc-reports/auth-report-cron.txt
```

I confirmed the report existed using:

```bash
ls -l ~/soc-reports/auth-report-cron.txt
```

I reviewed the report with:

```bash
cat ~/soc-reports/auth-report-cron.txt
```

## Findings

The authentication monitoring script ran manually.

The script output was saved to:

```text
auth-report-test.txt
```

The script was scheduled with cron to run every 5 minutes.

The scheduled report was saved to:

```text
auth-report-cron.txt
```

The cron report showed alerts for:

- Invalid username attempts
- SSH connection reset events
- High sudo activity

The report included:

- Recent invalid user events
- Successful public key logins
- Recent sudo activity

## Example Alert Summary

```text
[ALERT] Invalid username attempts detected.
[ALERT] SSH connection reset events detected.
[NOTICE] High sudo activity detected. Review sudo logs.
```

## Security Lessons Learned

- Scheduled monitoring helps automate repeated security checks.
- Cron can be used to run detection scripts on a regular schedule.
- Authentication reports can help identify suspicious login activity.
- Invalid username attempts can indicate scanning or brute-force activity.
- High sudo activity should be reviewed because sudo allows elevated/admin commands.
- Scheduled reports should be checked to confirm the job is actually running.
- Errors should be redirected into the report file so failures are visible.
- Root cron is useful when the scheduled job needs elevated permissions.

## Interview Summary

In this lab, I scheduled my Linux authentication monitoring script using cron. The script runs every 5 minutes and writes its output to a report file. I verified that the cron job existed, confirmed the report file was created, and reviewed the report output showing authentication alerts, invalid username activity, successful SSH key logins, and sudo activity.
