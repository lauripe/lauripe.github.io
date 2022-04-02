---
layout: post
title:  "Adversarial mindset"
date:   2022-04-01 20:12:00 +0300
categories: ICT4HM103
---
## Cyber Kill Chain and MITRE ATT&CK
##### Foundations
- Cyber Kill Chain -model is based on [Locheed Martins research paper from 2011](https://lockheedmartin.com/content/dam/lockheed-martin/rms/documents/cyber/LM-White-Paper-Intel-Driven-Defense.pdf)
    - Presents a 7-phase "kill-chain" model derived from traditional warfare into cyber security
        - Instead of trivial run-and-gun cyber-mischief, the model concentrates on so called Advanced Persistent Threat (APT), where the attacker might use years to discreetly close upon the objectives
        - Key takeaway in the model is, that when any of the phases is disturbed, the outcome is also compromised
        - Countermeasures are set against these known 7 phases, including constant monitoring of the attempted measures.
        - By identifying and collecting indicators against the known phases, the model provides a framework for assigning  mitigations against also the probable upcoming efforts.

- MITRE ATT&CK is maintained by a non-profit organization
    - Provides a [highly detailed framwork](https://attack.mitre.org/matrices/enterprise/) of the structure and applicaple methods for cyber warfare and general mischief, along with their corresponding mitigations.
        - The framework shouldn't have any commercial or other limitations for anyone to take advantage of the provided information.
        - The openess obviously swings both ways, and I would assume that while providing a solid ground for protection against even APT kinds of threats, the MITRE framework also works as a playbook for malicious actors against maybe not so well informed targets.


##### Ponderings
While Kill Chain has lots of interesting insight, and the model is clearly laid out and supposingly relatively easy to apply on mitigating real life threat, the publicly available research paper is from a quite different time (2008). Meanwhile MITREs framework is constantly reviewed against emerging incidents, and therefore it should be applicaple also to the threats of tomorrow by anyone concerned. Locheed Martin does seem to have kept up the research, but at least to a peasant in the field, their public offering does come out as [a marketing front end](https://www.lockheedmartin.com/en-us/capabilities/cyber/cyber-kill-chain.html#Resources).

On the other hand, these shouldn't be competing against each other, and I suppose one can find complementary use of both - While having also a good amount of detail, the publicly available Kill chain -model provides a higher level scaffolding, while MITREs framework really deep dives on very spesific technical details related to potential threats and their possible mitigations.

---

## NSO Group and Pegasus -  The road to hell is paved with good intentions?
[Darknet Diaries, Episode 100](https://darknetdiaries.com/episode/100/)

NSO Group is a Israeli tech company, and their main product is a highly sophisticated spyware called Pegasus. Pegasus allows the operator to target any arbitary mobile phone by luring the user to click a malicicious link on a text message, email or a website, which then launches an chained attack against the system eventually giving the remote operator full access to the mobile device. This includes exposing the location of the user along with a wiretap-cabability to all communications, such as calls and Whatsapp messages.

NSO Group states that the software is created to save lives and it should be targeted towards hostile or criminal actors - however, many emerging cases show that it has ended up being used for spying human right activists, political adversaries and basically anyone close to a person or organization seen as a threat by the operator. NSO Group obviously has control over who they sell the software, and have also agreed with Israeli goverment that all customers need to be approved by them, as in trade of conventional weapons. After the sales, NSO supposingly doesn't intervene with the actual use of their software, and many examples have been exposed to the public where Pegasus has been used against off-target persons, such as minor family members of the actual target - and also against persons who's intentions can't really be seen as criminal or life threatening, but having a political discord with the spyware operator instead.

Pegasus came to the limelights on 2016 incident, when a regognized human rights spokesperson from United Arab Emirates, Ahmed Mansoor was targeted by a highly elaborate attack gaining control over his iPhone. Mansoor was however vigilant, as he had been a target of multiple spyware attacks also prior Pegasus, and did find the SMS message used in the exploit suspicious enough to take the matter to be looked into by experts in the field, a interdisciplinary laboratory based at University of Toronto called [Citizenlab](https://citizenlab.ca/2016/08/million-dollar-dissident-iphone-zero-day-nso-group-uae/).

### Breakdown and analysis
Prior 2016 Apple devices were considered very difficult to break into, as the system doesn't allow execution of any code that doensn't originate from the official marketplace. NSO:s Pegasus software however took advantage of chaining three zero-day exploits to gain control of the device. Exploit chain dubbed as "Trident" was crafted from the following exploits:
- [CVE-2016-4657](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2016-4657) WebKit in Apple iOS before 9.3.5 allows remote attackers to execute arbitrary code
    - Link to malicious website was sent via highly targeted SMS, with a content very relevant to the target
        - This was made possible by the intelligence gained from 1st reconnaissance phase in "Cyber Kill Chain" model
    - The JavaScript program on the linked website took advantage of the bug in WebKit, and was able to write an executable code on the phone
        - Phases 2 and 3 in Kill Chain - Weaponization and Delivery
- [CVE-2016-4655](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2016-4655) The kernel in Apple iOS before 9.3.5 allows attackers to obtain sensitive information from memory
    - As Apple devices won't allow execution of any foreign code without jailbreaking, the second and third step did just that. By using two vuneralibities unknown to Apple at that point of time, the exploit jailbroke the device, and then installed and started the Pegasus software on it.
        - Phases 4 and 5 in Kill Chain - Exploitation and Installation
- [CVE-2016-4656](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2016-4656) The kernel in Apple iOS before 9.3.5 allows attackers to execute arbitrary code in a privileged context 
    - After being deployed, the Pegasus software itself doesn't rely on any exploits but works with the phones inbuilt features instead
        - This completes the Kill Chain model with phases 6 and 7, establishing remote access to the device and providing the attackers the means to the actual goal - gaining access to all communications and the physical location of the device.



---

## Virtual sandbox setup
When trying to cause havoc or break into something, it's good to have a separate environment - such as a summer cabin or a VM.
- This isn't the [suggested VirtualBox](https://terokarvinen.com/2021/install-debian-on-virtualbox/), but I was keen to try how [Canonical Multipass](https://multipass.run) works with a recent arm-cpu. Let's see..

#### Host:
- CPU: 10 Core (arm)
- Mem: 16 GB

#### VM:
- CPU: 1 Core
- Mem: 1 GB 

{% highlight bash %}
# Spin up default vm and check where and what it is
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
    - I might try changing the desktop from Ubuntu to XFCE, as it's supposed to be less demanding
        - Xubuntu was also suggested by course materials as an alternative to Debian.
- Everything was fine from the shell prior RDP, so I'll try to stick with that
    - If GUI is needed, It's either XFCE, scale up the VM or create a beefier instance
        - This is a good test for Multipass features
    - If x86 is needed, the same should be easily brought up on another host
        - Portability is another good multipass test-case
- Initial experience from multipass was very nice and creating a local VM has a "cloudish" feel to it. 
    - I did use the interactive shell a bit in the setup, but I'd assume everything should be possible to drive (automated) from the host.

#### Hardinfo utility on Ubuntu via RDP
![Screencap](/assets/img/desktop2-2022-04-01.png)

#### Remote desktop and terminal on host
![Screencap](/assets/img/desktop-2022-04-01.png)

---

References:
- [Cyber Kill Chain Whitepaper](https://lockheedmartin.com/content/dam/lockheed-martin/rms/documents/cyber/LM-White-Paper-Intel-Driven-Defense.pdf)
- [Locheed Martin](https://www.lockheedmartin.com/en-us/capabilities/cyber/cyber-kill-chain.html)
- [MITRE ATT&CK](https://attack.mitre.org/)
- [Common Vulnerabilities and Exposures](https://cve.mitre.org/index.html)
- [Darknet Diaries](https://darknetdiaries.com/)
- [Canonical Multipass](https://multipass.run)

Assignment: [h1](https://terokarvinen.com/2021/trust-to-blockchain-2022/)
