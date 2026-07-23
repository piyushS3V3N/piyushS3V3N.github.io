---
layout: post
title: "PiQME: Preparing for a Post-Quantum Future"
date: 2026-05-02 10:00:00 +0530
categories: [Cryptography, Security, C Programming]
tags: [post-quantum, csidh, chacha20, piqme, crypto]
---

As quantum computing architectures continue to mature, the cryptographic community is preparing for the day when traditional public-key infrastructures (like RSA and ECC) become vulnerable. To explore how we can stay ahead of this curve, I developed **[PiQME](https://github.com/piyushS3V3N/piqme)**, a high-performance post-quantum cryptographic tool written in C.

### 1. The Cryptographic Pipeline

The primary goal of PiQME was to combine quantum-resistant key exchange with fast symmetric encryption, minimizing overhead while maximizing throughput. 

![PiQME Architecture Diagram](/assets/piqme-arch.svg)
*PiQME's hybrid cryptographic execution pipeline.*

1. **CSIDH (Commutative Supersingular Isogeny Diffie-Hellman):** For the key exchange, I opted for CSIDH. Unlike lattice-based alternatives that require massive key sizes, CSIDH yields highly compact keys. It functions similarly to traditional Diffie-Hellman, making it a great drop-in replacement for achieving forward secrecy without bloating network handshakes.
2. **ChaCha20 Stream Cipher:** Once the shared secret is established, the actual payload encryption is handed off to ChaCha20. While AES-256 remains computationally secure against quantum threats, ChaCha20 is inherently parallelizable and often faster in software on devices without dedicated AES hardware instructions.

### 2. GPU Hardware Acceleration via Apple Metal

To push the throughput limits of ChaCha20, I moved away from standard CPU SIMD pathways and integrated directly with Apple's Metal API. 

Because ChaCha20 relies on a deterministic counter matrix, it is an embarrassingly parallel workload. By writing custom Metal compute shaders, we can offload keystream generation directly to the Mac's GPU. The host CPU divides the payload into large grids and dispatches thousands of simultaneous threads. We also implemented a custom contiguous memory pool to stream data to the GPU without bottlenecking the system bus. This allows PiQME to process large files significantly faster than traditional CPU-bound encryptors.

### 3. Mitigating Side-Channel Attacks

Implementing CSIDH in pure C meant building a custom multi-precision arithmetic (bignum) library from scratch to handle operations on massive prime numbers. The biggest challenge here was guaranteeing **constant-time execution** to prevent side-channel timing attacks.

Traditional `if/else` branching introduces slight execution delays depending on whether a bit of the secret key is 0 or 1, which an attacker can measure. To fix this, I had to eliminate all control-flow branching from the core math loops. Instead, conditional logic was replaced with bitwise masking:

```c
// Constant-time conditional selection
value = (mask & A) | (~mask & B);
```

Ensuring that the code takes the exact same number of CPU cycles regardless of the input data is extremely difficult, and debugging constant-time Montgomery multiplication loops is a uniquely painful experience.

### Looking Ahead

Building PiQME was a deep dive into advanced cryptography and systems-level C programming. It demonstrates that integrating post-quantum algorithms efficiently requires a lot of low-level optimization. If you want to poke around the source code, check out the repository on GitHub—just remember it's still experimental!
