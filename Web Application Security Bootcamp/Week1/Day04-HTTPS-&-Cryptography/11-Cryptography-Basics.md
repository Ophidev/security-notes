# 11 - Cryptography Basics

## Learning Objectives

After completing this chapter, you will understand:

- What Cryptography is
- Encoding vs Encryption vs Hashing
- Symmetric Encryption
- Asymmetric Encryption
- Hash Functions
- Digital Signatures
- Salting
- Password Hashing
- HMAC
- Common Cryptographic Algorithms
- Where cryptography is used in web applications

---

# What is Cryptography?

Cryptography is the science of protecting information from unauthorized access while ensuring confidentiality, integrity, authentication, and non-repudiation.

It is used in:

- HTTPS
- Online Banking
- WhatsApp
- SSH
- VPN
- JWT
- Password Storage

---

# Four Fundamental Concepts

```
Cryptography
│
├── Encoding
├── Encryption
├── Hashing
└── Digital Signature
```

Many beginners confuse these concepts, but they serve completely different purposes.

---

# Encoding

## Definition

Encoding converts data from one format to another so it can be safely stored or transmitted.

It is **NOT** meant for security.

Example:

```
Hello

↓

SGVsbG8=
```

Anyone can decode it.

```
SGVsbG8=

↓

Hello
```

---

## Characteristics

- Reversible
- No Secret Key
- No Security

---

## Common Encoding Algorithms

- Base64
- Base64URL
- URL Encoding
- UTF-8

---

## Uses

- Email Attachments
- JWT Payload
- Images in HTML
- Data Transfer

---

# Encryption

## Definition

Encryption converts readable data (Plaintext) into unreadable data (Ciphertext) using an encryption key.

Only someone with the correct key can decrypt it.

```
Plaintext

↓

Encryption

↓

Ciphertext

↓

Decryption

↓

Plaintext
```

Purpose:

Confidentiality

---

# Types of Encryption

There are two major types.

```
Encryption
│
├── Symmetric
└── Asymmetric
```

---

# Symmetric Encryption

Uses the **same key** for encryption and decryption.

```
Secret Key

↓

Encrypt

↓

Ciphertext

↓

Decrypt

↓

Original Data
```

Example

```
Key

Secret123
```

Encrypt

```
Password123

↓

X8A#91KL
```

Decrypt using

```
Secret123
```

Advantages

- Very Fast
- Efficient
- Ideal for large amounts of data

Disadvantages

- Secret key must be shared securely

---

## Common Algorithms

- AES
- ChaCha20
- DES (obsolete)
- 3DES (legacy)

---

## Used In

- HTTPS (after handshake)
- VPN
- Wi-Fi Encryption
- Disk Encryption

---

# Asymmetric Encryption

Uses two keys.

```
Public Key

↓

Encrypt

↓

Ciphertext

↓

Private Key

↓

Decrypt
```

Public Key

Can be shared.

Private Key

Must remain secret.

Advantages

- Secure key exchange
- No need to share private key

Disadvantages

- Much slower than symmetric encryption

---

## Common Algorithms

- RSA
- ECC

---

## Used In

- HTTPS Handshake
- Digital Certificates
- SSH
- PGP

---

# Symmetric vs Asymmetric

| Symmetric | Asymmetric |
|------------|------------|
| One Key | Two Keys |
| Fast | Slow |
| AES | RSA / ECC |
| Bulk Encryption | Key Exchange & Authentication |

---

# Hashing

## Definition

A Hash is a fixed-length fingerprint generated from data using a Hash Function.

```
Input

↓

Hash Function

↓

Hash
```

Example

```
Hello World

↓

SHA-256

↓

a591a6d40bf42...
```

---

## Characteristics

### Fixed Length

No matter how large the input is:

```
1 Byte

↓

256-bit Hash
```

```
10 GB File

↓

256-bit Hash
```

---

### One-Way

```
Data

↓

Hash
```

Cannot recover the original data.

---

### Deterministic

Same Input

↓

Same Hash

Always.

---

### Avalanche Effect

Tiny change in input

↓

Completely different hash.

Example

```
Hello

↓

ABC123
```

```
hello

↓

XYZ789
```

---

# Common Hash Algorithms

- SHA-256
- SHA-512
- SHA-3

Obsolete

- MD5 ❌
- SHA-1 ❌

---

# Uses of Hashing

- Password Storage
- File Integrity
- Digital Signatures
- Blockchain
- Checksums

---

# Password Hashing

Never store passwords in plain text.

Bad

```
Password123
```

Good

```
bcrypt

↓

$2b$12$....
```

---

# Why Not Encrypt Passwords?

A website never needs to know the user's original password.

Instead:

```
User Login

↓

Hash Password

↓

Compare Hashes

↓

Login
```

---

# Why bcrypt or Argon2?

SHA-256 is designed to be fast.

Attackers can compute billions of SHA-256 hashes every second.

