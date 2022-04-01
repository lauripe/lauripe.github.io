---
layout: post
title:  "Adversarial mindset"
date:   2022-04-01 20:12:00 +0300
categories: ICT4HM103
---
## Cyber Kill Chain and MITRE ATT&CK
Foundations:
- Cyber Kill Chain -model is based on Locheed Martins research paper from 2011
- MITRE ATT&CK is maintained by a non-profit organization

While Kill Chain has lots of interesting insight, and the model is clearly laid out and supposingly relatively easy to apply on mitigating real life threat, it does represent a point of time. Meanwhile MITREs framework is constantly reviewed against emerging incidents, and therefore it should be applicaple also to threats of tomorrow.

On the other hand, these shouldn't be competing against each other, and I suppose one can find complementary use of both - Kill chain provides a higher level scaffolding, while MITRE deep dives on very spesific technical details related to potential threats and their possible mitigations.

---

## Security incident (tbd) summary
asdf

### Breakdown and analysis
asdf

---

## Virtual sandbox setup
- Yes this isn't the suggested VirtualBox, but I was keen to try how Canonical Multipass works with arm-cpu. Let's see..

#### Host:
- CPU: 10 core arm
- Mem: 16 GB
- VM hosted by Canonical Multipass

{% highlight bash %}
### Spin up default vm
lauripessi@Lauris-MBP-14 ~ % multipass launch --name sandbox
Launched: sandbox                                                               
lauripessi@Lauris-MBP-14 ~ % multipass list
Name                    State             IPv4             Image
sandbox                 Running           192.168.205.3    Ubuntu 20.04 LTS
lauripessi@Lauris-MBP-14 ~ % multipass exec sandbox -- lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 20.04.4 LTS
Release:	20.04
Codename:	focal

# Login, set a clever password and setup gui and rdp
ubuntu@sandbox:~$ multipass shell sandbox
ubuntu@sandbox:~$ sudo passwd ubunbu
ubuntu@sandbox:~$ sudo apt update
ubuntu@sandbox:~$ sudo apt install ubuntu-desktop xrdp

# Check the guest os ip for connecting with RDP client
ubuntu@sandbox:~$ ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: enp0s1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 52:54:00:96:ad:6c brd ff:ff:ff:ff:ff:ff
    inet 192.168.205.3/24 brd 192.168.205.255 scope global dynamic enp0s1
       valid_lft 84631sec preferred_lft 84631sec
    inet6 fd4d:65f6:c981:6e2c:5054:ff:fe96:ad6c/64 scope global dynamic mngtmpaddr noprefixroute 
       valid_lft 2591998sec preferred_lft 604798sec
    inet6 fe80::5054:ff:fe96:ad6c/64 scope link 
       valid_lft forever preferred_lft forever

{% endhighlight %}

### Virtual sandbox up and running
- Microsoft Remote Desktop
    - credentials and ip from previous phase
    - Resolution 1280 x 1020
    - Everything else as defaults
- After a brief round with the gui I realize I should have been more generous with the vm resources 
    - Internet works, but browsing is a pain
    - Same applies to pretty much anything
- Everything was fine from the shell prior RDP, so I'll try to stick with that
    - If GUI is needed, I'll try to scale up or just instatiate another VM.
- Initial experience from multipass was however very nice and the instancing quite "cloud-alike". 
    - I did use the interactive shell a bit in the setup, but I'd assume everything should be possible to drive (automated) from the host.

#### Hardinfo utility on Ubuntu 
![Screencap](/assets/img/desktop2-2022-04-01.png)

#### Remote desktop, Terminal and host
![Screencap](/assets/img/desktop-2022-04-01.png)


---
References
- [Cyber Kill Chain](https://lockheedmartin.com/content/dam/lockheed-martin/rms/documents/cyber/LM-White-Paper-Intel-Driven-Defense.pdf)
- [MITRE ATT&CK](https://attack.mitre.org/)
- [Darknet Diaries](https://darknetdiaries.com/)
- [Canonical Multipass](https://multipass.run)

Assignment: [h1](https://terokarvinen.com/2021/trust-to-blockchain-2022/)
