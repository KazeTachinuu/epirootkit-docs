---
title: "Encryption"
description: "XOR-based communication encryption for C2 traffic"
icon: "lock"
date: "2025-05-30T00:00:00+01:00"
lastmod: "2025-05-30T00:00:00+01:00"
draft: false
toc: true
weight: 513
---



## Implementation

```c
/* XOR key (32 bytes) */
static const u8 XOR_KEY[] = {
    0x01, 0x23, 0x45, 0x67, 0x89, 0xab, 0xcd, 0xef,
    0x01, 0x23, 0x45, 0x67, 0x89, 0xab, 0xcd, 0xef,
    0x01, 0x23, 0x45, 0x67, 0x89, 0xab, 0xcd, 0xef,
    0x01, 0x23, 0x45, 0x67, 0x89, 0xab, 0xcd, 0xef
};

static void xor_crypt(const u8 *input, u8 *output, size_t length)
{
    for (i = 0; i < length; i++) {
        output[i] = input[i] ^ XOR_KEY[i % XOR_KEY_SIZE];
    }
}
```

Simple XOR cipher with key rotation - same operation for encryption and decryption.

## Functions

```c
int encrypt_message(const char *plaintext, char **ciphertext, size_t *outlen)
int decrypt_message(const char *ciphertext, size_t cipherlen, char **plaintext)
```


All C2 communication between rootkit and attacking program is automatically encrypted.
