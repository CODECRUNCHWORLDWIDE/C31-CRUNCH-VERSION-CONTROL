# Exercise 2 — Selective staging and reading diffs

**Goal:** Use `git add -p` to stage *part* of a file, and read the three kinds of diff (`diff`, `diff --staged`, `diff HEAD`) until the index stops being mysterious. By the end you can build a clean commit out of a messy working tree.

**Estimated time:** 45 minutes. **Do it after:** Lecture 2.

## Setup

```bash
mkdir -p ~/c31-w1/exercise-02
cd ~/c31-w1/exercise-02
git init -q

# a small file with several distinct lines
cat > config.py <<'EOF'
DEBUG = False
DATABASE = "sqlite:///app.db"
TIMEOUT = 30
LOG_LEVEL = "INFO"
EOF
git add config.py
git commit -q -m "chore: initial config"
```

## Part A — Make three unrelated changes at once

Simulate real life: you fixed three things in one editing session and now want them in *separate* commits.

```bash
cat > config.py <<'EOF'
DEBUG = True
DATABASE = "postgres://localhost/app"
TIMEOUT = 60
LOG_LEVEL = "DEBUG"
EOF
```

Look at what you did:

```bash
git diff             # all four lines changed (index ↔ working tree)
git diff --staged    # nothing yet (HEAD ↔ index; index untouched)
```

## Part B — Stage hunks selectively with `git add -p`

`git add -p` walks you through each change and asks whether to stage it. Because our lines are adjacent, first split them:

```bash
git add -p config.py
```

At each prompt you'll see options. The ones you need:

| Key | Meaning |
|-----|---------|
| `y` | stage this hunk |
| `n` | do **not** stage this hunk |
| `s` | split the hunk into smaller ones |
| `e` | edit the hunk manually |
| `q` | quit |
| `?` | help |

Press `s` to split. Then stage **only the `DEBUG` and `LOG_LEVEL` changes** with `y`, and answer `n` to the `DATABASE` and `TIMEOUT` changes. (If `s` can't split adjacent lines finely enough, use `e` and delete the `+`/context lines you don't want to stage — Git shows instructions in the editor.)

Now read all three diffs and understand the split:

```bash
git diff --staged    # ONLY the DEBUG + LOG_LEVEL changes  (HEAD ↔ index)
git diff             # ONLY the DATABASE + TIMEOUT changes  (index ↔ working)
git diff HEAD        # ALL of them together (HEAD ↔ working)
git status -s        # 'MM config.py' — staged AND unstaged changes coexist
```

Commit the staged slice:

```bash
git commit -m "chore: enable debug logging"
```

Then stage and commit the rest:

```bash
git add config.py
git commit -m "chore: point at postgres and raise timeout"
git log --oneline    # three commits, cleanly separated
```

## Expected result

- Two new commits, each containing only its own logical change — even though you edited everything in one pass.
- You watched `MM` in `git status -s` and can explain both columns.

## Done when…

- [ ] `git log --oneline` shows three commits with the messages above.
- [ ] `git show HEAD~1` (the debug commit) contains **only** the `DEBUG`/`LOG_LEVEL` lines, not the database change.
- [ ] You can state which two trees each of `git diff`, `git diff --staged`, and `git diff HEAD` compares.
- [ ] You reached and understood the `MM` state.

## Stretch

- Redo Part B but use `e` (edit hunk) instead of `s` on at least one hunk. Read the in-editor instructions about `-` vs `+` vs context lines.
- Add a brand-new file and try `git add -p newfile` — what happens with an entirely new file, and why? (Hint: it's all one hunk.)
- Configure `git config --global diff.colorMoved zebra` and re-run a diff where a line moved. Note what changes visually.

## Submission

Save your final `git log --oneline` and one `git show` of the debug commit into your portfolio under `c31-week-01/exercise-02/notes.md`.
