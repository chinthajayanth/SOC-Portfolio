# 01 — Allegation & OSINT Review

## Source of Allegation

On **September 8, 2016**, the Gotham City Police Department (GCPD) flagged a public
Pastebin post (`http://pastebin.com/Gw6dWjS9`) containing a defacement-style message
directed at Bruce Wayne, signed by a group self-identifying as:

- Handle: `po1s0n1vy`
- Twitter: `@po1s0n1vy`
- Domain: `www.po1s0n1vy.com`

GCPD's concern: this is evidence that **www.imreallynotbatman.com**, hosted within
Wayne Enterprises' IP space, may have been compromised and defaced — consistent with
this group's known modus operandi of defacing victim websites for public embarrassment.

## Investigation Objective

This allegation is treated as **unverified** until confirmed by internal evidence.
The objective of this investigation is to determine, using only Wayne Enterprises'
internal log data (web, network, host, and firewall telemetry), whether:

1. www.imreallynotbatman.com was actually compromised,
2. the compromise resulted in a defacement,
3. the activity can be attributed to the infrastructure/actor referenced in the
   Pastebin post,

and if confirmed, to reconstruct the full attack chain and assess impact.

## OSINT Finding 1 — Actor handle not present in internal telemetry

**Query:**
```spl
index=botsv1 "po1s0n1vy"
```

**Result:** 0 events.

**Interpretation:** The attacker's self-claimed handle does not appear anywhere in
Wayne Enterprises' logs (web traffic, network traffic, or host events). This is an
expected outcome — a handle used on a public bragging post would not typically
appear in server-side or network telemetry unless the actor made an unusual
operational security mistake (e.g., embedding it in a user-agent string, uploaded
filename, or request parameter).

**Conclusion:** The claimed actor identity cannot be used as a search pivot.
Attacker infrastructure (IPs, domains, request patterns) must instead be identified
through behavioral analysis of traffic to the target asset, and only *afterward*
cross-referenced against any public information about this group.

## Next Step

Proceed to reconnaissance analysis [02-reconnaissance.md](02-reconnaissance.md) against
www.imreallynotbatman.com web traffic to identify candidate attacker source IPs
based on behavior rather than claimed identity.
