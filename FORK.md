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
            +- deploy           # README patch notes; what you run; GitHub default branch
```

- **`main`** — a pristine mirror of `upstream/main` that only ever fast-forwards.
- **`base/current`** — the upstream commit the stack sits on. It started at `52216d7`
  (lil-bench 1.7.1).
- **`deploy`** — the top of the stack and the GitHub default branch. Its own commit puts the
  README patch notes on top, so they show on the fork's front page. That commit also keeps
  `deploy` strictly above the topmost patch. Without it, `--update-refs` would skip the
  patch branch that shares `deploy`'s commit, and `rebase.sh` refuses to run in that case.
  `scripts/fork/rebase.sh` defaults `STACK_TIP` to it.
- **A new patch** goes below `deploy`: `git rebase --onto patch/NN-<new> patch/NN-<old> deploy`,
  then add its README row on `deploy` and amend the notes commit.

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

## Patch inventory

The inventory lives in [README.md](README.md#raydius-fork-patch-notes) on `deploy`, so it
shows on the fork's GitHub page. Keep both tables current there, and amend the notes
commit on `deploy` rather than stacking new ones:

- **Active patches** — add a row when a patch lands.
- **Retired patches** — when upstream absorbs a patch or makes it obsolete, delete its
  branch and move its row to this table with the retirement date and the reason. Never
  drop a row.
