# Week 1 — Git Fundamentals

> *Git is not a pile of commands. It is a tiny content-addressed database with a handful of commands painted on top. Learn the database, and every command becomes predictable.*

Welcome to **C31 · Crunch Version Control**. Week 1 takes you from "I type `git commit` and hope" to "I can tell you what `git commit` will do to the object database *before* I press Enter." By Sunday you will have built commits by hand, inspected raw Git objects with plumbing commands, and reconstructed a repository's history from nothing but object dumps.

Everything after this week — branching, remotes, rebases, CI — is recombination of the model you learn here. Spend the time. It pays back for the rest of your career.

## The week's goal

**Understand Git's object model well enough to predict any command.** When you finish, you should be able to trace a `commit` down to the blobs it stores, name every ref that moved, and read `.git/` like a filesystem instead of a black box.

## Learning objectives

By the end of this week, you will be able to:

- **Explain** what version control is, why snapshots beat diffs, and how Git differs from the file-copy-and-rename habit most people start with.
- **Describe** Git's four object types — **blob**, **tree**, **commit**, **tag** — and how content-addressing with a SHA hash makes them immutable and deduplicated.
- **Trace** a change through the **three trees** — the working tree, the index (staging area), and `HEAD` — and predict what `add`, `status`, `diff`, and `commit` move between them.
- **Read** history with `git log`, `git show`, and `git diff`, choosing the right one for the question you're asking.
- **Use plumbing commands** — `hash-object`, `cat-file`, `ls-tree`, `rev-parse`, `update-ref` — to inspect and even build objects directly.
- **Navigate `.git/`** and say what every important entry (`HEAD`, `refs/`, `objects/`, `index`, `logs/`) is for.
- **Recover** a commit you thought you lost using `reflog` and the object database.

## Prerequisites

- You can run commands in a terminal and have written some code in any language. No prior Git knowledge is assumed.
- **Git 2.40 or newer** installed (`git --version`). Install guides are in [`resources.md`](./resources.md).
- A text editor you're comfortable in. Set it as Git's editor: `git config --global core.editor "code --wait"` (or `nano`, `vim`, etc.).
- One-time identity setup (do this now if you haven't):

```bash
git config --global user.name  "Your Name"
git config --global user.email "you@example.com"
```

## Topics covered

- What version control buys you, and the snapshot-vs-delta mental model
- Content-addressed storage: SHA-1 today, SHA-256 tomorrow, and why the hash *is* the name
- The four object types and how a commit points at a tree points at blobs
- The three trees: working tree, index, `HEAD` — the whole of everyday Git
- `add`, `status`, `diff`, `commit` explained as movements between trees
- Reading history: `log`, `show`, `diff`, and the refs that make them possible
- A guided tour of `.git/` with `cat-file`, `hash-object`, `ls-tree`, `rev-parse`
- `reflog` as the safety net that makes "lost" commits recoverable

## How to navigate this week

Work top to bottom. Each piece assumes the ones above it.

| # | File | What's inside | Time |
|--:|------|---------------|-----:|
| 1 | [lecture-notes/01-version-control-and-the-object-model.md](./lecture-notes/01-version-control-and-the-object-model.md) | What VC is; blobs, trees, commits; content-addressing and the SHA | ~2h |
| 2 | [lecture-notes/02-the-three-trees.md](./lecture-notes/02-the-three-trees.md) | Working tree, index, HEAD; add/status/diff/commit as movements | ~2h |
| 3 | [lecture-notes/03-reading-history-and-a-tour-of-dot-git.md](./lecture-notes/03-reading-history-and-a-tour-of-dot-git.md) | log/show/refs + plumbing tour of `.git/` | ~2h |
| 4 | [exercises/exercise-01-init-and-inspect-commits.md](./exercises/exercise-01-init-and-inspect-commits.md) | Init a repo, make commits, inspect what got stored | ~40m |
| 5 | [exercises/exercise-02-selective-staging-and-diffs.md](./exercises/exercise-02-selective-staging-and-diffs.md) | `git add -p` and reading the three kinds of diff | ~45m |
| 6 | [exercises/exercise-03-explore-the-object-database.md](./exercises/exercise-03-explore-the-object-database.md) | Walk blob→tree→commit with plumbing commands | ~50m |
| 7 | [challenges/challenge-01-reconstruct-the-object-db.md](./challenges/challenge-01-reconstruct-the-object-db.md) | Given a command sequence, predict the object DB | ~60m |
| 8 | [challenges/challenge-02-recover-a-lost-commit.md](./challenges/challenge-02-recover-a-lost-commit.md) | Recover a "lost" commit with reflog + cat-file | ~60m |
| 9 | [mini-project/README.md](./mini-project/README.md) | Reconstruct a repo's history by hand from raw object dumps | ~4h |
| 10 | [homework.md](./homework.md) | Six practice problems reinforcing the model | ~5h |
| 11 | [quiz.md](./quiz.md) | 12 self-check questions with an answer key | ~30m |
| 12 | [resources.md](./resources.md) | Official docs + the best free deep-dives | — |

Indexes: [exercises/README.md](./exercises/README.md) · [challenges/README.md](./challenges/README.md).

## By the end of this week you can…

- Draw the object graph of a repository and point to the SHA of any blob, tree, or commit in it.
- Predict, before running it, exactly what `git add`, `git commit`, `git reset`, or `git checkout` will change.
- Open `.git/` and explain every entry that matters.
- Recover work you thought was gone — without panic.

## Up next

[Week 2 — Branching & Merging](../week-02-branching-and-merging/) — where a "branch" turns out to be the cheapest thing in Git: a 41-byte file pointing at a commit.

---

*Part of the Code Crunch Worldwide open curriculum · GPL-3.0. Found an error? Open an issue or PR.*
