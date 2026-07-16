# Week 7 — Homework

Six problems, ~5 hours total. Commit each to your portfolio under `c31-week-07/homework/`. These reinforce the lectures between the exercises and the mini-project.

---

## Problem 1 — A `commit-msg` hook (45 min)

Write a `commit-msg` hook that enforces **Conventional Commits**: the first line must start with one of `feat`, `fix`, `docs`, `refactor`, `test`, `chore` (optionally with a scope in parentheses), then `: `, then a subject.

The hook receives the message-file path as `$1`.

**Acceptance:**

- `hooks/commit-msg` (or `.git/hooks/commit-msg`) rejects `updated stuff` and accepts `fix(export): handle empty rows`.
- A transcript in `p1.md` shows one rejected and one accepted commit.
- One sentence: why put message rules in `commit-msg` rather than `pre-commit`?

---

## Problem 2 — Bisect by hand (45 min)

Reuse Exercise 2's repo (or build a fresh 10-commit history with one planted bug). Run `git bisect` **manually** — no `run` script — judging each stop yourself.

**Acceptance:**

- `p2.md` records every `git bisect good`/`bad` you entered and the first-bad-commit result.
- The step count, and a note on how it compares to `log2(N)`.
- Confirmation you ran `git bisect reset` at the end.

---

## Problem 3 — Two worktrees, two builds (45 min)

Create a repo, then use `git worktree` to have `main` and a `feature` branch checked out simultaneously. In `p3.md`, capture `git worktree list` showing both, and explain one concrete task made easier by having both trees at once (e.g. diffing behaviour by eye, building one while editing the other).

**Acceptance:**

- `p3.md` shows two live worktrees on two branches.
- A short explanation of the task worktrees made easier.
- Evidence you cleaned up with `git worktree remove`.

---

## Problem 4 — Annotated vs. lightweight tags (45 min)

On any repo, create one **lightweight** tag and one **annotated** tag on different commits. Inspect both with `git show` and `git cat-file -t`.

**Acceptance:**

- `p4.md` shows the two `git tag` commands and both `git show` outputs.
- A table contrasting what metadata each carries.
- One sentence: why do releases require the annotated kind?

---

## Problem 5 — SemVer decisions (45 min)

For each change below, state the correct next version starting from `2.3.1`, and justify it in one line:

1. Fixed a crash when the input file is empty.
2. Added an optional `--verbose` flag; all existing behaviour unchanged.
3. Renamed the `--out` flag to `--output` and removed the old name.
4. Rewrote the internals for speed; public interface identical.
5. Added a new subcommand `export` alongside the existing ones.

**Acceptance:**

- `p5.md` gives the new version for each and a one-line SemVer justification.
- One of your answers must involve a **MAJOR** bump — identify which and why.

---

## Problem 6 — Cut a release with `gh` (60 min)

On a small GitHub repo of yours, create an annotated tag and cut a real release with `gh release create --generate-notes`, attaching at least one artifact (a tarball or checksums file).

**Acceptance:**

- `p6.md` contains the exact `git tag`, `git push --follow-tags`, and `gh release create` commands you ran.
- The URL of the published release.
- `gh release view <tag>` output pasted in, confirming the artifact is attached.

---

## Time budget

| Problem | Time |
|--------:|----:|
| 1 | 45 min |
| 2 | 45 min |
| 3 | 45 min |
| 4 | 45 min |
| 5 | 45 min |
| 6 | 1 h |
| **Total** | **~5 h** |

After homework, ship the [mini-project](./mini-project/README.md).
