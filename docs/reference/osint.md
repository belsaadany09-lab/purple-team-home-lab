# ATT&CK &amp; OSINT field guide

Two things every technique in this journey leans on: a **map** of where it sits
in a real attack, and the **public sources** you use to do reconnaissance before
you ever send a packet. Both live here, with links.

!!! info "Scope &amp; ethics — read first"

    Every source below returns *public* information, but reconnaissance is still
    only ever run against targets you own or are explicitly authorised to test.
    Passive lookups on your own domains and lab, or on a sanctioned practice
    target, are fine. Using any of this against someone else's infrastructure is
    not. When in doubt, don't.

## The map — MITRE ATT&CK

ATT&CK is the shared language for "what attackers actually do," organised by
tactic (the goal) and technique (the method, with IDs like `T1557`). Every
attack in this journey is tagged with its ATT&CK ID so you can cross-reference
it with real-world threat reporting and detection ideas.

| Resource | What it's for | Link |
| :---- | :---- | :---- |
| MITRE ATT&CK | The full technique matrix — the reference for every tagged technique here | [attack.mitre.org](https://attack.mitre.org/) |
| ATT&CK Navigator | Lay your coverage over the matrix to see gaps | [mitre-attack.github.io/attack-navigator](https://mitre-attack.github.io/attack-navigator/) |
| D3FEND | The defensive counterpart — maps detections and mitigations to techniques | [d3fend.mitre.org](https://d3fend.mitre.org/) |

**Techniques used across this journey:**
[T1557 AiTM](https://attack.mitre.org/techniques/T1557/) ·
[T1557.002 ARP poisoning](https://attack.mitre.org/techniques/T1557/002/) ·
[T1557.003 DHCP spoofing](https://attack.mitre.org/techniques/T1557/003/) ·
[T1003.002 credential dumping](https://attack.mitre.org/techniques/T1003/002/) ·
[T1053.005 scheduled task](https://attack.mitre.org/techniques/T1053/005/) ·
[T1562.001 impair defenses](https://attack.mitre.org/techniques/T1562/001/) ·
[T1572 tunnelling](https://attack.mitre.org/techniques/T1572/) ·
[T1090 proxy](https://attack.mitre.org/techniques/T1090/) ·
[T1040 sniffing](https://attack.mitre.org/techniques/T1040/) ·
[T1110.002 cracking](https://attack.mitre.org/techniques/T1110/002/) ·
[T1498 network DoS](https://attack.mitre.org/techniques/T1498/) ·
[T1190 exploit public app](https://attack.mitre.org/techniques/T1190/)

## Passive OSINT sources

Reconnaissance before contact — all of this reads public data without touching
the target directly.

### Infrastructure &amp; exposed services

| Source | What it shows | Link |
| :---- | :---- | :---- |
| Shodan | Internet-exposed services and banners for an IP/org | [shodan.io](https://www.shodan.io/) |
| Censys | Searchable view of hosts, certs and services | [search.censys.io](https://search.censys.io/) |
| crt.sh | Certificate-transparency logs → subdomains, forgotten hosts | [crt.sh](https://crt.sh/) |
| DNSDumpster | DNS recon and a quick network map for a domain | [dnsdumpster.com](https://dnsdumpster.com/) |
| ViewDNS | A grab-bag of DNS/WHOIS/reverse lookups | [viewdns.info](https://viewdns.info/) |

### Reputation &amp; threat context

| Source | What it shows | Link |
| :---- | :---- | :---- |
| VirusTotal | File/URL/domain reputation across many engines | [virustotal.com](https://www.virustotal.com/) |
| GreyNoise | Whether an IP is mass-scanning the internet (noise vs. targeted) | [viz.greynoise.io](https://viz.greynoise.io/) |
| AbuseIPDB | Community abuse reports for an IP | [abuseipdb.com](https://www.abuseipdb.com/) |
| URLScan | Renders and records what a URL actually loads | [urlscan.io](https://urlscan.io/) |

### People, accounts &amp; breaches

| Source | What it shows | Link |
| :---- | :---- | :---- |
| Have I Been Pwned | Whether an email/number appeared in a known breach | [haveibeenpwned.com](https://haveibeenpwned.com/) |
| Hunter | Email-address patterns for a domain | [hunter.io](https://hunter.io/) |
| OSINT Framework | A directory of OSINT sources by category | [osintframework.com](https://osintframework.com/) |

### Collection tooling (run locally)

| Tool | What it does | Link |
| :---- | :---- | :---- |
| theHarvester | Gathers emails, subdomains and hosts from public sources | [github.com/laramies/theHarvester](https://github.com/laramies/theHarvester) |
| Amass | In-depth subdomain/asset discovery | [github.com/owasp-amass/amass](https://github.com/owasp-amass/amass) |
| SpiderFoot | Automates many OSINT sources into one scan | [github.com/smicallef/spiderfoot](https://github.com/smicallef/spiderfoot) |
| Sherlock | Hunts a username across platforms | [github.com/sherlock-project/sherlock](https://github.com/sherlock-project/sherlock) |

## Authorised practice targets

Where to learn web and recon technique legally, with no target of your own.

| Target | What it's for | Link |
| :---- | :---- | :---- |
| PortSwigger Web Security Academy | Free, structured web-attack labs | [portswigger.net/web-security](https://portswigger.net/web-security) |
| OWASP Top 10 | The canonical list of web risks, explained | [owasp.org/Top10](https://owasp.org/Top10/) |
| DVWA | A deliberately-vulnerable app to self-host in your lab | [github.com/digininja/DVWA](https://github.com/digininja/DVWA) |
| TryHackMe / Hack The Box | Guided and free-form practice ranges | [tryhackme.com](https://tryhackme.com/) · [hackthebox.com](https://www.hackthebox.com/) |
