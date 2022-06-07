---
layout: post
title:  "Public key encryption and PGP"
date:   2022-04-15 00:00:00 +0300
categories: ICT4HM103
---
## Applied cryptography - Foundations

### Terminology

The payload containing the actual message is called __plaintext__ or alternatively __cleartext__, and the general science on securing the messages is called __cryptography__.

The process is made of following elements:
- __encryption__ is the process of encrypting cleartext, and its output is the message transformed into __ciphertext__
- __decryption__ is the counterprocess, where __ciphertext__ is transformed back to consumable __plaintext__
- ISO 7498-2 suggests using terms __encipher__ and __decipher__ instead of encryption and decryption, as crypting has been found to be offensive term in some cultures

__Cryptographic algorithm__ aka __cipher__ describes the functions applied in trasformations.

These can be broadly classified as:
- __symmetric algorithms__ where the the encryption- and decryption keys are the same or can be calculated from each other
- __public-key algorithms__ where a __public key__ is used to encipher and __private key__ is required to decipher the message

In addition to securing message confidentiality, cryptography is often applied also to:
- __Authentication__ to validate the senders identity
- __Integrity__ to validate the message was delivered unaltered
- __Nonrepudiation__ to prove that a certain message was actually sent by its sender.


### Steganography
Steganography is hiding a secret message within another message. This method actually originates from analog era, and has been used with the aid of invisible ink, minute changes in handwriting and other tricks to point out the relevant characters to the recipient for reconstructing the hidden message.

Common modern day equivalent is hiding the message within images by replacing the least significant bit of each byte in the image. I would imagine that this might lead into visible color shifts when applied to a "web friendly" 8-bit per channel RGB image, but with higher bit-depth colour or a standard 16-bit grayscale images, tinkering with the least significant bit should be very hard if not impossible to detect visually.


### Substitution and transposition ciphers
As the name might suggest, a __substitution cipher__ is based on substituting each letter in the plaintext to another in the ciphertext. This might be done by simply rotating the alphabets, using some more elaborate calculation in deducting the substitute or by using an external map for substitution. Funny easter-egg example of substitution cipher can be found in the 60's movie "2001: A Space Odyssey", where the eventually malicious central computer dubbed as __HAL__ can be "deciphered" as __IBM__ by rotating each alphabet by amount of one.

__Transposition cipher__ is one, where the plaintext message is written in e.g. rows of certain length, and the ciphertext is generated from reading the characters as rows from top to bottom. Deciphering is done by simply reverting the process.


