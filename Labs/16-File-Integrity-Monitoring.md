# Lab 16 – File Integrity Monitoring

## Objective

Create a File Integrity Monitoring lab using SHA-256 hashes to detect when important files change.

## Key Concepts

FIM stands for **File Integrity Monitoring**.

FIM is used to detect when important files change.

SHA-256 stands for **Secure Hash Algorithm 256-bit**.

A SHA-256 hash is a fingerprint of a file. If the file changes, the hash changes.

SOC stands for **Security Operations Center**.

In this lab, I created a baseline of file hashes, changed a file, detected the change, and wrote a simple FIM script.

## Commands Practiced

```bash
mkdir -p ~/fim-lab
cd ~/fim-lab
echo "admin_login=false" > app.conf
echo "max_users=50" > users.conf
ls -l
cat app.conf
cat users.conf
```

```bash
sha256sum *.conf > baseline.sha256
cat baseline.sha256
sha256sum -c baseline.sha256
```

```bash
echo "admin_login=true" > app.conf
cat app.conf
sha256sum -c baseline.sha256
```

```bash
mkdir -p ~/soc-scripts
nano ~/soc-scripts/fim-check.sh
chmod +x ~/soc-scripts/fim-check.sh
~/soc-scripts/fim-check.sh
```

```bash
echo "admin_login=false" > app.conf
sha256sum -c baseline.sha256
~/soc-scripts/fim-check.sh
```

## Files Monitored

```text
/home/joey/fim-lab/app.conf
/home/joey/fim-lab/users.conf
```

## Baseline File

```text
/home/joey/fim-lab/baseline.sha256
```

## Script Created

```text
/home/joey/soc-scripts/fim-check.sh
```

## Report File

```text
/home/joey/fim-lab/fim-report.txt
```

## FIM Script

```bash
#!/bin/bash

# Lab 16 - File Integrity Monitoring Script
# FIM means File Integrity Monitoring.
# SHA-256 means Secure Hash Algorithm 256-bit.
# SOC means Security Operations Center.

BASELINE="/home/joey/fim-lab/baseline.sha256"
REPORT="/home/joey/fim-lab/fim-report.txt"

echo "======================================"
echo " File Integrity Monitoring Report"
echo "======================================"
echo "Time: $(date)"
echo ""

if [ ! -f "$BASELINE" ]; then
    echo "[ERROR] Baseline file not found: $BASELINE"
    exit 1
fi

cd /home/joey/fim-lab || exit 1

echo "[+] Checking files against baseline..."
sha256sum -c "$BASELINE" > "$REPORT" 2>&1

cat "$REPORT"

echo ""
echo "------------ Alert Summary ------------"

if grep -q "FAILED" "$REPORT"; then
    echo "[ALERT] File integrity change detected."
else
    echo "[OK] All monitored files match the baseline."
fi
```

## Test Performed

I created two test configuration files:

```text
app.conf
users.conf
```

I generated a SHA-256 baseline using:

```bash
sha256sum *.conf > baseline.sha256
```

Then I verified both files matched the baseline:

```bash
sha256sum -c baseline.sha256
```

Initial result:

```text
app.conf: OK
users.conf: OK
```

## Simulated File Change

I changed `app.conf` from:

```text
admin_login=false
```

to:

```text
admin_login=true
```

Then I checked the baseline again.

Result:

```text
app.conf: FAILED
users.conf: OK
sha256sum: WARNING: 1 computed checksum did NOT match
```

This confirmed that the file integrity check detected the change.

## Script Alert

After running the FIM script, it showed:

```text
[ALERT] File integrity change detected.
```

This confirmed the script could detect file changes automatically.

## Restoring the File

I restored `app.conf` back to:

```text
admin_login=false
```

Then I checked the baseline again:

```bash
sha256sum -c baseline.sha256
```

Final result:

```text
app.conf: OK
users.conf: OK
```

The script then showed:

```text
[OK] All monitored files match the baseline.
```

## Security Lessons Learned

- File Integrity Monitoring can detect unauthorized changes.
- Configuration files should be monitored because small changes can create security risk.
- Hashes help verify whether a file has changed.
- A baseline represents the known-good state.
- If the current hash does not match the baseline, the file may have been modified.
- FIM is useful for detecting suspicious changes to important files.
- A script can automate file integrity checks and generate alerts.

## Interview Summary

In this lab, I created a File Integrity Monitoring lab using SHA-256 hashes. I created a baseline for configuration files, modified one file, detected the change with `sha256sum`, wrote a Bash script to automate the check, generated an alert when a file failed integrity verification, restored the file, and confirmed the files matched the baseline again.
