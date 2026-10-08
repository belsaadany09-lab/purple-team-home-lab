<div class="chapter-head" markdown>
<span class="chapter-head__num">04</span>
<div markdown>
<span class="chapter-head__eyebrow">The build · Part 4 of 6</span>
# <span class="chapter-head__title">Wireless &amp; web</span>
</div>
</div>

Two surfaces that live at the edges of what a normal firewall can even perceive.
**Wireless** happens below the network layer entirely — a firewall simply can't
see it, so it needs its own kind of sensor. **Web** rides on top of everything,
where the attack is valid traffic to a valid service and the only tell is in the
payload. Different ends of the stack, same lesson: match your defence to the
layer the attack lives on.

!!! concept "Some attacks a firewall will never see"

    A firewall watches layers 3 and up. Wireless deauthentication and handshake
    capture happen at layers 1–2, in the air — structurally invisible to it. Web
    attacks are the opposite problem: they're perfectly well-formed requests, so
    the firewall waves them through and only a content-aware rule catches them.
    Recognising *which* tool belongs at *which* layer is the real skill here.

## Wireless

!!! info "Your own hardware only"

    Everything in this section is done against an access point **you own**, with
    a USB adapter that supports monitor mode and packet injection. Capturing or
    disrupting anyone else's network is illegal — the point is to learn the
    radio layer on your own kit.

<div class="tech" markdown>

### WPA2 handshake capture &amp; offline crack <span class="status wip">attack done</span>

<div class="meta" markdown>
[ATT&CK T1040](https://attack.mitre.org/techniques/T1040/){ .attck }
[ATT&CK T1110.002](https://attack.mitre.org/techniques/T1110/002/){ .attck }
<span>needs: a monitor-mode Wi-Fi adapter + the aircrack-ng suite</span>
</div>

**What it is.** When a device joins a WPA2 network it performs a four-way
handshake. Capture that handshake and you can take it offline and try to recover
the password by guessing — the network never knows you're trying, because all the
work happens on your laptop, off the air.

**Try it, briefly.** Put your adapter in monitor mode, capture on the target
channel, and grab the handshake (a brief deauth, below, forces a device to
re-join so you don't have to wait). Then run an offline cracker against it —
with a wordlist, a mask built from likely password structure, or rules. Knowing
*why* it doesn't crack (no GPU, a search space measured in years) is itself the
result.

**Catch it.** The capture is passive — there's nothing to detect. The *deauth*
that usually precedes it is the detectable moment (next).

</div>

<div class="tech" markdown>

### Deauthentication flood <span class="status done">attack + detect</span>

<div class="meta" markdown>
[ATT&CK T1498](https://attack.mitre.org/techniques/T1498/){ .attck }
<span>needs: injection-capable adapter + a wireless IDS (e.g. Kismet)</span>
</div>

**What it is.** Wi-Fi management frames aren't authenticated by default, so an
attacker can forge "disconnect now" frames and knock clients off — annoying on
its own, and the lever that forces the handshake above.

**Try it, briefly.** Send a bounded burst of deauth frames at your own access
point while capturing, and watch a client drop and re-join (handing you the
handshake as a byproduct).

**Catch it.** A dedicated **wireless IDS** flags the flood — and names *why* it
worked: the client wasn't using management-frame protection. **Stop it.** Turn on
802.11w / PMF, the fix the detection itself points at. Proving this detection
deterministically — by replaying a captured file through the IDS — is a neat
trick when you only have one radio.

</div>

## Web

<div class="tech" markdown>

### SQL injection <span class="status done">closed</span>

<div class="meta" markdown>
[ATT&CK T1190](https://attack.mitre.org/techniques/T1190/){ .attck }
<span>needs: a vulnerable web app + an intercepting proxy / sqlmap</span>
</div>

**What it is.** When an app glues your input straight into a database query, you
can smuggle in query *logic* instead of data — and read or change anything the
database holds. Still one of the highest-impact web bugs there is.

**Try it, briefly.** Against your lab's deliberately-vulnerable web app, find an
input that reaches a query, confirm injection, then enumerate databases and
tables and dump data. Automated tooling does the heavy lifting; the stealth
knobs (how aggressive, how fast, randomised headers) are the levers that trade
speed for noise.

**Catch it.** Both a content-aware IDS signature and a SIEM rule flag the
injection patterns in the request.

</div>

<div class="tech" markdown>

### Broken access control / IDOR <span class="status done">topic complete</span>

<div class="meta" markdown>
[ATT&CK T1190](https://attack.mitre.org/techniques/T1190/){ .attck }
[OWASP: Broken Access Control](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)
<span>needs: an intercepting proxy + a practice target</span>
</div>

**What it is.** The app checks *who you are* at login but forgets to check *what
you're allowed to touch* on each request — so changing an ID in a URL, or
replaying a request, hands you someone else's data or an admin action. The
single most common serious web bug in the real world.

**Try it, briefly.** Work a structured set of access-control labs end to end
(see the recon note below for where authorised practice targets live), tampering
with identifiers and request methods to reach what you shouldn't.

</div>

<div class="aside" markdown>
<span class="aside__h">Before you touch a web target — recon &amp; OSINT.</span>
Real web work starts with mapping what's exposed, using only passive,
public-data sources against targets you're authorised to test. Subdomain and
certificate-transparency lookups, exposed-service search, and breach-exposure
checks all live in the
[ATT&CK &amp; OSINT field guide](../reference/osint.md), with links. Scope
discipline is the whole game: your own apps, deliberately-vulnerable practice
targets, or something you have written permission to test — nothing else.
</div>

## What you just learned

You've now worked both ends of the stack and seen why each needed a *different*
kind of defence — a radio sensor below, a content-aware rule above. That's the
last of the attack-heavy parts. From here the journey turns to building the
network up into something that can actually hold: zones, then an internal core.

[Continue to Part 5: Segment the network](../lab/architecture.md){ .btn-primary }
