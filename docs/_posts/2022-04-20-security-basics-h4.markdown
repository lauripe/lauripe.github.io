---
layout: post
title:  "Tor Network"
date:   2022-04-20 00:00:00 +0300
categories: ICT4HM103
---
## Building Tor from source

As the Tor-browser bundle wasn't available for Linux running on arm-cpu (the VM host used was an Apple laptop with a M1 chip), I decided to try whether I can get it to work by simply building the Tor standalone from sources and setting it as SOCKS proxy to Firefox.


```
# Get a recent source, it's chekcsum and signature
ubuntu@xubu-sandbox:~$ wget https://dist.torproject.org/tor-0.4.6.10.tar.gz
ubuntu@xubu-sandbox:~$ wget https://dist.torproject.org/tor-0.4.6.10.tar.gz.sha256sum
ubuntu@xubu-sandbox:~$ wget https://dist.torproject.org/tor-0.4.6.10.tar.gz.sha256sum.asc

# Get signers PGP key
ubuntu@xubu-sandbox:~$ gpg --auto-key-locate nodefault,wkd --locate-keys dgoulet@torproject.org
gpg: key 42E86A2A11F48D36: public key "David Goulet <dgoulet@torproject.org>" imported
gpg: Total number processed: 1
gpg:               imported: 1
pub   rsa2048 2011-05-11 [SC] [expires: 2023-04-07]
      B74417EDDF22AC9F9E90F49142E86A2A11F48D36
uid           [ unknown] David Goulet <dgoulet@torproject.org>
sub   rsa4096 2013-09-10 [E] [expires: 2023-04-07]

# Store key in keyring and verify the checksum against its signature
ubuntu@xubu-sandbox:~$ gpg --output ./tor.keyring --export B74417EDDF22AC9F9E90F49142E86A2A11F48D36
ubuntu@xubu-sandbox:~$ gpgv --keyring ./tor.keyring tor-0.4.6.10.tar.gz.sha256sum.asc tor-0.4.6.10.tar.gz.sha256sum
gpgv: Signature made Tue Feb 15 15:36:31 2022 EET
gpgv:                using RSA key B74417EDDF22AC9F9E90F49142E86A2A11F48D36
gpgv: Good signature from "David Goulet <dgoulet@torproject.org>"

# And now that signatures check out, make sure that the source matches it's chekcsum
ubuntu@xubu-sandbox:~$ sha256sum -c tor-0.4.6.10.tar.gz.sha256sum
tor-0.4.6.10.tar.gz: OK


# Unzip, configure (or try to configure) and install the build
ubuntu@xubu-sandbox:~$ tar -xzf tor-0.4.6.10.tar.gz 
ubuntu@xubu-sandbox:~$ cd tor-0.4.6.10
ubuntu@xubu-sandbox:~/tor-0.4.6.10$ ./configure && make && sudo make install

# Dependencies that were needed for the build to eventually succeed
ubuntu@xubu-sandbox:~/tor-0.4.6.10$ apt-get install libevent-dev
ubuntu@xubu-sandbox:~/tor-0.4.6.10$ apt-get install libssl-dev
ubuntu@xubu-sandbox:~/tor-0.4.6.10$ apt-get install zlib1g-dev

## Start tor
ubuntu@xubu-sandbox:~/tor-0.4.6.10$ tor
```

- [Tor Source Code](https://www.torproject.org/download/tor/)
- [Tor FAQ: Signature verification](https://support.torproject.org/#how-to-verify-signature)

---

## Tor proxy and Firefox settings
Tor was left with vanilla settings so the SOCKS proxy is served at its default location, port 9050 of localhost.

Firefox > Settings > General > Connection Settings
- Set "Manual proxy configuration"
    - SOCKS Host: 127.0.0.1, Port: 9050
    - SOCKS v5
- Select "Proxy DNS when using SOCKS v5"

#### Firefox Connection Settings
![Firefox proxy settings](/assets/img/firefox-proxy.png)

#### Enabling .onion services

Now checking e.g. https://www.whatismyip.com/ already shows that my ip-address and location are hidden from the service provider (in this case my location was initially Warsow and then Amsterdam).

Finally, in order to access .onion services, the following needs to be set as false in the about:config (in addition to enabling SOCKS proxy for DSN in the connection settings earlier):
- network.dns.blockDotOnion: true

#### It works! All dirty secrets of the onion-made darkweb are now browsable.
![Success](/assets/img/tor-firefox.png)

---

