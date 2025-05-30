---
title: "XOR Encryption"
description: "Simple, reliable encryption for C2 communication"
icon: "lock"
date: "2025-05-30T00:00:00+01:00"
lastmod: "2025-05-30T00:00:00+01:00"
draft: false
toc: true
weight: 515
---

# XOR Encryption

EpiRootkit uses XOR encryption for secure C2 communication with a bulletproof, dependency-free implementation.

## Overview

The XOR encryption system provides:
- **Simple Implementation**: No complex crypto API dependencies
- **Bulletproof Reliability**: Cannot fail due to timing or key derivation issues
- **Perfect Synchronization**: Server and kernel use identical keys
- **High Performance**: Minimal computational overhead

## Implementation

### Server Side (`attacking_program/src/utils/encryption.js`)
```javascript
// 32-byte hardcoded XOR key
const XOR_KEY = Buffer.from('0123456789abcdef0123456789abcdef0123456789abcdef0123456789abcdef', 'hex');

function xorCrypt(data) {
  const input = Buffer.isBuffer(data) ? data : Buffer.from(data, 'utf8');
  const result = Buffer.alloc(input.length);
  
  for (let i = 0; i < input.length; i++) {
    result[i] = input[i] ^ XOR_KEY[i % XOR_KEY.length];
  }
  
  return result;
}

const encryption = {
  encrypt(text) { return xorCrypt(text); },
  decrypt(data) { return xorCrypt(data).toString('utf8'); }
};
```

### Kernel Side (`rootkit/security/crypto.c`)
```c
// Matching XOR key (32 bytes)
static const u8 XOR_KEY[] = {
    0x01, 0x23, 0x45, 0x67, 0x89, 0xab, 0xcd, 0xef,
    0x01, 0x23, 0x45, 0x67, 0x89, 0xab, 0xcd, 0xef,
    0x01, 0x23, 0x45, 0x67, 0x89, 0xab, 0xcd, 0xef,
    0x01, 0x23, 0x45, 0x67, 0x89, 0xab, 0xcd, 0xef
};

static void xor_crypt(const u8 *input, u8 *output, size_t length)
{
    size_t i;
    
    for (i = 0; i < length; i++) {
        output[i] = input[i] ^ XOR_KEY[i % XOR_KEY_SIZE];
    }
}

int encrypt_message(const char *plaintext, char **ciphertext, size_t *outlen);
int decrypt_message(const char *ciphertext, size_t cipherlen, char **plaintext);
```

## Key Features

### 1. Hardcoded Key Synchronization
Both server and kernel use the **exact same 32-byte key**:
- **Server**: `0123456789abcdef0123456789abcdef0123456789abcdef0123456789abcdef`
- **Kernel**: `{0x01, 0x23, 0x45, 0x67, 0x89, 0xab, 0xcd, 0xef, ...}`

### 2. Binary Data Handling
- **Server returns**: Raw binary `Buffer` objects (not hex-encoded)
- **Kernel expects**: Raw binary data for decryption
- **No encoding issues**: Perfect binary compatibility

### 3. Symmetric Operation
XOR encryption is symmetric: **encryption = decryption**
- Same function handles both operations
- No separate encrypt/decrypt algorithms
- Impossible to have implementation mismatches

## Message Flow

### 1. Command Encryption (C2 → Rootkit)
```
Plaintext: "CMD:auth:password"
    ↓ XOR with key
Encrypted: [binary data]
    ↓ TCP transmission
Kernel: receives binary data
    ↓ XOR with same key  
Decrypted: "CMD:auth:password"
```

### 2. Response Encryption (Rootkit → C2)
```
Plaintext: "AUTH_SUCCESS"
    ↓ XOR with key
Encrypted: [binary data]
    ↓ TCP transmission
Server: receives binary data
    ↓ XOR with same key
Decrypted: "AUTH_SUCCESS"
```

## Advantages Over AES-GCM

| Feature | XOR | AES-GCM |
|---------|-----|---------|
| **Dependencies** | None | Linux Crypto API |
| **Key Management** | Static hardcoded | Time-based derivation |
| **Failure Points** | Zero | Multiple (IV, tags, timing) |
| **Performance** | Instant | Crypto API overhead |
| **Reliability** | 100% | Complex error handling |
| **Binary Handling** | Perfect | Encoding issues |

## Security Considerations

### Strengths
- **Traffic Obfuscation**: Prevents cleartext analysis
- **Simple Implementation**: No crypto implementation bugs
- **Perfect Sync**: Server and kernel always compatible
- **No Key Exchange**: No complex key negotiation

### Limitations
- **Static Key**: Key is hardcoded (suitable for educational/demo purposes)
- **Known Plaintext**: XOR vulnerable to known plaintext attacks
- **Not Cryptographically Secure**: For obfuscation, not military-grade security

## Configuration

### Enable/Disable Encryption
```c
// rootkit/core/config.h
#define ENABLE_ENCRYPTION 1  // 1 = enabled, 0 = disabled
```

### Change Encryption Key
```c
// Update both locations with matching values:

// Server: attacking_program/src/utils/encryption.js
const XOR_KEY = Buffer.from('your32bytehexkeyhere...', 'hex');

// Kernel: rootkit/security/crypto.c
static const u8 XOR_KEY[] = { 0xYO, 0xUR, 0xKE, 0xY... };
```

## Verification

### Test Encryption Compatibility
```bash
# From project root
node -e "
const enc = require('./attacking_program/src/utils/encryption.js');
const test = 'PING';
const encrypted = enc.encrypt(test);
const decrypted = enc.decrypt(encrypted);
console.log('Test:', test);
console.log('Encrypted length:', encrypted.length);
console.log('Decrypted:', decrypted);
console.log('Success:', test === decrypted);
"
```

**Expected Output:**
```
Test: PING
Encrypted length: 4
Decrypted: PING
Success: true
```

The XOR encryption provides **reliable, bulletproof communication** perfect for educational rootkit demonstrations. 