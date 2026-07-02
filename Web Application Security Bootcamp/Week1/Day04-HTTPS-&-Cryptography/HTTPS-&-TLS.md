# 09 - HTTPS & TLS

## Learning Objectives

After completing this chapter, you will understand:

- Why HTTP is insecure
- What HTTPS is
- Difference between HTTP and HTTPS
- TLS vs SSL
- Digital Certificates
- Certificate Authorities (CA)
- Public Key Infrastructure (PKI)

---

# Why HTTP is Insecure

HTTP transmits data in plain text.

Example:

```
POST /login

username=aditya
password=MyPassword123
```

Anyone monitoring the network can read the data.

Examples:

- Public Wi-Fi attacker
- ISP
- Malicious Router
- Man-in-the-Middle (MITM)

---

# Problems with HTTP

## 1. No Confidentiality

Data is readable by anyone intercepting the traffic.

Example:

```
Browser ---- HTTP ----> Server
            ^
            |
        Attacker
```

---

## 2. No Integrity

An attacker can modify data during transmission.

Example:

```
salary.pdf

↓

virus.exe
```

The browser cannot detect the modification.

---

## 3. No Authentication

HTTP provides no way to verify the identity of the server.

Example:

```
Browser

↓

"Is this really github.com?"
```

HTTP has no mechanism to answer this.

---

# HTTPS

HTTPS = HTTP + TLS

HTTP handles:

- GET
- POST
- PUT
- DELETE
- Cookies
- Headers

TLS provides:

- Confidentiality
- Integrity
- Authentication

---

# Protocol Stack

```
Application
    │
 HTTP
    │
 TLS
    │
 TCP
    │
 IP
```

---

# TLS

TLS (Transport Layer Security) protects data while it travels across the network.

Responsibilities:

- Encrypt communication
- Verify server identity
- Detect tampering

---

# SSL vs TLS

| SSL | TLS |
|------|------|
| Old | Modern |
| Insecure | Secure |

Versions:

```
SSL 2.0 ❌
SSL 3.0 ❌
TLS 1.0 ❌
TLS 1.1 ❌
TLS 1.2 ✅
TLS 1.3 ✅
```

Today, "SSL Certificate" actually refers to a TLS certificate.

---

# Digital Certificate

A Digital Certificate is the identity card of a website.

Contains:

- Domain Name
- Organization
- Public Key
- Validity Period
- Issuer
- Digital Signature

Example:

```
Domain:
github.com

Public Key:
ABC123

Issuer:
DigiCert

Valid:
2026 - 2027
```

---

# Certificate Authority (CA)

A trusted organization that verifies website ownership and issues certificates.

Examples:

- Let's Encrypt
- DigiCert
- GlobalSign
- Sectigo

Responsibilities:

- Verify identity
- Issue certificates
- Digitally sign certificates

---

# Certificate Signing Process

```
Website

↓

Generate Key Pair

↓

Create CSR

↓

Certificate Authority

↓

Verify Domain

↓

Sign Certificate

↓

Return Certificate
```

---

# What is a Digital Signature?

A Digital Signature is a cryptographic signature created by signing the hash of a certificate using the CA's private key.

Formula:

```
Digital Signature

=

Sign(

Hash(Certificate),

CA Private Key

)
```

Provides:

- Authentication
- Integrity

---

# Certificate Verification

Browser receives:

- Certificate
- Digital Signature

Browser:

1. Hashes the certificate.
2. Uses the CA Public Key to verify the signature.
3. Compares hashes.

If hashes match:

✅ Certificate is genuine

Otherwise:

❌ Connection rejected

---

# Browser Trust Store

Operating systems and browsers already contain trusted CA public keys.

Examples:

- DigiCert
- Let's Encrypt
- Google Trust Services

---

# PKI (Public Key Infrastructure)

PKI is the complete ecosystem that enables trust on the Internet.

Includes:

- Public Keys
- Private Keys
- Certificates
- Certificate Authorities
- Root Certificates
- Certificate Validation
- Certificate Revocation

---

# Key Takeaways

- HTTP is insecure.
- HTTPS = HTTP + TLS.
- Certificates identify websites.
- CA verifies website identity.
- Digital Signatures protect certificates.
- Browser trusts certificates using trusted CA public keys.