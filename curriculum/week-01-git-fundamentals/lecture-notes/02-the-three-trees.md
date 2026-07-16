# Lecture 2 — The three trees: working tree, index, HEAD

> **Duration:** ~2 hours. **Outcome:** You can name Git's three trees, say what `add`, `status`, `diff`, and `commit` move between them, and read a `git status` report by knowing *which pair of trees* it is comparing.

## 1. Everyday Git is three trees

Ninety percent of daily Git is moving content between **three trees**. Lecture 1 gave you the object database; this lecture gives you the three *views* into it that you actually work with. Learn these three and their transitions, and `status`, `add`, `diff`, `commit`, `reset`, and `checkout` stop being commands to memorize and become moves you can predict.

| Tree | Also called | What it is | Where it lives |
|------|-------------|------------|----------------|
| **Working tree** | working directory, worktree | The actual files on disk you edit | Your project folder |
| **Index** | staging area, cache | The proposed *next* commit, built up file by file | `.git/index` (a binary file) |
| **HEAD** | current commit | A pointer to the snapshot of your last commit | `.git/HEAD` → a branch → a commit |

Think of it as an assembly line:

```text
  edit here          stage here          commit here
 WORKING TREE  ──▶     INDEX        ──▶      HEAD
   (on disk)      (.git/index)         (last commit)
       git add ────────┘   git commit ──────┘
```

- The **working tree** is where you make changes.
- The **index** is a staging area: a *draft* of your next commit. You add changes to it deliberately.
- **HEAD** is the last committed snapshot — your baseline.

The index is the tree beginners find surprising, because most tools have only "the file" and "the saved version." Git inserts a deliberate middle layer so you can craft a commit precisely instead of dumping every change at once.

## 2. The index is a real, inspectable thing

The index is not a metaphor. It is a file, `.git/index`, and Git offers plumbing to read it:

```bash
git ls-files --stage
# 100644 e69de29bb2d1d6434b8b29ae775ad8c2e48c5391 0	README.md
# 100644 8ab686eafeb1f44702738c8b0f24f2567c36da6d 0	main.py
```

Each line is a staged entry: mode, blob SHA, stage number, path. When you `git add` a file, Git writes its content as a **blob** into the object database *immediately* and records that blob's SHA in the index. Staging is not "remembering to include this later" — it is "store the bytes now, and note them in the draft."

This is why `git add` followed by *further edits* stages the *old* version: the blob was written at `add` time. Edit again and you must `add` again to stage the new bytes.

## 3. status, diff, add, commit — as movements

Here is the mental unlock: every one of these commands is defined by **which two trees it compares or which direction it moves content**.

| Command | What it does in terms of trees |
|---------|--------------------------------|
| `git status` | Compares HEAD↔index (what's *staged*) and index↔working tree (what's *unstaged*) |
| `git diff` | Shows **index ↔ working tree** — unstaged changes |
| `git diff --staged` | Shows **HEAD ↔ index** — what a commit *right now* would record |
| `git diff HEAD` | Shows **HEAD ↔ working tree** — everything, staged or not |
| `git add <f>` | Copies working-tree content of `<f>` **into the index** (writes a blob) |
| `git commit` | Turns the **index** into a new commit; moves HEAD to it |
| `git restore --staged <f>` | Copies HEAD's version of `<f>` back **into the index** (unstages) |
| `git restore <f>` | Copies the index version of `<f>` back **into the working tree** (discards edits) |

### Reading `git status`

`git status` is really two reports stacked together:

```text
Changes to be committed:       ← HEAD  vs  INDEX   (staged)
        modified:   main.py

Changes not staged for commit: ← INDEX vs  WORKING  (unstaged)
        modified:   README.md

Untracked files:               ← in WORKING, in neither INDEX nor HEAD
        notes.txt
```

Once you know each section names a *pair of trees*, a confusing report becomes obvious. A file can even appear in **two** sections at once — staged with one change, then edited again — because its index version differs from HEAD *and* its working version differs from the index.

## 4. A full walkthrough

Let's move one file through all three trees and watch each command. Start clean:

```bash
mkdir /tmp/three-trees && cd /tmp/three-trees
git init -q
printf 'line one\n' > notes.txt
git add notes.txt
git commit -q -m "add notes"
```

Right now all three trees agree: HEAD, index, and working tree all hold `line one`. `git status` says "nothing to commit, working tree clean."

