# 02 — The Local Oracle (what I could test, and what I couldn't)

A submission score you can't reproduce locally is a coin toss. So I built a **local oracle** that faithfully reproduces the production stack, so I could iterate on behaviour at zero submission cost.

## What it reproduced

| Component | Reproduction | Why it matters |
|-----------|--------------|----------------|
| Target models | The exact GGUF weights (gpt-oss-20b, gemma-4-26b) via `llama.cpp` | Same model = same behaviour |
| Agent + parser | The exact SDK `GPTOSSAgent` / `Gemma4Agent` + `KaggleGemma4ToolCallParser` | The board wires the *Kaggle* parser, not the SDK default — testing with the default parser silently tests the wrong thing |
| Guardrail | `OptimalGuardrail` (the exact public guardrail) | Same seam behaviour |
| RNG | `temp=0` greedy decode + fixed seed | Firing is **deterministic** |
| Env | `build_attack_env(seed=123, fixtures_dir, env_selection="gym", max_tool_hops=8)` | Same fixtures, same 8-hop cap |

Because of determinism, a **byte-identical message replays identically** — which is the basis of every "verified copy" / duplication strategy.

## What the oracle measured (25+ rounds, c67–c92)

- **gpt firing**: the FRAME-harmony chained messages fire 4/4; the message structure dominates — not the URL count.
- **gemma firing**: with the *board* parser, gemma never emits a second tool call; it does one call then a short wrap-up. 13 phrasings / 5 probes / 1.00 posts or worse. (An earlier 2/2 reading was a chat-template-version skew — the deployed template differs from some local mirrors.)
- **Determinism**: same message replayed 20× → 0 variance at default settings; at explicit temperature 0.8/1.5/2.0 the fire *rate* stays 1.0 (never 0) with mild quality decay. So duplication never produces a "whole message dead" worst case.
- **Guardrail interactions**: `email.send` with a marker in the body → denied (ok=false) 8/9; with a benign body → allowed and CONFUSED_DEPUTY fires. The trigger-word-free phrasing reliably gets the model to *call* `email.send`.

## The wall you can't cross locally

**The private guardrail wheel is not available locally.** No amount of local rigour reproduces the split. This single fact is what makes a private/public divergence evaluation fundamentally un-hedge-able from the public side: you can only ever *guess* the private guardrail's silhouette, and the final private score is decided by whether your guess was in the right family.

## Discipline that helped

1. **One hypothesis per arm** on experiment days, with a documented decision tree for reading the scores.
2. **Weather-aware** day-over-day comparisons (server speed shifts all arms by a few points).
3. **A/B structures that isolate one variable** (e.g., two arms with identical gemma branches so the public delta ≈ the gpt-branch delta/2).
4. **External independent review** — a second agent audited the code and arithmetic before every submission wave. It caught every error that mattered (see `05-lessons.md`).
