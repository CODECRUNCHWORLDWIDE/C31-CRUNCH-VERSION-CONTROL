# Week 4 — Quiz

Twelve questions. Lectures closed. Aim for 10/12 before Week 5. Mix of multiple-choice and short-answer.

---

**Q1.** Why does `git commit --amend` change the commit's SHA?

- A) It doesn't — the SHA stays the same.
- B) A commit is immutable, so amend builds a brand-new commit (with a new SHA) and moves the branch pointer to it.
- C) Because amend always changes the author date.
- D) Because the index is cleared first.

---

**Q2.** After `git reset --soft HEAD~1`, the changes from the undone commit are:

- A) Discarded.
- B) Unstaged (in the working tree only).
- C) Staged (in the index), ready to re-commit.
- D) Pushed to the remote.

---

**Q3.** Which reset mode can permanently lose uncommitted work?

- A) `--soft`
- B) `--mixed`
- C) `--hard`
- D) None of them.

---

**Q4.** A bad commit is already pushed to `main` and teammates have pulled it. The correct fix is:

- A) `git reset --hard` then force-push.
- B) `git commit --amend` then force-push.
- C) `git revert` — a new commit that undoes it.
- D) Delete and recreate the branch.

---

**Q5.** In an interactive-rebase to-do list, the commit at the **top** is:

- A) The newest commit.
- B) The oldest commit in the range.
- C) HEAD.
- D) The merge base only.

---

**Q6.** The difference between `squash` and `fixup` in a rebase is:

- A) `squash` deletes the commit; `fixup` keeps it.
- B) `fixup` combines the commit into the previous one and **discards** its message; `squash` keeps the message for editing.
- C) They are identical.
- D) `squash` works only on the last commit.

---

**Q7.** What does `git rebase --abort` do?

- A) Skips the current conflicting commit.
- B) Finishes the rebase immediately.
- C) Returns the branch to its exact state before the rebase started.
- D) Deletes the branch.

---

**Q8.** `git cherry-pick a1b2c3d` does what to the original commit `a1b2c3d`?

- A) Moves it to the current branch.
- B) Deletes it.
- C) Leaves it in place; creates a **copy** (new SHA) of its changes on the current branch.
- D) Merges its whole branch in.

---

**Q9.** The commit range `main~4..main~1` is:

- A) Inclusive of `main~4`.
- B) Exclusive of `main~4` (commits *after* it, up to `main~1`).
- C) The same as `main~4^..main~1`.
- D) Invalid syntax.

---

**Q10.** After `git reset --hard HEAD~3`, how do you get the three commits back?

- A) You can't — hard reset is permanent.
- B) `git revert`.
- C) `git reflog` to find the pre-reset SHA, then `git reset --hard <sha>`.
- D) Re-clone from the remote.

---

**Q11.** (Short answer) What does the notation `HEAD@{2}` mean, and where does it come from?

---

**Q12.** (Short answer) Name one thing the reflog **cannot** recover, and explain why in one sentence.

---

## Answer key

<details>
<summary>Reveal after attempting</summary>

1. **B** — commits are immutable; amend creates a new commit with a new SHA and repoints the branch. This is why amending a *pushed* commit rewrites shared history.
2. **C** — `--soft` moves HEAD only; the changes stay **staged** in the index. `--mixed` would leave them unstaged.
3. **C** — `--hard` overwrites the working tree, discarding uncommitted changes. `--soft` and `--mixed` never touch the working tree destructively.
4. **C** — once history is shared, you **revert** (a forward, additive undo everyone can pull). Never reset + force-push a shared branch.
5. **B** — the to-do list is oldest-first, the reverse of `git log`. HEAD (newest) is at the bottom.
6. **B** — `fixup` = squash but throw away the message; `squash` opens an editor to combine messages. Use `fixup` for junk messages.
7. **C** — `--abort` restores the exact pre-rebase state. It's your safety net mid-rebase.
8. **C** — cherry-pick leaves the original untouched and applies a copy of its changes as a new commit (new SHA) on the current branch.
9. **B** — `A..B` is exclusive of `A`. To *include* `A`, write `A^..B`. So C is wrong (that form includes `main~4`).
10. **C** — `git reflog` still references the orphaned commits; `git reset --hard <sha>` (or `HEAD@{n}`) restores them. Nothing was destroyed.
11. **`HEAD@{2}`** is a **reflog reference**: "where HEAD pointed two moves ago." It comes from the reflog, Git's local journal of every HEAD movement (commit, checkout, reset, rebase…). You can use it anywhere a SHA is expected.
12. Acceptable answers include: **uncommitted working-tree changes** wiped by `reset --hard` (they were never a commit, so the reflog — which tracks commits/HEAD movement — never recorded them); or commits after the reflog has **expired** (default ~30 days for unreachable objects) and been garbage-collected; or anything in **another clone's** reflog (the reflog is local and never pushed).

</details>

**Scoring:** 10+ → on to Week 5. 7–9 → re-read the lecture behind each miss. <7 → re-read all three lectures from the top before continuing.
