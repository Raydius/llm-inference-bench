# Set up the fork as a rebaseable patch stack with a deploy branch

Tier: 2
Type: feature
Mode: hotfix
External Review: None — hotfix

## Stated Requirements

- "what if I start with just building it for myself?"
- "what about the deploy branch pattern we use in ~/dev/flare-local-inference/ ?"

## Why

Local work (starting with a code-edit benchmark profile) needs to live on the
`Raydius/llm-inference-bench` fork and survive frequent upstream releases. Today the
clone has no fork doctrine. On top of that, `llm_decode_bench.py`'s startup self-update
runs `git checkout origin/main -- llm_decode_bench.py`, which silently replaces a
patched file with the unpatched mirror.

## What Changes

Adopt the flare-local-inference fork model here:

- **Remotes:** `origin` becomes the fork and `upstream` becomes fetch-only.
- **Branches:** `base/current` tags the upstream base, and `patch/00-fork-tooling` carries
  `FORK.md` plus `scripts/fork/{sync,rebase}.sh`, copied verbatim from flare.
- **Self-update guard:** `.envrc` exports `LLM_BENCH_NO_UPDATE_CHECK=1` so the
  self-update never runs on a patched checkout.

## Blast Radius

1. llm_decode_bench.py:21806 — `LLM_BENCH_NO_UPDATE_CHECK` is the existing opt-out the
   `.envrc` sets; no code change.
2. llm_decode_bench.py:21835 — the `git checkout origin/main -- <script>` this guards
   against.
3. llm_decode_bench.py:21834 — the self-update fetches the remote named `origin`; after
   the rename that is the fork, not upstream, which is one more reason to set the guard.
4. FORK.md, scripts/fork/ — new files (copied from flare's fork tooling, whose defaults
   `upstream` / `base/current` / `deploy` / `origin` match the renamed remotes):
   `grep -rln "base/current\|STACK_TIP" --exclude-dir=.venv --exclude-dir=openspec .`
   returns 0 hits, so no existing fork tooling collides.
