# 10 - SSL/TLS Handshake

## Learning Objectives

After completing this chapter, you will understand:

- What an SSL/TLS Handshake is
- Why the handshake is required
- How a browser authenticates a server
- What happens in Client Hello and Server Hello
- How certificates are verified
- How a shared secret is generated
- What ECDHE is
- What HKDF is
- What a Session Key is
- Why TLS uses AES after the handshake
- What Forward Secrecy is

---

# What is an SSL/TLS Handshake?

The SSL/TLS Handshake is the process that occurs **before any HTTP data is exchanged**.

Its purpose is to:

- Authenticate the server
- Negotiate the TLS version
- Agree on cryptographic algorithms
- Generate a shared secret
- Create session keys
- Establish a secure encrypted connection

Only after a successful handshake does HTTPS communication begin.

---

# Why Do We Need a Handshake?

Suppose your browser wants to communicate securely with:

```
https://github.com
```

Questions that must be answered first:

- Is this really GitHub?
- Which TLS version should be used?
- Which encryption algorithm should be used?
- How can both sides create the same secret key?
- How can this happen without sending the secret key over the Internet?

The TLS Handshake answers all of these questions.

---

# Where Does the Handshake Occur?

```
Browser
    │
    │ HTTPS Request
    ▼
Server
```

Before any HTTP request is sent:

```
Browser
      │
      │ TLS Handshake
      ▼
Server
```

After the handshake completes:

```
Browser
      │
Encrypted HTTP Request
      ▼
Server
```

> **Important:** No HTTP request or response is transmitted until the TLS Handshake is successfully completed.

---

# Why Not Encrypt Everything Using RSA?

Public-key cryptography (RSA/ECC) is computationally expensive.

Encrypting every HTTP request using RSA would be extremely slow.

Instead TLS uses:

```
Public Key Cryptography
        │
        ▼
Generate Shared Secret
        │
        ▼
Symmetric Encryption (AES)
        │
        ▼
Encrypt All HTTP Data
```

This provides both security and high performance.

---

# Session Key

A Session Key is a temporary symmetric key used to encrypt communication after the handshake.

Properties:

- Randomly generated
- Shared only by browser and server
- Valid only for one connection
- Used by AES or ChaCha20

Example:

```
Browser
Session Key = X7A92

Server
Session Key = X7A92
```

Both sides possess the same key without ever sending it directly.

---

# TLS Handshake Overview

```
Browser
    │
    │ Client Hello
    ▼
Server
    │
    │ Server Hello
    ▼
Browser
    │
    │ Certificate
    ▼
Browser
    │
    │ Verify Certificate
    ▼
Browser & Server
    │
    │ ECDHE Key Exchange
    ▼
Shared Secret
    │
    ▼
HKDF
    │
    ▼
Session Keys
    │
    ▼
Encrypted Communication
```

---

# Step 1 - Client Hello

The browser starts the handshake.

```
Browser
      │
Client Hello
      ▼
Server
```

The Client Hello contains:

- Supported TLS Versions
- Supported Cipher Suites
- Client Random Number
- Extensions (SNI, ALPN, etc.)

Example:

```
TLS Versions

TLS 1.2
TLS 1.3

Cipher Suites

AES-256-GCM
ChaCha20

Client Random

A82F91...
```

Purpose:

The browser tells the server what security options it supports.

---

# Step 2 - Server Hello

The server chooses one of the browser's supported configurations.

```
Server
      │
Server Hello
      ▼
Browser
```

Contains:

- Selected TLS Version
- Selected Cipher Suite
- Server Random Number

Example:

```
TLS Version

TLS 1.3

Cipher Suite

AES-256-GCM

Server Random

83AD91...
```

Purpose:

Both browser and server now agree on the cryptographic algorithms.

---

# Step 3 - Server Sends Certificate

The server now proves its identity.

```
Server
      │
Certificate
      ▼
Browser
```

Certificate contains:

- Domain Name
- Server Public Key
- Issuer
- Validity Period
- Digital Signature

Example:

```
Domain

github.com

Public Key

ABC123

Issuer

DigiCert

Digital Signature
```

Purpose:

Allow the browser to verify that it is communicating with the genuine server.

---

# Step 4 - Browser Verifies the Certificate

The browser performs several checks.

### 1. Is the certificate expired?

```
Current Date

↓

Before Expiry?

YES
```

---

### 2. Does the domain match?

```
Certificate

github.com

↓

Requested URL

github.com

↓

Match?
```

---

### 3. Is the issuing CA trusted?

The browser checks its Trusted Root Store.

Examples:

- DigiCert
- Let's Encrypt
- GlobalSign

---

### 4. Is the Digital Signature valid?

Browser:

```
Certificate

↓

Hash
```

Browser then verifies the Digital Signature using the CA's public key.

If:

```
Browser Hash

==

Recovered Hash
```

The certificate is trusted.

Otherwise:

```
Connection Rejected
```

---

# Step 5 - ECDHE Key Exchange

After authentication, browser and server must create the same secret key.

They use:

**ECDHE**

(Elliptic Curve Diffie-Hellman Ephemeral)

Purpose:

Generate a shared secret without transmitting it.

---

## Simplified Flow

Browser:

```
Private Secret = A
```

