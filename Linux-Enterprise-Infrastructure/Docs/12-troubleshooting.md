# 12 · Troubleshooting Record

This document captures the most valuable operational lessons from the project.

## 1. DNS worked locally but not from clients

**Symptom**

```text
dig timed out
```

**Diagnosis**

- Checked `named`
- Verified TCP and UDP port 53 listeners
- Tested `dig @127.0.0.1`
- Tested direct client queries
- Checked security-group/NACL rules
- Used packet capture to see whether queries arrived

**Root cause**

Network access and resolver-selection problems.

**Fix**

Allowed required DNS traffic and configured clients/Server1 to use the intended resolver.

---

## 2. Server1 bypassed local BIND

**Symptom**

Normal `dig` used the AWS resolver instead of `127.0.0.1`.

**Root cause**

The system resolver configuration still preferred `172.31.0.2`.

**Fix**

Configured `systemd-resolved` with local DNS first and AWS DNS as fallback.

---

## 3. RHEL repository hostnames failed

**Symptom**

`dnf` could not resolve RHUI repository names.

**Root cause**

Broken external DNS forwarding.

**Fix**

Corrected BIND forwarding and resolver behaviour before retrying package installation.

---

## 4. Apache configuration path was wrong

**Symptom**

The editor reported that it could not write the file.

**Root cause**

Used:

```text
/etc/httpd.conf.d/ssl.conf
```

instead of:

```text
/etc/httpd/conf.d/ssl.conf
```

**Fix**

Corrected the path and used appropriate privileges.

---

## 5. HTTPS certificate uncertainty

**Symptom**

Needed to verify whether the certificate and private key matched.

**Fix**

Compared OpenSSL modulus hashes. Matching results confirmed the pair.

---

## 6. NFS client command missing

**Symptom**

`showmount: command not found`

**Root cause**

NFS client utilities were not installed.

**Fix**

Installed the appropriate client package and repeated `showmount -e`.

---

## 7. FTP configuration copied from the wrong host

**Symptom**

```text
cp: cannot stat '/etc/vsftpd/vsftpd.conf'
```

**Root cause**

The command was run on Server1 or Ubuntu, but FTP was installed on Server2.

**Fix**

Connected to Server2 and copied the file there.

---

## 8. Kea configuration failed validation

**Symptoms**

- Shell heredoc lines appeared inside the JSON file
- `missing parameter 'id'`

**Root causes**

- Wrapper commands were accidentally pasted into the configuration
- Kea required an explicit subnet ID

**Fix**

Removed shell-only lines and added:

```json
"id": 1
```

---

## 9. Commands were suspended accidentally

**Symptom**

`dnf` and service commands appeared to hang after `Ctrl+Z`.

**Root cause**

`Ctrl+Z` sends a terminal stop signal and leaves the process suspended.

**Fix**

Used:

```bash
jobs
kill %JOB_NUMBER
```

Use `Ctrl+C` when the intention is to interrupt a foreground command.

---

## 10. rsync permission denied

**Symptoms**

```text
chgrp failed
mkstemp Permission denied
```

**Root cause**

The remote backup directory was owned by a different user.

**Fix**

```bash
sudo chown -R ec2-user:ec2-user /backup/server1
```

---

## 11. scp used from the wrong operating system

**Symptom**

```text
Could not resolve hostname c
```

**Root cause**

A Windows path such as `C:\Users\...` was used inside an EC2 Linux shell. Linux interpreted `C:` as part of a remote-host expression.

**Fix**

Run Windows-to-EC2 `scp` commands from Windows PowerShell.

---

## 12. scp could not read a staged file

**Symptom**

```text
remote open "named.conf": Permission denied
```

**Root cause**

The file in `/home/ec2-user` had been copied with `sudo` and remained owned by root or lacked read permission.

**Fix**

```bash
sudo chown ec2-user:ec2-user /home/ec2-user/named.conf
chmod 600 /home/ec2-user/named.conf
```

Then repeat `scp` from PowerShell.

---

## Troubleshooting method learned

Use the same sequence for future incidents:

```text
1. Identify the exact symptom.
2. Confirm the service package and host.
3. Validate configuration syntax.
4. Check service status and logs.
5. Confirm listening ports.
6. Test locally.
7. Test over the private network.
8. Check DNS, routing, firewalls, and permissions.
9. Change one variable at a time.
10. Re-test and document the result.
```
