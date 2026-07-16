# Week 2 — Homework

Six problems, ~5 hours total. Do them in a scratch directory (`~/git-w2-hw/`) and commit each write-up to your portfolio under `c31-week-02/`.

---

## Problem 1 — Predict the pointers (30 min)

*No typing at first — reason on paper.* Given this sequence, **predict** where `main`, `feature`, and `HEAD` point after each step, then run it and check.

```bash
git init
echo a > f.txt && git add f.txt && git commit -m "A"     # step 1
git switch -c feature                                     # step 2
echo b >> f.txt && git commit -am "B"                     # step 3
git switch main                                           # step 4
echo c >> f.txt && git commit -am "C"                     # step 5
```

**Deliverable:** `predictions.md` with a small table — after each step, which commit does each of `main`, `feature`, `HEAD` point to? Then a paste of `git lg` confirming (or correcting) you. Where were you wrong, and why?

---

## Problem 2 — Fast-forward vs. no-ff, side by side (45 min)

Create the same simple two-commit feature branch **twice**, in two separate repos. Merge one with a plain fast-forward, the other with `--no-ff`.

**Deliverable:** `ff-vs-noff.md` containing both `git log --oneline --graph` outputs and a paragraph answering: what's structurally different, and give one concrete reason a team might mandate `--no-ff` on `main`.

---

## Problem 3 — Manufacture and resolve three conflicts (60 min)

Create three separate conflicts and resolve each a different way:

1. One resolved by **keeping "ours"** (`git checkout --ours` or hand-edit).
2. One resolved by **keeping "theirs"**.
3. One resolved by **combining both sides** into new content.

**Deliverable:** `conflicts.md` with, for each: the two commits that caused it, the raw marker block (paste it), how you resolved it, and the final line(s). Enable `zdiff3` for at least one and show the `||||||| base` section.

---

## Problem 4 — The abort drill (30 min)

Start a merge that conflicts. **Before** resolving, run `git merge --abort`. Confirm with `git status` and `git lg` that you're exactly where you started. Then redo the merge and actually resolve it.

**Deliverable:** `abort.md` showing the conflict starting, the `--abort`, the clean status afterward, and then the successful resolution. Two sentences: why is `--abort` always safe *before* you complete the merge?

---

## Problem 5 — Read a merge commit (45 min)

Produce a three-way merge (both branches moved). Then, using only Git commands, answer:

1. How many parents does the merge commit have? (`git cat-file -p HEAD`)
2. Which parent is "ours" and which is "theirs"? (`git rev-parse HEAD^1` / `HEAD^2`)
3. What does `git log --first-parent --oneline` show vs. plain `git log --oneline`?
4. What single diff did the merge *introduce*? (`git show HEAD`)

**Deliverable:** `merge-anatomy.md` answering all four with the commands and their output.

---

## Problem 6 — Reflection (45 min)

`reflection.md`, 300–400 words:

1. Before this week, what was your mental model of a branch? What is it now?
2. Which is clearer to you: fast-forward or three-way merge? What still feels fuzzy?
3. Describe, in your own words, exactly what "resolving a conflict" consists of. No jargon you can't define.
4. When would you reach for `--no-ff`, and when would you rebase instead? Cite the one rule about shared history.
5. What habit do you want to carry into Week 3 (remotes), where conflicts start coming from *other people*?

---

## Time budget

| Problem | Time |
|--------:|-----:|
| 1 | 30 min |
| 2 | 45 min |
| 3 | 1 h |
| 4 | 30 min |
| 5 | 45 min |
| 6 | 45 min |
| **Total** | **~4.25 h** |

After homework, ship the [mini-project](./mini-project/README.md).
