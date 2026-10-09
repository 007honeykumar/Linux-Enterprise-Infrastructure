# 09 · Kea DHCP

## What DHCP is

DHCP supplies network configuration such as IP address, subnet mask, gateway, and DNS servers.

## Why it was used

Kea was installed to practise modern DHCP configuration and service validation.

> AWS normally provides EC2 addressing through AWS-managed VPC DHCP behaviour and DHCP option sets. The Kea service in this project was a learning exercise, not a replacement for AWS VPC address assignment.

## Server location

Kea was configured on the RHEL host:

```text
RHEL client · 172.31.45.206
```

Main file:

```text
/etc/kea/kea-dhcp4.conf
```

## Validation and service control

```bash
sudo kea-dhcp4 -t /etc/kea/kea-dhcp4.conf
sudo systemctl enable --now kea-dhcp4
systemctl status kea-dhcp4
ss -lunp | grep ':67'
```

## Problems encountered

### Suspended package operations

Pressing `Ctrl+Z` suspended `dnf` and `systemctl` processes instead of stopping them.

Useful commands:

```bash
jobs
kill %1
```

### Shell text pasted into JSON

The configuration accidentally contained:

```text
sudo tee ...
EOF
```

Those are shell heredoc wrapper lines, not JSON configuration, so they had to be removed.

### Missing subnet identifier

Kea 3.x validation reported:

```text
missing parameter 'id'
```

The subnet entry required:

```json
"id": 1
```

## Lesson

Always validate structured configuration before restarting a service. Syntax checks turn a vague service failure into a precise line-level problem.
