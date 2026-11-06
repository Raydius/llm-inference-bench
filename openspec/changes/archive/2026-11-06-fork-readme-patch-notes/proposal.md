# Show the fork's patch notes at the top of README.md on a deploy branch

Tier: 1 — two documentation files (README.md, FORK.md); scripts/fork/rebase.sh is read-only evidence
Type: feature

External Review: Waived — Tier 1 (two documentation files, no code or behavior change)

## Stated Requirements

- "I want to create a similar README patch notes section."

## Why

The fork's patch index currently sits at the bottom of FORK.md, which is only on
`patch/00-fork-tooling`. Someone visiting `Raydius/llm-inference-bench` sees upstream's
README with no indication of what this fork changes. flare-local-inference solves this with
a patch-notes block at the top of README.md on `deploy`, its GitHub default branch.

## What Changes

- **New `deploy` branch:** created on `patch/00-fork-tooling`. Its own commit prepends a
  "Raydius fork: patch notes" block to README.md: upstream link, which branch is which,
  stack base `52216d7`, a FORK.md link, an **Active patches** table (Patch | Added | What it
  does | Still needed) and an empty **Retired patches** table. Upstream's README follows
  unchanged.
- **FORK.md on `patch/00`:** `deploy` is described as the tip that carries the notes, and
  the "create it before the first rebase" wording goes. The Active patches table is replaced
  by a "Patch inventory" section pointing to the README tables and their upkeep rules
  (add a row per patch; retired rows move, never drop).
- **GitHub:** the fork's default branch becomes `deploy`, and both branches are pushed.

## Blast Radius

1. README.md:1 — upstream's title line; the notes block is inserted above it on `deploy`
   only, so `main` and `patch/*` keep upstream's README byte-identical.
2. FORK.md:25 — the `deploy` line in the branch diagram; FORK.md:31-34 — the `deploy`
   bullet rewritten; FORK.md:58-62 — the Active patches table replaced by the pointer.
3. scripts/fork/rebase.sh:22 — `STACK_TIP` defaults to `deploy`, which now exists with its
   own commit above `patch/00`, satisfying the coincident-tip guard at scripts/fork/rebase.sh:38.
