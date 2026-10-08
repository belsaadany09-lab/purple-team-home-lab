# Challenges Overcome

The techniques are the visible part of the lab. The obstacles are where the real
depth is — the wrong theories caught and retracted, the tools that lied about
their own success, the four-fault bring-ups that only yielded once I stopped
trusting the dashboard and went to ground truth. Every one of these was a wall
that stopped progress until it was understood and beaten. They're written as
transferable takeaways, because the *habit* each one taught matters more than
the specific bug.

## Tools lie. Ground truth doesn't.

The single most repeated lesson, in a dozen forms. A tool's success message is a
claim, not a fact — and the whole lab runs on checking the fact instead.

!!! example "A running service with a dead socket"

    The SIEM's control script reported the manager as *running* while the thing
    was effectively down. `ss -tlnp` showed the listening socket wasn't there.
    **Takeaway:** "the service is running" and "the service is listening" are
    different claims — verify the one that matters.

!!! example "A GUI save that saved nothing"

    A firewall package's config GUI reported every OSPF save as successful while
    writing nothing to the running config (a reload wrapper pointed at the wrong
    path). **Takeaway:** confirm the change landed in the running state / on
    disk, not in the dialog box.

!!! example "A config file silently at zero bytes"

    A per-daemon "write memory" left the OSPF daemon's config empty — it would
    have vanished on the next restart. **Takeaway:** after you save, go *look at
    the file*. Persistence you didn't verify isn't persistence.

!!! example "Disk-full wearing a daemon-failure mask"

    A SIEM dashboard failure and an API "did not start correctly" were both
    really a 100%-full root disk. The generic error pointed everywhere except
    the cause; `df -h` pointed straight at it. **Takeaway:** when several
    unrelated daemons fail at once, suspect a shared resource (disk, memory)
    before debugging each daemon.

## Measurement artifacts aren't findings

!!! example "nmap lies through a SOCKS tunnel"

    Port scans through a reverse-SOCKS tunnel returned "filtered" verdicts that
    were tunneling artifacts, not real port states. A single `nc -zv` connect —
    with a *known-open reference port* as a control — gave the truth.
    **Takeaway:** when the measurement path is weird (a proxy, a tunnel, a
    degraded link), validate the measurement tool against a known-good case
    before trusting any result.

!!! example "A garbled scan that wasn't corruption"

    A wireless scan returning a garbled SSID looked like USB data corruption.
    The real cause was a service still managing the interface during the scan.
    Killing it produced a clean scan. **Takeaway:** the dramatic explanation
    (hardware corruption) is usually wrong; check the boring one (something else
    is holding the resource) first.

## The output format matters as much as the output

!!! example "Truncated process arguments"

    `ps aux` truncates long command lines — exactly the part you need when
    you're trying to see what a process was actually invoked with. `ps -auxww`
    shows the full args. **Takeaway:** know which of your tools quietly
    truncate, and reach for the untruncated form when the detail is the point.

!!! example "Routing by longest prefix, not by order"

    A `/32` host route wins over a default route regardless of where it sits —
    the kernel chooses by longest prefix match. `ip route get <dest>` shows the
    *actual* decision rather than making you reason about the table by eye.
    **Takeaway:** ask the system what it will do; don't simulate it in your head.

## Networking surprises

!!! example "'Out-of-band' that shared a broadcast domain"

    Putting an admin NIC on a 'host-only' network turned out to dual-home every
    server onto the same L2 as the user zone — a quiet bypass of the whole
    firewall. **Takeaway:** out-of-band is a layer-2 property. If the admin path
    shares a broadcast domain with what it administers, it isn't out-of-band,
    whatever you named it.

!!! example "The firewall filters by source address, not by link"

    Traffic from a zone routed *behind* the internal core was still evaluated by
    its source address on ingress — so a routed zone needs its own pass rule,
    with a **network** source type (an address object of a `/24` collapses to a
    single host). **Takeaway:** know what your firewall keys its decisions on
    before you reason about why a packet was dropped.

!!! example "Multicast protocols need their own firewall rule"

    OSPF adjacencies wouldn't form because the Hellos are multicast and a new
    interface's implicit deny was dropping them. "The OSPF config is correct"
    and "the OSPF packets can flow" were different problems. **Takeaway:** a
    protocol working requires both its config *and* a path for its packets —
    including the multicast ones.

## The meta-lesson: retract wrong theories out loud

Several of the debugging wins above started as a *wrong* theory — USB
corruption, a missing return route, a bad OSPF config — that better evidence
then overturned. The discipline that made them wins was retracting the wrong
theory **explicitly** the moment ground truth contradicted it, instead of
quietly sliding to the next guess. A debugging session is a chain of falsified
hypotheses; naming each one as it dies is how you avoid going in circles.

!!! quote "The habit, in one line"

    Never trust a tool's claim of its own success. Check the socket, the file,
    the frame, the route — the thing itself.
