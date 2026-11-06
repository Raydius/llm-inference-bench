# Tasks

- [x] 1. Copy `scripts/fork/sync.sh` and `scripts/fork/rebase.sh` verbatim from
  `~/dev/flare-local-inference/scripts/fork/`, executable.
  Done when: `diff -r ~/dev/flare-local-inference/scripts/fork scripts/fork` is empty and
  `bash -n` passes on both.
  evidence: `diff -r ~/dev/flare-local-inference/scripts/fork scripts/fork` printed nothing; `bash -n` ok on both; both -rwxrwxr-x.
- [x] 2. Write `FORK.md` (remotes, branch model, rebase/sync usage, self-update guard, live
  patch index), then apply the git state it describes. Rename `origin` to `upstream` with
  its push URL set to `DISABLE`, and `fork` to `origin`. Tag `base/current` at
  upstream `52216d7`.
  Done when: `git remote -v` shows `origin` = Raydius (fetch+push), `upstream` push =
  `DISABLE`; `git rev-parse base/current` = `52216d7…`; `NO_PUSH=1 scripts/fork/sync.sh`
  exits 0 and reports base/current 0 commits behind.
  evidence: FORK.md written; `git remote -v` → origin git@github.com:Raydius/llm-inference-bench.git (fetch+push), upstream push DISABLE; `git rev-parse base/current` → 52216d70865a…; `NO_PUSH=1 scripts/fork/sync.sh` exit=0, "base/current is 0 commit(s) behind upstream/main"; rerere.enabled=true.
- [x] 3. Add `export LLM_BENCH_NO_UPDATE_CHECK=1` to `.envrc`.
  Done when: `bash -c 'source .envrc; echo $LLM_BENCH_NO_UPDATE_CHECK'` prints `1`.
  evidence: line appended to .envrc; `bash -c 'source .envrc; echo $LLM_BENCH_NO_UPDATE_CHECK'` printed `1`.
  discovery: `.envrc` is untracked and not in .gitignore, so it is not part of patch/00's commit unless staged; `benchmark_results.json` and `benchmark_results.tool-eval/` are also untracked and would be swept by a blanket commit — declined: outside this change's scope; committing stages explicit paths.
