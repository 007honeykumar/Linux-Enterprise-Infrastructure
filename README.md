# Linux Enterprise Infrastructure on AWS

A hands-on Linux administration project that builds a small multi-server environment on AWS. The lab combines name resolution, virtual web hosting, TLS, network file sharing, FTP, DHCP configuration practice, backups, and a basic OpenLDAP service.

> **Project scope:** This is a learning lab, not a production deployment. OpenLDAP was installed and validated at service level; centralized PAM/SSSD login was not completed. Kea DHCP was configured for learning purposes, while AWS VPC networking normally supplies instance addressing through AWS-managed DHCP.

![Overall infrastructure](diagrams/01-overall-infrastructure.svg)

## Project objectives

- Build a multi-host Linux environment in an AWS VPC.
- Assign clear server and client roles.
- Configure BIND as an internal authoritative DNS server and forward external queries.
- Host multiple websites from one Apache server using name-based virtual hosts.
- Enable HTTPS with a self-signed lab certificate.
- Share files through NFS and Samba.
- Configure and test FTP with `vsftpd`.
- Practise Kea DHCP configuration and validation.
- Copy data between servers with `rsync` over SSH.
- Install and validate an OpenLDAP service.
- Troubleshoot networking, permissions, package repositories, service configuration, and client resolution.

## Architecture

| Host | Private IP | Operating system | Primary role |
|---|---:|---|---|
| Server1 | `172.31.36.76` | Amazon Linux | BIND, Apache, HTTPS, NFS, Samba, OpenLDAP |
| Server2 | `172.31.42.133` | Amazon Linux | FTP and rsync backup destination |
| Client1 | `172.31.41.65` | Amazon Linux | Service testing |
| Ubuntu client | `172.31.46.191` | Ubuntu | Service testing and NFS client |
| RHEL client | `172.31.45.206` | RHEL | Kea DHCP lab and service testing |

Additional diagrams:

- [DNS resolution flow](diagrams/02-dns-flow.svg)
- [Apache virtual-host flow](diagrams/03-apache-virtual-host-flow.svg)
- [NFS and Samba flow](diagrams/04-nfs-samba-flow.svg)
- [rsync backup flow](diagrams/05-rsync-backup-flow.svg)
- [Complete network architecture](diagrams/06-complete-network-architecture.svg)

## Technologies used

AWS EC2, VPC, subnets, route tables, Internet Gateway, security groups, Amazon Linux, Ubuntu, RHEL, BIND, Apache HTTP Server, OpenSSL/TLS, NFS, Samba, vsftpd, Kea DHCP, rsync, SSH, OpenLDAP, systemd, `dig`, `curl`, `smbclient`, `showmount`, and `ss`.

## Repository structure

```text
Linux-Enterprise-Infrastructure/
├── README.md
├── .gitignore
├── configs/
│   ├── apache/
│   ├── backup/
│   ├── dhcp/
│   ├── dns/
│   ├── ftp/
│   ├── ldap/
│   ├── nfs/
│   └── samba/
├── diagrams/
├── docs/
└── screenshots/
    ├── Apache/
    ├── aws/
    ├── backup/
    ├── dhcp/
    ├── dns/
    ├── FTP/
    ├── https/
    ├── ldap/
    ├── nfs/
    └── Samba/
```

## Installation summary

1. Created EC2 instances in one AWS VPC and verified routing and security-group rules.
2. Configured hostnames and private-IP communication.
3. Installed BIND on Server1 and created the `linuxlab.local` zone.
4. Configured Server1 to use local BIND first and AWS DNS as the upstream forwarder.
5. Installed Apache, created two document roots, and configured name-based virtual hosts.
6. Created a self-signed TLS certificate and enabled HTTPS.
7. Exported a directory with NFS and configured a Samba share.
8. Installed `vsftpd` on Server2 and tested an FTP user.
9. Installed and validated Kea DHCP on the RHEL host.
10. Configured `rsync` transfers from Server1 to `/backup/server1` on Server2.
11. Installed OpenLDAP server packages and validated `slapd`.

Detailed procedures are in the [`docs`](docs/) directory.

## Testing summary

| Service | Validation |
|---|---|
| DNS | `dig web1.linuxlab.local`, `dig google.com`, `systemctl status named` |
| Apache | `curl http://localhost`, `curl http://web1.linuxlab.local` |
| HTTPS | `curl -k https://web1.linuxlab.local`, `openssl s_client` |
| NFS | `showmount -e`, mount test, file visibility |
| Samba | `smbclient -L`, share access |
| FTP | `lftp` login and file creation |
| DHCP | Kea syntax validation, service status, UDP 67 listener |
| Backup | `rsync` completion and files present on Server2 |
| LDAP | `systemctl status slapd`, TCP 389 listener, `ldapsearch` service response |

## Key troubleshooting outcomes

- Fixed client DNS timeouts by checking port 53 traffic, security rules, and resolver configuration.
- Made Server1 query local BIND before the AWS resolver.
- Corrected Apache paths and virtual-host configuration.
- Verified that the TLS certificate matched its private key.
- Resolved RHEL repository failures caused by DNS forwarding.
- Corrected Kea JSON content and added the required subnet `id`.
- Fixed rsync destination ownership and temporary-file permission failures.
- Fixed `scp` downloads by staging root-owned files under `/home/ec2-user` and changing ownership.
- Learned that Windows paths must be used from PowerShell, not from an EC2 shell.

See [Troubleshooting](docs/12-troubleshooting.md) for the full record.

## Security notes

Never commit:

- `.pem` SSH keys
- TLS private keys such as `server.key`
- AWS credentials
- passwords, tokens, or private user keys

The included `.gitignore` blocks common secret-file patterns. Always inspect staged files with `git status` before committing.

## Future improvements

- Deploy workloads across at least two Availability Zones.
- Replace self-signed TLS with a trusted certificate and automated renewal.
- Complete LDAP directory design, users/groups, SSSD, NSS, and PAM integration.
- Automate configuration with Ansible.
- Add monitoring, centralized logs, backups with retention, and recovery testing.
- Use private subnets and a bastion host or AWS Systems Manager.
- Restrict security-group rules to required source ranges.
- Replace the `.local` lab zone with a subdomain that does not conflict with mDNS.
- Add Infrastructure as Code using Terraform or CloudFormation.

## Documentation index

1. [Project overview](docs/01-project-overview.md)
2. [Architecture](docs/02-architecture.md)
3. [DNS](docs/03-dns.md)
4. [Apache](docs/04-apache.md)
5. [HTTPS](docs/05-https.md)
6. [NFS](docs/06-nfs.md)
7. [Samba](docs/07-samba.md)
8. [FTP](docs/08-ftp.md)
9. [DHCP](docs/09-dhcp.md)
10. [Backup](docs/10-backup.md)
11. [LDAP](docs/11-ldap.md)
12. [Troubleshooting](docs/12-troubleshooting.md)
13. [Lessons learned](docs/13-lessons-learned.md)
