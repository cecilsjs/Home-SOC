# Lab 15 – Linux Audit Basics with auditd

## Objective

Install and use `auditd` to monitor Linux file activity, create an audit rule, search audit logs, generate an audit report, and remove the temporary rule after testing.

## Key Concepts

`auditd` stands for **Linux Audit Daemon**.

`auditd` records security-relevant events on Linux systems.

`auditctl` is used to manage audit rules.

`ausearch` is used to search audit logs.

`aureport` is used to create audit reports.

SOC stands for **Security Operations Center**.

In this lab, I used `auditd` to monitor a specific file for read, write, execute, and attribute-change events.

## Commands Practiced

```bash
sudo apt update
sudo apt install auditd -y
systemctl is-active auditd
sudo ls -l /var/log/audit/
sudo tail -n 20 /var/log/audit/audit.log
```

```bash
mkdir -p ~/audit-lab
echo "important security setting" > ~/audit-lab/important.conf
ls -l ~/audit-lab/important.conf
cat ~/audit-lab/important.conf
```

```bash
sudo auditctl -w /home/joey/audit-lab/important.conf -p rwxa -k important_file_watch
sudo auditctl -l
```

```bash
cat ~/audit-lab/important.conf
echo "new security setting" >> ~/audit-lab/important.conf
chmod 600 ~/audit-lab/important.conf
```

```bash
sudo ausearch -k important_file_watch
sudo ausearch -k important_file_watch -i
sudo aureport -f -i | tail -n 20
```

```bash
sudo auditctl -W /home/joey/audit-lab/important.conf -p rwxa -k important_file_watch
sudo auditctl -l
```

## Audit Rule Used

```bash
sudo auditctl -w /home/joey/audit-lab/important.conf -p rwxa -k important_file_watch
```

## Rule Meaning

| Option | Meaning |
|---|---|
| `-w` | Watch this file |
| `-p` | Define which permissions/events to monitor |
| `r` | Read |
| `w` | Write |
| `x` | Execute |
| `a` | Attribute changes, such as `chmod` or ownership changes |
| `-k` | Add a searchable key to the audit rule |

## File Monitored

```text
/home/joey/audit-lab/important.conf
```

## Audit Key Used

```text
important_file_watch
```

## Events Generated

I generated audit events by reading, modifying, and changing permissions on the monitored file.

Read event:

```bash
cat ~/audit-lab/important.conf
```

Write event:

```bash
echo "new security setting" >> ~/audit-lab/important.conf
```

Attribute-change event:

```bash
chmod 600 ~/audit-lab/important.conf
```

## Searching Audit Logs

I searched for events using the custom audit key:

```bash
sudo ausearch -k important_file_watch
```

I also used interpreted output:

```bash
sudo ausearch -k important_file_watch -i
```

The `-i` option makes some audit log values easier to read.

## Audit Report

I generated a file activity report using:

```bash
sudo aureport -f -i | tail -n 20
```

This helped summarize file-related audit activity.

## Findings

- `auditd` was active.
- The audit rule was added successfully.
- `auditctl -l` showed the `important_file_watch` rule.
- `ausearch` found events using the `important_file_watch` key.
- The audit logs showed activity involving `/home/joey/audit-lab/important.conf`.
- `aureport` generated a file activity report.
- The temporary audit rule was removed after testing.
- `auditctl -l` showed `no rules` after removal.

## Rule Cleanup

After the test, I removed the temporary audit rule:

```bash
sudo auditctl -W /home/joey/audit-lab/important.conf -p rwxa -k important_file_watch
```

Then I confirmed the rule was removed:

```bash
sudo auditctl -l
```

The result showed:

```text
no rules
```

## Security Lessons Learned

- `auditd` can monitor sensitive files for access and changes.
- Audit rules can track read, write, execute, and attribute-change events.
- Custom audit keys make events easier to search.
- `ausearch` helps investigate specific audit events.
- `aureport` helps summarize audit log activity.
- Temporary lab audit rules should be removed after testing.
- File auditing is useful for detecting changes to important configuration files.

## Interview Summary

In this lab, I installed and configured `auditd` on Ubuntu, created a test file, added an audit rule to monitor read, write, execute, and attribute-change events, generated test activity, searched the audit logs with `ausearch`, reviewed file activity with `aureport`, and removed the temporary audit rule after testing.
