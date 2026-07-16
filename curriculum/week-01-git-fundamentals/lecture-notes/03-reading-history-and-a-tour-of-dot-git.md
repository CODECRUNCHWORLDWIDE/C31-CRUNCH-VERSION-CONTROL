# Lecture 3 — Reading history and a guided tour of `.git/`

> **Duration:** ~2 hours. **Outcome:** You can read history with `log`, `show`, and `diff`, name every important entry inside `.git/`, and walk a commit down to its blobs using plumbing (`cat-file`, `hash-object`, `ls-tree`, `rev-parse`).

## 1. Porcelain and plumbing

Git commands come in two layers. **Porcelain** commands are the friendly ones you use daily: `log`, `status`, `commit`, `diff`. **Plumbing** commands are the low-level tools those are built from: `cat-file`, `hash-object`, `ls-tree`, `rev-parse`, `update-ref`. Porcelain output can change between versions for readability; plumbing output is stable and script-safe.

This lecture uses both — porcelain to *read* history, plumbing to *see the machine* underneath. By the end you'll trust that "the magic" is just files in a folder.

## 2. Reading history with `git log`

`git log` walks the commit graph backward from HEAD, following parent links. Raw `git log` is verbose; the flags are where the power is.

| Command | Shows |
|---------|-------|
| `git log` | Full entries: SHA, author, date, message |
| `git log --oneline` | One line per commit: short SHA + summary |
| `git log --oneline --graph --all` | ASCII graph of *all* branches — the mental picture |
| `git log --stat` | Each commit plus which files changed and by how much |
| `git log -p` | Each commit plus its full diff |
| `git log -3` | Only the last 3 commits |
| `git log --author="Ada"` | Filter by author |
| `git log -- path/to/file` | Only commits that touched that file |
| `git log HEAD~5..HEAD` | A *range*: the last five commits |

```bash
git log --oneline --graph --all
# * 9c1f0a2 (HEAD -> main) add line two
# * 4b2e881 add notes
```

`--oneline --graph --all` is the command to burn into muscle memory. It is how you orient yourself in *any* repository in seconds.

### Commit-ish: ways to name a commit

You rarely type full SHAs. Git accepts many **revision specifiers** (`git help revisions`):

| Spec | Means |
|------|-------|
| `HEAD` | The current commit |
| `HEAD~` or `HEAD~1` | HEAD's (first) parent |
| `HEAD~3` | Three first-parents back |
| `HEAD^` | Same as `HEAD~1` |
| `HEAD^2` | The **second** parent (only meaningful on a merge) |
| `main` | The commit branch `main` points at |
| `9c1f0a2` | An (abbreviated) SHA |
| `@` | Shorthand for `HEAD` |

`~` walks straight back through first-parents; `^N` chooses *which* parent at a merge. You'll lean on both from Week 2 onward.

## 3. Inspecting a single commit with `git show`

`git log` surveys; `git show` zooms. `git show <commit>` prints the commit metadata **and** the diff it introduced (by comparing its tree to its parent's tree — remember, Git computes the diff, it isn't stored):

```bash
git show HEAD              # last commit + its diff
git show HEAD~1            # the one before
git show HEAD:notes.txt    # the *contents* of notes.txt as of HEAD
```

That last form — `<commit>:<path>` — is how you read any file at any point in history without checking anything out. Want `main.py` as it was five commits ago? `git show HEAD~5:main.py`.

## 4. `git diff`, one more time, across history

Lecture 2 covered `diff` between the three trees. `diff` also compares any two commits or trees:

```bash
git diff HEAD~2 HEAD          # what changed over the last two commits
git diff main feature         # difference between two branches' tips
git diff 4b2e881 9c1f0a2      # between any two commits
git diff --stat HEAD~5        # summary of changes since 5 commits ago
```

Every one of these is Git comparing two tree objects and rendering the difference. There is no stored diff anywhere — it's recomputed each time, fast, because trees are cheap to walk.

## 5. Refs: how names point at commits

A **ref** (reference) is a human-friendly name that points at a commit SHA. That's it. `main`, `HEAD`, tags, remote-tracking branches — all refs. They live under `.git/refs/` and in one packed file.

```bash
cat .git/refs/heads/main     # 40 hex chars — the commit main points at
git rev-parse main           # same thing, the proper way to ask
git symbolic-ref HEAD        # refs/heads/main — what HEAD points at
```

A **branch is a ref that moves.** When you commit on `main`, Git writes the new commit and overwrites `.git/refs/heads/main` with the new SHA. That's the whole mechanism. Creating a branch just writes a new 41-byte file (40 hex + newline) — which is why branches are effectively free.

You can move a ref by hand with plumbing (carefully):

```bash
git update-ref refs/heads/experiment HEAD   # make 'experiment' point at HEAD
```

`HEAD` itself is a *symbolic* ref: instead of a SHA it usually holds `ref: refs/heads/main`. That indirection is what "being on a branch" means.

## 6. A guided tour of `.git/`

Run `ls -a1 .git` in any repo. Here's what earns its place:

