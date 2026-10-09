# 06 · NFS

![NFS and Samba flow](../diagrams/04-nfs-samba-flow.svg)

## What NFS is

NFS allows Linux and Unix systems to mount a remote directory as part of the local filesystem.

## Why it was used

The project needed Linux-native shared storage between Server1 and client machines.

## Configuration

Main file:

```text
/etc/exports
```

Typical workflow:

```bash
sudo dnf install -y nfs-utils
sudo systemctl enable --now nfs-server
sudo exportfs -rav
sudo exportfs -v
```

## Client testing

```bash
showmount -e SERVER1_PRIVATE_IP
sudo mkdir -p /mnt/nfs-share
sudo mount SERVER1_PRIVATE_IP:/EXPORTED_PATH /mnt/nfs-share
mount | grep nfs
ls -la /mnt/nfs-share
```

A file created on one side should become visible on the other.

## Problems encountered

`showmount` was initially unavailable on a client. Installing the correct NFS client utilities provided the command.

## Lesson

NFS troubleshooting spans package availability, exports, service status, network ports, mount syntax, and filesystem permissions.
