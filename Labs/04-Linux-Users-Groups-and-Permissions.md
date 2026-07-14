# Lab 04 – Linux Users, Groups, and Permissions

## Objective

Learn how Linux uses users, groups, sudo privileges, and file permissions to control access to files, directories, and administrative commands.

## Key Concepts

Linux access control is based on:

- Users
- Groups
- File owners
- Group owners
- Permission bits
- Sudo privileges

## Commands Practiced

```bash
whoami
id
groups
getent passwd joey
getent group sudo
ls -l /etc/passwd /etc/shadow
sudo -l
mkdir -p ~/permissions-lab
cd ~/permissions-lab
touch notes.txt
echo "This is a permissions test file." > notes.txt
ls -l
chmod 600 notes.txt
chmod 644 notes.txt
mkdir private
chmod 700 private
echo "secret lab note" > private/secret.txt
chmod 600 private/secret.txt
ls -ld private
ls -l private/secret.txt
```

## User and Group Discovery

I used:

```bash
whoami
```

to identify my current user.

I used:

```bash
id
```

to view my UID, GID, and group memberships.

My user belonged to the `sudo` group, which means I can run administrative commands using `sudo`.

Example:

```text
uid=1000(joey) gid=1000(joey) groups=1000(joey),27(sudo)
```

## `/etc/passwd` and `/etc/shadow`

I inspected my user account with:

```bash
getent passwd joey
```

Example output:

```text
joey:x:1000:1000:Joey Cecil:/home/joey:/bin/bash
```

This shows:

- Username
- UID
- GID
- Full name/comment field
- Home directory
- Login shell

The `x` means the password hash is not stored in `/etc/passwd`.

I compared permissions on `/etc/passwd` and `/etc/shadow`:

```bash
ls -l /etc/passwd /etc/shadow
```

`/etc/passwd` is readable by normal users because it stores account metadata.

`/etc/shadow` is restricted because it stores protected password hashes.

## Sudo Privileges

I checked sudo privileges using:

```bash
sudo -l
```

The output included:

```text
(ALL : ALL) ALL
```

This means my user can run any command as any user or group through `sudo`.

## File Permissions

I created a test file:

```bash
touch notes.txt
echo "This is a permissions test file." > notes.txt
```

The default permission looked like:

```text
-rw-rw-r--
```

This means:

- Owner can read and write
- Group can read and write
- Others can read

I changed the file to private:

```bash
chmod 600 notes.txt
```

Result:

```text
-rw-------
```

This means only the owner can read and write the file.

Then I changed it to:

```bash
chmod 644 notes.txt
```

Result:

```text
-rw-r--r--
```

This means:

- Owner can read and write
- Group can read
- Others can read

## Directory Permissions

I created a private directory:

```bash
mkdir private
chmod 700 private
```

Result:

```text
drwx------
```

For directories:

- `r` means list contents
- `w` means create or delete files inside
- `x` means enter or traverse the directory

`chmod 700` means only the owner can read, write, and enter the directory.

## Sensitive File Practice

Inside the private directory, I created:

```bash
echo "secret lab note" > private/secret.txt
```

Then I locked it down:

```bash
chmod 600 private/secret.txt
```

Result:

```text
-rw-------
```

This means only the owner can read and write the file.

## Security Lessons Learned

- `/etc/passwd` stores account metadata.
- `/etc/shadow` stores protected password hashes.
- Users are identified by UID.
- Groups are identified by GID.
- Membership in the `sudo` group gives administrative capability.
- `chmod 600` is useful for sensitive files.
- `chmod 700` is useful for private directories.
- File and directory permissions are separate.
- Directory execute permission means the ability to enter or traverse the directory.
- Poor permissions can expose sensitive data.

## Interview Summary

In this lab, I inspected Linux users, groups, sudo privileges, and file permissions. I used `whoami`, `id`, `groups`, `getent`, `sudo -l`, and `ls -l` to understand account and permission information. I practiced using `chmod` to restrict access to files and directories, including setting a private directory to `700` and a sensitive file to `600`.
