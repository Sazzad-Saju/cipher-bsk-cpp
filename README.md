# Cipher BSK — C++ Console Application for Text Encryption

[![Made with C++](https://forthebadge.com/images/badges/made-with-c-plus-plus.svg)](https://forthebadge.com)
[![Built with love](https://forthebadge.com/images/badges/built-with-love.svg)](https://forthebadge.com)

**Cipher BSK (Bornomala Symmetric Key)** is a C++ console application for encrypting and decrypting text. Developed by **Sazzad Hossain Saju** during his undergraduate studies at **Hajee Mohammad Danesh Science and Technology University (HSTU)**, this repository contains the original symmetric cipher implementation at the core of the Cipher BSK cryptosystem.

The work is documented in the research publication **[A Hybrid Cryptographic Scheme of Modified Vigenère Cipher using Randomized Approach for Enhancing Data Security](https://www.ijcaonline.org/archives/volume183/number2/31897-2021921290/)**, published in the *International Journal of Computer Applications* in May 2021.

The broader research combines Cipher BSK for text encryption with RSA for encrypting the symmetric key. This repository implements the **Cipher BSK component**; the [RSA implementation in Python](https://github.com/Sazzad-Saju/RSA_Algorithm) is maintained separately.

**[Research paper](https://www.ijcaonline.org/archives/volume183/number2/31897-2021921290/) · [Find on Google Scholar](https://scholar.google.com/scholar?q=A+Hybrid+Cryptographic+Scheme+of+Modified+Vigenere+Cipher+using+Randomized+Approach+for+Enhancing+Data+Security) · [Project story and tools](https://medium.com/@sham3r/cipher-bsk-from-a-simple-experiment-to-practical-solutions-my-passionate-journey-in-cryptography-d5de4e83add5)**

<p align="center">
  <img src="images/crypto.jpg" width="200" alt="Cipher BSK cryptography illustration">
</p>

[Getting Started](#getting-started) · [Key Generation](#key-generation) · [Encryption](#encryption) · [Decryption](#decryption) · [Features and Scope](#features-and-scope) · [Research and Citation](#research-and-citation)

## Overview

Cipher BSK grew from experiments with classical ciphers, particularly the [Caesar cipher](https://en.wikipedia.org/wiki/Caesar_cipher) and [Vigenère cipher](https://en.wikipedia.org/wiki/Vigen%C3%A8re_cipher). The implementation combines key generation, character substitution, key-dependent shuffling, padding, and hexadecimal output.

In the proposed hybrid cryptosystem:

1. The sender encrypts a message using Cipher BSK and a secret key.
2. The sender encrypts that secret key using the recipient's RSA public key.
3. The recipient recovers the secret key with their RSA private key, then decrypts the message using Cipher BSK.

This console application became the starting point for later web tools, developer packages, and browser extensions in the Cipher BSK ecosystem.

## Getting Started

The application source is [Bornomala.cpp](Bornomala.cpp). It uses Windows-specific headers and APIs, including `Windows.h`, `conio.h`, and clipboard functions, alongside the GNU-specific `bits/stdc++.h` header. It is a legacy Windows console implementation, rather than a portable Linux or macOS application.

You can inspect and build the source using a compatible Windows C++ toolchain; compiler-specific adjustments may be needed. An [original Windows executable](https://sazzad-saju.github.io/BSK-Online/file/BSK%20Stream%20Cipher.exe) is also available.

The console menu provides:

| Option | Action |
| --- | --- |
| Encryption | Enter a message or supply a text-file path, then enter a key. |
| Decryption | Enter hexadecimal ciphertext or supply its text-file path, then enter the original key. |
| Clean Hand | Clear the Windows clipboard. |
| Exit | Close the application. |

Encryption and decryption results are automatically copied to the clipboard. **Clean Hand** clears that clipboard content; it does not securely erase process memory or clipboard history.

The plaintext-file reader joins lines with spaces, so it does not preserve the original line breaks. For direct ciphertext entry, paste one continuous hexadecimal string without display line breaks.

## Key Generation

### Key requirements

The console application requires a key with:

- At least eight characters.
- At least one uppercase letter, one lowercase letter, and one digit.
- No spaces.

These are input-validation rules, not a guarantee of key strength. Use the same original key for encryption and decryption.

### Process

Key generation uses the input key and message length, with three main stages:

1. **Key enhancement** — repeat or extend the key when the message is longer.
2. **Key substitution** — transform the key characters.
3. **Random-indexing shuffle** — rearrange the transformed characters.

The shuffle uses a fixed pseudorandom seed in this implementation. The generated key material is not a standard password hash or password-based key derivation function.

### Example

The following walkthrough retains the example from the original project documentation. Intermediate shuffle results can depend on the C runtime's pseudorandom implementation.

```text
Initial key:       HstU@5200
Message:           Hajee DanesH 26/10/19

Enhanced key:      HstU@5200HstU@5200Hst
Substituted key:   6u,b.X(V'(g}T JyHxyYo
Shuffled key:      ,(V6T.xu 'boyXH}ygYJ(
```

Generated key:

```text
,(V6T.xu 'boyXH}ygYJ(
```

## Encryption

Encryption has two main parts: **base-format encryption** and **padding and output formatting**.

After transforming the plaintext with the generated key, the implementation:

1. Pads the ciphertext to a multiple of eight bytes.
2. Adjusts the generated key for the padded length.
3. Applies a key-triggered reversible shuffle.
4. Converts the result to hexadecimal.
5. Replaces padding markers with values from the control-character (CC) and extended-ASCII (EA) ranges.

### Example

```text
Generated key (21 characters):
,(V6T.xu 'boyXH}ygYJ(

Message (21 characters):
Hajee DanesH 26/10/19

Base-format ciphertext (21 characters):
t+b=[N^x0.wY;, NL9*{a

Padded ciphertext (24 characters):
t+b=[N^x0.wY;, NL9*{a~~~

Adjusted key (24 characters):
,(V6T.xu 'boyXH}ygYJ(,(V

Key-triggered shuffle:
~,t~~L=a0Y{N[+w.;x9b^*N

Hexadecimal conversion:
7E2C747E7E4C3D6130597B4E5B2B772E3B7839625E2A4E20

After blending CC and EA values:
082C7498034C3D6130597B4E5B2B772E3B7839625E2A4E20
```

Final encrypted result:

```text
082C7498034C3D6130597B4E5B2B772E3B7839625E2A4E20
```

Here, 24 encoded bytes are represented by 48 hexadecimal characters. Hexadecimal is the output representation, not an additional encryption layer.

## Decryption

Decryption reconstructs the key material and reverses the formatting and encryption stages. It:

1. Identifies the CC and EA values used for padding and restores the padding markers.
2. Converts hexadecimal ciphertext back to bytes.
3. Adjusts the generated key to match the padded ciphertext.
4. Reverses the key-triggered shuffle.
5. Removes the padding and performs base-format decryption.

### Example

```text
Ciphertext (48 hexadecimal characters / 24 encoded bytes):
082C7498034C3D6130597B4E5B2B772E3B7839625E2A4E20

Restored padding markers in hexadecimal:
7E2C747E7E4C3D6130597B4E5B2B772E3B7839625E2A4E20

Converted back to characters:
~,t~~L=a0Y{N[+w.;x9b^*N

Adjusted key:
,(V6T.xu 'boyXH}ygYJ(,(V

After reversing the shuffle:
t+b=[N^x0.wY;, NL9*{a~~~

After removing padding:
t+b=[N^x0.wY;, NL9*{a

Recovered plaintext:
Hajee DanesH 26/10/19
```

Final decrypted result:

```text
Hajee DanesH 26/10/19
```

## Features and Scope

- Symmetric text encryption and decryption through an interactive C++ console.
- Key-dependent substitution and shuffling, with input-key sensitivity explored in the research.
- Padding to eight-byte boundaries and hexadecimal output.
- Text input, text-file input, and automatic Windows clipboard copying.
- A foundational symmetric component for the proposed hybrid system with RSA.

Cipher BSK is an experimental research implementation. The paper and examples document its design and observations; they do not establish equivalence to modern, independently reviewed cryptographic standards. This application does not implement ciphertext authentication, and the separate RSA component addresses key distribution rather than every security requirement of a communication system.

## Research and Citation

The underlying research was authored by **Sazzad Hossain Saju**, **Sayed Mahmudul Haque**, and **Liakot Hossain Lingcon**.

> Saju, S. H., Haque, S. M., and Lingcon, L. H. (2021). A Hybrid Cryptographic Scheme of Modified Vigenère Cipher using Randomized Approach for Enhancing Data Security. *International Journal of Computer Applications*, 183(2), 1–8. https://doi.org/10.5120/ijca2021921290

If you use or build on this work in your research, please cite the original paper. You can find its record through [Google Scholar](https://scholar.google.com/scholar?q=A+Hybrid+Cryptographic+Scheme+of+Modified+Vigenere+Cipher+using+Randomized+Approach+for+Enhancing+Data+Security) or the [publisher's page](https://www.ijcaonline.org/archives/volume183/number2/31897-2021921290/).

### BibTeX

```bibtex
@article{saju2021hybrid,
  author  = {Sazzad Hossain Saju and Sayed Mahmudul Haque and Liakot Hossain Lingcon},
  title   = {A Hybrid Cryptographic Scheme of Modified Vigen{\`e}re Cipher using Randomized Approach for Enhancing Data Security},
  journal = {International Journal of Computer Applications},
  volume  = {183},
  number  = {2},
  pages   = {1--8},
  month   = may,
  year    = {2021},
  doi     = {10.5120/ijca2021921290},
  url     = {https://www.ijcaonline.org/archives/volume183/number2/31897-2021921290/}
}
```

## Explore the Cipher BSK Ecosystem

- [RSA component in Python](https://github.com/Sazzad-Saju/RSA_Algorithm)
- [BSK Online web application](https://sazzad-saju.github.io/BSK-Online/)
- [JavaScript npm package source](https://github.com/Sazzad-Saju/cipher-bsk-npm-package)
- [PHP and Laravel Composer package](https://packagist.org/packages/sham3r/cipher-bsk)
- [Browser encryption extension](https://github.com/Sazzad-Saju/cipher-bsk-extension)
- [Password manager extension](https://github.com/Sazzad-Saju/cipherbsk-pass-manager-ext)
- [The story behind the research and tools](https://medium.com/@sham3r/cipher-bsk-from-a-simple-experiment-to-practical-solutions-my-passionate-journey-in-cryptography-d5de4e83add5)

## Contributing

Questions, documentation improvements, implementation fixes, and independent analysis are welcome. Open an issue or submit a pull request with a clear explanation of your findings or proposed changes.

## License

Released under the [MIT License](LICENSE).

Copyright © 2020 Sazzad Hossain Saju.
