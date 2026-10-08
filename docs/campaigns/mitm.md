<div class="chapter-head" markdown>
<span class="chapter-head__num">02</span>
<div markdown>
<span class="chapter-head__eyebrow">The build · Part 2 of 6</span>
# <span class="chapter-head__title">First attacks &amp; detections</span>
</div>
</div>

This is where the whole method clicks into place. You'll run three classic
attacks that abuse the trust built into how a network hands out addresses and
finds its way around — and for each one, you'll build the detection that catches
it and the control that stops it. By the end you'll have done the full
**attack → detect → mitigate** loop three times, and it'll feel natural for
everything after.

!!! concept "Why these three first"

    ARP, DNS and DHCP are the plumbing every network runs on, and none of them
    were designed with an attacker in mind — they simply trust whatever answers
    first. That makes them the perfect first targets: the attacks are quick, the
    effect is dramatic, and each one has a clean, satisfying detection. Learn the
    loop here on easy mode.

<div class="tech" markdown>

### ARP cache poisoning <span class="status done">closed</span>

<div class="meta" markdown>
[ATT&CK T1557.002](https://attack.mitre.org/techniques/T1557/002/){ .attck }
<span>needs: a packet sniffer + an ARP-spoofing tool</span>
</div>

**What it is.** On a local network, machines find each other's hardware
addresses by shouting "who has this IP?" and trusting the first reply. An
attacker just answers faster and more often — claiming to be the gateway — so the
victim starts sending its traffic through the attacker instead. It's the
textbook on-path (man-in-the-middle) position, and everything downstream
(sniffing, tampering, DNS spoofing) builds on it.

**Try it, briefly.** Put your attacker on the same network as the victim, point
an ARP-spoofing tool at the victim and the gateway, and watch the victim's ARP
table now list the attacker's hardware address for the gateway. Capture the
victim's traffic to confirm it's flowing through you.

**Catch it.** An ARP-watching tool feeds your SIEM, which fires when a known IP
suddenly maps to a new hardware address (the "flip-flop"). **Stop it.** The real
prevention — Dynamic ARP Inspection on a managed switch — arrives with the
switch layer in [Part 6](../lab/routing-core.md); until then, an automated
response that blocks the attacker is the answer.

</div>

<div class="tech" markdown>

### DNS spoofing <span class="status done">closed</span>

<div class="meta" markdown>
[ATT&CK T1557](https://attack.mitre.org/techniques/T1557/){ .attck }
<span>needs: an on-path position (above) + a DNS-spoof tool</span>
</div>

**What it is.** Once you're on-path, you can answer the victim's DNS questions
before the real resolver does — so "where is this website?" comes back pointing
at a machine you control. It turns a man-in-the-middle position into
redirection: the victim types the right name and lands somewhere wrong.

**Try it, briefly.** With the ARP position from above in place, run a DNS-spoof
tool that watches for the victim's lookups and forges a reply for the name you
care about, pointing it at your attacker box. Confirm from the victim that the
name now resolves to your address.

**Catch it.** A SIEM rule flags a DNS answer that doesn't match what the
legitimate resolver would have returned, at high severity — a mismatch is a
strong signal something on-path is lying.

</div>

<div class="tech" markdown>

### DHCP spoofing &amp; starvation <span class="status done">attack + detect</span>

<div class="meta" markdown>
[ATT&CK T1557.003](https://attack.mitre.org/techniques/T1557/003/){ .attck }
<span>needs: a rogue-DHCP tool + a packet-crafting library</span>
</div>

**What it is.** When a machine joins a network it asks "who'll give me an
address?" and trusts the first offer. A rogue DHCP server answers first and
hands out an attacker-controlled gateway and DNS — on-path again, from the moment
a device connects. *Starvation* is the companion trick: flood the real server
with fake requests until its pool is empty, so yours is the only one left
answering.

**Try it, briefly.** Stand up a rogue DHCP server on your attacker box offering
your address as the gateway. To win the race reliably, run a small
packet-crafting script that drains the legitimate pool first, then watch a
fresh client take your lease.

**Catch it.** Two network-IDS signatures — one for an unexpected DHCP server on
the wire, one for the flood pattern of a starvation attack — both fire cleanly.
**Stop it.** DHCP snooping on the managed-switch layer ([Part 6](../lab/routing-core.md))
is the prevention; it shares that upgrade with the ARP fix.

</div>

## What you just learned

Three attacks, three detections, the loop three times. More importantly, you've
seen the shape that repeats for everything ahead: an attacker abuses something
that trusts too easily, a detection notices the thing that's now *different*, and
a control removes the trust. Notice too that two of these mitigations point at
the *same* future upgrade — a managed switch — which is a theme: defences often
consolidate onto one good piece of infrastructure.

Next you go past the plumbing and into the host itself — stealing credentials,
escalating privilege, and moving somewhere you're not supposed to be.

[Continue to Part 3: Go deeper](go-deeper.md){ .btn-primary }
