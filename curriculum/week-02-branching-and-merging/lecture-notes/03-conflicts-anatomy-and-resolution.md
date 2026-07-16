# Lecture 3 — Conflicts: anatomy, resolution, and the panic button

> **Duration:** ~2 hours. **Outcome:** You can read conflict markers fluently, resolve a conflict by hand or with a tool, tell "ours" from "theirs" correctly, and abort a merge that's going nowhere — all without deleting the repo and starting over.

## 1. A conflict is not an error. It's a question.

The single most important reframe this week: **a merge conflict is not Git failing.** It's Git succeeding at the honest part of its job and then *refusing to guess* about the ambiguous part.

Recall the three-way merge from Lecture 2. For each hunk, Git compares "ours" and "theirs" against the merge base. When **both** sides changed the **same lines** in **different** ways, Git has no principled way to know which you meant — so instead of silently picking one and possibly destroying work, it stops, marks the spot, and hands you the decision. That's the whole mechanism.

Conflicts happen only in that specific overlap. If you edited `auth.py` and your teammate edited `db.py`, they merge automatically — different files, no overlap. Conflicts require *the same region of the same file, changed differently, on both branches.*

## 2. Anatomy of a conflict marker

When a merge conflicts, Git writes **both** versions into the file, wrapped in markers, and leaves the file in your working tree for you to fix. It looks like this:

```
<<<<<<< HEAD
timeout = 30
=======
timeout = 60
>>>>>>> feature-login
```

Decode it precisely — every symbol means something:

| Marker | Meaning |
|--------|---------|
| `<<<<<<< HEAD` | Start of **our** version — the branch you're on (the merge destination) |
| (lines above `=======`) | What **our** side has |
| `=======` | The divider between the two versions |
| (lines below `=======`) | What **their** side has |
| `>>>>>>> feature-login` | End of **their** version, labeled with the source branch |

So: **top is "ours" (`HEAD`), bottom is "theirs" (the branch you named in `git merge`).** Getting this backwards is the classic beginner mistake — it silently reintroduces the exact bug you were trying to remove.

The seven-character markers (`<<<<<<<`, `=======`, `>>>>>>>`) are chosen because they essentially never appear in real code, so you can search for them reliably:

```bash
grep -rn '^<<<<<<<\|^=======\|^>>>>>>>' .
```

**Resolving means editing the file so those markers are gone** and what remains is the correct final content. Git does not care how you decide — it only checks that you removed the markers and staged the result.

## 3. The resolution workflow, step by step

The moment a merge conflicts, `git status` becomes your dashboard:

```bash
git merge feature-login
# Auto-merging config.py
# CONFLICT (content): Merge conflict in config.py
# Automatic merge failed; fix conflicts and then commit the result.

git status
# You have unmerged paths.
#   (fix conflicts and run "git commit")
#   (use "git merge --abort" to abort the merge)
#
# Unmerged paths:
#   (use "git add <file>..." to mark resolution)
#         both modified:   config.py
```

The full loop:

1. **See what conflicts:** `git status` lists every file under "Unmerged paths." Note that some files may have auto-merged cleanly — you only touch the conflicted ones.
2. **Open each conflicted file** and find the `<<<<<<<` blocks.
3. **Decide the final content.** Keep ours, keep theirs, keep both, or write something new that combines them. Delete all three marker lines.
4. **Stage the resolved file:** `git add config.py`. Staging is how you tell Git "this one's handled."
5. **Repeat** for every conflicted file. `git status` shrinks as you go.
6. **Commit:** `git commit` (or `git merge --continue`). Git pre-fills a merge message. The conflict is now recorded as resolved inside the merge commit.

```bash
# after editing config.py to remove markers:
git add config.py
git status                 # confirm no more unmerged paths
git commit                 # completes the merge
```

That's it. There is no separate "resolve" command — resolution *is* editing the file and staging it. `git add` on a conflicted file is you asserting "I fixed this."

## 4. "Ours," "theirs," and taking one side wholesale

Sometimes you don't want to hand-edit — you know one side is simply correct. Git gives you shortcuts:

```bash
git checkout --ours config.py     # keep OUR version entirely (HEAD / current branch)
git checkout --theirs config.py   # keep THEIR version entirely (the merged-in branch)
git add config.py                 # then stage, as usual
```

Or with the modern verb:

```bash
git restore --ours config.py
git restore --theirs config.py
```

Two warnings that catch everyone once:

- **"ours"/"theirs" is per-file, all-or-nothing** here. `--ours` takes *your entire version* of that file and discards their changes to it — even the non-conflicting parts. If both sides made good changes to the same file, hand-merge instead of taking a side.
- **During a `rebase`, "ours" and "theirs" are swapped** relative to a merge, because rebase replays *your* commits on top of the other branch, so "ours" becomes the branch you're rebasing onto. Don't memorize it blindly; read `git status`, which tells you what's what. (Rebase is Week 4 — flagged here so it doesn't surprise you.)

