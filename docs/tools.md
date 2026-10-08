# Toolbox

Every tool here was actually used in the lab, not just installed. Grouped by
what they're for.

## Recon & vulnerability scanning

| Tool | What it's for |
| :---- | :---- |
| Nmap | Host discovery, port/service/OS fingerprinting, scripted vuln checks |
| arp-scan | Fast ARP-based host discovery on the local subnet |
| Nikto | Web server vulnerability / misconfiguration scanner |
| searchsploit | Local exploit-DB lookup by service/version/CVE |
| GVM / OpenVAS | Full vulnerability-scanning platform |

## MITM & spoofing

| Tool | What it's for |
| :---- | :---- |
| arpspoof / dnsspoof (dsniff) | Forged ARP replies; forged DNS responses from a hosts file |
| Scapy | Python packet-crafting — hand-built DHCP handshakes |
| custom scripts | DHCP starvation (pool-drain) and flood generators, built from scratch |

## Credential access & exploitation

| Tool | What it's for |
| :---- | :---- |
| Hydra / netexec | Online brute-force & multi-protocol credential spraying |
| evil-winrm | Interactive remote-management shell |
| Metasploit Framework | Exploit/payload framework, module search, RCE-applicability checks |
| impacket-secretsdump | Offline SAM/SYSTEM/SECURITY hive decrypt (SMB-independent) |
| native Windows (`reg save`, `schtasks`) | Local hive export; scheduled-task privesc to SYSTEM |

## Networking & pivot

| Tool | What it's for |
| :---- | :---- |
| chisel | Reverse-SOCKS pivot tunnel |
| proxychains | Routes non-SOCKS-aware tools through the proxy |
| nc (netcat) | Single-port connect tests through the tunnel (trustworthy where a scanner isn't) |
| VBoxManage | VirtualBox from the CLI — add NICs the GUI won't expose, read true adapter config |

## Detection & defense

| Tool | What it's for |
| :---- | :---- |
| Wazuh | SIEM/EDR — correlation, alerting, Active Response |
| Suricata | Network IDS/IPS, per-interface instances per zone |
| arpwatch | ARP-change monitoring feeding the SIEM |
| Sysmon | Windows system-level event logging |
| Shuffle | SOAR — automated response workflows |
| pfSense | Zone firewall + DHCP + the IDS host |

## Wireless

| Tool | What it's for |
| :---- | :---- |
| aircrack-ng suite | Monitor mode (airmon-ng), capture (airodump-ng), injection/deauth (aireplay-ng) |
| hcxtools | Convert captures to a crackable hash format; MAC-vendor lookups |
| Wireshark | Read the real EAPOL handshake fields packet by packet |
| Kismet | Continuous wireless IDS — deauth, WPS, rogue-AP detection |
| CUPP / PACK / hashcat / aircrack-ng | Wordlist generation, mask modeling, and the cracking engines |

## Network emulation & routing

| Tool | What it's for |
| :---- | :---- |
| GNS3 | Network emulator orchestrating the VMs + emulated routers/switches |
| FRRouting (FRR) | The internal routing core — Cisco-like CLI, OSPF |
| Docker / QEMU | Appliance engines behind the emulated nodes |

## OSINT & web

| Tool | What it's for |
| :---- | :---- |
| Burp Suite / browser DevTools | Intercepting proxy, request tampering, session-cookie inspection |
| sqlmap / ghauri | Automated SQL injection (wrapped in proxychains for pivot work) |
| passive-recon toolkits | Certificate-transparency, subdomain, and metadata OSINT against authorized targets |
