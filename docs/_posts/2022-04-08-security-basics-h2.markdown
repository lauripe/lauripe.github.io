---
layout: post
title:  "Hashes"
date:   2022-04-09 00:00:00 +0300
categories: ICT4HM103
---

## Applied cryptography - Protocol Building Blocks

#### The definition
- What distiguishes __protocol__ from an arbitary task is that it has at least two participants
- In addition, all participants need to know the steps involved, and also agree to follow them
- All the steps need to be clearly defined, and cover all the possible scenarios within the protocol

In the context of cryptography, a __cryptographic protocol__ is one that is using some cryptographic algorithm, for example as a mean to gain trust
- among two or more distrustful actors
- in confidentiality of passed messages
- in convincing one for others identity
- in simultaneus signing of a contract
- and lot of other scenarios.

On high level the cryptographic protocols can be classified in three categories
- Arbitrated Protocols
- Adjudicated Protocols
- Self-Enforcing Protocols

[Schneier 2015: Applied Cryptography](https://learning.oreilly.com/library/view/applied-cryptography-protocols/9781119096726/10_chap02.html)


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

As build seemed to go without errors and the GPU (or whatever the integrated SoC should it be called) cores were correctly identified, I ran few benchmarks:
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
- I did try to invoke also the OpenCL API with "-d 2" to compare results with default Metal, but couldn't get anything but kernel build errors out of it.

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

Now having a good assumption on MD5, I fetched a leaked [rockyou -dictionary from GitHub](https://github.com/danielmiessler/SecLists/blob/master/Passwords/Leaked-Databases/rockyou.txt.tar.gz) and gave it a go

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

Let's see what was hidden in that hash:

```
% cat crack.pot 
21232f297a57a5a743894a0e4a801fc3:admin
```

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

## Bonus
### Crack your own hash
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
% echo -n "kissa123" | md5 > my.hash

# Crack the calculated hash
% hashcat -O -m 0 -a 3 -i "dcf14e3f456a8ceb0d96dac7410af78c" -o crack.pot

...

Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 0 (MD5)
Hash.Target......: 13c3a117d0013ab22417c8edca354b76
Time.Started.....: Sat Apr  9 14:29:59 2022 (0 secs)
Time.Estimated...: Sat Apr  9 14:29:59 2022 (0 secs)
Kernel.Feature...: Optimized Kernel
Guess.Mask.......: ?1?2?2?2?2?2?2?3 [8]
Guess.Charset....: -1 ?l?d?u, -2 ?l?d, -3 ?l?d*!$@_, -4 Undefined 
Guess.Queue......: 8/15 (53.33%)
Speed.#1.........:  4822.2 MH/s (6.05ms) @ Accel:512 Loops:128 Thr:32 Vec:1
Recovered.Total..: 1/1 (100.00%) Digests
Progress.........: 1778384896/5533380698112 (0.03%)
Rejected.........: 0/1778384896 (0.00%)
Restore.Point....: 0/68864256 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:6656-6784 Iteration:0-128
Candidate.Engine.: Device Generator
Candidates.#1....: 3qaerane -> 42264d12
Hardware.Mon.SMC.: Fan0: 0%, Fan1: 0%
Hardware.Mon.#1..: Util:100%

Started: Sat Apr  9 14:29:26 2022
Stopped: Sat Apr  9 14:30:01 2022


# Validate the result
% cat crack.pot
% 13c3a117d0013ab22417c8edca354b76:kissa123
```
Even though 8 characters used to be ok for passwords, MD5 is just weak against modern computers.
- Correct input for given hash was guessed using brute force in ~35 seconds.
- Hash rate: 4822 MH/s
- 1 778 384 896 candidates were tried prior finding the correct answer

---

### Cryptopals challenge 1
Notebook hosted in [GitHub](https://github.com/lauripe/security-basics/blob/main/cryptopals/basics.ipynb)

---

## Links and references
- [hashcat](https://hashcat.net/hashcat/)
- [name-that-hash](https://github.com/HashPals/Name-That-Hash)
- [homebrew](https://brew.sh)
- [Apple SoC](https://debugger.medium.com/why-is-apples-m1-chip-so-fast-3262b158cba2)