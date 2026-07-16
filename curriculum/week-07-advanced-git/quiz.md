# Week 7 — Quiz

Thirteen questions. Lectures closed. Aim for 11/13 before Week 8.

---

**Q1.** Where do a repository's hooks live by default?

- A) `.githooks/` in the working tree
- B) `.git/hooks/`
- C) `~/.config/git/hooks/`
- D) `refs/hooks/`

---

**Q2.** A `pre-commit` hook aborts the commit by:

- A) printing "abort"
- B) exiting with a **non-zero** status
- C) deleting the staged files
- D) exiting `0`

---

**Q3.** Why don't hooks travel with `git clone`?

- A) They're encrypted
- B) They live inside `.git/`, which isn't part of the tracked tree
- C) GitHub strips them for security
- D) They only work on the machine that created them

---

**Q4.** Which flag skips client-side `pre-commit` and `commit-msg` hooks?

- A) `--force`
- B) `--skip-hooks`
- C) `--no-verify`
- D) `--amend`

---

**Q5.** `git bisect` performs which kind of search?

- A) Linear, oldest to newest
- B) Binary search over the commit range
- C) Depth-first over branches
- D) A full-text search of diffs

---

**Q6.** To start a bisect you must give Git:

- A) two branch names
- B) a known-good and a known-bad commit
- C) the buggy line number
- D) the author of the bad commit

---

**Q7.** In `git bisect run <script>`, what does exit code **125** mean?

- A) The commit is good
- B) The commit is bad
- C) **Skip** this commit — it can't be tested
- D) Abort the bisect

---

**Q8.** What single command ends a bisect and returns you to where you started?

- A) `git bisect stop`
- B) `git bisect end`
- C) `git bisect reset`
- D) `git checkout main`

---

**Q9.** Two worktrees of the same repo share:

- A) their index and HEAD
- B) one object database (and refs/stashes)
- C) nothing — they're independent clones
- D) the same checked-out branch

---

**Q10.** Why does Git refuse to check out `main` in a second worktree?

- A) A branch can be checked out in only one worktree at a time
- B) `main` is protected
- C) Worktrees can't hold branches, only detached HEADs
- D) It doesn't refuse; that's allowed

---

**Q11.** After a plain `git clone` of a repo with a submodule, the submodule directory is:

- A) fully populated automatically
- B) **empty** until you run `git submodule update --init`
- C) a symlink to the upstream repo
- D) replaced with a `.gitmodules` copy

---

**Q12.** A **release** requires which kind of tag?

- A) lightweight
- B) annotated
- C) either works identically
- D) no tag at all

---

**Q13.** Starting from `2.4.1`, you make a **backwards-compatible bug fix**. The new version is:

- A) `3.0.0`
- B) `2.5.0`
- C) `2.4.2`
- D) `2.4.1-fix`

---

## Answer key

<details>
<summary>Reveal after attempting</summary>

1. **B** — `.git/hooks/`. Ordinary executables Git runs by name.
2. **B** — a non-zero exit aborts the operation; exit `0` allows it.
3. **B** — hooks live in `.git/`, which is not tracked, so `clone` doesn't copy them. Share via `core.hooksPath` or a hook manager.
4. **C** — `--no-verify` (`-n`). It's why unskippable rules belong in CI or a server-side hook.
5. **B** — binary search: each verdict halves the remaining range, so ~`log2(N)` tests.
6. **B** — one known-good and one known-bad commit; "bad" is usually `HEAD`.
7. **C** — 125 means "skip." Codes 1–124/126/127 mean bad; 0 means good; 128+ aborts.
8. **C** — `git bisect reset`. Until you run it you're in detached HEAD mid-search.
9. **B** — one object database, plus shared refs and stashes; each worktree has its **own** index and HEAD.
10. **A** — the same branch can only be checked out in one worktree (use `--detach` for the same commit elsewhere).
11. **B** — empty until `git submodule update --init` (or clone with `--recurse-submodules`).
12. **B** — annotated. Releases need the tagger/date/message metadata (and optional signature) that lightweight tags don't store.
13. **C** — `2.4.2`. A backwards-compatible bug fix bumps **PATCH**.

</details>

If you scored 11+: on to the mini-project. 8–10: re-read the lecture behind each miss. <8: re-read all three lectures from the top before Week 8.
