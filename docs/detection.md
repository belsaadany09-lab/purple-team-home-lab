# Detection Engineering

Detection is the half of the lab that makes it a *purple* team exercise rather
than a red one. The SIEM (Wazuh) correlates host and network telemetry and can
trigger Active Response; the network IDS (Suricata) runs on the firewall across
multiple zones; a wireless IDS covers the layer the others can't see. This page
is the honest inventory — what fires, and what doesn't yet.

## Detection stack

| Layer | Tool | Role |
| :---- | :---- | :---- |
| Host / correlation | **Wazuh** | SIEM + EDR — rules, FIM, correlation, Active Response |
| Network | **Suricata** | IDS/IPS on the firewall; per-interface instances per zone |
| Endpoint telemetry | **Sysmon** | Windows system-level event feed into the SIEM |
| Wireless (L1–L2) | **Kismet** | Deauth/flood, rogue-AP, WPS detection |
| Response | **Shuffle (SOAR)** | Automated response workflows |

## What's detected & proven

| Detection | Engine | Status |
| :---- | :---- | :---- |
| DNS attacks incl. tunneling signatures | Wazuh | <span class="status done">built</span> |
| DNS spoofing (unexpected answer) | Wazuh | <span class="status done">built</span> |
| SSH brute-force **+ Active Response** | Wazuh | <span class="status done">proven</span> |
| HTTP / HTTPS incl. web attacks | Wazuh | <span class="status done">built</span> |
| SQL injection | Suricata + Wazuh | <span class="status done">built</span> |
| XSS | Suricata | <span class="status wip">rule built, live trigger unconfirmed</span> |
| Port / UDP scan detection | Suricata → Wazuh | <span class="status done">built</span> |
| ARP new-host / flip-flop / spoof **+ Active Response** | Wazuh (built-in) | <span class="status done">proven</span> |
| Rogue DHCP server | Suricata | <span class="status done">proven</span> |
| DHCP flood / starvation | Suricata | <span class="status done">proven</span> |
| Honeypot (fake-SSH) events | Wazuh | <span class="status wip">rules exist, pipeline not wired</span> |
| File Integrity Monitoring change events | Wazuh | <span class="status done">proven (registry FIM still failing)</span> |
| Wireless deauth flood | Kismet | <span class="status done">proven via pcap replay</span> |

## Active Response: closing the loop automatically

The ARP-spoofing defense is the cleanest example of a *complete* detection:
`arpwatch` feeds the SIEM, a rule fires on the spoof, and an Active Response
automatically resolves the attacker and blocks it at the firewall — detection
and response in one chain, no human in the loop. SSH brute-force has the same
end-to-end shape.

## The detection roadmap

The lab's guiding principle means the next detections are already named, not
vague — each one answers a technique that's been proven on the offensive side.
The build queue:

- host-side telemetry for the credential-access cluster — registry-hive export
  (`reg save`), Defender-exclusion events, privilege escalation to SYSTEM
- auth-event detection for remote-management (WinRM) logons
- tunnel / pivot detection — unusual outbound patterns, beacon-interval
  signatures
- routing & switch-layer telemetry, as that cluster comes online

One insight shapes the whole queue: some of these aren't "write another SIEM
rule" problems at all — they're **structural**. Layer-2 wireless and switch-layer
traffic are invisible to a firewall/SIEM by nature, and get answered with the
*right tool for the layer* (a wireless IDS, a managed switch's own telemetry).
Knowing which gap is a rule and which is a tooling decision is itself a
detection-engineering skill — and it's why the detection roadmap is tied so
tightly to the [infrastructure roadmap](lab/routing-core.md).

## Verify detections against ground truth

A detection isn't trusted because the dashboard is green. The deauth detection
was proven by **replaying captured packets** through the IDS engine and watching
it fire — deterministic, repeatable, independent of whether the live radio
happened to catch it. The same instinct runs through everything: confirm the
*socket* is listening, not that the service "started"; confirm the *frame*
reached the interface, not that the rule was "applied."