bcrypt and Argon2 are intentionally slow.

This slows brute-force attacks dramatically.

---

# Salting

## Definition

A Salt is a random value added to a password before hashing.

Purpose

Prevent identical passwords from generating identical hashes.

Without Salt

```
Password123

↓

Hash

↓

ABC123
```

Every user gets the same hash.

With Salt

```
Password123

+

Random Salt

↓

bcrypt

↓

Hash A
```

Another user

```
Password123

+

Different Salt

↓

bcrypt

↓

Hash B
```

Hash A ≠ Hash B

---

# Digital Signature

## Definition

A Digital Signature is a cryptographic value created by signing the hash of data using a private key.

Formula

```
Digital Signature

=

Sign(

Hash(Data),

Private Key

)
```

Purpose

- Authentication
- Integrity
- Non-Repudiation

---

# Why Sign the Hash Instead of the Entire File?

Imagine signing a 5 GB file.

Very slow.

Instead

```
5 GB File

↓

SHA-256

↓

32-byte Hash

↓

Sign Hash
```

Much faster.

---

# Digital Signature Verification

Sender

```
Data

↓

Hash

↓

Private Key

↓

Digital Signature
```

Receiver

```
Data

↓

Hash Again
```

and

```
Digital Signature

↓

Verify using Public Key

↓

Recovered Hash
```

Compare

```
Hash

==

Recovered Hash
```

If equal

- Authentic
- Not Modified

---

# HMAC

## Definition

HMAC (Hash-based Message Authentication Code) combines a secret key with a hash function.

Formula

```
HMAC

=

Hash(

Secret Key

+

Message

)
```

Unlike a normal hash,

HMAC requires a secret key.

---

## Why HMAC?

A normal hash proves integrity.

HMAC proves:

- Integrity
- Authenticity

because only someone who knows the secret key can generate the correct HMAC.

---

## Uses

- API Authentication
- JWT (HS256)
- HKDF
- AWS Request Signing

---

# Common Cryptographic Algorithms

## Encoding

- Base64
- Base64URL

---

## Symmetric Encryption

- AES
- ChaCha20

---

## Asymmetric Encryption

- RSA
- ECC

---

## Hashing

- SHA-256
- SHA-512
- SHA-3

---

## Password Hashing

- bcrypt
- Argon2
- scrypt

---

## Message Authentication

- HMAC

---

# Cryptography in HTTPS

```
Browser
      │
Certificate
      │
      ▼
Digital Signature Verification
      │
      ▼
ECDHE
      │
      ▼
Shared Secret
      │
      ▼
HKDF
      │
      ▼
AES Session Key
      │
      ▼
Encrypted HTTPS Communication
```

---

# Cryptography in a MERN Application

## User Login

```
Password

↓

bcrypt

↓

MongoDB
```

---

## HTTPS

```
TLS Handshake

↓

AES Session Key

↓

Encrypted API Calls
```

---

## JWT

```
Payload

↓

Base64URL Encoding

↓

Digital Signature

↓

JWT Token
```

---

## File Integrity

```
Downloaded File

↓

SHA-256

↓

Compare Published Hash
```

---

# Comparison

| Technique | Reversible | Key Required | Primary Purpose |
|------------|------------|--------------|-----------------|
| Encoding | ✅ Yes | ❌ No | Data Representation |
| Encryption | ✅ Yes | ✅ Yes | Confidentiality |
| Hashing | ❌ No | ❌ No | Integrity |
| Digital Signature | Verification Only | Private/Public Keys | Authentication + Integrity |
| HMAC | Verification Only | Shared Secret Key | Integrity + Authentication |

---

# Interview Questions

### Q1. What is the difference between Encoding and Encryption?

Encoding changes the format of data and is reversible without a secret key. Encryption protects confidentiality and requires a cryptographic key.

---

### Q2. Why are passwords hashed instead of encrypted?

Because the server only needs to verify the password, not recover the original password. Hashing is one-way, making leaked password databases much safer.

---

### Q3. Why is bcrypt preferred over SHA-256 for passwords?

bcrypt is intentionally slow and automatically uses a salt, making brute-force and rainbow table attacks significantly harder.

---

### Q4. What does a Digital Signature provide?

- Authentication
- Integrity
- Non-Repudiation

---

### Q5. What is HMAC?

HMAC combines a secret key with a hash function to verify both the authenticity and integrity of a message.

---

# Key Takeaways

- Encoding is not security.
- Encryption protects confidentiality.
- Symmetric encryption (AES) is fast and used for bulk data.
- Asymmetric encryption (RSA/ECC) is used for authentication and key exchange.
- Hashes provide data integrity.
- Digital Signatures prove authenticity and integrity.
- Salts prevent identical passwords from producing identical hashes.
- bcrypt and Argon2 are recommended for password storage.
- HMAC verifies integrity and authenticity using a shared secret.
- Modern web applications rely on all these cryptographic techniques together to provide secure communication.