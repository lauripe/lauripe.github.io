---
layout: post
title:  "Public key encryption and PGP"
date:   2022-04-15 00:00:00 +0300
categories: ICT4HM103
---
## Applied cryptography - Foundations

### Terminology

The payload containing the actual message is called __plaintext__ or alternatively __cleartext__, and the general science on securing the messages is called __cryptography__
The process is made of following elements:
- __encryption__ is the process of encrypting cleartext, and outputs the message transformed into __ciphertext__
- __decryption__ is the counterprocess, where __ciphertext__ is transformed back to consumable __plaintext__
- ISO 7498-2 suggests using terms __encipher__ and __decipher__ instead of encryption and decryption, as crypting has been found to be offensive term in some cultures

__Cryptographic algorithm__ aka __cipher__ describes the functions applied in trasformations.
These can be broadly classified as:
- __symmetric algorithms__ where the the encryption- and decryption keys are the same or can be calculated from the other
- __public-key algorithms__ where a __public key__ is used to encrypt and __private key__ is required to decipher the message

In addition to securing message confidentiality, cryptography is often applied also to:
- __Authentication__ to validate the senders identity
- __Integrity__ to validate the message was delivered unaltered
- __Nonrepudiation__ to prove that a certain message was actually sent by it's sender.


### Steganography
Steganography is hiding a secret message within another message. This method actually originates from analog era, and has been used with the aid of invisible ink, minute changes in handwriting and other tricks to point out the relevant characters to the recipient for reconstructing the hidden message.

Common modern day equivalent is hiding the message within images by replacing the least significant bit of each byte in the image. I would personally doubt that this would lead into visible color shifts when applied to a "web friendly" 8-bit per channel RGB image, but with higher bit-depth or a standard 16-bit grayscale images, tinkering with the least significant bit should be very hard if not impossible to detect visually.


### Substitution and transposition ciphers
As the name might suggest, a __substitution cipher__ is based on substituting each letter in the plaintext to another in the ciphertext. This might be done by simply rotating the alphabets, using some more elaborate calculation in deducting the substitute or by using an external map for substitution. Funny easter-egg example of substitution cipher can be found in the 60's movie "2001: A Space Odyssey", where the eventually malicious central computer dubbed as __HAL__ can be "deciphered" as __IBM__ by rotating each alphabet by amount of one.

__Transposition cipher__ is one, where the plaintext message is written in e.g. rows of certain length, and the ciphertext is generated from reading the characters as rows from top to bottom. Deciphering is done by simply reverting the process.


### Simple XOR
__XOR__ is abbreviation of __exclusive or__, as might be implied in the question "Are you coming or not?" - you can't really choose to do both or neither. On bitwise operations XOR is true when one is 1 and the other is 0 and false when both are either 1 or 0.

Using XOR as cipher can be done by calculating the output of each bit in the plaintext against each bit of a string representing a key. While certainly possible, it's not advised to use XOR as a cipher as it's trivial to crack even with longer keys. Process of cracking a XOR cipher is following:
- Solve the length of the key with [__counting coincidences__](https://pages.mtu.edu/~shene/NSF-4/Tutorial/VIG/Vig-IOC.html) procedure
- Shift the cleartext bytes with the amount of keys lenght and XOR the shifted ciphertext against itself
- Solve the remaining plaintext XORed with the plaintext shifted by the keylength

### One-time Pad
One-time pad is an encryption scheme where the key is an array of random letters with the length of the message to be encrypted, and each letter in the message is summed with the corresponding letter in the key, and the ciphertext is modulo 26 of the sum.
This is an symmetric algorithm where each encryption key should be completely random and used only once. 
While the approach does have quite a few practical shortcomings, it still has one quite remarkable feature - taken that the key creation is truly random, the encryption is completely unhackable. Without access to the key used in encryption, there's no way of figuring out if a cleartext derived out of the encrypted message is the actual message or any other string of the same length.

Only way to hack a one-time pad encryption is to attack against the key generation scheme - if the key or even parts of it are reused, or if the generation has any non-random properties, it might open a possibility to attacker to get something out of the ciphertext.


### Computer algorithms
- DES (Data Encryption Standard) - symmetric encryption algorithm
- RSA (Rivest, Shamir, and Adleman) - public-key encryption and signature algorithm
- DSA (Digital Signature Algorithm) - public-key signature algorithm



---

#### Cryptopals challenge 1
Notebook hosted in [GitHub](https://github.com/lauripe/security-basics/blob/main/cryptopals/basics.ipynb)

---

---
## Links and references
- [Schneier 2015: Applied Cryptography](https://learning.oreilly.com/library/view/applied-cryptography-protocols/9781119096726/08_chap01.html)
