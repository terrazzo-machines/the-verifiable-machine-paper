# The Verifiable Machine

**A Software-First Fabric for Frontier Inference: Deterministic MicroVMs, Replayable Trust, and Silicon as a Feature Flag**

Jessie Hermosillo — terrazzo machines · Version 3.2, August 2026

**[Read the paper](the-verifiable-machine-v3.md)** · **[PDF](the-verifiable-machine-v3.pdf)**

---

The question this paper answers: how do you run a frontier language model
such that its privacy, integrity, and identity are *verifiable* rather than
promised — on hardware you own?

The answer, in one sentence: a deterministic C engine makes every response a
pure function of (prompt, seed), so a small signed **replay receipt** — model
hash, engine hash, config hash, seed, prompt hash, output hash — lets anyone
re-derive and check any response on commodity hardware, after the fact, with
no permission and no exotic silicon; mmap-first weights mean the same binary
runs the same model bit-identically on any machine, where **insufficient
resources may reduce speed but never silently redefine the model**.

## Measured, not estimated

Every number below was measured on one 64 GB M4 Max laptop and is carried by
a receipt ([measured.json](measured.json) holds per-number provenance):

| Model | Total / active params | Container | Result |
|---|---|---|---|
| GLM-5.2 | 743B / 40.1B | 468 GB q4 — 7× RAM | speaks + signed receipt replay-verified; wall 41m53s → **10m44s** across the optimization campaign, same golden `993fd1` at every rung |
| gpt-oss-120b | 117B / 5.1B | 70 GB q4 — larger than RAM | minted *through the wire* (never fully on disk); first words 4m13s cold; replay-verified 4m46s (`f5668f`) |
| gpt-oss-20b | 21B / 3.6B | q8 | 2.6 tok/s warm; replay-verified 90 s (`0dc86d`) |
| Mixtral-8x7B | 47B / 13B | q8 | 3.8 tok/s warm (`84dbdd`) |
| DeepSeek-V2-Lite | 16B / 2.4B | q8 | 11.7 tok/s warm (`046306`) |
| Kimi K2.6 | 1.026T / 31.7B | ~640 GB q4 (expected) | **mint in progress at this writing** — in progress, not measured |

Five architecture families are token-exact against their reference
implementations; determinism is bit-identical across runs, thread counts,
streaming on/off, and — demonstrated at kernel-matrix, tiny end-to-end, and
real-receipt-under-emulation scale — across x86_64 and aarch64.

## Where the code is

The engine, verifier, receipts, and desktop app live in the companion
repository (opening separately). File references in the paper of the form
`docs/…`, `bench/…`, `receipts/…` point there. Receipts are ed25519-signed;
the offline verifier replays them against hash-matched artifacts.

## License

Paper text and figures: CC BY 4.0. Cite as: Hermosillo, J. *The Verifiable
Machine* v3.2, terrazzo machines, August 2026.
