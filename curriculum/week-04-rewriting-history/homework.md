# Week 4 — Homework

Six problems, ~5.5 hours total. Do them in a throwaway repo and commit each write-up to your portfolio under `c31-week-04/homework/`.

---

## Problem 1 — Amend three ways (30 min)

Make a commit, then practise all three amend situations:

1. Amend to **fix only the message**.
2. Amend to **add a forgotten file** (keep the message).
3. Amend to **remove a file you shouldn't have committed** (`git rm --cached`, then amend).

**Acceptance** (`amend.md`):

- The SHA before and after each amend, showing it changes every time.
- One sentence explaining why amend produces a new SHA instead of editing in place.

---

## Problem 2 — The three-resets table, from memory (30 min)

Without looking at the lecture, fill in this table in `resets.md`, then verify each row by actually running it in a scratch repo:

| Mode | HEAD moves? | Index reset? | Working tree touched? | Changes end up… |
|------|:-----------:|:------------:|:---------------------:|-----------------|
| `--soft` | ? | ? | ? | ? |
| `--mixed` | ? | ? | ? | ? |
| `--hard` | ? | ? | ? | ? |

**Acceptance:**

- The completed table.
- A short transcript proving you tested each row (`git status` output after each reset).
- One sentence: which mode can lose work, and why.

---

## Problem 3 — reset vs. revert (45 min)

Create a repo with three commits. The middle commit is "bad."

1. In one branch, **remove it with `reset`** (rewriting history).
2. In a second branch from the same start, **undo it with `revert`** (adding a new commit).

**Acceptance** (`reset-vs-revert.md`):

- The `git log --oneline` of both branches side by side.
- A paragraph: which approach you'd use if the bad commit were already pushed and pulled by teammates, and *why*.

---

## Problem 4 — Squash a feature with interactive rebase (60 min)

Build a branch of **six commits** implementing some small thing (your choice), deliberately messy. Then use `git rebase -i` to reduce it to **two clean commits**, using at least one `squash`, one `fixup`, one `reword`, and one `drop`.

**Acceptance** (`rebase.md`):

- `git log --oneline` before and after.
- Which verb you used for each original commit and why (`fixup` vs `squash` especially).
- Confirmation via `git diff` that no real change was lost.

---

## Problem 5 — Cherry-pick a hotfix (45 min)

Set up a `main` and a `feature` branch. On `feature`, make three commits, one of which is an urgent fix. Cherry-pick **only the fix** onto `main`.

**Acceptance** (`cherry-pick.md`):

- The commands you ran.
- Proof the fix is on `main` and the other two feature commits are **not**.
- The SHA of the fix on `feature` vs. its copy on `main` — and one sentence on why they differ.

---

## Problem 6 — Reflog recovery drill (60 min)

Do each of these, recovering with `git reflog` (time yourself — aim for under a minute each once warmed up):

1. `git reset --hard HEAD~2`, then recover the two commits.
2. `git branch -D` a branch, then recreate it at its old tip.
3. `git commit --amend`, then get the *pre-amend* commit back.

**Acceptance** (`reflog.md`):

- A transcript of all three recoveries with the `HEAD@{N}` / SHA you used.
- One sentence naming something the reflog **cannot** recover, and why.
- One sentence on how long the reflog keeps unreachable commits by default.

---

## Time budget

| Problem | Time |
|--------:|-----:|
| 1 | 30 min |
| 2 | 30 min |
| 3 | 45 min |
| 4 | 60 min |
| 5 | 45 min |
| 6 | 60 min |
| **Total** | **~4.5–5.5 h** |

After homework, ship the [mini-project](./mini-project/README.md) and take the [quiz](./quiz.md).
