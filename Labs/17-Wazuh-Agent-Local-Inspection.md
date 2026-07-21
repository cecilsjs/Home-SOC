# Lab 17 – Wazuh Agent Installation and Local Inspection

## Objective

Install the Wazuh agent on Ubuntu, inspect its service, files, configuration, and logs, and troubleshoot why the agent does not fully start without a Wazuh manager.

## Key Concepts

Wazuh is a security monitoring platform with SIEM and XDR features.

SIEM stands for **Security Information and Event Management**.

XDR stands for **Extended Detection and Response**.

The **Wazuh agent** is installed on an endpoint being monitored.

The **Wazuh manager** is the central server that receives data from agents.

The Wazuh agent needs a configured Wazuh manager before it can operate normally.

## Commands Practiced

```bash
free -h
df -h
nproc
```

```bash
sudo apt update
sudo apt install curl gnupg apt-transport-https -y
```

```bash
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | sudo gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import
sudo chmod 644 /usr/share/keyrings/wazuh.gpg
```

```bash
echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | sudo tee /etc/apt/sources.list.d/wazuh.list
sudo apt update
sudo apt install wazuh-agent -y
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable wazuh-agent
sudo systemctl start wazuh-agent
systemctl status wazuh-agent --no-pager
systemctl is-active wazuh-agent
```

```bash
sudo tail -n 50 /var/ossec/logs/ossec.log
sudo grep -n "address" /var/ossec/etc/ossec.conf
```

```bash
dpkg -l | grep wazuh
sudo ls -l /var/ossec/
sudo ls -l /var/ossec/etc/
sudo tail -n 30 /var/ossec/logs/ossec.log
```

```bash
sudo systemctl disable wazuh-agent
systemctl is-enabled wazuh-agent
systemctl is-active wazuh-agent
```

## Important Files and Folders

```text
/var/ossec/
/var/ossec/etc/ossec.conf
/var/ossec/logs/ossec.log
/etc/apt/sources.list.d/wazuh.list
/usr/share/keyrings/wazuh.gpg
```

## Package Installed

```text
wazuh-agent 4.14.6-1
```

## Findings

The Wazuh agent package installed successfully.

The `/var/ossec` directory was created.

The Wazuh configuration files were present in:

```text
/var/ossec/etc/
```

The Wazuh log file was present at:

```text
/var/ossec/logs/ossec.log
```

The Wazuh agent service failed to start.

The `ossec.log` file showed:

```text
Invalid server address found: 'MANAGER_IP'
```

The `ossec.log` file also showed:

```text
No client configured. Exiting.
```

The `ossec.conf` file contained the placeholder manager address:

```text
MANAGER_IP
```

## Service State

After troubleshooting, the Wazuh agent was disabled from auto-starting because no Wazuh manager was configured yet.

```text
systemctl is-enabled wazuh-agent
```

Result:

```text
disabled
```

```text
systemctl is-active wazuh-agent
```

Result:

```text
failed
```

## Why the Service Failed

The Wazuh agent expects a valid Wazuh manager address.

The configuration still had the placeholder:

```text
MANAGER_IP
```

Because no Wazuh manager was configured, the agent could not connect and exited.

This was expected for this lab because only the agent was installed locally.

## Troubleshooting Process

I checked the service status with:

```bash
systemctl status wazuh-agent --no-pager
```

I reviewed the Wazuh application log with:

```bash
sudo tail -n 50 /var/ossec/logs/ossec.log
```

I searched the configuration file for the manager address with:

```bash
sudo grep -n "address" /var/ossec/etc/ossec.conf
```

This showed:

```text
<address>MANAGER_IP</address>
```

That confirmed the service failed because the Wazuh manager address was not configured.

## Security Lessons Learned

- Agents collect endpoint security data.
- A SIEM manager receives, analyzes, and correlates events from agents.
- Service failures should be investigated using `systemctl`, `journalctl`, and application logs.
- Application logs often explain the real reason a service failed.
- Configuration placeholders like `MANAGER_IP` must be replaced with real values before production use.
- Disabling a failed service can prevent repeated startup failures until the configuration is ready.
- Troubleshooting failed services is an important SOC and system administration skill.

## Interview Summary

In this lab, I installed the Wazuh agent on Ubuntu and inspected its service, files, configuration, and logs. The package installed successfully, but the service failed to start because the manager address was still set to the placeholder `MANAGER_IP`. I confirmed this by reviewing `/var/ossec/logs/ossec.log` and checking `/var/ossec/etc/ossec.conf`. Since no Wazuh manager was configured yet, I disabled the agent from auto-starting and documented the troubleshooting process.
