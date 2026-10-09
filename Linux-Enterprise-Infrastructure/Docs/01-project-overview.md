# 01 · Project Overview

## What this project is

This project recreates a compact enterprise-style Linux environment on AWS. Multiple EC2 instances perform separate infrastructure roles instead of placing every service on one machine.

## Why it exists

A Linux administrator or DevOps engineer must understand how services interact across hosts. Installing packages is only one layer. The real learning comes from DNS dependencies, ports, routing, permissions, service users, logs, client configuration, and verification.

## Business-style scenario

A small organisation needs:

- Internal host-name resolution
- Two internal websites
- Encrypted web access
- Linux and cross-platform file sharing
- File transfer
- A secondary backup destination
- A directory service foundation
- Repeatable testing and troubleshooting evidence

## Scope

Implemented and tested:

- BIND authoritative DNS and recursive forwarding
- Apache name-based virtual hosts
- HTTPS using a self-signed lab certificate
- NFS and Samba shares
- vsftpd
- Kea DHCP configuration practice
- rsync over SSH
- Basic OpenLDAP installation and service validation

Not completed:

- Production high availability
- Trusted public TLS
- Full LDAP login integration through SSSD/PAM
- Automated deployment
- Monitoring and alerting

## Outcome

The lab demonstrates both service configuration and real troubleshooting. The documentation deliberately records failures and fixes because those are the strongest evidence of operational understanding.
