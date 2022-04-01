---
layout: post
title:  "Adversarial mindset"
date:   2022-04-01 20:12:00 +0300
categories: ICT4HM103
---
## Cyber Kill Chain and MITRE ATT&CK
Foundations:
- Cyber Kill Chain -model is based on Locheed Martins research paper from 2011
    - Presents a 7-phase "kill-chain" model derived from traditional warfare into cyber security
        - Instead of trivial run-and-gun cyber-mischief, the model concentrates on so called Advanced Persistent Threat (APT), where the attacker might use years to discreetly close upon the objectives
        - Key takeaway in the model is, that when any of the phases is disturbed, the outcome is also compromised
        - Countermeasures are set against these known 7 phases, including constant monitoring of the attempted measures.
        - By identifying and collecting indicators against the known phases, the model provides a framework for assigning  mitigations against also the probable upcoming efforts - leading into a cat and mouse game of adversarial engineering.

- MITRE ATT&CK is maintained by a non-profit organization
    - Provides a highly detailed framwork of the structure and applicaple methods for cyber warfare and general mischief, along with their corresponding mitigations.
        - The framework shouldn't have any commercial or other limitations for anyone to take advantage of the provided information.
        - The openess obviously swings both ways, and I would assume that while providing a solid ground for protection against even APT kinds of threats, the MITRE framework also works as a playbook for malicious actors against maybe not so well informed targets.



While Kill Chain has lots of interesting insight, and the model is clearly laid out and supposingly relatively easy to apply on mitigating real life threat, the publicly available research paper is from a point of time. Meanwhile MITREs framework is constantly reviewed against emerging incidents, and therefore it should be applicaple also to the threats of tomorrow by anyone concerned. Locheed Martin does seem to have kept up the research, but at least to a peasant in the field, their public offering does come out as a marketing front end ([source](https://www.lockheedmartin.com/en-us/capabilities/cyber/cyber-kill-chain.html#Resources)).

On the other hand, these shouldn't be competing against each other, and I suppose one can find complementary use of both - While having also a good amount of detail, the publicly available Kill chain -model provides a higher level scaffolding, while MITRE:S framework really deep dives on very spesific technical details related to potential threats and their possible mitigations.

---

## Security incident (tbd) summary
asdf

### Breakdown and analysis
asdf

---

## Virtual sandbox setup
- When trying to cause havoc or break into something, it's good to have a separate environment - such as a summer cabin or a VM
- This isn't the suggested VirtualBox, but I was keen to try how Canonical Multipass works with arm-cpu. Let's see..

#### Host:
- CPU: 10 core arm
- Mem: 16 GB

{% highlight bash %}
### Spin up default vm and check where and what it is
lauripessi@Lauris-MBP-14 ~ % multipass launch --name sandbox
Launched: sandbox                                                 

lauripessi@Lauris-MBP-14 ~ % multipass ls
Name                    State             IPv4             Image
sandbox                 Running           192.168.205.3    Ubuntu 20.04 LTS

# Login to VM, set a clever password and setup gui & rdp
lauripessi@Lauris-MBP-14 ~ % multipass shell sandbox
ubuntu@sandbox:~$ sudo passwd ubunbu
ubuntu@sandbox:~$ sudo apt update
ubuntu@sandbox:~$ sudo apt install ubuntu-desktop xrdp

{% endhighlight %}

### Virtual sandbox up and running
- Microsoft Remote Desktop
    - credentials and ip from previous phase
    - Resolution 1280 x 1020
    - Everything else as defaults
- After a brief tinkering with the GUI, I realize I should be more generous with the VM resources 
    - Internet works, but browsing is a pain
    - Same applies to pretty much anything
- Everything was fine from the shell prior RDP, so I'll try to stick with that
    - If GUI is needed, I'll try to scale up or just instatiate another VM
    - If x86 is needed, the same is easily brought up on another host
- Initial experience from multipass was very nice and creating a local VM has a "cloudish" feel to it. 
    - I did use the interactive shell a bit in the setup, but I'd assume everything should be possible to drive (automated) from the host.

#### Hardinfo utility on Ubuntu via RDP
![Screencap](/assets/img/desktop2-2022-04-01.png)

#### Remote desktop and terminal on host
![Screencap](/assets/img/desktop-2022-04-01.png)


---
References
- [Cyber Kill Chain](https://lockheedmartin.com/content/dam/lockheed-martin/rms/documents/cyber/LM-White-Paper-Intel-Driven-Defense.pdf)
- [MITRE ATT&CK](https://attack.mitre.org/)
- [Darknet Diaries](https://darknetdiaries.com/)
- [Canonical Multipass](https://multipass.run)

Assignment: [h1](https://terokarvinen.com/2021/trust-to-blockchain-2022/)
