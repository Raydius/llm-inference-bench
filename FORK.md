# Fork maintenance — local lil-bench builds

This fork carries local changes as a **rebaseable patch stack** on top of
`local-inference-lab/llm-inference-bench`. It uses the same doctrine as
`~/dev/flare-local-inference/FORK.md`; this file covers the specifics for this repo and
holds the live patch index.

**Scope:** local features first. A patch that proves worth it goes upstream as a PR from
a `pr/*` branch cut on `upstream/main`. Upstream has no LICENSE yet (issue #17), so settle
that before opening one.

## Remotes

| Remote     | URL                                                    | Role                                 |
| ---------- | ------------------------------------------------------ | ------------------------------------ |
| `origin`   | `git@github.com:Raydius/llm-inference-bench.git`       | our fork (push here)                 |
| `upstream` | `https://github.com/local-inference-lab/llm-inference-bench` | fetch-only; push URL is `DISABLE` |

## Branch model

```text
base/current (tag -> an upstream commit)
  +- patch/00-fork-tooling      # this file + scripts/fork/ + openspec/ (never upstreamed)
       +- patch/NN-<topic>      # one logical change each
            +- deploy           # stack tip: what you run; GitHub default branch
```

- **`main`** — a pristine mirror of `upstream/main` that only ever fast-forwards.
- **`base/current`** — the upstream commit the stack sits on. It started at `52216d7`
  (lil-bench 1.7.1).
- **`deploy`** — the top of the stack. `scripts/fork/rebase.sh` defaults to it, so create
  it before the first rebase. It must sit strictly above the topmost patch: give it its
  own commit, or `--update-refs` skips the patch branch that shares its commit, and
  `rebase.sh` refuses to run.
- **A new patch** goes below `deploy`: `git rebase --onto patch/NN-<new> patch/NN-<old> deploy`.

## Moving to a newer upstream

```bash
scripts/fork/sync.sh                 # fast-forward main, push it to origin (NO_PUSH=1 skips)
scripts/fork/rebase.sh upstream/main # or a sha / tag / pr/<N>
git push origin --force-with-lease 'patch/*' deploy && git push origin -f base/current
```

Enable `rerere` (`git config rerere.enabled true`) so conflict resolutions replay.
Upstream releases often, and most patches touch the single large `llm_decode_bench.py`.
Keep patches to a few small, well-separated sites so the rebases stay cheap.

## Self-update guard (required)

On startup, `llm_decode_bench.py` checks for a newer upstream `VERSION`. When it finds
one, it offers to upgrade, with yes as the default. Accepting runs
`git fetch origin main && git checkout origin/main -- llm_decode_bench.py`, which
replaces the patched script with the unpatched `main`. `.envrc` exports
`LLM_BENCH_NO_UPDATE_CHECK=1` to switch the check off. Load it with direnv, or source it
before running the benchmark.

## Active patches

| Branch | What it carries |
| --- | --- |
| `patch/00-fork-tooling` | FORK.md, `scripts/fork/{sync,rebase}.sh` (copied from flare), `openspec/` (radflow) |
