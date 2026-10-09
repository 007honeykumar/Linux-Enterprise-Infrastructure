# 07 · Samba

## What Samba is

Samba implements the SMB protocol and enables file sharing for Windows and Linux clients.

## Why it was used

NFS is natural for Linux-to-Linux sharing. Samba demonstrates a cross-platform sharing method.

## Main configuration

```text
/etc/samba/smb.conf
```

Typical service workflow:

```bash
sudo dnf install -y samba samba-client
sudo testparm
sudo systemctl enable --now smb
```

A Samba user can be created with:

```bash
sudo smbpasswd -a USERNAME
```

## Testing

```bash
smbclient -L //SERVER1_PRIVATE_IP -U USERNAME
smbclient //SERVER1_PRIVATE_IP/SHARE_NAME -U USERNAME
```

## Problems encountered

The RHEL client initially could not install `samba-client` because RHUI repository hostnames failed to resolve. The actual root cause was DNS forwarding, not Samba.

## Lesson

Package installation failures can be downstream symptoms. Before blaming a package manager, verify DNS resolution and repository reachability.