## 5. Better tools than raw markers

Editing markers by hand is fine for a two-line conflict. For anything bigger, use a **three-way merge tool** that shows you *base*, *ours*, and *theirs* side by side.

### Your editor probably already does this

VS Code detects conflicts and shows inline **"Accept Current Change / Accept Incoming Change / Accept Both / Compare Changes"** buttons over each block, plus a three-way merge editor. Most modern editors (JetBrains IDEs, Neovim with plugins) do the same. For most people this is the best option — the base pane shows what you started from, which is exactly the context raw markers hide.

### `git mergetool`

Git can launch a dedicated diff tool:

```bash
git mergetool                         # opens your configured tool on each conflict
git config --global merge.tool vimdiff   # or: meld, kdiff3, vscode, etc.
```

Free, cross-platform choices:

| Tool | Notes |
|------|-------|
| **VS Code** (`code`) | Built-in three-way editor; set `git config --global merge.tool vscode` |
| **Meld** | Friendly GUI three-pane diff; great for beginners; Linux/Win/mac |
| **vimdiff** | Ships with Vim; no install; steep but always available |
| **KDiff3** | Powerful three-way merge, good for big conflicts |

By default `git mergetool` leaves `.orig` backup files behind. Turn that off once and forget it:

```bash
git config --global mergetool.keepBackup false
```

### See more context with diff3

The two-way marker (ours vs. theirs) hides the *base* — what both sides started from. Turn on the three-way style to see it:

```bash
git config --global merge.conflictStyle zdiff3
```

Now conflicts include a third section showing the common ancestor:

```
<<<<<<< HEAD
timeout = 30
||||||| base
timeout = 45
=======
timeout = 60
>>>>>>> feature-login
```

The `||||||| base` block tells you the original value was `45` — so you can see that *both* sides changed it, and reason about which change is right. This is a genuine upgrade; many experienced users leave `zdiff3` on permanently.

## 6. The panic button: `--abort`

If a conflict is bigger or messier than you expected, you are **never trapped**. You can throw the whole merge away and return to exactly where you started:

```bash
git merge --abort
```

This restores your working tree, index, and `HEAD` to the pre-merge state — as if you never ran `git merge`. Nothing is lost, because merging didn't move your branch yet (the merge commit is only created at the very end, after you resolve). Use it whenever you want to step back, understand the branches better, and retry.

Related escape hatches:

```bash
git merge --abort            # cancel a merge, restore pre-merge state
git restore --staged <file>  # unstage a file you added too eagerly
git checkout -m <file>       # re-create the conflict markers in a file (redo the conflict)
```

There is no scenario in a normal merge where "delete the folder and re-clone" is the right move. `--abort` exists precisely so you never do that.

## 7. Avoiding conflicts in the first place

You can't eliminate conflicts, but you can make them rare and small:

- **Merge often.** The longer a branch lives without integrating, the more the two sides drift and the worse the eventual conflict. Small, frequent merges beat one giant reconciliation.
- **Keep branches focused.** A branch that touches ten files across the codebase conflicts with everything. A branch that touches one feature conflicts rarely.
- **Communicate on shared files.** If two people must edit the same config or the same core module, coordinate — don't discover it at merge time.
- **Pull before you push** (Week 3): integrate others' work into yours frequently, in small doses.

Git even has a feature that *remembers* how you resolved a given conflict so it can replay your resolution if the same conflict reappears:

```bash
git config --global rerere.enabled true    # "reuse recorded resolution"
```

`rerere` is a quiet productivity win on long-lived branches. Turn it on; you'll thank yourself.

## 8. Check yourself

- In a conflict block, which side is "ours" and which is "theirs" — top or bottom? Which branch is each?
- What does resolving a conflict physically consist of? Is there a `git resolve` command?
- How do you keep *your* entire version of a conflicted file in one command?
- What does `zdiff3` conflict style add that the default doesn't, and why is that useful?
- You've made a mess halfway through a conflict. What's the one command that safely undoes the whole merge?
- Name two habits that make conflicts smaller and rarer.

## Further reading

- **Pro Git — "Basic Merge Conflicts":** <https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging#_basic_merge_conflicts>
- **`git mergetool` documentation:** <https://git-scm.com/docs/git-mergetool>
- **`git rerere` documentation:** <https://git-scm.com/docs/git-rerere>
- **GitHub Docs — "Resolving a merge conflict using the command line":** <https://docs.github.com/en/get-started/using-git/resolving-a-merge-conflict-using-the-command-line>
