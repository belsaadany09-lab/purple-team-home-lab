---
hide:
  - navigation
  - toc
---

<div class="hero" markdown>

<span class="hero__kicker"><span class="dot"></span> A build-along security journey</span>

# <span class="hero__title">Build a network worth <span class="red">attacking</span> — then learn to <span class="blue">defend</span> it.</span>

<p class="hero__lead">This is the path from a single flat subnet to a segmented enterprise with its own routing core — where every attack you learn to run comes paired with the detection that catches it. No prior lab required. Follow the same route I did, one stage at a time.</p>

<div class="hero__cta" markdown>
[Start the journey](lab/stand-up.md){ .btn-primary }
[How the method works](methodology.md){ .btn-ghost }
</div>

</div>

<div class="journey">
<svg viewBox="0 0 960 210" role="img" aria-labelledby="jmap-t jmap-d" xmlns="http://www.w3.org/2000/svg">
  <title id="jmap-t">The build journey</title>
  <desc id="jmap-d">Six stages from a flat network to a defended enterprise: stand up the lab, run first attacks and detections, go deeper, wireless and web, segment the network, build a routing core.</desc>
  <defs>
    <linearGradient id="jline" x1="0" y1="0" x2="1" y2="0">
      <stop offset="0" stop-color="#4a4568"/>
      <stop offset="0.25" stop-color="#7d5fd6"/>
      <stop offset="1" stop-color="#a16bff"/>
    </linearGradient>
    <style>
      .jlbl { fill:#ece9f5; font-family:"Space Grotesk","IBM Plex Sans",sans-serif; font-weight:600; font-size:14px; }
      .jsub { fill:#9d96bb; font-family:"IBM Plex Mono",monospace; font-size:9px; letter-spacing:.04em; }
      .jnum { fill:#fff; font-family:"Space Grotesk",sans-serif; font-weight:700; font-size:16px; }
      .jcap { fill:#8e87ac; font-family:"IBM Plex Mono",monospace; font-size:10px; }
    </style>
  </defs>

  <text x="42" y="56" class="jcap">start</text>
  <rect x="34" y="60" width="30" height="30" rx="6" fill="none" stroke="#4a4568" stroke-dasharray="3 3"/>
  <text x="49" y="79" text-anchor="middle" class="jsub" font-size="8">flat</text>

  <line x1="84" y1="75" x2="872" y2="75" stroke="url(#jline)" stroke-width="3"/>

  <!-- 1 -->
  <circle cx="150" cy="75" r="17" fill="#a16bff"/>
  <text x="150" y="81" text-anchor="middle" class="jnum">1</text>
  <text x="150" y="112" text-anchor="middle" class="jsub">BUILD</text>
  <text x="150" y="132" text-anchor="middle" class="jlbl">Stand up</text>
  <text x="150" y="149" text-anchor="middle" class="jlbl">the lab</text>

  <!-- 2 split -->
  <path d="M281 75 a17 17 0 0 1 34 0 Z" fill="#4aa3ff"/>
  <path d="M281 75 a17 17 0 0 0 34 0 Z" fill="#ff5c7a"/>
  <circle cx="298" cy="75" r="17" fill="none" stroke="#0a0812" stroke-width="2"/>
  <text x="298" y="81" text-anchor="middle" class="jnum">2</text>
  <text x="298" y="112" text-anchor="middle" class="jsub" fill="#c0a0ff">ATTACK + DEFEND</text>
  <text x="298" y="132" text-anchor="middle" class="jlbl">First attacks</text>
  <text x="298" y="149" text-anchor="middle" class="jlbl">&amp; detections</text>

  <!-- 3 -->
  <circle cx="446" cy="75" r="17" fill="#ff5c7a"/>
  <text x="446" y="81" text-anchor="middle" class="jnum">3</text>
  <text x="446" y="112" text-anchor="middle" class="jsub">ATTACK</text>
  <text x="446" y="132" text-anchor="middle" class="jlbl">Go deeper:</text>
  <text x="446" y="149" text-anchor="middle" class="jlbl">creds · pivot</text>

  <!-- 4 -->
  <circle cx="594" cy="75" r="17" fill="#ff5c7a"/>
  <text x="594" y="81" text-anchor="middle" class="jnum">4</text>
  <text x="594" y="112" text-anchor="middle" class="jsub">ATTACK</text>
  <text x="594" y="132" text-anchor="middle" class="jlbl">Wireless</text>
  <text x="594" y="149" text-anchor="middle" class="jlbl">&amp; web</text>

  <!-- 5 -->
  <circle cx="724" cy="75" r="17" fill="#a16bff"/>
  <text x="724" y="81" text-anchor="middle" class="jnum">5</text>
  <text x="724" y="112" text-anchor="middle" class="jsub">BUILD</text>
  <text x="724" y="132" text-anchor="middle" class="jlbl">Segment the</text>
  <text x="724" y="149" text-anchor="middle" class="jlbl">network</text>

  <!-- 6 -->
  <circle cx="838" cy="75" r="17" fill="#a16bff"/>
  <text x="838" y="81" text-anchor="middle" class="jnum">6</text>
  <text x="838" y="112" text-anchor="middle" class="jsub">BUILD</text>
  <text x="838" y="132" text-anchor="middle" class="jlbl">Routing</text>
  <text x="838" y="149" text-anchor="middle" class="jlbl">core</text>

  <!-- goal -->
  <path d="M892 67 l0 16 M892 67 l14 4 -14 4" fill="none" stroke="#c0a0ff" stroke-width="2" stroke-linejoin="round"/>
  <text x="905" y="112" text-anchor="middle" class="jsub" fill="#c0a0ff">defended</text>
</svg>
<p class="journey__caption">six stages — build, attack, defend, repeat — from a flat /24 to a defended enterprise</p>
</div>

<span class="sides">
<span class="attack"><span class="k"></span> red = attack</span>
<span class="defend"><span class="k"></span> blue = detect &amp; defend</span>
<span class="brand"><span class="k"></span> violet = build the ground it all runs on</span>
</span>

## What this lab has already produced

This isn't theory, and it isn't the whole list — it's the highlights. Every item
below was run and proven in the lab, the attacks from the adversary's seat and
the defences by watching those same attacks trip them. The full record runs far
deeper (that's what the 119 logged lessons are).

<div class="board" markdown>
<div class="board__col--atk" markdown>
### :material-sword-cross: Attacks run live
- ARP, DNS & DHCP man-in-the-middle
- Credential theft & escalation to full system control
- A reverse-SOCKS pivot into an isolated segment
- SQL injection & a complete access-control topic
- WPA2 handshake capture & a deauth flood
</div>
<div class="board__col--def" markdown>
### :material-shield-check: Defences built & proven
- Custom SIEM & intrusion-detection rules that fire
- Automated response that blocks the attacker end-to-end
- A wireless sensor, proven deterministically by replay
- A segmented enterprise with out-of-band management
- An internal routing core peering with the firewall
</div>
</div>

<div class="stats" markdown>
<div markdown><div class="stat__n">6</div><div class="stat__l">stages, flat → defended</div></div>
<div markdown><div class="stat__n">119</div><div class="stat__l">lessons logged from real debugging</div></div>
<div markdown><div class="stat__n">2 sides</div><div class="stat__l">red + blue on every technique</div></div>
</div>

And the deeper payoff isn't a list of exploits — it's the skills they forced:
**detection engineering**, **network segmentation and routing done for real**,
and a debugging habit that transfers to everything — *never trust a tool's word;
go check the ground truth.*

## Why start flat?

A flat network — one subnet, everything able to reach everything — is the easiest
thing in the world to attack and the hardest thing to defend, because there's
nothing *between* the attacker and the target. That's exactly why it's where you
begin. You can't appreciate a wall until you've walked through the empty doorway
where one should be.

So the first thing you'll do is attack your own flat lab and watch it fall over
with nothing to stop you. Then, piece by piece, you'll give it the things a real
network has: detections that notice the attack, responses that block it, and
segments that contain it. By the end, the same attacks that sailed through on day
one hit a wall — one you built, and understand all the way down.

## The one rule that makes this work

!!! quote "An attack isn't finished when it works. It's finished when you can catch it and stop it."

    Getting a shell is the easy half, and it's where most tutorials stop. Here,
    every technique you learn comes with its defensive answer — you run the
    attack, build the detection that sees it, then harden so it can't happen
    quietly again. That loop is the whole craft, and it's why this is a *purple*
    team journey, not a red one.

## What you'll have built by the end

<div class="grid cards" markdown>

-   :material-sitemap:{ .lg .middle } __A real enterprise topology__

    ---

    Firewalled zones, an out-of-band management plane, and an internal routing
    core — the shape of a network a company would actually run, on one laptop.

-   :material-target:{ .lg .middle } __Hands-on across the kill chain__

    ---

    Recon, man-in-the-middle, credential theft, privilege escalation, pivoting,
    web and wireless — run for real, each mapped to where it sits in an attack.

-   :material-radar:{ .lg .middle } __Detections that actually fire__

    ---

    Custom SIEM and IDS rules, automated blocking, a wireless sensor — the blue
    half, proven by watching your own attacks trip them.

-   :material-compass-outline:{ .lg .middle } __A way of thinking__

    ---

    The habit of never trusting a tool's word, always checking ground truth, and
    never calling something done until you can defend against it.

</div>

## This never ends — that's the point

A lab like this is never "finished." Every layer you add is a new attack surface
*and* a new thing to learn to defend, so the ground keeps opening up underneath
you. Each build unlocks the next, and the list of what's left only ever gets
longer — which is the most honest and most alive way to learn security there is.

<div class="frontier" markdown>

**What I'm building right now — and what each one unlocks:**

- **Cutting a real zone over onto the internal routing core** — a live migration,
  one zone at a time, so the network's never undefended mid-move.
- **A managed-switch layer** — which opens a whole new class of attacks (VLAN
  hopping, MAC flooding, spanning-tree takeover) *and* finally gives ARP
  spoofing a real prevention, closing a loop that's been open for months.
- **An Active Directory domain** — which unlocks the entire identity-attack track
  that most real enterprise intrusions actually run on.
- **Safe malware reverse-engineering** — through emulation only, plus the
  low-level languages behind it.

Every one of those opens three more. I've barely scratched the surface — new
tools and techniques arrive faster than anyone could ever close them. That's not
a problem to finish; it's a frontier to keep walking.

</div>

!!! quote "Where to find the endless rest"

    The full map of what attackers do — and how defenders answer — is
    [MITRE ATT&CK](https://attack.mitre.org/): hundreds of techniques, each with
    real-world detection and mitigation guidance, and its defensive companion
    [D3FEND](https://d3fend.mitre.org/). Everything in this journey is tagged with
    its ATT&CK ID so you can keep pulling threads long after the six parts. Pick a
    technique that isn't closed yet, and go close it. There's no end to the list —
    and that's the whole point.

[Start the journey](lab/stand-up.md){ .btn-primary }

---

!!! info "Scope & ethics"

    A personal, isolated lab on private address space. Everything here is run
    against systems you own — that's the point of building your own range. This
    teaches you to *defend*; it is not a licence to touch anything that isn't
    yours. — *Built & documented by Belal.*
