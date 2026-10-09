# 03 · DNS with BIND

![DNS flow](../diagrams/02-dns-flow.svg)

## What DNS is

DNS converts names into IP addresses. Without it, users and applications would need to remember addresses such as `172.31.36.76`.

## Why it was used

The project needed friendly internal names:

- `server1.linuxlab.local`
- `server2.linuxlab.local`
- `client1.linuxlab.local`
- `ubuntu-client.linuxlab.local`
- `rhel-client.linuxlab.local`
- `web1.linuxlab.local`
- `web2.linuxlab.local`
- `files.linuxlab.local`

## Main configuration

Files:

```text
/etc/named.conf
/var/named/linuxlab.local.zone
```

Important design:

- BIND is authoritative for `linuxlab.local`.
- Queries are permitted from the lab network.
- External queries are forwarded to `172.31.0.2`.
- Server1 uses `127.0.0.1` as its preferred resolver.

Example validation:

```bash
sudo named-checkconf
sudo named-checkzone linuxlab.local /var/named/linuxlab.local.zone
sudo systemctl restart named
systemctl status named
```

## Testing

```bash
dig @127.0.0.1 web1.linuxlab.local
dig web1.linuxlab.local
dig google.com
ss -lntup | grep ':53'
```

Expected internal result:

```text
web1.linuxlab.local.  IN  A  172.31.36.76
```

## Problems encountered

### Clients timed out

Symptoms:

```text
communications error
connection timed out
```

Investigation included:

```bash
ss -lntup | grep ':53'
sudo tcpdump -ni any port 53
nc -vz SERVER1_PRIVATE_IP 53
dig @SERVER1_PRIVATE_IP web1.linuxlab.local
```

Root causes included network access to TCP/UDP 53 and resolver configuration.

### Server1 used the AWS resolver directly

`/etc/resolv.conf` still pointed to `172.31.0.2`, so normal queries bypassed local BIND.

The resolver was corrected through `systemd-resolved`:

```ini
[Resolve]
DNS=127.0.0.1
FallbackDNS=172.31.0.2
Domains=linuxlab.local
```

After restarting `systemd-resolved`, `dig` showed:

```text
SERVER: 127.0.0.1#53
```

## Lesson

A running DNS service is not enough. You must verify four layers:

1. BIND configuration
2. Listener on port 53
3. Network access
4. Client resolver selection

> Note: `.local` is commonly reserved for multicast DNS. It was acceptable for this lab, but a production environment should use a controlled subdomain such as `lab.example.com`.
