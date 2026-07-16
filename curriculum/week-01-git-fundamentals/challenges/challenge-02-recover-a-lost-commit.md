# Challenge 2 — Recover a "lost" commit

**Time:** ~60 minutes. **Difficulty:** Medium.

## Scenario

You made a good commit, then ran `git reset --hard` and moved on. An hour later you realize that commit had work you need — and `git log` no longer shows it. The branch moved away from it, so it *looks* gone.

It isn't. A committed object stays in the object database even when no branch points at it (Lecture 1 §6), and every ref move is recorded in the reflog (Lecture 3 §8). Your task: get the commit back and prove you understand *why* it was recoverable.

## Setup — create the "loss"

Run this in a scratch repo. It builds three commits, then throws the last one away with `reset --hard`.

```bash
mkdir -p /tmp/chal-02 && cd /tmp/chal-02
git init -q
echo 'v1' > feature.txt && git add . && git commit -q -m "c1"
echo 'v2' > feature.txt && git add . && git commit -q -m "c2"
echo 'IMPORTANT WORK' > feature.txt && git add . && git commit -q -m "c3: the good one"

git log --oneline           # three commits; c3 on top
git reset --hard HEAD~1     # <-- the "mistake": main now points at c2
git log --oneline           # c3 is GONE from the log
cat feature.txt             # shows 'v2' — the important work is not in the working tree
```

As far as `git log` and your files are concerned, `c3` is lost.

## Your task

Recover `c3` **without** having written down its SHA in advance. Then restore its content.

Constraints:

- You may **not** re-type the "IMPORTANT WORK" content by hand — you must recover the *actual* commit/blob from the database.
- Use the reflog and/or the object database to find it.
- Write up, in `writeup.md`, the exact steps and — more importantly — *why each one works*.

## Guiding questions for your writeup

1. Where does Git record that `main` used to point at `c3`? What command reveals it?
2. Once you have `c3`'s SHA, name **two** different ways to get its work back (e.g. move a branch to it vs. read its blob out with `cat-file`/`show`).
3. Why was recovery possible at all? What would eventually make it *impossible*, and roughly how long do you have?

## Hints

<details>
<summary>Finding the lost SHA</summary>

```bash
git reflog                 # HEAD@{1} is where you were before the reset — that's c3
git reflog show main       # the branch's own move history
```

The line labeled `reset: moving to HEAD~1` shows the jump; the entry just above it is `c3`.

</details>

<details>
<summary>Getting the work back — at least two ways</summary>

```bash
# Way 1: point a branch (or reset) at the recovered commit
git branch recovered <c3-sha>          # non-destructive: c3 is now referenced again
git checkout recovered                 # feature.txt shows IMPORTANT WORK

# Way 2: pull just the file contents out of the object database
git show <c3-sha>:feature.txt          # prints IMPORTANT WORK
git restore --source=<c3-sha> feature.txt   # write it back into the working tree
```

</details>

<details>
<summary>Why it worked — and the deadline</summary>

`c3`'s commit and blob objects were never deleted by `reset --hard`; only the *ref* moved. Unreferenced objects survive until `git gc` prunes them, which by default won't happen for ~2 weeks (`gc.reflogExpireUnreachable`). After that, or after an aggressive `git gc --prune=now`, recovery may fail.

</details>

## How success is judged

- You recovered `c3` **without** retyping its content — the restored `feature.txt` contains the original bytes.
- Your `writeup.md` explains *why* the reflog and object database made this possible, not just which commands you ran.
- You named at least two distinct recovery paths.
- Bonus: you explain the eventual expiry window that would make recovery impossible.

## Submission

Commit `writeup.md` (and optionally the recovered branch) to your portfolio under `c31-week-01/challenge-02/`.
