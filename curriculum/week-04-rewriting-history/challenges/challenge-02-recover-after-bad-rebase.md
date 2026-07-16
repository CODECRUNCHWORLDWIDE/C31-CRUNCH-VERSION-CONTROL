# Challenge 2 — Recover a Repo After a Bad Rebase

**Time:** ~60 minutes. **Difficulty:** Medium-Hard.

## Scenario

A teammate (or past-you at 2 a.m.) ran an interactive rebase, resolved conflicts badly, dropped the wrong commit, and force-completed the whole thing. The branch now looks wrecked: a commit is missing, another is mangled, and nobody wrote down the SHAs beforehand. Panic is setting in. Your job: **calmly return the branch to a known-good state and reconstruct what should have happened** — using only what's already in the local repository. No backups, no remote copy. Just the reflog and your head.

## Setup

Run this to create the disaster. It builds a good branch, then rebases it badly so history is genuinely damaged:

```bash
mkdir -p /tmp/c31-w4/challenge-02 && cd /tmp/c31-w4/challenge-02
git init -q
echo "v1" > service.py && git add . && git commit -qm "chore: init service"
git switch -c feature/payments

echo "def charge(): pass"   >> service.py && git add . && git commit -qm "feat: add charge()"
echo "def refund(): pass"   >> service.py && git add . && git commit -qm "feat: add refund()"
echo "def receipt(): pass"  >> service.py && git add . && git commit -qm "feat: add receipt()"
echo "def audit(): pass"    >> service.py && git add . && git commit -qm "feat: add audit log"

echo "=== GOOD STATE (memorise the shape, do NOT copy the SHAs) ==="
git log --oneline           # 4 feature commits + init

# --- the bad rebase: drop refund() by mistake and mangle receipt() ---
GIT_SEQUENCE_EDITOR='sed -i.bak "/refund/d"' git rebase -i HEAD~4
# refund() is now gone from history entirely.
# Then someone "fixes" receipt badly:
git commit --amend -m "feat: add receipt() [MANGLED]" --no-edit 2>/dev/null || true

echo "=== WRECKED STATE ==="
git log --oneline           # refund() commit missing; history rewritten
grep -c refund service.py   # 0 — the refund code is gone from the file too
```

You are now standing in the wreckage. `refund()` is missing from both history and the file. The most recent commit message is mangled. **You did not save any SHAs.**

## Your task

Restore `feature/payments` so that:

1. All four feature commits exist again — `charge`, `refund`, `receipt`, `audit` — each as its own logical commit.
2. `service.py` contains all four function definitions.
3. The mangled commit message is corrected back to something clean.

You must do this using **`git reflog`** (and optionally `git fsck`) to find the pre-rebase state — not by re-typing the code from scratch. The whole point is recovering *real history*, not faking it.

## Constraints

- No re-cloning (there's no remote anyway), no manually re-typing the lost functions. Recover the actual commits.
- Do not delete or expire the reflog (`git reflog expire`, `git gc --prune=now`) — that's the one thing that would make recovery impossible, and it's off-limits.
- Document your recovery as a timeline in `INCIDENT.md`: when you noticed the damage, what you inspected, which reflog entry you chose and why, and how you verified success.

## Hints

<details>
<summary>Step 1 — find the pre-rebase tip</summary>

`git reflog` shows every HEAD movement. Look for the entry labelled `rebase (start): ...` or the last `commit:` line *before* the rebase — the commit just before the rebase began is your intact, four-commit branch. It'll look like `HEAD@{n}: commit: feat: add audit log`.
</details>

<details>
<summary>Step 2 — get back there without losing your current spot</summary>

Don't blindly `reset --hard` yet — first *look*: `git log --oneline <good-sha>` to confirm all four functions are present in that history. When you're sure, `git switch -c recovered <good-sha>` creates a branch at the good state so you can compare both. Or `git reset --hard <good-sha>` to move `feature/payments` itself back.
</details>

<details>
<summary>Step 3 — verify nothing's missing</summary>

`git log --oneline` should show four feature commits. `grep -c 'def ' service.py` should show four functions. Diff against what you expect.
</details>

<details>
<summary>If reflog somehow doesn't show it</summary>

`git fsck --lost-found` lists dangling commits the reflog might have rotated past. `git show <dangling-sha>` to inspect, then branch from it.
</details>

## How success is judged

| Criterion | What "great" looks like |
|-----------|-------------------------|
| **Full recovery** | All four feature commits present; `service.py` has `charge`, `refund`, `receipt`, `audit` |
| **Real history** | You recovered actual commits via reflog/fsck — you did **not** hand-retype the lost code |
| **Clean tip** | The mangled commit message is fixed |
| **Diagnosis** | `INCIDENT.md` shows a clear timeline and names the exact reflog entry used |
| **Calm method** | You inspected before resetting; you kept a way back at each step |

## Why this matters

This is not a contrived puzzle — it is a Tuesday. Bad rebases and stray `reset --hard`s happen to everyone, and the engineer who stays calm and reaches for `git reflog` looks like a wizard to everyone else in the room. The lesson to carry forward: **Git almost never loses committed work.** The reflog is a local, ~30-day safety journal, and knowing it exists turns "I destroyed everything" into "give me sixty seconds."

## Deliverable

Commit to your portfolio under `c31-week-04/challenge-02/`:

- `INCIDENT.md` — the recovery timeline.
- The final `git log --oneline` and the `grep -c 'def ' service.py` output proving all four functions are back.
