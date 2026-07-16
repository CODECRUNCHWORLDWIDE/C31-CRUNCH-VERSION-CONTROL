# Lecture 1 — Branches as movable pointers, and where `HEAD` points

> **Duration:** ~2 hours. **Outcome:** You can state, in one sentence, what a branch *is* at the byte level; you can predict what `git switch`, `git branch`, and `HEAD` do to the commit graph before running them; and you can create, rename, and delete branches without guessing.

## 1. The one idea that makes branching easy

Most people carry a wrong mental model from other tools: they imagine a branch is a **copy** of the project — a separate folder, a duplicated set of files, an expensive thing you create carefully and delete reluctantly.

In Git, a branch is none of that. A branch is a **movable pointer to a single commit**. That's it. On disk, `main` is literally a small text file whose entire contents are one 40-character commit hash plus a newline:

```bash
cat .git/refs/heads/main
# 9f4c2b1e8a3d5c6f0b2a1d9e8c7f6a5b4c3d2e1f
```

Forty hex characters. Forty-one bytes with the newline. Creating a branch means writing one more such file. That is why branching in Git is instant and free, no matter how large your project is — you are copying a hash, not a codebase.

Because a commit records its **parent**, and each parent records *its* parent, a single commit hash is enough to reconstruct the entire history behind it. A branch pointer at the tip gives you the whole line for free.

> **The sentence to memorize:** *A branch is a movable pointer to a commit; a commit knows its parent; so a pointer to the tip is a pointer to the whole history.*

## 2. `HEAD`: the pointer to your current branch

If a branch points to a commit, what points to your current branch? `HEAD`.

`HEAD` is another tiny file, `.git/HEAD`. Normally it doesn't contain a commit hash — it contains a **symbolic reference** to a branch:

```bash
cat .git/HEAD
# ref: refs/heads/main
```

Read that as: "I am currently on the branch `main`." So the chain is:

```
HEAD  ──▶  refs/heads/main  ──▶  commit 9f4c2b1…  ──▶  parent  ──▶  parent  ──▶ …
```

Two levels of indirection. This is the whole trick, and it explains a lot of behavior:

- When you **commit**, Git creates a new commit whose parent is wherever `HEAD` currently resolves to, then advances the **branch** that `HEAD` points at to the new commit. `HEAD` still says `ref: refs/heads/main`; `main` now points one commit further along.
- When you **switch branches**, Git rewrites `.git/HEAD` to point at the new branch, then updates your working tree to match that branch's commit.

`git branch` moves a branch pointer. `git commit` moves it too. `HEAD` just follows along, naming whichever branch is "current."

### Detached HEAD

Sometimes `HEAD` contains a raw commit hash instead of `ref: refs/heads/...`. This is **detached HEAD** — you're sitting on a commit directly, not on any branch.

```bash
git switch --detach 9f4c2b1     # deliberately detach onto a commit
cat .git/HEAD
# 9f4c2b1e8a3d5c6f0b2a1d9e8c7f6a5b4c3d2e1f
```

It's not an error. It's useful for "let me look at an old commit" or "let me experiment without a branch." The catch: commits you make in detached HEAD aren't on any branch, so when you switch away, nothing points to them and they become candidates for garbage collection. Git warns you loudly. To keep such work, make a branch: `git switch -c my-experiment`.

## 3. Seeing the pointers

Git's diagrams live in your terminal. The single most useful log format this week:

```bash
git log --oneline --graph --all --decorate
```

Decoded, flag by flag:

| Flag | Effect |
|------|--------|
| `--oneline` | One line per commit: short hash + subject |
| `--graph` | Draw the commit graph with ASCII lines |
| `--all` | Show **every** branch, not just the current one |
| `--decorate` | Label commits with the branch/tag/`HEAD` pointers at them |

Alias it now so you type it a hundred times this week without pain:

```bash
git config --global alias.lg "log --oneline --graph --all --decorate"
# now just: git lg
```

A typical `git lg` right after creating a branch:

```
* 9f4c2b1 (HEAD -> main, feature-login) Add README
* 3a1b8c2 Initial commit
```

Read the decoration carefully: `HEAD -> main` means `HEAD` points at `main`, and both `main` **and** `feature-login` point at commit `9f4c2b1`. Two branches, same commit, zero duplicated files. That's what "branching is cheap" looks like.

## 4. Creating branches

There are two commands that make a branch. Know both, because you'll read both in other people's docs.

```bash
git branch feature-login          # create 'feature-login' at HEAD, but DO NOT switch to it
git switch feature-login          # now move onto it (HEAD -> feature-login)
```

Or do both in one step — the common case:

```bash
git switch -c feature-login       # create AND switch (modern; -c = create)
git checkout -b feature-login     # create AND switch (legacy; -b = branch)
```

You can also branch from a specific starting point rather than `HEAD`:

```bash
git switch -c hotfix main         # start the new branch at 'main'
git branch experiment 3a1b8c2     # start at a specific commit
git switch -c release-2 v1.9.0    # start at a tag
```

Nothing is copied. Each of these writes one 41-byte ref file and (for `switch`) updates `HEAD`.

