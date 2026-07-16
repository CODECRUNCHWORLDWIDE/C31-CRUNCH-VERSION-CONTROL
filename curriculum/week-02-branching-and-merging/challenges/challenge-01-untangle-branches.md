# Challenge 1 — Untangle a tangled set of branches

**Scenario:** You've inherited a small repo mid-project. Someone created four branches, merged some of them into each other in a confusing order, left one branch half-finished, and never wrote down what's where. Your job: understand the mess, then produce a **clean, readable history on `main`** that contains all the *intended* work — with nothing lost.

**Estimated time:** 1.5 hours.

## Build the mess

Run this script exactly (it manufactures a realistic tangle). Don't peek ahead — part of the challenge is reconstructing what happened from the graph, not from the script.

```bash
mkdir -p ~/git-w2/untangle && cd ~/git-w2/untangle
git init -q

seed () { echo "$2" >> "$1"; git add "$1"; git commit -qm "$3"; }

seed app.py "def main(): pass"            "Initial app"
git switch -qc feature-auth
seed auth.py "def login(): pass"          "auth: login"
seed auth.py "def logout(): pass"         "auth: logout"

git switch -q main
git switch -qc feature-ui
seed ui.py "def render(): pass"           "ui: render"

git switch -q feature-auth
git switch -qc feature-auth-2fa
seed auth.py "def totp(): pass"           "auth: add 2FA (WIP)"

git switch -q main
seed app.py "VERSION = '0.2'"             "bump version"

git switch -q feature-ui
git merge -q --no-edit feature-auth       # tangle: ui absorbed auth
seed ui.py "def theme(): pass"            "ui: theming"

git switch -q main
echo "=== Here is your mess ==="
git log --oneline --graph --all --decorate
```

## What you're looking at

- **`main`** — has the initial app, a version bump. The trunk.
- **`feature-auth`** — login + logout. Finished work you want.
- **`feature-auth-2fa`** — branched off `feature-auth`, adds a **WIP** (work-in-progress) 2FA function. *Not* ready to ship.
- **`feature-ui`** — render + theming, but it *also* merged `feature-auth` into itself, so its history now tangles the two features together.

## Your task

Produce a `main` that cleanly contains: **the auth feature (login + logout)** and **the UI feature (render + theming)** — as understandable history — while **leaving the WIP 2FA work out** of `main` (but not deleting it; it should still exist on its branch for later).

## Constraints

- **No committed work may be lost.** Everything reachable now must still be reachable when you're done (verify with `git reflog` and `git branch`). The WIP 2FA commit must survive *on a branch*, just not on `main`.
- The final `git log --oneline --graph main` must be something you'd be comfortable showing a teammate — no accidental double-merges, no WIP commits sitting on `main`.
- You choose the strategy. Merging, `--no-ff`, cherry-picking individual commits, or resetting a branch pointer are all fair game. (Rebase is allowed but not required — and remember its rule: only rewrite branches nobody else depends on. Here you're the only one, so it's safe.)

## Hints

<details>
<summary>Where do I even start?</summary>

Before touching anything, map it. `git log --oneline --graph --all --decorate` and, for each branch, `git log --oneline <branch>`. Write down which commits each branch uniquely contains. You can't untangle what you can't see.
</details>

<details>
<summary>The feature-ui branch already swallowed feature-auth. Is that a problem?</summary>

Not necessarily. If you merge `feature-ui` into `main`, you get auth *and* ui in one shot — because `feature-ui` already contains auth. But then your history has a merge-of-a-merge, which may or may not read cleanly. Compare that against merging `feature-auth` first, then merging only the *ui-specific* commits. Try both; keep the cleaner one.
</details>

<details>
<summary>How do I bring in theming WITHOUT the tangled merge?</summary>

`git cherry-pick <hash>` copies a single commit onto your current branch. If you only want the two ui commits (render, theming) applied on top of a clean `main`, cherry-pick them rather than merging the whole tangled branch. Watch for conflicts on `auth.py` if you take a mixed path.
</details>

<details>
<summary>How do I keep the WIP off main but not lose it?</summary>

Just… don't merge `feature-auth-2fa` into `main`. Leave the branch where it is. As long as the branch pointer exists, the commit is safe. Confirm with `git branch --no-merged main` — it should list `feature-auth-2fa`.
</details>

## How success is judged

| Criterion | What "great" looks like |
|-----------|-------------------------|
| Correctness | `main` contains login, logout, render, theming; does **not** contain the 2FA WIP |
| Nothing lost | `git reflog` / `git branch` show every original commit still reachable; 2FA survives on its branch |
| Readability | `git log --oneline --graph main` is something a reviewer can follow at a glance |
| Justification | `SOLUTION.md` explains your strategy and one alternative you rejected |

## Deliverable

A `SOLUTION.md` in the repo containing: (1) your initial map of what each branch held, (2) the exact commands you ran, (3) a paste of the final `git log --oneline --graph --all --decorate`, and (4) two or three sentences on *why* you chose your approach over an alternative.
