# 11 · OpenLDAP

## What LDAP is

LDAP is a protocol for accessing directory information such as users, groups, and organisational data.

## Why it was used

The project introduced the foundation of centralised identity management.

## Implemented scope

Completed:

- Installed OpenLDAP server and client packages
- Started and enabled `slapd`
- Verified that port 389 was listening
- Confirmed that an LDAP query reached the server

Not completed:

- Base DN and directory information tree
- LDAP users and groups
- TLS for LDAP
- SSSD client integration
- PAM/NSS login integration

Therefore, this project should describe LDAP as **basic service installation and validation**, not complete central authentication.

## Main configuration

Depending on the package, configuration was available under:

```text
/etc/openldap/slapd.conf
/etc/openldap/slapd.d/
```

## Installation and testing

```bash
sudo dnf install -y openldap-servers openldap-clients
sudo systemctl enable --now slapd
systemctl status slapd
ss -lntp | grep ':389'
ldapsearch -x -LLL -H ldap://localhost -b dc=example,dc=com
```

The query returned:

```text
No such object (32)
```

This proved that the server responded, while also showing that the requested directory tree had not been created.

## Lesson

A listening LDAP daemon is only the starting point. A usable identity platform also needs schema planning, a directory tree, access controls, users/groups, TLS, and client authentication integration.
