---
description: Things to be sorted
---

# Misc

## CTF SSH Login One-Liner

Prevents your files from getting clogged up with pointless connections.

```
ssh -o "UserKnownHostsFile=/dev/null" -o "StrictHostKeyChecking=no" <user>@<address>
```

## msf Shell Catch One-Liner

You can't alias it so idk what you would actually do with this.

```
sudo msfconsole -q -x "user exploit/multi/handler; set payload <payload>; set LHOST <IP>; set LPORT <PORT>; exploit"
```

## Screen Sessions

Used for managing tasks remotely. &#x20;

```
Create Session:
screen -S <name>

ctrl+a, c (New Window in Session)
ctrl+a, n (Alt+Tab for Windows)
ctrl+d (Kill Window)
ctrl+a, d (Detach from Session)

List Running Sessions:
screen -ls

Return to Detached Session:
screen -x <name>
```

{% embed url="https://aperiodic.net/screen/quick_reference" %}
See also the docs :)
{% endembed %}

## Misc Commands

```
Locate a Binary in Windows:
where <service> 

where ssh
C:\Windows\System32\OpenSSH\ssh.exe

Spring4Shell Vuln Check (400 = Vulnerable)
curl ip:port/path?class.module.classLoader.URLs%5B0%5D=0
```

## External Exploit Resources

I have not written any of the articles found here, but I have used them or learned from them enough to warrant their indication on this page.

* [HackTricks](https://book.hacktricks.xyz) Thanks, Carlos
* [lxd Virtualization Privesc](https://www.hackingarticles.in/lxd-privilege-escalation/) lxdeez nuts
* [RustScan](https://rustscan.github.io/RustScan/) Sick Scanner
* [EternalBlue for x86 Architecture](https://medium.com/x4v1s3c/eternalblue-doublepulsar-x86-architecture-and-using-metasploit-4fd65322a801#\_Table\_of\_Windows) Remember to X-11 Forward
* [https://www.hackingarticles.in/exploiting-wildcard-for-privilege-escalation/](https://www.hackingarticles.in/exploiting-wildcard-for-privilege-escalation/)
* [LOLBAS ](https://lolbas-project.github.io)Homesteading Tutorials
* [Anonymity](https://anonymousplanet.org/guide.html) Important Regardless of Motive

## The Link Graveyard

Things I want to check out but don't have the time to categorize

* Tools for Restricted Environments - [https://github.com/InfosecMatter](https://github.com/InfosecMatter)
* DSInternals Windows Post-Ex Password Auditing [https://github.com/MichaelGrafnetter/DSInternals](https://github.com/MichaelGrafnetter/DSInternals)
* Pi0W DropBox [https://github.com/RoganDawes/P4wnP1\_aloa](https://github.com/RoganDawes/P4wnP1\_aloa)
* For a Project [https://github.com/attackdebris/auto-sslscan](https://github.com/attackdebris/auto-sslscan)
* Yet another Windows CVE: [https://github.com/zoemurmure/CVE-2023-21554-PoC](https://github.com/zoemurmure/CVE-2023-21554-PoC)
* Goodfellow Deep Learning: [https://www.deeplearningbook.org/](https://www.deeplearningbook.org/)

## Other People's Notes

These notebooks are good and I am pulling stuff from them if I use it enough.

* [https://cheatsheet.haax.fr/](https://cheatsheet.haax.fr/)
* The WebApp Notebook - [https://swisskyrepo.github.io/PayloadsAllTheThings](https://swisskyrepo.github.io/PayloadsAllTheThings)
* Snovvcrash - [https://ppn.snovvcrash.rocks/](https://ppn.snovvcrash.rocks/)
* Just this part of the notebook. (Rest is writeups): [https://manuelvazquez-contact.gitbook.io/oscp-prep/privelege-escalation/linux-privilege-escalation](https://manuelvazquez-contact.gitbook.io/oscp-prep/privelege-escalation/linux-privilege-escalation)&#x20;
* Yet another github.io - [https://martinkubecka.github.io/posts/thm/linux-privilege-escalation/](https://martinkubecka.github.io/posts/thm/linux-privilege-escalation/)
* IRedTeam - [https://www.ired.team/](https://www.ired.team/)&#x20;
* Hacker Recipes - [https://www.thehacker.recipes/](https://www.thehacker.recipes/) (AND tools subdomain for mimikatz tips)
* [https://cheatsheetseries.owasp.org/](https://cheatsheetseries.owasp.org/)
* xct's notes - [https://notes.vulndev.io/wiki/](https://notes.vulndev.io/wiki/)
* Sliver Guide - [https://dominicbreuker.com/](https://dominicbreuker.com/)
* More Notes - [https://exploit-notes.hdks.org](https://exploit-notes.hdks.org/)

