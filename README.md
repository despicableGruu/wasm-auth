# WASM Auth

This project defines a mechanism to ensure the authenticity and integrity of WebAssembly modules.


## Core Principles

- **Binary Signature:** The signature is applied directly to the serialized WASM binary, avoiding parsing.
- **Complete Module:** The signature covers the entire module, including custom sections, without modification.
- **Fixed Size:** Signatures maintain a consistent byte size.
- **Simplified Verification:** Verification doesn't require parsing the module.
- **ECDSA & SHA256:**  Leverages ECDSA with SHA256 for cryptographic operations.
- **Public Key Distribution:**  Public keys can be shared through standard means like DNS.


## Signature Structure

- **Custom Section:**  A custom section (ID 0) houses the signature.
- **Fixed Section Name:**  The section's name is a constant nine-character string: "signature".
- **Signature Data:** The payload contains the ECDSA signature within the section.
- **Standard Size:**  Each signature section is exactly 118 bytes.
- **Appended:** New signature sections are always added at the end of the module.


**Signature Format Breakdown:**

| Field | Bytes |
|---|---|
| Section Type | 1 |
| Section Size | 2 |
| Name Length | 1 |
| Section Name | 9 |
| Signature Type | 1 |
| Signature Length | 1 |
| Signature | Variable |
| Padding | Variable |


- **Signature Type:**  0 signifies ECDSA/SHA256 (secp256k1).
- **Signature Length:** Specifies the number of signature bytes. 
- **Padding:** Ensures the section's total size is 118 bytes.


## Tooling

The project includes command-line tools for key generation, signing, and verification:

**Key Generation:**
```bash
openssl ecparam -name secp256k1 -genkey -noout -out key.pem
openssl ec -in key.pem -pubout -outform pem -out key.pub.pem

Signing:

wasm-sign -k key.pem module.wasm signed-module.wasm
Bash

Verification:

wasm-sign -v -k key.pub.pem signed-module.wasm

Example Signature:

00000110  xx xx xx xx xx xx xx 00  74 09 73 69 67 6e 61 74  |j$......t.signat|
00000120  75 72 65 00 47 30 45 02  20 58 20 79 4b 91 52 39  |ure.G0E. X yK.R9|
```