Server:

```
Private Secret = B
```

Browser sends:

```
Public Value
```

Server sends:

```
Public Value
```

Using mathematics:

Browser computes:

```
Shared Secret
```

Server computes:

```
Shared Secret
```

Both obtain the same value.

The shared secret never travels over the network.

---

# Why Can't an Attacker Calculate It?

The attacker can observe:

- Public Values
- TLS Version
- Cipher Suite

But cannot see:

- Browser's Private Secret
- Server's Private Secret

Without those secrets, computing the shared secret is computationally infeasible.

---

# Shared Secret

The shared secret is known only by:

- Browser
- Server

Example:

```
Browser

Shared Secret = 82736

Server

Shared Secret = 82736
```

---

# HKDF (HMAC-based Key Derivation Function)

The shared secret is not directly used for encryption.

Instead, TLS passes it through HKDF.

Purpose:

Convert the shared secret into cryptographically strong session keys.

Flow:

```
Shared Secret

↓

HKDF

↓

Encryption Key

↓

Handshake Keys

↓

Application Keys
```

Think of HKDF as a **Key Factory**.

One shared secret becomes multiple secure keys.

---

# Session Keys

After HKDF, browser and server possess identical session keys.

Example:

```
Browser

AES Key

↓

Encryption
```

```
Server

AES Key

↓

Decryption
```

These keys are used only for the current TLS session.

---

# Step 6 - Encrypted Communication Begins

Once the session key is available:

```
Browser

↓

AES Encrypt

↓

Encrypted HTTP Request

↓

Server

↓

AES Decrypt
```

All subsequent HTTP requests and responses are encrypted.

---

# Forward Secrecy

TLS 1.3 uses **Ephemeral** keys.

"Ephemeral" means temporary.

Every new HTTPS connection creates:

- New private keys
- New shared secret
- New session keys

Example:

```
Connection 1

Secret = X1
```

```
Connection 2

Secret = Y7
```

```
Connection 3

Secret = M9
```

Each connection is independent.

---

# Why is Forward Secrecy Important?

Suppose an attacker records today's encrypted HTTPS traffic.

Years later they somehow steal GitHub's long-term private key.

Can they decrypt today's traffic?

**No.**

Because every TLS session generated its own temporary secret using ECDHE.

Old session keys cannot be recreated.

---

# TLS 1.2 vs TLS 1.3

| TLS 1.2 | TLS 1.3 |
|----------|----------|
| Supports RSA Key Exchange | Uses ECDHE by default |
| More handshake messages | Fewer handshake messages |
| Slower | Faster |
| Forward Secrecy optional | Forward Secrecy mandatory |

---

# Complete TLS Handshake Flow

```
Browser
        │
Client Hello
        │
        ▼
Server
        │
Server Hello
        │
Certificate
        │
        ▼
Browser
        │
Verify Certificate
        │
        ▼
ECDHE Key Exchange
        │
        ▼
Shared Secret
        │
        ▼
HKDF
        │
        ▼
Session Keys
        │
        ▼
AES Encryption
        │
        ▼
Encrypted HTTP Communication
```

---

# Key Terms

### Client Hello

The browser's first TLS message containing supported security options.

---

### Server Hello

The server's response selecting the TLS version and cipher suite.

---

### Cipher Suite

A collection of cryptographic algorithms used during the TLS session.

Example:

- AES-256-GCM
- ChaCha20-Poly1305

---

### Shared Secret

A secret independently computed by browser and server using ECDHE.

---

### Session Key

A temporary symmetric key derived from the shared secret and used to encrypt HTTPS traffic.

---

### HKDF

A Key Derivation Function that converts the shared secret into secure encryption keys.

---

### Forward Secrecy

A property ensuring that compromise of a server's long-term private key does not compromise previously recorded TLS sessions.

---

# Interview Questions

### Q1. What is the purpose of the TLS Handshake?

To authenticate the server, negotiate cryptographic algorithms, establish a shared secret, derive session keys, and create a secure encrypted connection before HTTP communication begins.

---

### Q2. Why is RSA/ECC not used for encrypting all HTTPS traffic?

Because public-key cryptography is slow. It is used only during the handshake. Bulk data is encrypted using fast symmetric encryption (AES or ChaCha20).

---

### Q3. What is the difference between a Shared Secret and a Session Key?

- **Shared Secret:** Generated by ECDHE.
- **Session Key:** Derived from the shared secret using HKDF and used for symmetric encryption.

---

### Q4. What is HKDF?

HKDF (HMAC-based Key Derivation Function) derives strong cryptographic keys from the shared secret generated during the TLS handshake.

---

### Q5. What is Forward Secrecy?

Forward Secrecy ensures that even if a server's long-term private key is compromised in the future, previously recorded TLS sessions remain secure because each session uses unique ephemeral keys.

---

# Key Takeaways

- TLS Handshake occurs before any HTTP communication.
- Client Hello and Server Hello negotiate security parameters.
- The server proves its identity using a digital certificate.
- The browser verifies the certificate using the CA's public key.
- ECDHE creates a shared secret without transmitting it.
- HKDF derives secure session keys.
- AES encrypts all HTTP traffic after the handshake.
- TLS 1.3 provides Forward Secrecy using ephemeral key exchange.