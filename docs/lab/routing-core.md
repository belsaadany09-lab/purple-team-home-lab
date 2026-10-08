<div class="chapter-head" markdown>
<span class="chapter-head__num">06</span>
<div markdown>
<span class="chapter-head__eyebrow">The build · Part 6 of 6</span>
# <span class="chapter-head__title">Build a routing core</span>
</div>
</div>

Segmentation put walls between your zones, with the perimeter firewall doing all
the work. A real enterprise doesn't route *everything* through its edge firewall
— it has an internal core that handles traffic *between* zones, leaving the
perimeter to guard the edge. This stage adds that core, and in doing so it opens
a whole new layer of the network to learn, attack, and defend.

## The concept

!!! concept "A routing core moves the east-west job off the perimeter"

    Right now, traffic from one zone to another is decided by the edge firewall.
    An internal **routing core** takes over that east-west job: it learns where
    every zone lives and routes between them, while the perimeter shrinks back to
    guarding the boundary with the outside world. The two talk using a routing
    protocol — **OSPF** is the classic one — so each automatically learns the
    other's networks instead of you hand-wiring every route.

## The story: one adjacency, four faults

Getting the core and the firewall to simply *recognise each other* over OSPF
took four separate failures, each hiding behind the last. None of them was
"routing is hard" — each was a different layer quietly lying about its state,
and each was caught the same way: by checking ground truth instead of trusting a
success message.

1. **The saves that saved nothing.** The firewall's routing add-on wrote its
   config through a helper that looked for its reload script in the wrong place.
   Every "save" in the interface reported success and changed nothing on disk.
   *Tell:* the config never showed up in the running state, no matter how many
   times it was saved.
2. **The config it refused to load.** With saves finally applying, the reload
   looped on an error — the interface had declared the same routing area two
   different ways at once, which the router treats as contradictory. Removing one
   of the two declarations fixed it.
3. **A correct config, still no connection.** Both sides were now right, and they
   *still* wouldn't pair up. OSPF finds its neighbours using **multicast**
   (`224.0.0.5`), and the brand-new link's default-deny was silently dropping
   those packets — a firewall problem wearing a routing costume. The fix was an
   explicit rule allowing the OSPF protocol on that link.
4. **The config that vanished on restart.** The neighbours paired. But the
   router's save had left one daemon's config file empty, so a reboot would have
   wiped it. Switching to its single-file config mode — and then checking the
   file *on disk*, not the save dialog — made it stick.

The lesson in all four is one habit: a subsystem reporting success is a claim,
not a fact. Confirm the running state, the actual packet, the file on disk.

<div class="how" markdown>
<span class="how__h">How to actually do it</span>

1. **Add a dedicated link between the core and the firewall** — a tiny
   point-to-point network of its own (a `/30` is plenty), separate from any zone.
2. **Run a lightweight router as the core.** An open-source router such as *FRR*
   (or *VyOS*) running in a small VM or container does the job; this is also
   where a tool like *GNS3* is handy for wiring an emulated fabric to your real
   VMs.
3. **Turn on OSPF on both ends** and put the shared link, plus each side's
   networks, into the same routing area so they advertise them to each other.
4. **Open the link for OSPF.** Remember fault #3 — add a firewall rule permitting
   the OSPF protocol on that link, because its discovery traffic is multicast and
   the default deny will eat it.
5. **Confirm they actually learned each other's routes** — not just that the
   neighbour shows "up." A route the core learned from the firewall (or vice
   versa) appearing in the routing table is the real proof.
6. **Then migrate for real, one zone at a time** (see the gotcha), re-running
   your containment test between each move.
</div>

## Rehearse the cutover before you touch anything real

Before moving a *real* zone behind the core, I rehearsed the whole thing against
a throwaway network (`10.0.50.0/24`) with a disposable test host — so nothing
production was ever at risk. The core advertised the test network into a second
routing area, the firewall learned the route exactly where the theory said it
would, and then the interesting part: the firewall could reach the test host,
but the test host **couldn't reach back** — until a specific rule existed. That
taught the rule shape the real cutover needs:

<div class="aside" markdown>
<span class="aside__h">The thing that surprised me —</span> the firewall filters
by **source address**, not by which link traffic arrived on. A zone sitting
*behind* the core still needs its own rule permitting that zone's network as a
source. Two wrong guesses got caught and dropped along the way: first I blamed a
missing return route (the firewall's own log showed it was an ingress *block*,
not a routing gap); then a test looked like it was passing until I realised the
core itself was answering it, not the firewall. Each was settled by checking
what actually replied, not what I expected to.
</div>

<div class="done" markdown>
<span class="done__h">BEFORE YOU MOVE ON</span>

- The core and the firewall have formed an OSPF neighbour relationship, and it
  survives a restart of the core.
- Each side has actually **learned** the other's routes — visible in the routing
  table, not just a neighbour marked "up."
- You've rehearsed a zone cutover on a throwaway network and know the exact
  firewall-rule shape a real zone needs.
- The live segmentation still passes its containment test — nothing real was put
  at risk during the experiment.
</div>

## And this is where it opens up

Standing up a real routing-and-switching layer doesn't just make the lab more
realistic — it **unlocks a whole class of attacks** that simply don't exist on a
flat network or firewall-only zones: attacks against the routing protocol itself,
and against the switch layer beneath it (VLAN hopping, MAC-table flooding,
spanning-tree takeover). Each of those is a new attack → detect → mitigate loop
to close.

That's the pattern of the whole journey in miniature: every piece you build is
the ground the next set of techniques stands on. The lab is never done — it just
keeps opening doors.
