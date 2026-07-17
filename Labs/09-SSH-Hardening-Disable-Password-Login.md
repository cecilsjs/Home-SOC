# Lab 09 – SSH Hardening: Disable Password Login Safely

## Objective

Harden SSH by disabling password authentication after confirming SSH key authentication works.

## Key Concepts

SSH password authentication allows users to log in with a password.

SSH public key authentication allows users to log in with a private/public key pair.

Disabling password authentication reduces the risk of brute-force password attacks.

Password login should only be disabled after SSH key login is tested and confirmed.

`sshd -T` shows the effective SSH server configuration.

## Commands Practiced

### Windows PowerShell

```powershell
ssh -i "$env:USERPROFILE\.ssh\id_ed25519" joey@127.0.0.1 -p 2222
ssh -o PreferredAuthentications=password -o PubkeyAuthentication=no joey@127.0.0.1 -p 2222
```

### Ubuntu

```bash
sudo journalctl -u ssh -n 20 --no-pager
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup-lab09
ls -l /etc/ssh/sshd_config.backup-lab09
sudo grep -Rni "PasswordAuthentication" /etc/ssh/sshd_config /etc/ssh/sshd_config.d/
sudo cat /etc/ssh/sshd_config.d/01-home-soc-hardening.conf
sudo sshd -t
sudo systemctl reload ssh
sudo sshd -T | grep -E 'passwordauthentication|pubkeyauthentication|permitrootlogin'
```

## Important Files

```text
/etc/ssh/sshd_config
/etc/ssh/sshd_config.backup-lab09
/etc/ssh/sshd_config.d/01-home-soc-hardening.conf
/etc/ssh/sshd_config.d/50-cloud-init.conf
```

## Safety Steps

Before disabling password authentication, I confirmed that SSH key login worked.

I opened a second SSH key session to make sure I had a backup session before changing SSH settings.

This helped prevent locking myself out of the server.

## SSH Configuration Backup

I backed up the SSH configuration with:

```bash
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup-lab09
```

## Troubleshooting PasswordAuthentication

The effective SSH configuration originally showed:

```text
passwordauthentication yes
```

I searched SSH config files with:

```bash
sudo grep -Rni "PasswordAuthentication" /etc/ssh/sshd_config /etc/ssh/sshd_config.d/
```

I found that this file was setting password authentication to yes:

```text
/etc/ssh/sshd_config.d/50-cloud-init.conf
```

## Hardening Configuration

I created a hardening file:

```text
/etc/ssh/sshd_config.d/01-home-soc-hardening.conf
```

The file contained:

```text
# Home SOC Lab SSH hardening
PasswordAuthentication no
PubkeyAuthentication yes
PermitRootLogin prohibit-password
```

## Validation

I tested SSH syntax with:

```bash
sudo sshd -t
```

No output meant the configuration syntax was valid.

I reloaded SSH:

```bash
sudo systemctl reload ssh
```

Then I checked the effective SSH configuration:

```bash
sudo sshd -T | grep -E 'passwordauthentication|pubkeyauthentication|permitrootlogin'
```

Final result:

```text
permitrootlogin prohibit-password
pubkeyauthentication yes
passwordauthentication no
```

## Login Testing

SSH key login still worked:

```powershell
ssh -i "$env:USERPROFILE\.ssh\id_ed25519" joey@127.0.0.1 -p 2222
```

Password-only login failed:

```powershell
ssh -o PreferredAuthentications=password -o PubkeyAuthentication=no joey@127.0.0.1 -p 2222
```

The result was:

```text
Permission denied (publickey)
```

This confirmed that password authentication was disabled and public key authentication still worked.

## Security Lessons Learned

- Always test SSH key login before disabling password login.
- Keep an active SSH session open when changing SSH settings.
- Open a second SSH session as a backup before making changes.
- Back up SSH configuration files before editing.
- Use `sshd -t` to test SSH syntax.
- Use `sshd -T` to check effective SSH settings.
- `PasswordAuthentication no` helps reduce brute-force password risk.
- Password-only login should fail after proper SSH hardening.
- Existing config files, such as cloud-init files, can override expected SSH settings.

## Interview Summary

In this lab, I hardened SSH by disabling password authentication after confirming SSH key authentication worked. I backed up the SSH configuration, reviewed effective settings with `sshd -T`, identified a cloud-init file setting `PasswordAuthentication yes`, created a hardening configuration file, tested syntax with `sshd -t`, reloaded SSH, verified that key login still worked, and confirmed password-only login failed with `Permission denied (publickey)`.
