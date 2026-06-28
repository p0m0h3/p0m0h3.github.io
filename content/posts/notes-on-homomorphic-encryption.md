---
title: "Notes on Homomorphic Encryption"
date: 2025-06-20
tags: ["cryptography", "fhe", "privacy"]
---

Homomorphic encryption lets you compute on encrypted data without decrypting it first. The server learns nothing about the inputs or outputs — only the encrypted results. It sounds like magic, and for a long time it more or less was: theoretically possible, practically useless.

That is changing.

## The Basic Idea

A standard encryption scheme takes plaintext `m`, a key `k`, and produces ciphertext `c = Enc(k, m)`. To compute `f(m)`, you decrypt, compute, re-encrypt.

A homomorphic scheme lets you compute `Enc(k, f(m))` directly from `c`, without ever seeing `m`. The server evaluates `f` in the encrypted domain.

For addition this is straightforward. RSA is multiplicatively homomorphic — `Enc(m₁) · Enc(m₂) = Enc(m₁ · m₂)` — though this property is actually a vulnerability in vanilla RSA, not a feature. The hard part is supporting both addition and multiplication, which together are sufficient for arbitrary computation.

## Why It Took So Long

Craig Gentry's 2009 construction was the first fully homomorphic encryption (FHE) scheme. The core insight was *bootstrapping*: each homomorphic operation adds noise to the ciphertext, and after enough operations the noise overwhelms the signal. Bootstrapping is a procedure that refreshes the ciphertext — essentially, homomorphically decrypting it to reset the noise level.

The problem: bootstrapping was spectacularly slow. Early implementations took minutes per gate. A 128-bit AES evaluation — a reasonable benchmark — would have taken longer than the age of the universe on contemporary hardware.

The decade since has been an engineering story. CKKS (2017) introduced approximate arithmetic suited to real-valued computation, trading exact results for a 1000× speedup on the workloads that matter for ML inference. TFHE brought single-gate bootstrapping under a millisecond. Hardware acceleration on GPUs and custom ASICs has brought further gains.

## What Is Practical Now

Today, FHE is usable for a specific class of problems:

- **Private ML inference.** Run a neural network on encrypted client data. The server evaluates the model without seeing the input. Latency is 10–100× that of plaintext inference, which is acceptable for some applications.
- **Private set intersection.** Two parties find the intersection of their datasets without revealing elements not in the intersection. Used in private contact discovery (Signal, Apple).
- **Secure aggregation.** Aggregate statistics across parties without any party seeing individual records. Relevant for federated learning.

General-purpose FHE — where you can run arbitrary programs at near-native speed on encrypted data — is still years away. The gap between "theoretically possible" and "runs in acceptable time" remains large for complex programs.

## The Noise Problem as a Feature

There is something philosophically interesting about the noise in FHE schemes. Each ciphertext carries an error term that must stay below a threshold for decryption to work. Operations grow the error. Bootstrapping resets it.

This noise budget is not just an implementation detail — it constrains the depth of circuits you can evaluate. Deeper circuits need larger parameters, which means larger ciphertexts and slower operations. Choosing FHE parameters is a careful trade-off between security level, supported circuit depth, and performance.

In a sense, the noise is the security. It is what makes it hard for an adversary to extract information from the ciphertext even after observing many encrypted computations.

## Where to Start

If you want to experiment, TFHE-rs (Rust) and OpenFHE (C++) are the most actively maintained open-source libraries. Microsoft SEAL is well-documented and Python-friendly via the `tenseal` wrapper.

The learning curve is steep — you are essentially writing programs in a new computational model with a strict budget — but the tooling has improved dramatically in the last three years.
