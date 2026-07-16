# Challenge 1 — Reconstruct the object DB from a command sequence

**Time:** ~60 minutes. **Difficulty:** Medium.

## Scenario

Someone hands you the exact commands they ran in a fresh repo — but not the results. Your job is to **predict the state of the object database and refs** *before* you run anything, then run it and check yourself. This is the skill that separates "I use Git" from "I understand Git."

## The command sequence

Read this carefully. Do **not** run it yet.

```bash
git init -q
echo 'A' > a.txt
echo 'B' > b.txt
git add a.txt
git commit -m "c1: add a"          # commit 1
git add b.txt
git commit -m "c2: add b"          # commit 2
echo 'A' > c.txt                    # NOTE: same content as a.txt
git add c.txt
git commit -m "c3: add c (dup of a)"   # commit 3
git branch experiment
echo 'B2' > b.txt
git add b.txt
git commit -m "c4: change b"       # commit 4, on main only
```

## Your task

Before running anything, write your predictions in `writeup.md`:

1. **How many commit objects** exist after the whole sequence? How many **tree** objects (top-level trees — one per commit)? 
2. **How many distinct blob objects** exist? This is the interesting one. List the distinct contents that ever got staged, and remember the deduplication rule from Lecture 1. (Hint: `A`, `B`, `B2` — but `c.txt` contains `A`…)
3. **What does each ref point at?** Specifically: which commit is `refs/heads/main` at, and which is `refs/heads/experiment` at? Are they the same or different, and why?
4. In commit 3's tree, `a.txt` and `c.txt` both contain `A`. **How many blobs does that tree reference for those two files** — one or two? Justify it.

## Verify

Now run the sequence in a scratch repo and check your predictions:

```bash
mkdir -p /tmp/chal-01 && cd /tmp/chal-01
# ...paste the sequence...

git count-objects -v                       # object counts
git cat-file --batch-check --batch-all-objects | awk '{print $2}' | sort | uniq -c
git rev-parse main experiment              # where each branch points
git ls-tree HEAD                           # commit 3? no — HEAD is commit 4 on main
git log --oneline --all --graph
```

To confirm the deduplication answer, inspect commit 3's tree specifically:

```bash
c3=$(git rev-parse main~1)                 # commit 3 is main's grandparent... count carefully!
git ls-tree "$c3"                          # compare the blob SHAs of a.txt and c.txt
```

## Constraints

- Predict **first**, in writing. Running before predicting defeats the challenge.
- Explain every discrepancy between prediction and reality — a wrong prediction you *understand* is worth more than a lucky guess.

## Hints

<details>
<summary>Counting blobs</summary>

A blob is created per distinct *content*, not per file. Contents ever staged: `A` (a.txt), `B` (b.txt at c2), `A` again (c.txt — dedup, no new blob), `B2` (b.txt at c4). So the distinct blob contents are `A`, `B`, `B2` = three blobs total. Trees and commits are separate objects on top of those.

</details>

<details>
<summary>Where does experiment point?</summary>

`git branch experiment` was created right after commit 3, so `experiment` points at **commit 3**. Commit 4 was made on `main` only, so `main` is ahead of `experiment` by one commit. They share history up to commit 3.

</details>

## How success is judged

- Your `writeup.md` predictions are made **before** running, and clearly separated from the verification.
- The blob count (3) is correct **and** justified via deduplication.
- You correctly identify that `main` and `experiment` diverge at commit 3.
- Every wrong prediction has a written explanation of *why* the real answer differs.

## Submission

Commit `writeup.md` to your portfolio under `c31-week-01/challenge-01/`.
