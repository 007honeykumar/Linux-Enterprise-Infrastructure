# 13 · Lessons Learned

## Technical lessons

- DNS is a dependency for package repositories, websites, and many client tools.
- A service can work on `localhost` while remaining unreachable remotely.
- Name-based virtual hosting depends on DNS and the HTTP `Host` header.
- TLS certificates are public; private keys are secrets.
- Linux permissions include owner, group, mode bits, directory permissions, and service context.
- NFS and Samba solve related problems through different protocols.
- `rsync` requires both transport access and destination write permission.
- Structured configuration should always be validated before service restart.
- Service configuration must be collected from the host where the service actually runs.
- AWS networking and operating-system networking must be debugged together.

## Operational lessons

- Verify with commands, not assumptions.
- Keep architecture, configuration, screenshots, and troubleshooting records together.
- Use clear file names and a predictable repository structure.
- Never commit SSH keys, TLS private keys, passwords, or cloud credentials.
- Record limitations honestly. A partial LDAP implementation should not be presented as complete centralised authentication.

## Career value

This project demonstrates:

- Linux service administration
- AWS networking fundamentals
- DNS and web infrastructure
- File sharing and transfer
- Backup design
- Identity-service fundamentals
- Security awareness
- Evidence-based troubleshooting
- Technical documentation

## Suggested interview summary

> I built a multi-instance Linux infrastructure lab on AWS. Server1 provided BIND DNS, Apache virtual hosts with HTTPS, NFS, Samba, and a basic OpenLDAP service. Server2 provided FTP and acted as an rsync backup target. I tested from Amazon Linux, Ubuntu, and RHEL clients. The most valuable part was troubleshooting DNS reachability and forwarding, Kea configuration validation, permissions for rsync and scp, and cross-host service verification.
