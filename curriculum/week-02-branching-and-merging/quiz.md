# Week 2 — Quiz

Twelve questions. Lectures closed. Aim for 10/12 before Week 3. Mix of multiple-choice and short-answer.

---

**Q1.** On disk, a branch like `main` is:

- A) A full copy of the project's files.
- B) A directory under `.git/branches/`.
- C) A small file containing one commit hash.
- D) An entry in the commit itself.

---

**Q2.** `.git/HEAD` normally contains:

- A) The hash of the latest commit.
- B) A symbolic ref like `ref: refs/heads/main`.
- C) The name of the repository.
- D) Nothing; it's empty until the first commit.

---

**Q3.** What is the modern, focused replacement for `git checkout <branch>`?

- A) `git branch <branch>`
- B) `git switch <branch>`
- C) `git restore <branch>`
- D) `git reset <branch>`

---

**Q4.** A merge **fast-forwards** when:

- A) There are no conflicts.
- B) The destination branch is a direct ancestor of the source (it hasn't moved since the branch point).
- C) You pass `--no-ff`.
- D) Both branches changed the same file.

---

**Q5.** How many parents does a three-way merge commit have?

- A) 0
- B) 1
- C) 2
- D) It depends on the number of conflicts.

---

**Q6.** In a three-way merge, the "base" is:

- A) The `main` branch.
- B) The commit you're currently on.
- C) The most recent common ancestor of the two branches.
- D) The first commit in the repo.

---

**Q7.** In this conflict block, `TIMEOUT = 45` belongs to:

```
<<<<<<< HEAD
TIMEOUT = 45
=======
TIMEOUT = 60
>>>>>>> feature
```

- A) The `feature` branch ("theirs").
- B) The branch you're on ("ours").
- C) The merge base.
- D) Can't tell without more context.

---

**Q8.** "Resolving a conflict" fundamentally means:

- A) Running `git resolve`.
- B) Editing the file to remove all markers, then staging it.
- C) Deleting the conflicted file.
- D) Running `git merge --abort`.

---

**Q9.** You're mid-conflict and want to bail out completely, back to the pre-merge state. You run:

- A) `git reset --hard`
- B) `git merge --abort`
- C) `git checkout .`
- D) `git revert HEAD`

---

**Q10.** `git branch -d feature` refuses with "not fully merged." What does that mean?

- A) The branch has uncommitted changes.
- B) The branch's commits aren't reachable from anywhere else, so deleting loses them.
- C) You're currently on that branch.
- D) The branch has a conflict.

---

**Q11. (Short answer)** In one sentence, why is rebasing a branch that four teammates have already pulled a bad idea?

---

**Q12. (Short answer)** You run `git merge --ff-only feature` and it aborts with "Not possible to fast-forward." What does that tell you about the two branches' histories?

---

## Answer key

<details>
<summary>Reveal after attempting</summary>

1. **C** — one commit hash (40 hex chars + newline) in `.git/refs/heads/main`. Branching copies a hash, not files.
2. **B** — a symbolic ref naming the current branch. It holds a raw hash only in detached-HEAD state.
3. **B** — `git switch`. (`git restore` replaces `git checkout -- file`; different job.)
4. **B** — the destination is a direct ancestor of the source, so Git just slides the pointer forward. No new commit.
5. **C** — two parents: "ours" (first parent) and "theirs" (second). That's the signature of a merge commit.
6. **C** — the merge base, the most recent common ancestor. It's the "three" in three-way and what makes merging smart.
7. **B** — "ours." The top block (between `<<<<<<< HEAD` and `=======`) is the branch you're on; the bottom is the incoming branch.
8. **B** — edit the file until the markers are gone and the content is correct, then `git add` it. There is no `git resolve` command.
9. **B** — `git merge --abort` restores the exact pre-merge state. Safe because the merge commit isn't created until you finish resolving.
10. **B** — the commits live only on that branch; `-d` protects you from losing them. Use `-D` only when you've decided you don't want that work.
11. Rebasing rewrites the commits' hashes; the teammates still hold the old commits, so their next pull sees divergent histories and forces a painful reconciliation (duplicate commits / phantom conflicts / lost local work). **Never rebase shared history.**
12. The histories have **diverged** — the destination branch has moved since the branch point, so neither is an ancestor of the other. A fast-forward is impossible; you'd need a three-way merge (or a rebase first).

</details>

**Scoring:** 10+ → move to the mini-project. 7–9 → re-read the lecture behind each miss. <7 → re-read Lectures 1–3 from the top before Week 3.
