# Reference

Quick-reference tables that cut across the campaigns.

## OSI model — attacks correlated to detections

Mapping every technique to the layer it operates on makes the lab's structural
blind spots obvious: everything at layer 3 and up is visible to the firewall and
SIEM; layers 1–2 are not, and need their own tooling.

| Layer | Name | Attacks / tools here | Detection |
| :----: | :---- | :---- | :---- |
| **7** | Application | SQL injection, XSS, IDOR/access control, login brute force | SIEM HTTP/SQLi/XSS rules |
| **6** | Presentation | TLS/cert weaknesses | — (not yet built) |
| **5** | Session | Remote-management & SSH sessions, interactive shells | SIEM SSH + Active Response |
| **4** | Transport | TCP/UDP scanning, the reverse-SOCKS tunnel, brute force | IDS scan signatures |
| **3** | Network | ARP poisoning, DHCP spoof/starvation, DNS spoof/tunnel, tunneled traffic; OSPF (now live) | arpwatch+SIEM, IDS DHCP/DNS rules; OSPF via syslog + firewall filter-log *(planned)* |
| **2** | Data Link | Deauth, WPA capture, MAC visibility; VLAN hopping / MAC flooding / STP *(coming)* | Wireless IDS (deauth **proven**); managed-switch telemetry *(planned)* |
| **1** | Physical | RF range/signal, the physical WPS button | RF shielding / reduced TX power only |

!!! info "Why the bottom two rows look different"

    Everything in layers 3–7 is visible to a firewall/SIEM. Layers 1–2 are not —
    structurally, not for lack of a rule. That's why wireless gets a **wireless
    IDS** and the switch layer gets a **managed switch's own telemetry**, rather
    than another SIEM signature. Matching the defense to the layer is the whole
    idea.

## Kill-chain lifecycle — the wireless cluster as a worked example

Every campaign maps onto the same lifecycle. Wireless makes a clean example of
how *detectability* varies by stage:

| Stage | Wireless equivalent | Detectable? |
| :---- | :---- | :---- |
| Recon | Passive scan | No — passive RX, zero transmission |
| Foothold | Deauth → forced handshake capture | **Yes** — the one loud moment (wireless-IDS alert, proven) |
| Escalation | Offline cracking of the captured handshake | No — entirely offline, off the air |
| Objective | Password recovery | (attempted, not achieved) |
| After-action | Lessons + cluster entry | — |

The lesson of the table: the attacker is only *visible* for one step of a
five-step chain. That's why the defense centers on catching the deauth — it's
the only moment there's anything to catch.

## Ports & protocols in play

| Port | Service | Role in the lab |
| :---- | :---- | :---- |
| 22 / 2200 | SSH | Real SSH; honeypot moved its real SSH off the bait port |
| 53 | DNS | Internal resolver; the one user-zone→server-zone flow allowed |
| 80 / 443 | HTTP/S | Web-app target; the only outbound egress ports for user/server zones |
| 89 | OSPF | The firewall ↔ routing-core peering (IP protocol 89) |
| 445 | SMB | Filtered on the victim — which is what forced the SMB-independent dump |
| 1080 / 8000 | SOCKS / chisel | The pivot's SOCKS proxy and reverse-tunnel listener |
| 1514 / 1515 | SIEM agent | Agent comms/enrollment; the one DMZ→Server-LAN flow allowed |
| 2222 | SSH (fake) | Honeypot bait; the one Attacker-Net→DMZ inbound allowed |
| 5985 | WinRM | Victim foothold / tunnel-control test |

## Lab addressing

| Zone | Subnet | Key hosts |
| :---- | :---- | :---- |
| Corp-LAN | `10.0.1.0/24` | Windows workstation (`.117`) |
| Server-LAN | `10.0.2.0/24` | SIEM `.10`, DNS/web `.20`, SOAR `.50` |
| DMZ | `10.0.3.0/24` | Honeypot `.50` |
| Attacker-Net | `10.0.4.0/24` | Kali |
| MGMT | `10.0.99.0/24` | Out-of-band admin |
| TRANSIT | `10.255.0.0/30` | Firewall `.2` ↔ routing core `.1` |
| inner-net | `10.9.0.0/24` | Isolated segment, reached only via the pivot |
