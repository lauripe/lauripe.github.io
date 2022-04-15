---
layout: post
title:  "Hashes"
date:   2022-04-09 00:00:00 +0300
categories: ICT4HM103
---

## Applied cryptography - Protocol Building Blocks

### The definition
- What distiguishes __protocol__ from an arbitary task is that it has at least two participants
- In addition, all participants need to know the steps involved, and also agree to follow them
- All the steps need to be clearly defined, and cover all the possible scenarios within the protocol

In the context of cryptography, a __cryptographic protocol__ is one that is using some cryptographic algorithm, for example as a mean to gain trust
- among two or more distrustful actors
- in confidentiality of passed messages
- in convincing one for others identity
- in simultaneus signing of a contract
- and lot of other scenarios.

### Cryptographic protocols
On high level the cryptographic protocols can be classified in three categories
- Arbitrated Protocols
    - Involves 3rd external who's trusted by both participants
    - Arbitrator mediates the transactions, and makes sure neither side is cheating
    - Single point of failure, all communications are dependent on arbitrator availability
- Adjudicated Protocols
    - Involves also an arbitrator party, but only when trust is explicitly needed
        - This kind of arbitrator can be called as adjucator
    - Interaction can take place directly between participants and adjucator is used only in case of dispute
    - While the protocol itself doesn't prevent cheating, the evident detection of cheating does
- Self-Enforcing Protocols
    - No external parties involved
    - The protocol itself protects against cheating

### Attacks against protocols
- Passive attacks
    - Eavesdropping
    - Aim is to gather information instead of affecting the outcome
    - Harder to detect
- Active attacks
    - Aims to change the outcome of the protocol to attackers advantage
        - Can be used also for information gathering
    - Methods can be e.g.
        - Impersonation
        - Altering, deleting or creating completely new messages
        - Replaying old messages
        - Altering stored information

### One-way cryptography
- On symmetric cryptography both participants need to agree on the used encryption algorithm and the secret key prior staring the communication
    - The setup likely involves physical involvement, as digital channels cannot be used prior encryption without risk of compromising the security
    - Not very practical

One way cryptography solves the setup challenge involved in symmetric approach
- One way functions can be easily calculated, but the original value is extremely difficult to solve from the function output
    - One way hash functions are used in many cryptographic protocols, such as public-key cryptography
        - Public key can be easily shared over non-trusted network
        - Private key is well kept secret
        - Messages are be encrypted with shared public key, and can be only decrypted with private key