```text
.git/
├── HEAD           → symbolic ref, usually "ref: refs/heads/main"
├── config         → this repo's config (remotes, user overrides)
├── description    → used only by GitWeb; ignore it
├── index          → the staging area (binary; read with ls-files --stage)
├── hooks/         → sample scripts that fire on events (Week 7)
├── info/          → repo-level exclude patterns and extras
├── logs/          → the reflog: a history of where refs have been
├── objects/       → THE object database — every blob, tree, commit, tag
│   ├── 4b/2e88…   → a loose object, filed by the first 2 hex of its SHA
│   ├── info/
│   └── pack/      → many objects compressed together (git gc)
└── refs/
    ├── heads/     → your local branches (one file per branch)
    └── tags/      → your tags
```

| Entry | What it's for |
|-------|---------------|
| `HEAD` | Which branch/commit you're on |
| `config` | Repo-local settings; overrides global config |
| `index` | The staging area — your draft next commit |
| `objects/` | The content-addressed database from Lecture 1 |
| `refs/heads/` | Branches — each a file holding one SHA |
| `refs/tags/` | Tags |
| `logs/` | The **reflog** — where every ref *used to* point |
| `hooks/` | Event-triggered scripts (covered in Week 7) |

### How objects are filed on disk

A loose object with SHA `4b2e881f…` is stored at `.git/objects/4b/2e881f…`. Git splits off the first two hex characters as a directory name so no single folder holds millions of files. The file itself is zlib-compressed and prefixed with a header (`blob 6\0hello\n`), which is why you can't just `cat` it — you use `git cat-file`.

## 7. Plumbing: walk a commit to its blobs

This is the payoff. Let's descend from a commit all the way to file contents using only plumbing. Make a tiny repo first:

```bash
mkdir /tmp/tour && cd /tmp/tour && git init -q
mkdir src
echo 'print("hi")' > src/app.py
echo '# Tour' > README.md
git add . && git commit -q -m "initial"
```

**Step 1 — what is HEAD?**

```bash
git rev-parse HEAD            # → a commit SHA, e.g. 7f3a…
git cat-file -t HEAD          # → commit
git cat-file -p HEAD          # print the commit object:
# tree 2c8b…
# author You <you@example.com> 1700000000 +0000
# committer You <you@example.com> 1700000000 +0000
#
# initial
```

The commit's first line names its **root tree**. Follow it.

**Step 2 — read the root tree.**

```bash
git cat-file -p HEAD^{tree}   # ^{tree} = "the tree of this commit"
# 100644 blob 9d3e…    README.md
# 040000 tree 5a1c…    src
```

`README.md` is a blob; `src` is another tree. Descend again.

**Step 3 — read the subtree, then the blob.**

```bash
git ls-tree HEAD src          # list the 'src' tree
# 100644 blob 0f2b…    app.py

git cat-file -p 0f2b          # the actual file contents
# print("hi")
```

You just traversed `commit → tree → tree → blob` by hand. That path — and the fact that it always terminates in blobs — *is* Git's storage model. Everything else is names and history layered on top.

### The plumbing cheat-sheet

| Command | Answers |
|---------|---------|
| `git rev-parse <ref>` | What SHA does this name resolve to? |
| `git cat-file -t <sha>` | What *type* is this object? |
| `git cat-file -s <sha>` | What *size*? |
| `git cat-file -p <sha>` | Pretty-print the object's contents |
| `git hash-object [-w] <file>` | Compute (and optionally store) a blob's SHA |
| `git ls-tree <tree-ish>` | List a tree's entries |
| `git ls-files --stage` | List the index's entries |
| `git write-tree` | Build a tree object from the index |
| `git commit-tree <tree>` | Build a commit object from a tree |

## 8. The reflog: your safety net

Every time a ref moves — a commit, a reset, a checkout, a rebase — Git records the *old* position in the **reflog** (`.git/logs/`). This is local-only and per-repo, and it is the reason "I lost my work" is almost always false.

```bash
git reflog
# 9c1f0a2 HEAD@{0}: commit: add line two
# 4b2e881 HEAD@{1}: commit (initial): add notes
```

`HEAD@{2}` means "where HEAD was two moves ago." If you `git reset --hard` and panic, `git reflog` shows the SHA you left, and you can get back with `git reset --hard HEAD@{1}` or `git checkout <sha>`. Because a committed object stays in the database even when no branch points at it (Lecture 1), it is still there to recover — until Git eventually garbage-collects truly unreferenced objects, which by default won't happen for weeks. You'll exploit exactly this in Challenge 2.

## 9. Check yourself

- What's the difference between porcelain and plumbing? Give one command from each.
- Which `git log` flags give you the whole-repo picture at a glance?
- What does `git show HEAD~3:file.txt` print?
- A branch is fundamentally what kind of file, holding what?
- Walk the chain of object types from a commit down to a file's bytes.
- What does `git cat-file -p HEAD^{tree}` show, and how is `^{tree}` different from `~`?
- Where does the reflog live, and why does it make "lost" commits usually recoverable?

If you can trace `commit → tree → blob` from memory, you're ready for the exercises.

## Further reading

- **Pro Git — "Git References"** and **"The Refspec":** <https://git-scm.com/book/en/v2/Git-Internals-Git-References>
- **Pro Git — "Viewing the Commit History":** <https://git-scm.com/book/en/v2/Git-Basics-Viewing-the-Commit-History>
- **`git help cat-file`, `git help rev-parse`, `git help ls-tree`** — the plumbing manuals; short and worth reading.
