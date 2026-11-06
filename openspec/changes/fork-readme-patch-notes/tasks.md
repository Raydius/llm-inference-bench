# Tasks

- [x] 1. On `patch/00-fork-tooling`, rewrite FORK.md's `deploy` bullet: `deploy` is the tip that carries the README patch notes and is the GitHub default branch. Replace the Active patches table with a "Patch inventory" pointer to README.md on `deploy` plus the upkeep rules. Commit this together with this change's artifacts.
  Done when: `grep -n "create it before the first rebase" FORK.md` returns nothing; `grep -n "Patch inventory" FORK.md` hits; `git status --porcelain FORK.md` is empty after commit.
  evidence: grep "create it before the first rebase" FORK.md → no hits; grep "Patch inventory" → FORK.md:60; committed as 6a4eafe on patch/00-fork-tooling, porcelain empty.
- [x] 2. Create `deploy` on `patch/00-fork-tooling` and give it its own commit that prepends the patch-notes block (header, Active patches with the `patch/00` row, empty Retired patches) to README.md. Push both branches, then set the fork's GitHub default branch to `deploy`.
  Done when: `git diff patch/00-fork-tooling deploy --stat` lists only README.md; `git rev-parse deploy` ≠ `git rev-parse patch/00-fork-tooling`; `gh repo view Raydius/llm-inference-bench --json defaultBranchRef` reports `deploy`; `origin/deploy` = `deploy`.
  evidence: `git diff patch/00-fork-tooling deploy --stat` → README.md | 23 +++ only; deploy commit is its own (above patch/00); `git push -u origin deploy` → new branch; `gh repo edit --default-branch deploy` then `gh repo view … defaultBranchRef` → deploy.
