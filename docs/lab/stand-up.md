<div class="chapter-head" markdown>
<span class="chapter-head__num">01</span>
<div markdown>
<span class="chapter-head__eyebrow">The build · Part 1 of 6</span>
# <span class="chapter-head__title">Stand up the lab</span>
</div>
</div>

Everything that follows — every attack, every detection — needs somewhere to
happen. That's this part: a handful of virtual machines on one computer, wired
into a single flat network. Flat on purpose. You'll spend the next parts
attacking it, and only once you've felt how defenceless a flat network is will
you start giving it walls.

## The concept

!!! concept "A lab is just a few roles on a virtual switch"

    You don't need hardware or a cloud bill. A **hypervisor** runs several small
    VMs on your laptop, and a **virtual network** connects them like a switch
    would. Give each VM one clear job — attacker, victim, a server or two, a
    place to collect logs — drop them all on the same virtual network to start,
    and you have a lab. The realism comes later, from how you *separate* them.

## The machines, and the job each one does

You can start smaller and grow this — but here's the cast the rest of the
journey uses. Tool names are examples; swap in equivalents you prefer.

| Machine | Job | A common choice |
| :---- | :---- | :---- |
| **Attacker** | where you run offensive tools | a pentest-focused Linux (e.g. Kali or Parrot) |
| **Victim** | the box you compromise first | a stock Windows or Linux desktop |
| **SIEM / log collector** | gathers logs and raises alerts | an open SIEM such as Wazuh (or Elastic/Security Onion) |
| **A server or two** | things worth attacking: DNS, a web app | any small Linux running a DNS service + a deliberately-vulnerable web app |
| **Firewall / router** | the edge, and later your zone enforcer | a software firewall such as pfSense or OPNsense |
| **Honeypot** *(optional early)* | a deliberate lure that logs intruders | a low-interaction honeypot such as Cowrie |