[Schneier 2015: Applied Cryptography](https://learning.oreilly.com/library/view/applied-cryptography-protocols/9781119096726/10_chap02.html)

---

## Security Penetration Testing
### Hacking user credentials

#### Password encryption
- User credentials are stored in applications own password storage, for which there are multiple implementations
    - In the worst case (or the best if you're the one hacking), the stored passwords are not encrypted at all, so all you need is to gain access to the storage
    - Different encryption methods offer a different level of protection
        - Old versions of Windows use LanMan hash, which has practically no protection against modern computers
            - Values are all-uppercase and split into 7-byte segments
            - Attacker can solve each segment separately, instead of struggling with 14 or 21 byte keys
        - Modern Windows OS uses NTLM which is more secure while still having one obvious trait
            - Unlike Linux, Windows passwords are hashed without salt, so if you solve a key from a single computer the same password is effectively solved in all other Windows computers in the world

#### Password hacking
To hack a password stored as an output of one-way cryptographic function, you need to:
- Identify the hashing algorithm used
- Try out possible candidates untill the hash matches the one you're solving


Obvious way to handle the second step is to try each possible combination until you find a matching hash - this approach is fittingly named as __brute force__. For short passwords with limited characters this can be a working solution, as there's only limited number of possibilities to go trough (e.g. LanMan:s 7-byte array of uppercase characters and digits).

Slightly more elaborate way of brute-forcing is to use a prebuilt dictionary holding commonly used passphrases instead of going arbitarily through every possible combination.

An alternative to brute force is using a __rainbow table__, which simply put is a precomputed database of hashes. This [introduces a tradeoff](https://eprint.iacr.org/2013/591.pdf) from required compute resources to time and space required to load and store the precomputed hashes into memory.

#### Computing hashes
While still having value in other areas, the hashing algorithms of yesteryear, such as MD5, have been made obsolete in cryptography by advances in compute power and parallellism. Especially the capability of modern GPU:s to handle arbitary arithmetic functions way beyond what's needed in graphics has given practically anyone access to high speed parallel processing. 

With a reasonably modern gaming or content creation computer one can simply install a GPU compatible open source tool such as hashcat, and start calculating hashes with rates of billions per second  without having pretty much any actual technical knowledge on the area.

[The Art of Hacking Series LiveLessons: Lesson 6: Hacking User Credentials](https://learning.oreilly.com/videos/security-penetration-testing/9780134833989/9780134833989-sptt_00_06_00_00)

As a final note, one must understand that also the algorithms generally accepted as unbreakable today, are already known to be weak against technologies of tomorrow. No need to say this is an ongoing game of cat and mouse, and there's already emerging protection for future proofing such as [SSH Quantum Safe Solutions](https://www.ssh.com/solutions/quantum-safe-cryptography-qsc-solutions).



---

## Hashcat

### a.) Install and test
- For reasons left unknown, I didn't manage to get the [version available from Homebrew](https://formulae.brew.sh/formula/hashcat) working
    - Without explicitly selecting device type it returned no-device -error
    - When using -D 2 flag, the attemts crashed on missing dependency

```
clBuildProgram(): CL_BUILD_PROGRAM_FAILURE

:7:10: fatal error: 'inc_vendor.h' file not found #include "inc_vendor.h" ^
````

I tried to solve the linking by assigning the correct location:

```
$ export CPLUS_INCLUDE_PATH=/opt/homebrew/Cellar/hashcat/6.2.5/share/hashcat/OpenCL
```

But now it crashed with a different error:

```
/Users/lauripessi/.cache/hashcat/kernels/shared.89afd1a5.kernel: No such file or directory

* Device #1: Kernel /opt/homebrew/Cellar/hashcat/6.2.5/share/hashcat/OpenCL/shared.cl build failed.
```

#### Local installation built from sources
- As Google failed to give me good advice on workarounds, I decided to remove the version installed by Homebrew and build the [latest from GitHub](https://github.com/hashcat/hashcat)

```
### Only commands here, as the make output is quite verbose
% git clone https://github.com/hashcat/hashcat.git
% cd hashcat
% sudo make 
% sudo make install


### Get info on available compute
% hashcat -I
hashcat (v2.01-8370-g521a931a8) starting in backend information mode

Metal Info:
===========

Metal.Version.: 261.13

Backend Device ID #1 (Alias: #2)
  Type...........: GPU
  Vendor.ID......: 2
  Vendor.........: Apple
  Name...........: Apple M1 Pro
  Processor(s)...: 16
  Clock..........: N/A
  Memory.Total...: 10922 MB (limited to 4096 MB allocatable in one block)
  Memory.Free....: 5408 MB
  Local.Memory...: 32 KB
  Phys.Location..: built-in
  Feature.Set....: macOS GPU Family 2 v1
  Registry.ID....: 2147
  Max.TX.Rate....: N/A
  GPU.Properties.: headless 0, low-power 0, removable 0

OpenCL Info:
============

OpenCL Platform ID #1
  Vendor..: Apple
  Name....: Apple
  Version.: OpenCL 1.2 (Feb 12 2022 01:56:48)

  Backend Device ID #2 (Alias: #1)
    Type...........: GPU
    Vendor.ID......: 2
    Vendor.........: Apple
    Name...........: Apple M1 Pro
    Version........: OpenCL 1.2 
    Processor(s)...: 16
    Clock..........: 1000
    Memory.Total...: 10922 MB (limited to 1024 MB allocatable in one block)
    Memory.Free....: 5408 MB
    Local.Memory...: 32 KB
    OpenCL.Version.: OpenCL C 1.2 
    Driver.Version.: 1.2 1.0

```

As build seemed to go without errors and the GPU (or whatever the [integrated SoC](https://debugger.medium.com/why-is-apples-m1-chip-so-fast-3262b158cba2) should be called) cores were correctly identified, I ran few benchmarks:
```
% # MD5
% hashcat -b -m 0 -O | grep "Speed.#1"
Speed.#1.........:  5733.8 MH/s (92.50ms) @ Accel:1024 Loops:1024 Thr:32 Vec:1

% # sha256
% hashcat -b -m 1400 -O | grep "Speed.#1"
Speed.#1.........:   607.8 MH/s (54.04ms) @ Accel:512 Loops:128 Thr:32 Vec:1

% # NTLM
% hashcat -b -d 1 -m 3000 -O | grep "Speed.#1"
Speed.#1.........:   305.0 MH/s (53.57ms) @ Accel:16 Loops:1024 Thr:128 Vec:1
```

As far as I'm able to intepret these, the figures are not bad at all for a small laptop!
- I did try to invoke also the OpenCL API with "-d 2" to compare results with default Metal, but couldn't get anything but kernel build errors out of it
    - Hashcat does actually print a warning regarding the unreliability of Apples OpenCL drivers every time it's run, so it is what it is..

---

### Crack this hash - task b
```
21232f297a57a5a743894a0e4a801fc3
```

I installed name-that-hash from Homebrew to help in identifying the algorithm

```
 % name-that-hash -t 21232f297a57a5a743894a0e4a801fc3

  _   _                           _____ _           _          _   _           _     
 | \ | |                         |_   _| |         | |        | | | |         | |    
 |  \| | __ _ _ __ ___   ___ ______| | | |__   __ _| |_ ______| |_| | __ _ ___| |__  
 | . ` |/ _` | '_ ` _ \ / _ \______| | | '_ \ / _` | __|______|  _  |/ _` / __| '_ \ 
 | |\  | (_| | | | | | |  __/      | | | | | | (_| | |_       | | | | (_| \__ \ | | |
 \_| \_/\__,_|_| |_| |_|\___|      \_/ |_| |_|\__,_|\__|      \_| |_/\__,_|___/_| |_|

https://twitter.com/bee_sec_san
https://github.com/HashPals/Name-That-Hash 
    

21232f297a57a5a743894a0e4a801fc3

Most Likely 
MD5, HC: 0 JtR: raw-md5 Summary: Used for Linux Shadow files.
MD4, HC: 900 JtR: raw-md4
NTLM, HC: 1000 JtR: nt Summary: Often used in Windows Active Directory.
Domain Cached Credentials, HC: 1100 JtR: mscach
```

Now having a good assumption on MD5, I fetched the leaked [rockyou -dictionary from GitHub](https://github.com/danielmiessler/SecLists/blob/master/Passwords/Leaked-Databases/rockyou.txt.tar.gz) and gave it a go

Flags:
- "-m 0" to attack against MD5
- "-O" to use optimized kernels
- "-o crack.pot" to store solved secrets in a file

```
% hashcat -m 0 -O "21232f297a57a5a743894a0e4a801fc3" rockyou.txt -o crack.pot
hashcat (v2.01-8370-g521a931a8) starting

* Device #2: Apple's OpenCL drivers (GPU) are known to be unreliable.
             You have been warned.

METAL API (Metal 261.13)
========================
* Device #1: Apple M1 Pro, 5408/10922 MB, 16MCU

OpenCL API (OpenCL 1.2 (Feb 12 2022 01:56:48)) - Platform #1 [Apple]
====================================================================
* Device #2: Apple M1 Pro, skipped

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 31

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Optimizers applied:
* Optimized-Kernel
* Zero-Byte
* Precompute-Init
* Meet-In-The-Middle
* Early-Skip
* Not-Salted
* Not-Iterated
* Single-Hash
* Single-Salt
* Raw-Hash

Watchdog: Temperature abort trigger set to 100c

Host memory required for this attack: 562 MB

Dictionary cache built:
* Filename..: rockyou.txt
* Passwords.: 14344391
* Bytes.....: 139921497
* Keyspace..: 14344384
* Runtime...: 1 sec

                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 0 (MD5)
Hash.Target......: 21232f297a57a5a743894a0e4a801fc3
Time.Started.....: Fri Apr  8 14:36:37 2022 (0 secs)
Time.Estimated...: Fri Apr  8 14:36:37 2022 (0 secs)
Kernel.Feature...: Optimized Kernel
Guess.Base.......: File (rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:   139.3 MH/s (2.97ms) @ Accel:4096 Loops:1 Thr:32 Vec:1
Recovered.Total..: 1/1 (100.00%) Digests
Progress.........: 2097218/14344384 (14.62%)
Rejected.........: 66/2097218 (0.00%)
Restore.Point....: 0/14344384 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#1....: 123456 -> SAGEM
Hardware.Mon.SMC.: Fan0: 0%, Fan1: 0%
Hardware.Mon.#1..: Util: 70%

Started: Fri Apr  8 14:36:21 2022
Stopped: Fri Apr  8 14:36:38 2022
```

Cracking took 17 seconds with rate of 139.3 MH/s
Let's see what was hidden in that hash:

```
% cat crack.pot 
21232f297a57a5a743894a0e4a801fc3:admin
```

As the passphrase was so short and the benchmark earlier estimated 5700 MH/s rate, I got curious to see how fast a simple brute without dictionary could solve the same.

```
# Disable potfile as it already holds the correct answer
% hashcat -O -m 0 -a 3 -i 21232f297a57a5a743894a0e4a801fc3 -o crack.pot --potfile-disable


Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 0 (MD5)
Hash.Target......: 21232f297a57a5a743894a0e4a801fc3
Time.Started.....: Sat Apr  9 18:38:29 2022 (0 secs)
Time.Estimated...: Sat Apr  9 18:38:29 2022 (0 secs)
Kernel.Feature...: Optimized Kernel
Guess.Mask.......: ?1?2?2?2?2 [5]
Guess.Charset....: -1 ?l?d?u, -2 ?l?d, -3 ?l?d*!$@_, -4 Undefined 
Guess.Queue......: 5/15 (33.33%)
Speed.#1.........:  2662.9 MH/s (6.18ms) @ Accel:1024 Loops:62 Thr:32 Vec:1
Recovered.Total..: 1/1 (100.00%) Digests
Progress.........: 32505856/104136192 (31.21%)
Rejected.........: 0/32505856 (0.00%)
Restore.Point....: 0/1679616 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-62 Iteration:0-62
Candidate.Engine.: Device Generator
Candidates.#1....: sarie -> Xsnc1
Hardware.Mon.SMC.: Fan0: 0%, Fan1: 0%
Hardware.Mon.#1..: Util:  0%

Started: Sat Apr  9 18:38:28 2022
Stopped: Sat Apr  9 18:38:31 2022
```

Ok, way faster! Simple guesswork took only 3 seconds with rate of 2662.9 MH/s
During that time 32 505 856 candidates were tried prior cracking the correct value.


---

### Crack this hash - task c
```
 f2477a144dff4f216ab81f2ac3e3207d
```

Same as previous, now with -m 1000 to attack against NTLM

```
% hashcat -m 1000 -O "f2477a144dff4f216ab81f2ac3e3207d" rockyou.txt -o crack.pot

...

Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 1000 (NTLM)
Hash.Target......: f2477a144dff4f216ab81f2ac3e3207d
Time.Started.....: Fri Apr  8 14:46:57 2022 (0 secs)
Time.Estimated...: Fri Apr  8 14:46:57 2022 (0 secs)
Kernel.Feature...: Optimized Kernel
Guess.Base.......: File (rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:   139.0 MH/s (2.96ms) @ Accel:4096 Loops:1 Thr:32 Vec:1
Recovered.Total..: 1/1 (100.00%) Digests
Progress.........: 2097282/14344384 (14.62%)
Rejected.........: 130/2097282 (0.01%)
Restore.Point....: 0/14344384 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#1....: 123456 -> SABOG
Hardware.Mon.SMC.: Fan0: 0%, Fan1: 0%
Hardware.Mon.#1..: Util: 68%

Started: Fri Apr  8 14:46:38 2022
Stopped: Fri Apr  8 14:46:58 2022
```
And the __monkey__ came out of hiding soon enough!

---

### d) Crack this hash - task d

```
$2y$18$axMtQ4N8j/NQVItQJed9uORfsUK667RAWfycwFMtDBD6zAo1Se2eu
```

Just by looking at the length and variety of characters user, one can tell that this is not a trivial task.

Let's start by guesstimating the used algorithm:

```
name-that-hash -t "$2y$18$axMtQ4N8j/NQVItQJed9uORfsUK667RAWfycwFMtDBD6zAo1Se2eu"

 ...

Most Likely 
bcrypt, HC: 3200 JtR: bcrypt
Blowfish(OpenBSD), HC: 3200 JtR: bcrypt Summary: Can be used in Linux Shadow Files.
Woltlab Burning Board 4.x,
```

I have no idea where such algorithm is used, and is the rockyou -dictionary at all relevant here, but I'll just keep using it and maybe get wiser later on.

Initally the estimate was around 309 days, so I ended up trying Workload Profile 4

```
- [ Workload Profiles ] -

  # | Performance | Runtime | Power Consumption | Desktop Impact
 ===+=============+=========+===================+=================
  1 | Low         |   2 ms  | Low               | Minimal
  2 | Default     |  12 ms  | Economic          | Noticeable
  3 | High        |  96 ms  | High              | Unresponsive
  4 | Nightmare   | 480 ms  | Insane            | Headless
```

This started also to affect general desktop performance, so obviously more effort was put into the work. 

```
% hashcat -O -m 3200 '$2y$18$axMtQ4N8j/NQVItQJed9uORfsUK667RAWfycwFMtDBD6zAo1Se2eu' rockyou.txt -o crack.pot -w 4

...

# Status output:
Session..........: hashcat
Status...........: Running
Hash.Mode........: 3200 (bcrypt $2*$, Blowfish (Unix))
Hash.Target......: $2y$18$axMtQ4N8j/NQVItQJed9uORfsUK667RAWfycwFMtDBD6...1Se2eu
Time.Started.....: Fri Apr  8 15:16:26 2022 (5 mins, 16 secs)
Time.Estimated...: Tue Jan 24 03:37:49 2023 (290 days, 13 hours)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:        1 H/s (436.01ms) @ Accel:4 Loops:128 Thr:8 Vec:1
Recovered.Total..: 0/1 (0.00%) Digests
Progress.........: 0/14344384 (0.00%)
Rejected.........: 0/0 (0.00%)
Restore.Point....: 0/14344384 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:92672-92800
Candidate.Engine.: Device Generator
Candidates.#1....: 123456 -> letmein
Hardware.Mon.SMC.: Fan0: 0%, Fan1: 0%
Hardware.Mon.#1..: Util:100%

```

With the more intensive profile I could shave off few weeks from the estimate having 290 days. Eventually I thought I'll have better use for my laptop for that duration, and decided to quit the process.

---

### Bonus

#### Crack your own hash
- To keep things simple, I'll stick to MD5 with input
    - Length of 8 characters
    - Uppercase, lowercase and digits
- Hashcat settings
    - "-O" Optimized kernels
    - "-m 0" MD5
    - "-a 3" Brute force attack mode
    - "-i" Incremental mode
        Starts with lenght of 1 and extends when options are exhausted

```
# Calculate a hash (and remember to omit the newline)
% echo -n 'Kissa234' | md5 > my.hash

# Crack the calculated hash
% hashcat -O -m 0 -a 3 -i my.hash -o crack.pot

...

Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 0 (MD5)
Hash.Target......: f2805d0412dbd008ad4c95fb01e342b9
Time.Started.....: Sat Apr  9 18:12:46 2022 (0 secs)
Time.Estimated...: Sat Apr  9 18:12:46 2022 (0 secs)
Kernel.Feature...: Optimized Kernel
Guess.Mask.......: ?1?2?2?2?2?2?2?3 [8]
Guess.Charset....: -1 ?l?d?u, -2 ?l?d, -3 ?l?d*!$@_, -4 Undefined 
Guess.Queue......: 8/15 (53.33%)
Speed.#1.........:  4824.5 MH/s (6.05ms) @ Accel:512 Loops:128 Thr:32 Vec:1
Recovered.Total..: 1/1 (100.00%) Digests
Progress.........: 1811939328/5533380698112 (0.03%)
Rejected.........: 0/1811939328 (0.00%)
Restore.Point....: 0/68864256 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:6784-6912 Iteration:0-128
Candidate.Engine.: Device Generator
Candidates.#1....: 522erane -> yut64d12
Hardware.Mon.SMC.: Fan0: 40%, Fan1: 39%
Hardware.Mon.#1..: Util:100%

Started: Sat Apr  9 18:12:13 2022
Stopped: Sat Apr  9 18:12:48 2022


# Validate the result
% cat crack.pot
f2805d0412dbd008ad4c95fb01e342b9:Kissa234
```

Even though 8 characters used to be ok for passwords, MD5 is just weak against modern computers.
- Correct input for given hash was guessed using brute force in ~35 seconds.
- Hash rate: 4824.5 MH/s
- 1 811 939 328 candidates were tried prior finding the correct answer

---


#### Run it on GPU
- I resorted to Windows desktop with discrete GPU here

```
CUDA API (CUDA 11.6)
====================
* Device #1: NVIDIA GeForce GTX 1080, 7213/8191 MB, 20MCU

> .\hashcat.exe -O -m 0 -a 3 -i f2805d0412dbd008ad4c95fb01e342b9 -o crack.pot
```

Using the same Kissa234 MD5 as in "Crack your own hash", here's how the stats from two quite different setups compare
Hash rates are from last iteration with 8-character mask (?1?2?2?2?2?2?2?3 [8])
- (Desktop) Nvidia GTX 1080, CUDA: 23435.7 MH/s, cracked in 11 seconds
- (Laptop) Apple M1 Pro, Metal: 4824.5 MH/s, cracked in 35 seconds

---

## Links and references
- [Schneier 2015: Applied Cryptography](https://learning.oreilly.com/library/view/applied-cryptography-protocols/9781119096726/10_chap02.html)
- [The Art of Hacking Series LiveLessons: Lesson 6: Hacking User Credentials](https://learning.oreilly.com/videos/security-penetration-testing/9780134833989/9780134833989-sptt_00_06_00_00)
- [Task assignment h2](https://terokarvinen.com/2021/trust-to-blockchain-2022/)
- [hashcat](https://hashcat.net/hashcat/)
- [name-that-hash](https://github.com/HashPals/Name-That-Hash)
- [homebrew](https://brew.sh)