**Step 1 — edit the working tree only.**

```bash
printf 'line one\nline two\n' > notes.txt
git status -s        # ' M notes.txt'  — space then M = modified, unstaged
git diff             # shows +line two   (index ↔ working)
git diff --staged    # shows NOTHING     (HEAD ↔ index; index unchanged)
```

The working tree moved ahead; the index and HEAD have not. `git diff` sees the gap; `git diff --staged` sees none.

**Step 2 — stage it.**

```bash
git add notes.txt
git status -s        # 'M  notes.txt'  — M then space = modified, staged
git diff             # shows NOTHING     (index now matches working)
git diff --staged    # shows +line two   (HEAD ↔ index)
```

Notice the two-column status: the **left** column is the HEAD↔index (staged) state, the **right** column is the index↔working (unstaged) state. Content moved from working tree into the index; the two diffs swapped which one is empty.

**Step 3 — edit again *before* committing.**

```bash
printf 'line one\nline two\nline three\n' > notes.txt
git status -s        # 'MM notes.txt'  — staged change AND a new unstaged change
git diff             # shows +line three (index ↔ working)
git diff --staged    # shows +line two   (HEAD ↔ index)
```

Both columns are `M`. The index holds the two-line version (staged), the working tree holds the three-line version (not yet staged). This `MM` state is the clearest proof the index is its own tree.

**Step 4 — commit what's staged.**

```bash
git commit -q -m "add line two"
git status -s        # ' M notes.txt'  — line three is still unstaged
```

`commit` snapshotted the **index** (two lines) into a new commit and advanced HEAD. `line three` was never staged, so it's still sitting in the working tree, unstaged. The commit contains *exactly* what the index held — no more, no less.

## 5. Undoing, in the same language

Because these are just tree-to-tree moves, "undo" is just moving content the other direction. You'll go deep on `reset` and `restore` in later weeks, but here is the model now:

| You want to… | Command | Move |
|--------------|---------|------|
| Unstage a file (keep the edit) | `git restore --staged <f>` | HEAD → index |
| Discard unstaged edits to a file | `git restore <f>` | index → working tree |
| Discard *both* staged and unstaged | `git restore --staged --worktree <f>` | HEAD → index → working |
| Move the branch/HEAD, leave files | `git reset --soft <commit>` | moves HEAD only |
| Move HEAD + reset index | `git reset --mixed <commit>` (default) | HEAD → index |
| Move HEAD + index + working tree | `git reset --hard <commit>` | HEAD → index → working (**destroys** edits) |

`--hard` is the only one here that can lose uncommitted work, because it overwrites the working tree. Every other row leaves your on-disk files alone. Knowing *which trees a command touches* tells you instantly whether it is safe.

## 6. Where HEAD actually points

`HEAD` is usually not a commit directly — it's a pointer to a *branch*, and the branch points to a commit. This indirection is why committing "just works": Git reads HEAD to find the current branch, then moves that branch to the new commit.

```bash
cat .git/HEAD
# ref: refs/heads/main
git rev-parse HEAD        # the commit SHA HEAD resolves to
git rev-parse main        # ...the same SHA, via the branch
```

When both resolve to the same SHA, you're on `main` and `main` is at that commit. In Week 2 you'll detach HEAD from a branch and see the difference. For now: **HEAD = "the commit my next commit will build on."**

## 7. Check yourself

- Name the three trees and where each physically lives.
- When you run `git add`, what gets written to the object database, and when?
- `git diff` vs `git diff --staged` vs `git diff HEAD` — which pair of trees does each compare?
- You edit a file, `git add` it, then edit it again. What does `git status -s` show, and why two columns?
- After `git commit`, exactly what did the commit capture — the working tree or the index?
- Which single `reset` mode can destroy uncommitted work, and why?
- What does `HEAD` normally point at — a commit, or a branch?

Answer all seven from memory before moving to Lecture 3.

## Further reading

- **Pro Git — "Reset Demystified"** (the definitive three-trees explainer): <https://git-scm.com/book/en/v2/Git-Tools-Reset-Demystified>
- **Pro Git — "Recording Changes to the Repository":** <https://git-scm.com/book/en/v2/Git-Basics-Recording-Changes-to-the-Repository>
- **`git help glossary`** — read the entries for *index*, *working tree*, and *HEAD*.