<div class="journey">
<svg viewBox="0 0 900 250" role="img" aria-labelledby="su-t su-d" xmlns="http://www.w3.org/2000/svg">
  <title id="su-t">The starting flat lab</title>
  <desc id="su-d">Five machines — attacker, victim, SIEM, a server, and a firewall — all connected to one flat virtual network with nothing between them.</desc>
  <defs>
    <style>
      .nm{fill:#ece9f5;font-family:"Space Grotesk",sans-serif;font-weight:600;font-size:12px;}
      .mt{fill:#9d96bb;font-family:"IBM Plex Mono",monospace;font-size:9px;}
      .ic{fill:none;stroke-width:1.8;stroke-linecap:round;stroke-linejoin:round;}
    </style>
    <symbol id="s-target" viewBox="0 0 24 24"><g class="ic" stroke="currentColor"><circle cx="12" cy="12" r="8.2"/><circle cx="12" cy="12" r="3.4"/><path d="M12 1.2v3.6M12 19.2v3.6M1.2 12h3.6M19.2 12h3.6"/></g></symbol>
    <symbol id="s-monitor" viewBox="0 0 24 24"><g class="ic" stroke="currentColor"><rect x="3" y="4" width="18" height="12" rx="1.5"/><path d="M9 20h6M12 16v4"/></g></symbol>
    <symbol id="s-radar" viewBox="0 0 24 24"><g class="ic" stroke="currentColor"><path d="M12 12V3a9 9 0 1 0 9 9z"/><path d="M12 12l6-3"/></g></symbol>
    <symbol id="s-server" viewBox="0 0 24 24"><g class="ic" stroke="currentColor"><rect x="3" y="4" width="18" height="6.2" rx="1.4"/><rect x="3" y="13.8" width="18" height="6.2" rx="1.4"/><path d="M6.5 7.1h.01M6.5 16.9h.01"/></g></symbol>
    <symbol id="s-shield" viewBox="0 0 24 24"><g class="ic" stroke="currentColor"><path d="M12 2.5l7.5 3v6c0 4.6-3.2 8-7.5 9.2C7.7 19.5 4.5 16.1 4.5 11.5v-6z"/></g></symbol>
  </defs>

  <text x="450" y="30" text-anchor="middle" class="mt" fill="#ff9bad">one flat network — everything can reach everything</text>
  <line x1="90" y1="70" x2="810" y2="70" stroke="#5a3a4a" stroke-width="2" stroke-dasharray="2 4"/>

  <g transform="translate(70,95)">
    <rect width="150" height="78" rx="10" fill="#ff5c7a" fill-opacity="0.1" stroke="#ff5c7a" stroke-width="1.5"/>
    <g style="color:#ff9bad"><use href="#s-target" x="14" y="14" width="20" height="20"/></g>
    <text x="42" y="29" class="nm">Attacker</text>
    <text x="14" y="55" class="mt">offensive tools</text>
    <line x1="75" y1="0" x2="75" y2="-25" stroke="#5a3a4a" stroke-width="1.5"/>
  </g>
  <g transform="translate(240,95)">
    <rect width="150" height="78" rx="10" fill="#5a5280" fill-opacity="0.1" stroke="#6a5fa0" stroke-width="1.5"/>
    <g style="color:#b3abd1"><use href="#s-monitor" x="14" y="14" width="20" height="20"/></g>
    <text x="42" y="29" class="nm">Victim</text>
    <text x="14" y="55" class="mt">first foothold</text>
    <line x1="75" y1="0" x2="75" y2="-25" stroke="#4f4873" stroke-width="1.5"/>
  </g>
  <g transform="translate(410,95)">
    <rect width="150" height="78" rx="10" fill="#4aa3ff" fill-opacity="0.1" stroke="#4aa3ff" stroke-width="1.5"/>
    <g style="color:#8fc3ff"><use href="#s-radar" x="14" y="14" width="20" height="20"/></g>
    <text x="42" y="29" class="nm">SIEM</text>
    <text x="14" y="55" class="mt">logs &amp; alerts</text>
    <line x1="75" y1="0" x2="75" y2="-25" stroke="#2f5a86" stroke-width="1.5"/>
  </g>
  <g transform="translate(580,95)">
    <rect width="150" height="78" rx="10" fill="#4aa3ff" fill-opacity="0.07" stroke="#4aa3ff" stroke-width="1.3"/>
    <g style="color:#8fc3ff"><use href="#s-server" x="14" y="14" width="20" height="20"/></g>
    <text x="42" y="29" class="nm">Server</text>
    <text x="14" y="55" class="mt">DNS · web app</text>
    <line x1="75" y1="0" x2="75" y2="-25" stroke="#2f5a86" stroke-width="1.5"/>
  </g>
  <g transform="translate(730,95)">
    <rect width="140" height="78" rx="10" fill="#a16bff" fill-opacity="0.09" stroke="#a16bff" stroke-width="1.4"/>
    <g style="color:#c0a0ff"><use href="#s-shield" x="14" y="14" width="20" height="20"/></g>
    <text x="42" y="29" class="nm">Firewall</text>
    <text x="14" y="55" class="mt">the edge</text>
    <line x1="70" y1="0" x2="70" y2="-25" stroke="#5a4b86" stroke-width="1.5"/>
  </g>
</svg>
<p class="journey__caption">example starting point — one flat /24 (10.0.1.0/24), nothing between any two machines. that's deliberate.</p>
</div>

<div class="how" markdown>
<span class="how__h">How to actually do it</span>

1. **Install a hypervisor.** Anything that runs several VMs works — VirtualBox
   (free) or VMware are the usual picks. Give it as much RAM as you can spare;
   this is the one real constraint on a laptop.
2. **Create one internal/host-only network** and put every VM on it. This is
   your flat starting network — think of it as a single unmanaged switch.
3. **Build the attacker and victim first.** A pentest Linux and a stock desktop
   OS are enough to start running Part 2. Snapshot each VM clean so you can roll
   back after you break something.
4. **Add the log collector early.** Stand up the SIEM and install its agent on
   the victim — you want to be *watching* from the very first attack, not bolting
   detection on later.
5. **Add a server to aim at.** A small Linux running a DNS service and a
   deliberately-vulnerable web app gives you realistic targets.
6. **Give everything a predictable address.** A simple flat range (the examples
   here use `10.0.1.0/24`) keeps your head clear; you'll re-address into zones in
   Part 5.
</div>

<div class="aside" markdown>
<span class="aside__h">Worth doing now —</span> snapshot every VM in a known-good
state before you touch it. You *will* break things on purpose, and a clean
snapshot turns "I bricked the victim" into a thirty-second rollback instead of a
rebuild. Also give the SIEM generous disk — logs grow faster than you expect,
and a full disk looks exactly like a dozen unrelated daemons failing at once.
</div>

<div class="done" markdown>
<span class="done__h">BEFORE YOU MOVE ON</span>

- Every VM is on the same flat network and can ping its neighbours.
- The attacker can reach the victim and the server.
- The SIEM is collecting logs, and the victim's agent shows as connected.
- You have a clean snapshot of each VM to roll back to.
</div>

With a lab that works — and is wide open — you're ready for the first real
lesson: run an attack, watch it succeed, then teach the network to see it.

[Continue to Part 2: First attacks & detections](../campaigns/mitm.md){ .btn-primary }
