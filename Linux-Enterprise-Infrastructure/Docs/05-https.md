# 05 · HTTPS and TLS

## What HTTPS is

HTTPS is HTTP protected by TLS. TLS encrypts traffic and lets a client validate the server certificate.

## Why it was used

The lab demonstrated:

- A certificate and private key
- Apache TLS configuration
- Port 443
- Certificate inspection
- The difference between encryption and certificate trust

## Main files

```text
/etc/httpd/conf.d/ssl.conf
/etc/pki/tls/certs/server.crt
/etc/pki/tls/private/server.key
```

The private key must never be committed to GitHub.

## Certificate validation

The certificate and private key were compared through their public modulus hashes:

```bash
openssl x509 -noout -modulus \
  -in /etc/pki/tls/certs/server.crt | openssl sha256

openssl rsa -noout -modulus \
  -in /etc/pki/tls/private/server.key | openssl sha256
```

Matching hashes confirmed that the certificate belonged to the private key.

## Testing

```bash
sudo httpd -t
sudo systemctl restart httpd
curl -k https://web1.linuxlab.local
openssl s_client -connect web1.linuxlab.local:443
ss -lntp | grep ':443'
```

`curl -k` was required because the certificate was self-signed and not trusted by the client.

## Problems encountered

- Wrong Apache configuration path
- Permission errors while editing system files
- Need to distinguish public certificate files from secret private keys

## Lesson

A self-signed certificate can provide encryption, but it does not provide public trust. Production services should use a certificate issued by a trusted CA and should automate renewal.
