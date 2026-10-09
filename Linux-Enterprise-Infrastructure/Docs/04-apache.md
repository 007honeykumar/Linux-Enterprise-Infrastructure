# 04 · Apache Virtual Hosts

![Apache flow](../diagrams/03-apache-virtual-host-flow.svg)

## What Apache is

Apache HTTP Server accepts web requests and returns content. Name-based virtual hosting allows one server IP to host multiple websites.

## Why it was used

The project hosted:

```text
web1.linuxlab.local
web2.linuxlab.local
```

Both names resolved to Server1, but Apache selected different document roots by reading the HTTP `Host` header.

## Files and directories

```text
/etc/httpd/conf/httpd.conf
/etc/httpd/conf.d/linuxlab.conf
/var/www/web1
/var/www/web2
```

Conceptual configuration:

```apache
<VirtualHost *:80>
    ServerName web1.linuxlab.local
    DocumentRoot /var/www/web1
</VirtualHost>

<VirtualHost *:80>
    ServerName web2.linuxlab.local
    DocumentRoot /var/www/web2
</VirtualHost>
```

## Installation and service control

```bash
sudo dnf install -y httpd
sudo systemctl enable --now httpd
sudo httpd -t
sudo httpd -S
```

On Amazon Linux, `httpd -S` was the appropriate virtual-host inspection command.

## Testing

```bash
curl http://localhost
curl http://web1.linuxlab.local
curl http://web2.linuxlab.local
systemctl status httpd
```

Each hostname should return content from its own document root.

## Problems encountered

- An incorrect Apache path was used: `/etc/httpd.conf.d/` instead of `/etc/httpd/conf.d/`.
- File editing failed because the path was wrong or the operation required root privileges.
- Website access from clients depended on DNS and security-group access, even when `curl localhost` worked.

## Lesson

Local success proves only that Apache is running. End-to-end success also requires DNS, network access, virtual-host matching, filesystem permissions, and correct content paths.