## 5. `switch` vs. `checkout`: why two commands?

`git checkout` is the old Swiss-army knife. It does *too many* unrelated things:

- `git checkout main` — switch branches.
- `git checkout -b new` — create and switch.
- `git checkout -- file.txt` — throw away changes to a file (destructive!).
- `git checkout 3a1b8c2` — detach HEAD onto a commit.

Overloading one verb for "change branch" and "permanently discard my edits" caused real accidents. In Git 2.23 (2019) the maintainers split it into two focused verbs:

| Intent | Modern command | Legacy `checkout` |
|--------|----------------|-------------------|
| Change which branch you're on | `git switch <branch>` | `git checkout <branch>` |
| Create and switch | `git switch -c <branch>` | `git checkout -b <branch>` |
| Detach onto a commit | `git switch --detach <hash>` | `git checkout <hash>` |
| Discard changes to a file | `git restore <file>` | `git checkout -- <file>` |
| Restore a file from another commit | `git restore --source=<commit> <file>` | `git checkout <commit> -- <file>` |

**Use `switch` and `restore` this course.** They're safer (harder to nuke your work by accident) and their names say what they do. `checkout` still works and isn't going away — you just won't reach for it first.

## 6. Renaming and deleting branches

```bash
git branch -m old-name new-name   # rename (move) a branch
git branch -m new-name            # rename the CURRENT branch
```

Deleting comes in two flavors, and the difference is a safety feature:

```bash
git branch -d feature-login       # delete — but REFUSE if it holds unmerged commits
git branch -D feature-login       # force delete — even if unmerged (work may be lost)
```

`-d` (safe) is Git protecting you: it only lets you delete a branch whose commits are already reachable from somewhere else (e.g. already merged into `main`). If Git says *"error: the branch 'feature-login' is not fully merged"*, it's telling you those commits exist **only** on that branch — deleting loses them. Stop and check with `git lg` before reaching for `-D`.

Remember: deleting a branch only removes the *pointer*. The commits themselves linger in the object database (recoverable via `git reflog`, which you'll meet in Week 4) until garbage collection. But don't rely on that — treat `-D` as "I have decided I don't want this work."

## 7. What actually happens when you switch

`git switch other-branch` does three things, in order:

1. Checks your working tree and index for changes that would be **overwritten** by the switch. If switching would clobber uncommitted work, Git **refuses** and tells you to commit or stash first. (This is a feature — it will not silently destroy edits.)
2. Rewrites `.git/HEAD` to `ref: refs/heads/other-branch`.
3. Updates the working tree and index to match `other-branch`'s commit — replacing, adding, and removing files as needed.

That step 1 refusal is why beginners sometimes feel "stuck." The fix is almost always: commit your work, or `git stash` it (a Week-4 tool that shelves changes temporarily), then switch.

```bash
git switch main
# error: Your local changes to the following files would be overwritten by checkout:
#   app.py
# Please commit your changes or stash them before you switch branches.

git stash          # shelve the edits
git switch main    # now it works
git switch -        # go back ('-' means "previous branch")
git stash pop      # bring the edits back
```

Note `git switch -`: like `cd -` in the shell, it jumps to the branch you were just on. Small, but you'll use it constantly.

## 8. A full worked session

Type this in a scratch repo and watch the pointers move with `git lg` after each step:

```bash
mkdir ~/branch-demo && cd ~/branch-demo
git init
echo "# My App" > README.md
git add README.md && git commit -m "Initial commit"

git switch -c feature-login        # branch off main
echo "def login(): pass" > auth.py
git add auth.py && git commit -m "Add login stub"

git lg
# * a1b2c3d (HEAD -> feature-login) Add login stub
# * 9f4c2b1 (main) Initial commit
```

Notice: `main` stayed put at the first commit. Only `feature-login` (which `HEAD` follows) advanced. Now hop back:

```bash
git switch main
ls                                 # auth.py is GONE — it only exists on feature-login
git lg                             # main still points at the first commit
```

`auth.py` didn't get deleted; it simply isn't part of `main`'s snapshot. Switch back to `feature-login` and it reappears. Files come and go because switching rewrites your working tree to match whichever commit the branch points at.

## 9. Check yourself

- On disk, what *is* the branch `main`? How many bytes, roughly, and what's in it?
- What does `.git/HEAD` normally contain, and what does it contain in detached-HEAD state?
- When you run `git commit`, which pointer moves — `HEAD`, or the branch it names? What about both?
- Why does `git branch -d` sometimes refuse, and what is it protecting you from?
- What's the modern replacement for `git checkout -- file.txt`, and why did the split happen?
- After `git switch -c feature main`, how many files were copied on disk?

If you can answer all six without peeking, move to Lecture 2.

## Further reading

- **Pro Git — "Git Branching: Branches in a Nutshell":** <https://git-scm.com/book/en/v2/Git-Branching-Branches-in-a-Nutshell>
- **`git switch` documentation:** <https://git-scm.com/docs/git-switch>
- **Git 2.23 release notes (`switch`/`restore` introduced):** <https://github.blog/2019-08-16-highlights-from-git-2-23/>
