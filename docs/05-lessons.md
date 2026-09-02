# 05 — Lessons (the durable, transferable value)

This campaign lost on the private board, but it produced a set of findings that transfer directly to any agent-security red-team or split-guardrail benchmark. Ranked by value.

## 1. If you're scored under two guardrails, your public score is not your signal

> **High public score can be the *opposite* of your private score.**

When an evaluation replays your candidates under a separate, hidden private guardrail, the public leaderboard rewards whatever seam the public guard happens to have. The private guard — built to be less exploitable — tends to close exactly those seams. So:

- Treat the public board as a *behavioural probe* (does my candidate fire at all?), not a *score target*.
- Never make **the final pick** the public-best structure of a public-only-verifiable attack.

## 2. "Public low score" on a marker-free channel is a positive, not a negative

The benign-email → CONFUSED_DEPUTY channel scored 4/event publicly (looks weak) and was **the only private survivor**. The things that score high publicly are the ones the private guard came to block. In a split-guardrail setting, **a channel that is marker-free and survives a comprehensive scan is worth more than a channel that tops the public board.**

## 3. Hedge the union of feared models, never one

I hedged against a "scan-only" private guard (marker scan). The real guard was scan **+** taint **+** keyword. A correct hedge enumerates the *joint* private-guardrail model space and includes a channel that survives each axis independently — the benign-email channel being the canonical all-axis survivor.

## 4. Verify-then-keep beats blind farms, and beats unverified coverage

- Unverified distinct-domain candidates fire at ~0.3–0.6 remotely.
- Byte-identical **verified copies** fire ~100% (deterministic greedy decode + fixed seed) — 1 cell.
- **Live-verified distinct-domain farms** (verify each candidate in the generation budget, keep only firing ones) get compliance ~1 **and** the per-domain cells (+2 each). On any board, that's the best of both.

## 5. Arithmetic is the highest-frequency failure mode in this domain

The most damaging errors were arithmetic/assumption, not strategy:

- `273×8×16/200 = 174.72 ≠ 172`; adding cells made it negative.
- Treating a non-uniquely-decomposable total ("4-fire cap") as established fact.
- Assuming farm ≥ copies without accounting for the generation double-budget (farm ≤ 0.996q × copies).

**Fix: every derived number gets a written formula, every behavioural claim gets a source line, every submission gets an independent code review.** External review caught all of these.

## 6. Determinism is a gift — use it

`temp=0` greedy decode + `seed=123` means a byte-identical message replays identically. That's the entire basis of duplication economics. If a harness is deterministic, measure the *distribution* (multi-seed) before trusting a single observation; if it's not, duplication is a gamble.

## 7. A local oracle is worth the cost — but only for what it can see

Building a faithful local oracle (same weights, same parser, same guardrail, same seed) let me iterate at zero cost. But it cannot see the **private guardrail**. The oracle's accuracy on the *public* stack is exactly what produced the overconfidence on the private side. **Know exactly what your oracle can and cannot reproduce before trusting any conclusion from it.**

## 8. The honest meta-lesson

The campaign was a genuine, rigorous red-team effort — 25+ oracle rounds, source-level reverse engineering, independent external review, honest experiment discipline. It still lost, badly, on the private board. The reason wasn't effort or competence; it was that **I optimised the wrong objective**. In an adversarial-research competition with a hidden evaluation, the winning skill isn't just finding the seam — it's correctly predicting **which guardrail** will see your attack, and being willing to bet on the channel that *survives* rather than the one that *tops*.
