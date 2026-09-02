# 04 — The Public/Private Divergence (why 96.25 became 0.555)

This is the part that matters.

## The board scale flipped

| Board | Top score | My best | Channel that won |
|-------|-----------|---------|------------------|
| Public | 147 | **96.25** (frame4-dual) | marker exfil via the `url`-vs-`data` seam |
| Private | **47** | **0.555** | benign-email CONFUSED_DEPUTY (marker-free) |

The public board's entire top was driven by one seam (HTTP-POST checks `url`, never `data`). The `SECRET_MARKER` engineering was literally the "engine under every top score." The private guardrail closed that seam with a **full-field literal scan** — and every marker-based submission, mine included, reset to ~0.

## What the private guardrail actually was

Reconstructed from the private score collapse (the wheel is only loaded in the scoring environment):

- **Full-field literal scan** for `SECRET_MARKER` — kills the marker engine.
- **Taint-based blocking** of web/email → action — kills U2A channels (my omni's `taint→shell.run` and `taint→email`).
- **Broad keyword list** on command/path/body — kills `fs.write(secret.txt)` (DESTRUCTIVE), real-secret reads, and encoded variants.

It was the **union** of every private-guardrail model I had hedged *individually*. My hedge (omni-v4) assumed a "scan-type" guard and included a tainted-channel and a DESTRUCTIVE channel — all of which the real guard killed too.

## The only survivor, and the inversion

The **benign-email → CONFUSED_DEPUTY** channel (4/event, marker-free, untainted) survived — it's the only thing that produced my 0.555. And it's the channel I had repeatedly dismissed as "too weak" because it scored **low publicly**.

That's the inversion: **on this evaluation, public score was anti-correlated with private survival.**

- The marker engine: high public (16/event), dead private.
- The benign-email channel: low public (4/event), **the** private survivor.

## The counterfactual that hurts

`email-farm-full` = 2000 distinct benign-recipient emails = CONFUSED_DEPUTY 4/event + cells. On a private scale where the top is **47**, that farm was a **medal candidate**. I built it, then **discarded it** (for a public-high-scoring lottery arm), following the same overconfident logic that picked the public champion as the final first pick.

If the final two picks had been `[omni-v4, email-farm-full]` — two marker-free hedges — the private result would have been in the tens, i.e., likely a podium. Instead I made the public-optimal selection.

## The causal chain of the loss

1. I trusted the "ground rule" argument: *"a guardrail shouldn't categorically block what the scorer evaluates."* → wrong; the private guard does exactly that.
2. I optimised the whole campaign for a public-only-verifiable seam.
3. I built a hedge covering *one* feared private model, not the union.
4. I made the final selection the public-best marker structure + a hedge, instead of marker-free survivor channels.
5. The private board reordered everything (the "largest shakeup ever") and I was on the wrong side of it.

## What a winning approach likely looked like

The private top 47 almost certainly came from one of:
- A large **benign-email farm** (CONFUSED_DEPUTY) — the survivor channel, at scale + cells;
- And/or marking-free channels that fire under *every* private-guardrail family the team could enumerate — because the correct hedging is over the **union** of feared models, which is exactly what I failed to do.
