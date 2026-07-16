# Week 1 — Quiz

Twelve questions. Lectures closed. Aim for 10/12 before Week 2. Mix of multiple-choice and short-answer.

---

**Q1.** Git stores each commit as:

- A) A diff against the previous commit.
- B) A full snapshot (a pointer to a complete tree).
- C) Only the files that changed, as separate patches.
- D) A compressed copy of the working directory, minus `.git`.

---

**Q2.** A file's *name* is stored in:

- A) The blob.
- B) The commit.
- C) The tree that references the blob.
- D) The index only.

---

**Q3.** Two files in a repo contain byte-for-byte identical content. How many blob objects does Git create?

- A) Two — one per file.
- B) One — content-addressing deduplicates.
- C) Zero until you commit.
- D) One per commit they appear in.

---

**Q4.** Which are Git's four object types?

- A) file, folder, snapshot, label
- B) blob, tree, commit, tag
- C) blob, index, ref, HEAD
- D) commit, branch, remote, stash

---

**Q5.** `git diff` (with no arguments) compares:

- A) HEAD and the working tree.
- B) HEAD and the index.
- C) The index and the working tree.
- D) Two branches.

---

**Q6.** `git diff --staged` compares:

- A) HEAD and the index.
- B) The index and the working tree.
- C) HEAD and the working tree.
- D) The last two commits.

---

**Q7.** You edit a file, `git add` it, then edit it again without re-adding. What does `git status -s` show for that file?

- A) `A ` (added)
- B) ` M` (modified, unstaged only)
- C) `M ` (modified, staged only)
- D) `MM` (staged change *and* a newer unstaged change)

---

**Q8.** Which `git reset` mode can destroy uncommitted work in the working tree?

- A) `--soft`
- B) `--mixed`
- C) `--hard`
- D) None of them touch the working tree.

---

**Q9.** A branch, at the storage level, is:

- A) A copy of every commit on that line of development.
- B) A file holding a single commit SHA.
- C) An entry inside each commit object.
- D) A directory under `.git/objects/`.

---

**Q10.** What does `HEAD` normally point at?

- A) A commit SHA directly.
- B) A branch (a symbolic ref), which points at a commit.
- C) The working tree.
- D) The index.

---

**Q11 (short answer).** In one sentence, explain why you can recover a commit after `git reset --hard` moved the branch away from it.

---

**Q12 (short answer).** You run `git cat-file -p HEAD^{tree}` and see a line `040000 tree 5a1c… src`. What does that line tell you, and what would you run next to see the contents of a file inside `src`?

---

## Answer key

<details>
<summary>Reveal after attempting</summary>

1. **B** — Git stores snapshots, not diffs. Diffs are computed on demand by comparing trees.
2. **C** — the tree holds `mode type sha name`; the blob is nameless bytes. That's why a rename changes a tree but not the blob.
3. **B** — one. Identical content hashes to the same SHA, so it's stored once (deduplication).
4. **B** — blob, tree, commit, tag.
5. **C** — index ↔ working tree (unstaged changes).
6. **A** — HEAD ↔ index (what a commit *right now* would record).
7. **D** — `MM`: the staged version differs from HEAD, and the working version differs from the staged version.
8. **C** — `--hard` overwrites the working tree. `--soft` and `--mixed` leave working-tree files alone.
9. **B** — a ref: a small file (`.git/refs/heads/<name>`) holding one 40-hex commit SHA.
10. **B** — HEAD is a symbolic ref, usually `ref: refs/heads/main`; the branch points at the commit.
11. The commit (and its blobs/tree) still exists in the object database — only the *ref* moved. The reflog records the old SHA, so you can point a branch back at it or read it with `cat-file`/`show`. (Recoverable until `git gc` prunes unreferenced objects, ~2 weeks by default.)
12. It says `src` is a **subtree** (a directory) whose tree object has SHA `5a1c…`. To see a file inside it, run `git ls-tree 5a1c…` (or `git ls-tree HEAD src`) to get the file's blob SHA, then `git cat-file -p <blob-sha>` (or directly `git show HEAD:src/<file>`).

</details>

If you scored 10+: move to the mini-project. 8–9: re-read the missed lecture sections. <8: re-read Lectures 1 and 2 from the top before Week 2.
