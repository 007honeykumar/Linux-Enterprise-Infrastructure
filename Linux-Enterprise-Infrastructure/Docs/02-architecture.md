# 02 · Architecture

![Complete network architecture](../diagrams/06-complete-network-architecture.svg)

## AWS layer

The environment used an AWS VPC with CIDR `172.31.0.0/16`, an Internet Gateway, a route table, a public subnet, and security groups. EC2 instances communicated through private addresses inside the VPC.

## Host responsibilities

### Server1

Private IP: `172.31.36.76`

Services:

- BIND DNS
- Apache HTTP Server
- HTTPS/TLS
- NFS
- Samba
- OpenLDAP

Server1 became the central infrastructure node. This simplified the lab, though production systems would normally separate critical services and add redundancy.

### Server2

Private IP: `172.31.42.133`

Services:

- vsftpd
- rsync backup destination

Separating the backup destination from Server1 demonstrates why backups should not live only on the source system.

### Clients

- Amazon Linux client: `172.31.41.65`
- Ubuntu client: `172.31.46.191`
- RHEL client: `172.31.45.206`

Clients verified service behaviour from outside each server. This matters because a service can succeed locally while still being unreachable over the network.

## Traffic flow

- Clients sent internal DNS queries to Server1 on port 53.
- BIND answered `linuxlab.local` records directly.
- External queries were forwarded to the AWS VPC resolver at `172.31.0.2`.
- HTTP and HTTPS requests reached Apache on ports 80 and 443.
- NFS and SMB traffic reached Server1.
- FTP traffic reached Server2.
- rsync used SSH from Server1 to Server2.
- LDAP listened on TCP 389.

## Design limitations

All systems were part of a training environment. A production design should use multiple Availability Zones, private subnets for internal services, stricter security groups, resilient DNS, managed certificates, monitoring, and automated recovery.