### Simple XOR
__XOR__ is abbreviation of __exclusive or__, logically something similiar to the question "Are you coming or not?" - you can't have both or neither, it's always either one or another ("maybe later" isn't an option in this example). 

On bitwise operations XOR is true (1) when one is 1 and the other is 0 and false (0) when both are either 1 or 0.

Using XOR as cipher can be done by calculating the output of each bit in the plaintext against each bit of a string representing a key. While certainly possible, it's not advised to use XOR as a cipher as it's trivial to crack even with longer keys. Process of cracking a XOR cipher is following:
- Solve the length of the key with [__counting coincidences__](https://pages.mtu.edu/~shene/NSF-4/Tutorial/VIG/Vig-IOC.html) procedure
- Shift the cleartext bytes with the amount of keys lenght and XOR the shifted ciphertext against itself
- Solve the remaining plaintext XOR:ed with the plaintext shifted by the keylength

### One-time Pad
One-time pad is an encryption scheme where the key is an array of random letters with the length of the message to be encrypted, and each letter in the message is summed with the corresponding letter in the key, and the ciphertext is modulo 26 of the sum.
This is an symmetric algorithm where each encryption key should be completely random and used only once. 

While the approach does have quite a few practical shortcomings, it still has one quite remarkable feature - taken that the key creation is truly random, the encryption is completely unhackable. Without access to the key used in encryption, there's no way of figuring out if a cleartext derived out of the encrypted message is the actual message or any other string of the same length.

Only way to hack a one-time pad encryption is to attack against the key generation scheme - if the key or even parts of it are reused, or if the generation has any non-random properties, it might open a possibility to attacker to get something out of the ciphertext.


### Computer algorithms
- DES (Data Encryption Standard) - symmetric encryption algorithm
- RSA (Rivest, Shamir, and Adleman) - public-key encryption and signature algorithm
- DSA (Digital Signature Algorithm) - public-key signature algorithm


[Schneier 2015: Applied Cryptography](https://learning.oreilly.com/library/view/applied-cryptography-protocols/9781119096726/08_chap01.html)

---

## Examples of public-key cryptography

__Secure Shell__ or __SSH__ provides a keypair based authentication as an alternative to password login.
- Clients public key is stored in the servers authorized_keys configuration
- When client connects to the server, the public key stored in the server acts as a lock, which can be opened only by its corresponding private key
- Supports several algorithms, such as RSA and ECDSA 

[SSH key based secure authentication](https://www.ssh.com/academy/ssh/key)

__Public Key Infrastructure__ or __PKI__ provides a keypair based method for digital signatures.
- Signer uses the personal private key to digitally sign a document. The private key is kept secret, as anyone gaining access could impersonate the keys original owner.
- Corresponding public key is used to verify the signers identity. Public key can be openly shared.
- Often also a __Certificate Authority__ or in short __CA__ is used as an arbitrator to provide trust in the document and the process of signing it.

[DocuSign - Digital Signatures FAQ](https://www.docusign.com/how-it-works/electronic-signature/digital-signature/digital-signature-faq)

---

## PGP Encrypt and sign a message

First we need to generate a keypair

```
ubuntu@xubu-sandbox:~$ gpg --generate-key
gpg (GnuPG) 2.2.19; Copyright (C) 2019 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Note: Use "gpg --full-generate-key" for a full featured key generation dialog.

GnuPG needs to construct a user ID to identify your key.

Real name: Lauri
Email address: xxx@xxx.fi
You selected this USER-ID:
    "Lauri <xxx@xxx.fi>"

Change (N)ame, (E)mail, or (O)kay/(Q)uit? o
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
gpg: /home/ubuntu/.gnupg/trustdb.gpg: trustdb created
gpg: key 65415629151C2903 marked as ultimately trusted
gpg: directory '/home/ubuntu/.gnupg/openpgp-revocs.d' created
gpg: revocation certificate stored as '/home/ubuntu/.gnupg/openpgp-revocs.d/5D108301A11D55E4A2B1EBE265415629151C2903.rev'
public and secret key created and signed.

pub   rsa3072 2022-04-15 [SC] [expires: 2024-04-14]
      5D108301A11D55E4A2B1EBE265415629151C2903
uid                      Lauri <xxx@xxx.fi>
sub   rsa3072 2022-04-15 [E] [expires: 2024-04-14]
```

Encrypt an important message using the previously generated key

```
ubuntu@xubu-sandbox:~$ echo "Ken sent me." > message.txt
ubuntu@xubu-sandbox:~$ gpg -se -r Lauri message.txt 
gpg: checking the trustdb
gpg: marginals needed: 3  completes needed: 1  trust model: pgp
gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u
gpg: next trustdb check due at 2024-04-14
```

Validate that the output is in fact ciphered

```
ubuntu@xubu-sandbox:~$ cat message.txt.gpg 
????$??
!^?    ?C$?@p??nd
????????M??d?E?;^??fR?\L*?????>???:?u?&???$???;Q?TϤ?[+?P???L;;?j
                                                                ???????V?Z?&????V?>r熬Gy???B???B?t?m׿~O???d?$?#?
                                                                                                                ?G??Au?E|a$?.2?uX? ?A?y??{???RK?ly?1???K.??Շ	?zsJ<?Q?(9???!??'?0?-?YV$	????߁?e??<?t??0??s?}???z?#9Հ?z#y????j?p
#??ֲ??F?	?1'?9&??v??zw??r?V?|%Y0ݳҬ\P?mΠ?zqB???????p?`??*??؁b?r???????t?2͒??m1q??1f?r3???!
i5?	?I4?9T??~.sݑ???]??Ǟo?R+
???3J???:B?%?~???o8?#??M$/??j?
    ??S?N?g??z?+	+h?y??,?G)??|??b???Z.)8?~????Ù9f7Dܝsik?Ո????|b(cb
                                                                         EN??oR??wor???t???t<~?????tH?!DR???Ԡ?@ҝ'??x9E?pv??+NMo{??p??????`f?ռ?9T׫?%Ur?q=8
?8?2??M??<h?#?q?~f?c?M?b???N	?gcx! ??EO
?u5+?3?d??wC???s?!>?]F???(p?????DH??w?6?????m&??f?!
                                                   ?,fx
*?n?I̬(HC¦????????|t??:?R#

???'?????|?rq?@1!܇k???6nk?_)(??j΅??b4y?Uh0??6=\O??Y?6?
```

Decrypt the message and check the signature

```
ubuntu@xubu-sandbox:~$ gpg -d -r Lauri message.txt.gpg
gpg: encrypted with 3072-bit RSA key, ID E9950403D824F5F6, created 2022-04-15
      "Lauri <xxx@xxx.fi>"
Ken sent me.
gpg: Signature made Fri Apr 15 18:47:50 2022 EEST
gpg:                using RSA key 5D108301A11D55E4A2B1EBE265415629151C2903
gpg: Good signature from "Lauri <xxx@xxx.fi>" [ultimate]
```

The important message was decrypted fine and also the signature was identified good.

---

## Richard Stalmans PGP

```
pub   4096R/2C6464AF2A8E4C02 2013-07-20
      Key fingerprint = 6781 9B34 3B2A B70D ED93  2087 2C64 64AF 2A8E 4C02
uid                  Richard Stallman <rms@gnu.org>
sub   4096R/2F30A2E162853425 2013-07-20
````

[https://stallman.org/gpg.html](https://stallman.org/gpg.html)

---

## Cryptopals challenge set 1
Notebook hosted in [GitHub](https://github.com/lauripe/security-basics/blob/main/cryptopals/basics.ipynb)

---
## Links and references
- [Schneier 2015: Applied Cryptography](https://learning.oreilly.com/library/view/applied-cryptography-protocols/9781119096726/08_chap01.html)
- [SSH](https://www.ssh.com/academy/ssh/key)
- [DocuSign](https://www.docusign.com/how-it-works/electronic-signature/digital-signature/digital-signature-faq)
- [Richard Stalman](https://stallman.org)
- [Cryptopals Challenge](https://cryptopals.com/sets/1)

Assignment: [h3](https://terokarvinen.com/2021/trust-to-blockchain-2022/)