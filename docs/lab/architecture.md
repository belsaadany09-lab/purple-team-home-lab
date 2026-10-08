<div class="chapter-head" markdown>
<span class="chapter-head__num">05</span>
<div markdown>
<span class="chapter-head__eyebrow">The build · Part 5 of 6</span>
# <span class="chapter-head__title">Segment the network</span>
</div>
</div>

Up to now your lab has been flat on purpose — so you could watch attacks move
through it unopposed. This is the stage where that changes. Segmentation is the
first control that doesn't just *notice* an attack but **contains** it: even if
one host falls, the blast radius stops at the edge of its zone. It's also the
single biggest step toward a network that looks like something a real
organisation runs.

## The story: the day "finished" turned out to mean "not started"

The first time I built this, I wrote the entire firewall policy — every zone,
every rule — felt done, and ran a test to confirm hosts in different zones
couldn't reach each other. They reached each other fine. Nothing was contained.

The policy was perfect and completely inert, because I'd authored the rules but
never actually *moved the hosts into the zones*. They were all still sitting on
the old flat network the rules didn't apply to. That gap — between a policy
written and a policy enforced — is the lesson that defines this whole stage, and
it's why the last thing you do here isn't "apply the rules," it's "prove it with
a test."

## The concept

!!! concept "Segmentation is the implicit deny, not the allow list"

    A zoned network isn't defined by the holes you open — it's defined by the
    **default deny** underneath them. You split the network into zones (a user
    zone, a server zone, a DMZ, the attacker's own segment), give the firewall
    one leg in each, and then write a short list of *narrow allows* for traffic
    that genuinely must flow. Everything you don't explicitly permit falls
    through to the deny at the bottom. That silent deny is the wall.

<div class="journey">
<svg viewBox="0 0 1000 470" role="img" aria-labelledby="top-t top-d" xmlns="http://www.w3.org/2000/svg">
  <title id="top-t">The segmented topology</title>
  <desc id="top-d">The internet reaches a perimeter firewall; an out-of-band management plane sits beside it; an internal routing core sits beneath it; and four zones hang below — users, servers (defensive), a contained DMZ, and the attacker's own segment.</desc>
  <defs>
    <style>
      .nm  { fill:#ece9f5; font-family:"Space Grotesk",sans-serif; font-weight:600; font-size:16px; }
      .mt  { fill:#9d96bb; font-family:"IBM Plex Mono",monospace; font-size:12px; }
      .ed  { stroke:#4f4873; stroke-width:1.8; fill:none; }
      .da  { stroke:#7d5fd6; stroke-width:1.6; stroke-dasharray:5 4; fill:none; }
      .ic  { fill:none; stroke-width:1.8; stroke-linecap:round; stroke-linejoin:round; }
    </style>
    <symbol id="ic-globe" viewBox="0 0 24 24"><g class="ic" stroke="currentColor"><circle cx="12" cy="12" r="9"/><path d="M3 12h18M12 3c3 3.2 3 14.8 0 18M12 3c-3 3.2-3 14.8 0 18"/></g></symbol>
    <symbol id="ic-shield" viewBox="0 0 24 24"><g class="ic" stroke="currentColor"><path d="M12 2.5l7.5 3v6c0 4.6-3.2 8-7.5 9.2C7.7 19.5 4.5 16.1 4.5 11.5v-6z"/><path d="M9 12l2 2 4-4.2"/></g></symbol>
    <symbol id="ic-key" viewBox="0 0 24 24"><g class="ic" stroke="currentColor"><circle cx="8" cy="12" r="4.2"/><path d="M11.8 12H21M18 12v3.4M21 12v2.6"/></g></symbol>
    <symbol id="ic-hub" viewBox="0 0 24 24"><g class="ic" stroke="currentColor"><circle cx="12" cy="12" r="2.6"/><circle cx="12" cy="4" r="1.6"/><circle cx="12" cy="20" r="1.6"/><circle cx="4" cy="12" r="1.6"/><circle cx="20" cy="12" r="1.6"/><path d="M12 6.4v3M12 14.6v3.8M6.6 12h2.8M14.6 12h3.8"/></g></symbol>
    <symbol id="ic-monitor" viewBox="0 0 24 24"><g class="ic" stroke="currentColor"><rect x="3" y="4" width="18" height="12" rx="1.5"/><path d="M9 20h6M12 16v4"/></g></symbol>
    <symbol id="ic-server" viewBox="0 0 24 24"><g class="ic" stroke="currentColor"><rect x="3" y="4" width="18" height="6.2" rx="1.4"/><rect x="3" y="13.8" width="18" height="6.2" rx="1.4"/><path d="M6.5 7.1h.01M6.5 16.9h.01"/></g></symbol>
    <symbol id="ic-hex" viewBox="0 0 24 24"><g class="ic" stroke="currentColor"><path d="M12 2.5l8.2 4.7v9.6L12 21.5l-8.2-4.7V7.2z"/></g></symbol>
    <symbol id="ic-target" viewBox="0 0 24 24"><g class="ic" stroke="currentColor"><circle cx="12" cy="12" r="8.2"/><circle cx="12" cy="12" r="3.4"/><path d="M12 1.2v3.6M12 19.2v3.6M1.2 12h3.6M19.2 12h3.6"/></g></symbol>
  </defs>

  <g style="color:#9d96bb"><use href="#ic-globe" x="484" y="16" width="28" height="28"/></g>
  <text x="498" y="62" text-anchor="middle" class="mt">internet</text>
  <path d="M498 66 V84" class="ed"/>

  <rect x="338" y="84" width="320" height="74" rx="12" fill="#a16bff" fill-opacity="0.09" stroke="#a16bff" stroke-width="1.8"/>
  <g style="color:#c0a0ff"><use href="#ic-shield" x="360" y="106" width="30" height="30"/></g>
  <text x="404" y="118" class="nm">Perimeter firewall</text>
  <text x="404" y="142" class="mt">default-deny · narrow allows only</text>

  <rect x="742" y="84" width="248" height="74" rx="12" fill="#5a5280" fill-opacity="0.12" stroke="#6a5fa0" stroke-width="1.5"/>
  <g style="color:#b3abd1"><use href="#ic-key" x="762" y="106" width="28" height="28"/></g>
  <text x="800" y="118" class="nm">Management</text>
  <text x="800" y="142" class="mt">admin → all · none → admin</text>
  <path d="M658 121 H742" class="da"/>
  <text x="700" y="110" text-anchor="middle" class="mt" fill="#b3abd1">out-of-band</text>

  <path d="M498 158 V192" class="da"/>
  <text x="516" y="180" class="mt" fill="#c0a0ff">OSPF</text>
  <rect x="348" y="192" width="300" height="66" rx="12" fill="#a16bff" fill-opacity="0.06" stroke="#a16bff" stroke-width="1.4" stroke-dasharray="6 4"/>
  <g style="color:#c0a0ff"><use href="#ic-hub" x="370" y="212" width="28" height="28"/></g>
  <text x="410" y="224" class="nm">Internal routing core</text>
  <text x="410" y="246" class="mt">east-west routing · Part 6</text>

  <path d="M498 258 V298" class="ed"/>
  <path d="M240 298 H860" class="ed"/>
  <path d="M240 298 V330 M447 298 V330 M653 298 V330 M860 298 V330" class="ed"/>

  <rect x="150" y="330" width="190" height="116" rx="12" fill="#5a5280" fill-opacity="0.1" stroke="#6a5fa0" stroke-width="1.6"/>
  <g style="color:#b3abd1"><use href="#ic-monitor" x="170" y="350" width="24" height="24"/></g>
  <text x="204" y="368" class="nm">Users</text>
  <text x="172" y="402" class="mt">10.0.1.0/24</text>
  <text x="172" y="426" class="mt">workstations</text>

  <rect x="357" y="330" width="190" height="116" rx="12" fill="#4aa3ff" fill-opacity="0.1" stroke="#4aa3ff" stroke-width="1.8"/>
  <g style="color:#8fc3ff"><use href="#ic-server" x="377" y="350" width="24" height="24"/></g>
  <text x="411" y="368" class="nm">Servers</text>
  <text x="379" y="402" class="mt">10.0.2.0/24</text>
  <text x="379" y="426" class="mt" fill="#8fc3ff">SIEM · DNS · apps</text>

  <rect x="563" y="330" width="190" height="116" rx="12" fill="#5a5280" fill-opacity="0.1" stroke="#6a5fa0" stroke-width="1.6"/>
  <g style="color:#b3abd1"><use href="#ic-hex" x="583" y="350" width="24" height="24"/></g>
  <text x="617" y="368" class="nm">DMZ</text>
  <text x="585" y="402" class="mt">10.0.3.0/24</text>
  <text x="585" y="426" class="mt">honeypot</text>

  <rect x="770" y="330" width="190" height="116" rx="12" fill="#ff5c7a" fill-opacity="0.1" stroke="#ff5c7a" stroke-width="1.8"/>
  <g style="color:#ff9bad"><use href="#ic-target" x="790" y="350" width="24" height="24"/></g>
  <text x="824" y="368" class="nm">Attacker net</text>
  <text x="792" y="402" class="mt">10.0.4.0/24</text>
  <text x="792" y="426" class="mt" fill="#ff9bad">your attack box</text>
</svg>
<p class="journey__caption">example addressing — use your own. red is where the attacker lives, blue is where your defences run, violet is the ground you build.</p>
</div>

## The zones, and why each exists

| Zone | What lives there | Why it's walled off |
| :---- | :---- | :---- |
| **Users** | everyday workstations | a user zone should do its job and nothing else — it can't reach other zones or the firewall itself |
| **Servers** | your SIEM, DNS, internal apps | your defensive crown jewels; reachable only for the specific services that need it |
| **DMZ** | the honeypot | built to be poked at, so it's contained — one narrow path out for telemetry, nothing else |
| **Attacker net** | your attack box | the adversary gets its own segment, so your attacks start from a realistic outside position |
| **Management** | admin access | out-of-band: it reaches every zone, but **no zone can reach it** — the asymmetry is the whole point |

<div class="how" markdown>
<span class="how__h">How to actually do it</span>

1. **Decide the zones first, on paper.** Group hosts by what they are and who
   should talk to them. The design is the hard part; the config is easy once
   it's right.
2. **Give the firewall one interface per zone.** In a firewall like *pfSense* or
   *OPNsense*, that's the interface-assignments screen — each zone gets its own
   leg with its own gateway address and subnet. Turn on DHCP per zone if you want
   hosts to auto-address.
3. **Make one reusable "private ranges" object.** Build an alias for all private
   address space once; you'll point rules at it two ways — *everything except
   this* (internet-only egress) and *exactly this* (block all cross-zone).
4. **Write allows as the exception, then stop.** Per zone, permit only the
   specific flows that must happen, and let the default deny at the bottom do the
   containment. Order matters — a broad rule above a narrow one hides it.
5. **Actually move the hosts in.** In your hypervisor, put each VM's network
   adapter on the matching zone network and confirm it picks up an address in
   that zone. This is the step the story above is about.
6. **Add a separate management path** (see the gotcha below) so you can still
   administer everything without flattening it.
7. **Prove it** with the test in the checklist — don't assume it.
</div>

<div class="aside" markdown>
<span class="aside__h">Where people get stuck —</span> the "out-of-band"
management trap. The obvious move is to give every server a second network
adapter on a "host-only" network so you can administer it. It *feels*
out-of-band. It usually isn't: that host-only network is often the same layer-2
as your user zone, so every server ends up quietly bridged straight across the
firewall — a bypass around the very thing you just built. Give management its
own real segment and its own firewall leg instead, with a deliberate asymmetry:
admin reaches every zone, no zone reaches admin. The tell is simple — if your
admin path shares a broadcast domain with the thing it administers, it isn't
out-of-band, whatever you named it.
</div>

<div class="done" markdown>
<span class="done__h">BEFORE YOU MOVE ON</span>

- From a host *inside* one zone, you try to reach another zone and it **fails** —
  tested live, not assumed from the diagram.
- From that same host, normal traffic (its own zone, and the internet) still
  **works**.
- Every host actually lives on its zone's network and pulled a zone address.
- You can administer every zone from the management plane, but nothing can reach
  the management plane back.
</div>

Next, the network stops leaning on the perimeter firewall for everything and
grows an internal core of its own — which is where routing, and a whole new
class of attacks, enters the picture.

[Continue to Part 6: Build a routing core](routing-core.md){ .btn-primary }
