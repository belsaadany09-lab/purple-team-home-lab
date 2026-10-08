<div class="chapter-head" markdown>
<span class="chapter-head__num">03</span>
<div markdown>
<span class="chapter-head__eyebrow">The build · Part 3 of 6</span>
# <span class="chapter-head__title">Go deeper — access &amp; movement</span>
</div>
</div>

Part 2 was the network's plumbing. Now you're inside a host. This part is the
middle of a real intrusion: take credentials, become more powerful than you're
meant to be, quiet the defences, and then reach somewhere the network tried to
keep you out of. These are the techniques that turn "I got a shell" into "I own
the place" — and they're the ones defenders find hardest to see, which is
exactly why they're worth doing with a blue eye open.

!!! concept "This is where detection gets hard — and interesting"

    Network attacks are loud; host attacks can be whisper-quiet. A credential
    dump or a privilege escalation can look almost identical to normal admin
    activity. So the lesson here isn't just the attacks — it's learning *what
    signal a defender actually has*, and being honest about where you don't yet
    have one.

<div class="tech" markdown>

### Offline credential dumping <span class="status wip">attack proven</span>

<div class="meta" markdown>
[ATT&CK T1003.002](https://attack.mitre.org/techniques/T1003/002/){ .attck }
<span>needs: a foothold shell + an offline hash-extraction tool</span>
</div>

**What it is.** Windows keeps password hashes in protected registry hives. The
textbook grab is over the file-sharing service — but that's often blocked. The
quieter path, and the one worth learning, sidesteps it entirely: export the
hives *locally* on the victim, copy them out over the access you already have,
and crack them **offline** on your own box. No file-sharing, a much smaller
footprint.

**Try it, briefly.** From your foothold, export the relevant registry hives to
files, pull those files back over your existing shell, and run an offline
extraction tool against them to recover the hashes. Delete the exported files
afterward — hygiene, and realism.

**Catch it.** The honest answer today is *not yet* — this is the frontier. The
signal to build is host telemetry (a tool like Sysmon) flagging the hive export
itself, since the usual file-sharing path a defender watches for never happens
here.

</div>

<div class="tech" markdown>

### Privilege escalation to SYSTEM <span class="status wip">attack proven</span>

<div class="meta" markdown>
[ATT&CK T1053.005](https://attack.mitre.org/techniques/T1053/005/){ .attck }
<span>needs: local-admin access on the victim</span>
</div>

**What it is.** Local admin isn't the top — `SYSTEM` is. A clean way up is to
abuse a native scheduling mechanism that runs tasks as `SYSTEM` with no
start-timeout (unlike a bare service call, which dies on a one-shot timeout — a
small distinction that's the difference between the trick working and silently
failing).

**Try it, briefly.** As local admin, register a scheduled task set to run as
`SYSTEM`, point it at a command that proves the context (whoami), and run it.
You're now the most privileged account on the box.

**Catch it.** *Not yet* — another frontier item. The detection to build watches
for task creation and unusual parent-child process chains in host telemetry.

</div>

<div class="tech" markdown>

### Impairing the defences <span class="status wip">attack proven</span>

<div class="meta" markdown>
[ATT&CK T1562.001](https://attack.mitre.org/techniques/T1562/001/){ .attck }
<span>needs: admin access</span>
</div>

**What it is.** Before making noise, a smart attacker quiets the endpoint. Adding
an exclusion to the built-in antivirus — *before* your tool ever lands on disk —
means it's never scanned. It's registry-backed, so it persists.

**Try it, briefly.** Add an antivirus exclusion for the folder you're about to
work from, then drop your tooling there. The ordering matters: the exclusion has
to exist before the file does.

**Catch it.** *Not yet.* The event that records a new exclusion is the signal to
start monitoring — a defender who watches for it catches this the moment it
happens.

</div>

<div class="tech" markdown>

### Pivoting into an isolated segment <span class="status wip">attack proven</span>

<div class="meta" markdown>
[ATT&CK T1572](https://attack.mitre.org/techniques/T1572/){ .attck }
[ATT&CK T1090.001](https://attack.mitre.org/techniques/T1090/001/){ .attck }
<span>needs: a dual-homed foothold + a tunnelling tool</span>
</div>

**What it is.** Some things can't be reached directly — that's the whole point of
segmentation. But a host you've compromised that sits on *two* networks is a
bridge. You turn it into one with a tunnel, and suddenly the hidden segment is a
proxy hop away.

<div class="journey">
<svg viewBox="0 0 860 150" role="img" aria-labelledby="pv-t pv-d" xmlns="http://www.w3.org/2000/svg">
  <title id="pv-t">The pivot chain</title>
  <desc id="pv-d">The attacker gets a foothold on a dual-homed victim, opens an outbound tunnel back to itself, and through it reaches an isolated inner network it cannot touch directly.</desc>
  <defs>
    <style>
      .pn{fill:#ece9f5;font-family:"Space Grotesk",sans-serif;font-weight:600;font-size:12px;}
      .pm{fill:#9d96bb;font-family:"IBM Plex Mono",monospace;font-size:8.5px;}
    </style>
  </defs>
  <rect x="20" y="45" width="150" height="58" rx="10" fill="#ff5c7a" fill-opacity="0.1" stroke="#ff5c7a" stroke-width="1.5"/>
  <text x="95" y="70" text-anchor="middle" class="pn">Attacker</text>
  <text x="95" y="88" text-anchor="middle" class="pm">your box</text>

  <rect x="355" y="45" width="150" height="58" rx="10" fill="#5a5280" fill-opacity="0.12" stroke="#6a5fa0" stroke-width="1.5"/>
  <text x="430" y="68" text-anchor="middle" class="pn">Victim</text>
  <text x="430" y="86" text-anchor="middle" class="pm">dual-homed</text>

  <rect x="690" y="45" width="150" height="58" rx="10" fill="#455a64" fill-opacity="0.18" stroke="#6a5fa0" stroke-width="1.3" stroke-dasharray="4 3"/>
  <text x="765" y="68" text-anchor="middle" class="pn">Inner net</text>
  <text x="765" y="86" text-anchor="middle" class="pm">10.9.0.0/24</text>

  <path d="M170 66 H355" stroke="#ff5c7a" stroke-width="1.6" fill="none"/>
  <text x="262" y="60" text-anchor="middle" class="pm" fill="#ff9bad">1 · foothold</text>
  <path d="M355 86 H175" stroke="#7d5fd6" stroke-width="1.6" fill="none" stroke-dasharray="4 3"/>
  <text x="262" y="102" text-anchor="middle" class="pm" fill="#c0a0ff">2 · outbound tunnel</text>
  <path d="M505 74 H690" stroke="#9d96bb" stroke-width="1.6" fill="none"/>
  <text x="597" y="68" text-anchor="middle" class="pm">3 · now reachable</text>
  <path d="M20 118 H690" stroke="#5a3a4a" stroke-width="1.2" fill="none" stroke-dasharray="2 4"/>
  <text x="300" y="134" text-anchor="middle" class="pm" fill="#ff9bad">attacker → inner net directly: blocked</text>
</svg>
<p class="journey__caption">reach the hidden segment the way a real attacker would — through the compromised host, never around it</p>
</div>

**Try it, briefly.** From your foothold on the dual-homed victim, open a
*reverse* tunnel — one the victim dials *outbound* back to you, which slips past
egress filtering far more easily than an inbound listener — and expose it as a
proxy. Background it so it survives a dropped session. Now route your tools
through that proxy to reach the inner segment.

!!! concept "Do it honestly — no owner's shortcuts"

    It's tempting to just reach the inner host directly because *you own the
    lab*. Don't. The inner segment gets reached through the tunnel or not at all —
    that's the only path a real adversary has, and taking the shortcut teaches
    you nothing. Verify from the victim's own session, and use a known-open port
    as a control so a "filtered" result is real and not a tunnelling artefact.

**Catch it.** *Not yet* — and this one's worth being loud about. Tunnel and
lateral-movement detection (unusual outbound patterns, beacon timing) plus
egress filtering are the build ahead. A working pivot with no detection is the
clearest reason Rule 7 exists.

</div>

## What you just learned

You now hold the middle of the kill chain — and an honest map of where your blue
side can and can't see. That gap isn't a failure; it's the next build queue, and
it's named precisely because you ran the attacks first. Next, two surfaces that
sit right at the edges of what a firewall can even perceive.

[Continue to Part 4: Wireless &amp; web](wireless-web.md){ .btn-primary }
