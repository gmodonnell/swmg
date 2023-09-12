---
description: Big Prime Numbers
---

# 🔢 Cryptography Trivia

## Key Exchanges

Secure methods for cryptographic key transfer between two parties. You need a key exchange method to securely begin communications. Generally, a shared secret key is agreed upon by the two communicating parties.&#x20;

### Diffie-Hellman

DHKE is used to establish TLS communication. It is vulnerable to MITM when attackers intercept the communication between two parties and establishes a separate DHKE for client to attacker and server to attacker. It is also CPU intensive, meaning it cannot be used when low latency or low power is required.&#x20;

### RSA

Rivest-Shamir-Adleman used large prime numbers to generate a shared secret. It is commonly used for signing, SSL/TLS communication establishment, and PKINIT Kerberos Authentication.

### ECDH

Elliptic Curve Diffie-Hellman is a DHKE variant that uses ECC to generate the shared secret. It is more efficient than DHKE and provides forward secrecy. It is used in IKE VPN protocol.

### ECDSA

Elliptic Curve Digital Signature Algorithm uses ECC to generate signatures. This is what you use for SSH key signing.&#x20;

## Internet Key Exchange (IKE)

IKE is commonly used with VPNs to establish communication channels. It has a main mode and an aggressive mode. Main mode takes 3 phases to establish a channel and aggressive mode takes 2 phases. Aggressive is less secure.&#x20;

Using a Pre-Shared Key (PSK) is a great way to overcome the inherent vulnerability of the key exchange. Just skip it by sharing the key before you need to establish the channel.&#x20;

## Encryption

Encryption can be symmetric or asymmetric. Symmetric encryption uses the same key to encrypt and decrypt.  If the key is lost, confidentiality is no longer guaranteed. AES and DES are symmetric algorithms. Symmetric encryption is used when large amounts of data need to be encrypted, like hard drives or network file transfers. AES is considered the most secure encryption algorithm today, DES is deprecated.&#x20;

Asymmetric encryption uses public and private keys. The public key encrypts data and the private key decrypts it. RSA, PGP and ECC are all asymmetric.&#x20;

### Encrypting Files
If you want to keep your files safe you are going to want to encrypt them before you send them anywhere. Here is how to do that on Windows with AES:
* Copy [Invoke-AESEcnryption to the System](https://www.powershellgallery.com/packages/DRTools/4.0.2.3/Content/Functions%5CInvoke-AESEncryption.ps1)
* Import it to a PSSession with `Import-Module`
* Encrypt a file with `Invoke-AESEncryption -Mode Encrypt -Key "password" -Path .\file`

And on Linux with OpenSSL:
* Encrypt the file with `openssl enc -aes256 -iter 100000 -pbkdf2 -in /targetfile -out outfile`
* Decrypt the file with `openssl enc -d -aes256 -iter 100000 -pbkdf2 -in /encryptedfile -out outfile`
* You will be prompted for a password each time in the CLI

Obviously, the encryption method, iterations, and desire to include Password-Based Key Derivation Funciton 2 are all up to your preference. 