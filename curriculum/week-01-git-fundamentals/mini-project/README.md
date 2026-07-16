# Mini-Project — Reconstruct a repo's history by hand

> You will be given nothing but raw object dumps — the printed contents of a handful of blobs, trees, and commits. From those dumps alone, you will reconstruct the repository's history: draw its object graph, recover every file's contents at every commit, and rebuild a working repo that produces the *same* structure. No shortcuts, no `clone`.

**Estimated time:** 4–5 hours, spread across the back half of the week.

This is the capstone of Week 1. Everything you learned — content-addressing, the four object types, the three trees, plumbing — comes together here. If you can do this, you *understand* Git's model. You are not memorizing commands; you are reading the database the way Git reads it.

---

## Part 1 — Generate your own dumps (45 min)

To reconstruct a history, first build one and dump it, so you have an answer key. Build a small repo with a branch and a few files:

```bash
mkdir -p /tmp/mp-source && cd /tmp/mp-source && git init -q
mkdir src
echo '# Calculator'                 > README.md
printf 'def add(a,b):\n    return a+b\n' > src/calc.py
git add . && git commit -q -m "c1: add and README"

printf 'def add(a,b):\n    return a+b\n\ndef sub(a,b):\n    return a-b\n' > src/calc.py
git add . && git commit -q -m "c2: add subtraction"

echo 'MIT'                          > LICENSE
git add . && git commit -q -m "c3: add license"
```

Now **dump every object** to a text file. This is the raw material a teammate might hand you:

```bash
{
  echo "=== refs ==="
  git show-ref
  echo
  echo "=== objects ==="
  for o in $(git cat-file --batch-all-objects --batch-check | awk '{print $1}'); do
      echo "--- $o  ($(git cat-file -t "$o")) ---"
      git cat-file -p "$o"
      echo
  done
} > /tmp/object-dump.txt
```

Open `/tmp/object-dump.txt`. That file — refs plus every blob, tree, and commit printed — is your entire repository in human-readable form.

## Part 2 — Reconstruct the graph on paper (90 min)

Move `/tmp/mp-source` aside so you can't peek (`mv /tmp/mp-source /tmp/mp-hidden`). Working **only** from `object-dump.txt`, produce `history.md` containing:

1. **The object graph**, as a diagram (Mermaid, Excalidraw, or hand-drawn photo). Show every commit, the tree each points at, and the blobs each tree references. Draw parent-links between commits and label the SHAs (abbreviated is fine).
2. **A commit-by-commit table**: for each commit, its message, its parent, its root-tree SHA, and the files (with blob SHAs) present in that commit.
3. **A reconstruction of each file's contents at each commit** — e.g. what did `src/calc.py` contain at c1 vs c2? You have the blobs; match them to trees.

The skill: a tree dump lists `mode type sha name`. A commit dump names its tree and parent. Follow the pointers by hand, exactly as Lecture 3 §7 did with `cat-file`.

## Part 3 — Rebuild a matching repo (60 min)

Now prove your reconstruction is correct by **rebuilding** the repo from your understanding — not by un-hiding the original. In a new directory, recreate the files and commits in order:

```bash
mkdir -p /tmp/mp-rebuild && cd /tmp/mp-rebuild && git init -q
# recreate c1's files exactly as you reconstructed them, then commit
# recreate c2's change, commit
# recreate c3's change, commit
```

Then compare the **tree** SHAs (not commit SHAs — those differ because of author timestamps):

```bash
# in your rebuild:
git rev-parse HEAD^{tree} HEAD~1^{tree} HEAD~2^{tree}
```

Compare each against the corresponding root-tree SHAs from `object-dump.txt`. **If your reconstructed file contents are byte-for-byte correct, the tree SHAs will match** — because content-addressing guarantees identical content produces identical hashes. This is the whole point of the week, proven end-to-end.

> Commit SHAs will *not* match (they include author/committer timestamps and the parent SHA), but **tree SHAs will** if the snapshots are identical. That distinction is itself a key insight — record it in `history.md`.

## Part 4 — Write-up (30 min)

In `history.md`, add a closing section answering:

- Which tree SHAs matched, and which didn't? What does a mismatch tell you about your reconstruction?
- Why do commit SHAs differ even when the file contents are identical?
- Where did the dump make history *obvious*, and where did you have to reason carefully?

---

## Deliverable

A directory `c31-week-01/mini-project/` in your portfolio containing:

1. `object-dump.txt` — the raw dump you worked from.
2. `history.md` — the graph, the commit table, the per-commit file contents, and the write-up.
3. `rebuild/` — your rebuilt repo (or its `git log --oneline` + tree SHAs pasted into `history.md`).

## Milestones

- [ ] **M1:** `object-dump.txt` generated and the source repo hidden away.
- [ ] **M2:** Object graph drawn from the dump alone.
- [ ] **M3:** Every file's contents at every commit reconstructed.
- [ ] **M4:** Repo rebuilt; tree SHAs compared against the dump.
- [ ] **M5:** Write-up explains matches, mismatches, and why commit SHAs differ.

## Rubric

| Criterion | Weight | "Great" looks like |
|-----------|------:|--------------------|
| Graph accuracy | 25% | Every commit→tree→blob link correct, SHAs labeled |
| File reconstruction | 25% | All file contents at all commits recovered from blobs |
| Rebuild verification | 25% | Tree SHAs match the dump; matches/mismatches explained |
| Conceptual write-up | 15% | Correctly explains tree-match vs commit-mismatch |
| Reproducibility | 10% | Someone could follow your steps and get the same result |

## Why this matters

Once you can read a repository as raw objects, no Git command can surprise you again. `merge`, `rebase`, `cherry-pick`, `bisect` — every one of them is just moving refs and building new trees and commits out of old blobs. You'll spend the next seven weeks doing exactly that, and you'll do it with the confidence of someone who has seen the machine from the inside.

---

When done: push, then start [Week 2 — Branching & Merging](../../week-02-branching-and-merging/).
