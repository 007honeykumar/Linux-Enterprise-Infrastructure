# 08 · FTP with vsftpd

## What FTP is

FTP transfers files between a client and server. `vsftpd` is a lightweight FTP server.

## Why it was used

The lab demonstrated a dedicated file-transfer service on Server2 and the relationship between service accounts, home directories, permissions, and client access.

## Server location

FTP was configured on:

```text
Server2 · 172.31.42.133
```

Main file:

```text
/etc/vsftpd/vsftpd.conf
```

## Installation and service control

```bash
sudo dnf install -y vsftpd
sudo systemctl enable --now vsftpd
systemctl status vsftpd
```

## User and file access

A local FTP account was used for testing. Directory ownership and write permissions had to allow that account to create files.

## Client testing

Amazon Linux 2023 did not provide the old `ftp` client package in the expected way, so `lftp` was used:

```bash
sudo dnf install -y lftp
lftp ftpuser@SERVER2_PRIVATE_IP
```

Inside `lftp`:

```text
ls
put test-file.txt
bye
```

## Problems encountered

- The configuration file was searched for on Server1 and Ubuntu, where `vsftpd` was not installed.
- File creation failed until directory ownership and permissions were corrected.
- A privileged redirection such as `sudo echo > file` does not make the shell redirection privileged. `sudo tee` is a safer pattern.

## Lesson

A path such as `/etc/vsftpd/vsftpd.conf` exists only on the host where the package is installed. Always map each service to its actual server before collecting configuration files.
