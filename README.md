# Cybersecurity Lab: Crypto & Forensics with Kali Linux

This repo documents a hands-on cybersecurity lab covering symmetric/asymmetric encryption, hashing, password cracking, and Linux forensics using Kali Linux + OpenSSL.

All 27 lab screenshots are explained step-by-step with code breakdowns, reasoning, and conclusions.

## Lab Overview

| Topic | Tools Used | Key Concepts |
| --- | --- | --- |
| **AES Encryption** | OpenSSL | ECB vs CBC/CFB/OFB modes, IVs, PKCS#7 padding, visual pattern leakage |
| **Hashing** | shasum, md5hashing.net | SHA-1/224/256/384/512, avalanche effect, integrity checks |
| **RSA + Digital Signatures** | OpenSSL | Keypair generation, private key signing, public key verification |
| **Password Cracking** | John the Ripper, Hashcat, hashid | Dictionary attacks, mask attacks, /etc/shadow, CPU vs GPU cracking |
| **Linux Forensics** | mount, hexdump, strings, dd, shred | Loop devices, file carving, data remanence, secure deletion |

## Key Takeaways

### 1. AES Mode Matters
ECB mode leaks patterns. You can still see the original image after encryption. CBC, CFB, and OFB properly randomize data and are safe for bulk encryption. Always use an IV with modes that require it.

### 2. Hashes Detect Tampering
SHA-256+ produces a unique fingerprint of any file. Changing even 1 character completely changes the hash. This is how we verify file integrity. SHA-1 is deprecated.

### 3. Digital Signatures = Authenticity
Signing with a private key proves a file came from you and wasn't altered. Anyone with your public key can verify. This is the basis of code signing and SSL certs.

### 4. Weak Passwords Fall Fast
Unsalted SHA-1 hashes were cracked in seconds with John the Ripper using dictionary + mask attacks. `123456`, `administrator`, `phpadmin` all failed. Use bcrypt/argon2 + strong passwords.

### 5. Deletion ≠ Erasure
`rm` only removes the directory entry. `strings` + `dd` can recover deleted files from disk images. For real security, use `shred -uxz` or full-disk encryption.

## Repo Structure

/screenshots/          # All 27 raw terminal screenshots
cylabdraft_final.docx  # Full lab report with explanations for each screenshot
README.md              # You are here

## How to Reproduce This Lab

**Environment**: Kali Linux 2025+ VM

**AES Image Encryption**
openssl enc -aes-128-ecb -K 00112233445566778899AABBCCDDEEFF -in logo.bmp -out logo_ecb.bmp
dd if=logo.bmp of=logo_ecb.bmp bs=1 count=54 conv=notrunc

**Hashing**
echo "Hello world" > file.txt
shasum -a 256 file.txt

**RSA Sign/Verify**
openssl genrsa -out private_key.pem 2048
openssl rsa -in private_key.pem -pubout -out pub.pem
openssl dgst -sign private_key.pem -sha256 -out sig.bin file.txt
openssl dgst -verify pub.pem -sha256 -signature sig.bin file.txt

**Password Cracking**
john --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt
john --mask='?d?d?d?d' hashes.txt

**Forensics**
sudo mount -o loop myfs.img /mnt
strings -td myfs.img | grep "First file"
dd if=myfs.img bs=1 skip=47107 count=18 of=recovered.txt
shred -uxz /mnt/secret.txt

## Disclaimer
This repo is for educational purposes only. All commands were run in an isolated lab VM. Do not use these techniques on systems you don't own or have explicit permission to test.

## License
MIT — feel free to fork and use for your own cybersecurity coursework.
