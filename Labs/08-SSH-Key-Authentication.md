# Lab 08 – SSH Key Authentication and Hardening Part 2

## Objective

Configure SSH key-based authentication between a Windows host and an Ubuntu Server VM, verify key-based login, and review SSH logs to confirm successful public key authentication.

## Key Concepts

SSH key authentication uses a public/private key pair.

- The private key stays on the client machine and must be protected.
- The public key is copied to the server.
- The server stores trusted public keys in `~/.ssh/authorized_keys`.
- SSH is strict about file permissions for `.ssh` and `authorized_keys`.

## Commands Practiced on Windows PowerShell

```powershell
dir $env:USERPROFILE\.ssh
ssh-keygen -t ed25519 -C "joey-home-soc-lab"
type $env:USERPROFILE\.ssh\id_ed25519.pub | ssh joey@127.0.0.1 -p 2222 "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys"
ssh -i "$env:USERPROFILE\.ssh\id_ed25519" joey@127.0.0.1 -p 2222
```

## Commands Practiced on Ubuntu

```bash
ls -ld ~/.ssh
ls -l ~/.ssh/authorized_keys
sudo journalctl -u ssh -n 30 --no-pager
```

## Important Files

Windows private key:

```text
C:\Users\cecil\.ssh\id_ed25519
```

Windows public key:

```text
C:\Users\cecil\.ssh\id_ed25519.pub
```

Ubuntu authorized keys file:

```text
/home/joey/.ssh/authorized_keys
```

## SSH Key Setup

I generated an ED25519 SSH key pair on my Windows host using:

```powershell
ssh-keygen -t ed25519 -C "joey-home-soc-lab"
```

This created:

```text
id_ed25519
id_ed25519.pub
```

The private key stays on Windows and should not be shared.

The public key was copied to the Ubuntu server’s `authorized_keys` file.

## Permissions

I confirmed the `.ssh` directory and `authorized_keys` file had secure permissions:

```text
~/.ssh              drwx------
authorized_keys     -rw-------
```

These permissions mean only the user can access the SSH directory and authorized keys file.

## SSH Log Verification

I checked SSH logs using:

```bash
sudo journalctl -u ssh -n 30 --no-pager
```

The logs showed:

```text
Accepted publickey for joey from 10.0.2.2
```

This confirmed that key-based SSH authentication worked.

## Security Lessons Learned

- SSH keys are stronger than password-only login.
- The private key must never be shared.
- The public key can be copied to the server.
- `authorized_keys` controls which public keys are allowed to log in.
- SSH logs show whether login used password authentication or public key authentication.
- Password login should not be disabled until key login is tested and confirmed.
- File permissions matter because SSH may reject keys if permissions are too open.

## Interview Summary

In this lab, I generated an ED25519 SSH key pair on my Windows host, copied the public key to my Ubuntu server, configured secure permissions on the `.ssh` directory and `authorized_keys` file, tested SSH key-based login, and verified the result in SSH logs showing `Accepted publickey` for my user.
