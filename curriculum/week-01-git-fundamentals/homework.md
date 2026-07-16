# Week 1 — Homework

Six problems, ~5 hours total. Each reinforces the object model or the three trees. Commit each result to your portfolio under `c31-week-01/homework/`.

---

## Problem 1 — Predict, then verify (30 min)

For each command below, **write down what you expect** before running it in a scratch repo, then run it and compare.

```bash
git init -q
echo 'x' > f.txt
# (a) after `git add f.txt`, how many objects exist? which type(s)?
git add f.txt
git count-objects -v
# (b) after the commit, how many MORE objects, and of which types?
git commit -m "add f"
git count-objects -v
```

**Acceptance:** `predictions.md` with your before/after guesses and the real counts, plus one sentence explaining the difference (hint: `add` creates a blob; `commit` creates a tree and a commit).

---

## Problem 2 — The three trees, narrated (45 min)

Take one file through the full cycle and narrate every step. In `three-trees.md`, for each of these actions, record the output of `git status -s`, `git diff`, and `git diff --staged`, and say **which trees differ**:

1. Edit the file (don't stage).
2. `git add` it.
3. Edit it again.
4. `git commit`.
5. Edit once more, then `git restore` it.

**Acceptance:** A table with a row per step, columns for the three commands' output, and a "which trees differ" note. You must reach and explain the `MM` state.

---

## Problem 3 — Compute a hash by hand-ish (30 min)

Git blobs are hashed with a header: the object is `blob <size>\0<content>`, and *that* is what gets SHA-1'd. Verify it:

```bash
printf 'hello\n' | git hash-object --stdin          # Git's answer
# Now reproduce it manually:
printf 'blob 6\0hello\n' | shasum                    # should match (macOS)
printf 'blob 6\0hello\n' | sha1sum                   # Linux
```

**Acceptance:** `hash.md` showing both outputs match, plus a sentence: why is the `6` there, and what is the `\0`? (Hint: `hello\n` is six bytes; `\0` separates header from content.)

---

## Problem 4 — Read a real project's history (45 min)

Clone a small open-source repo (any you like — e.g. a tiny CLI tool) and *only read* it:

```bash
git clone --depth 50 <some-repo-url> hw-read && cd hw-read
git log --oneline --graph --all | head -40
git log --stat -3
git show HEAD
git show HEAD~5:<some-file>       # a file's contents 5 commits ago
```

**Acceptance:** `read.md` answering: How many commits are on the default branch (roughly)? Who are the top 3 authors (`git shortlog -sn | head -3`)? Pick one commit and describe, from `git show`, exactly what it changed.

---

## Problem 5 — Plumbing scavenger hunt (60 min)

In a repo of your own with at least three commits, use **only plumbing** to answer each question. Record the command and answer in `plumbing.md`:

1. The root-tree SHA of `HEAD`.
2. The blob SHA of a specific file at `HEAD`.
3. The blob SHA of that *same file* two commits ago — did it change?
4. The full list of every object in the repo, grouped by type (`git cat-file --batch-check --batch-all-objects`).
5. The commit message of `HEAD~2` via `git cat-file -p` (not `git log`).

**Acceptance:** `plumbing.md` with five command/answer pairs, all using `rev-parse`, `cat-file`, or `ls-tree` — no porcelain shortcuts.

---

## Problem 6 — Reflection (30 min)

`reflection.md`, 300–400 words:

1. Before this week, what did you *think* `git commit` did? What do you now know it does?
2. Which of the three trees was hardest to internalize, and what finally made it click?
3. What's still fuzzy? Be specific — "the object model" is too broad; "why a rename doesn't create a new blob" is useful.
4. One habit you want to carry into Week 2 (Branching & Merging).

---

## Time budget

| Problem | Time |
|--------:|-----:|
| 1 | 30 min |
| 2 | 45 min |
| 3 | 30 min |
| 4 | 45 min |
| 5 | 60 min |
| 6 | 30 min |
| **Total** | **~4h** |

After homework, ship the [mini-project](./mini-project/README.md) and take the [quiz](./quiz.md).
