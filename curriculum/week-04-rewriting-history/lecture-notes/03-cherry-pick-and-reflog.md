# Lecture 3 — Cherry-pick and Total Recovery with Reflog

> **Duration:** ~2 hours. **Outcome:** You can replay any commit onto your current branch with `cherry-pick` (and resolve the conflicts that follow), and you can recover almost anything — a deleted commit, a lost branch, a botched `reset --hard` — using `git reflog`. After this lecture, "I lost my work" stops being a sentence you say.

The first two lectures gave you tools to *change* history. This one gives you the two tools that make changing history *safe*: `cherry-pick`, which copies a single commit anywhere you need it, and `reflog`, the hidden log of everywhere HEAD has ever been — your undo button for operations that looked irreversible.

## 1. `cherry-pick` — copy one commit onto here

`git cherry-pick <sha>` takes the changes introduced by one commit and applies them as a **new commit on your current branch.** The original commit stays where it is; you get a copy (new SHA, same diff and message) on top of wherever HEAD currently points.

```bash
git switch main
git log --oneline feature      # find the commit you want, e.g. a1b2c3d
git cherry-pick a1b2c3d        # apply that one commit's changes onto main
```

The canonical use case: a **hotfix** was committed on a feature branch, but production (`main`) needs it *now*, and you don't want to merge the whole unfinished feature. Cherry-pick just that commit.

```
feature:  A---B---C---D          (D = the urgent fix)
main:     M---N

git switch main && git cherry-pick <D>

main:     M---N---D'             (D' = a copy of D's changes)
```

### Cherry-pick multiple commits and ranges

```bash
git cherry-pick a1b2c3d f9e8d7c        # two specific commits, in that order
git cherry-pick main~4..main~1         # a RANGE (exclusive of the first)
git cherry-pick main~4^..main~1        # add the ^ to INCLUDE the first
git cherry-pick -n a1b2c3d             # apply changes but don't commit yet (-n = --no-commit)
```

The `A..B` range is **exclusive** of `A` — it means "commits after A, up to and including B." Add `^` to `A` (`A^..B`) to include A itself. This off-by-one bites everyone once; expect it.

### When cherry-pick conflicts

Because the target branch differs from where the commit was born, a cherry-pick can conflict. The flow mirrors rebase:

```bash
git cherry-pick a1b2c3d
# CONFLICT — edit the files to resolve, then:
git add <resolved-files>
git cherry-pick --continue

# or bail out:
git cherry-pick --abort        # undo, back to before the cherry-pick
git cherry-pick --skip         # skip this one commit in a multi-pick
```

### A caution about cherry-picking

Cherry-picking **duplicates** a change: now the same diff exists as two different commits (different SHAs) in two places. If you later merge the two branches, Git usually notices and handles it, but duplicated commits can muddy history. Cherry-pick is a scalpel for "I need *this one* change over *there* now" — not a substitute for merging or rebasing whole branches.

## 2. `git reflog` — the safety net you didn't know you had

Every time HEAD moves — commit, checkout, reset, rebase, merge, amend — Git appends a line to the **reflog**, a local journal of where HEAD has been. Crucially, the reflog keeps references to commits **even after they become unreachable** from any branch. That is what makes "lost" commits recoverable.

```bash
git reflog          # or: git reflog show HEAD
```

Typical output:

```
f9e8d7c HEAD@{0}: reset: moving to HEAD~2
a1b2c3d HEAD@{1}: commit: feat: add sort
b2c3d4e HEAD@{2}: commit: feat: add search
c3d4e5f HEAD@{3}: commit: chore: init
```

Each `HEAD@{N}` is a **reference you can use** anywhere you'd use a SHA. `HEAD@{0}` is where HEAD is now; `HEAD@{1}` is where it was one move ago; and so on. The message after the colon tells you *what operation* moved HEAD there — `commit`, `reset`, `rebase`, `checkout` — so you can find the exact moment before things went wrong.

### The killer recovery: undo a `reset --hard`

In Lecture 1 you learned `reset --hard` can throw away commits. Here is why that's survivable:

```bash
git log --oneline
# a1b2c3d (HEAD) feat: add sort
# b2c3d4e feat: add search
# c3d4e5f chore: init

git reset --hard HEAD~2        # 😱 "deleted" the last two commits
git log --oneline
# c3d4e5f (HEAD) chore: init    -- the two feature commits are GONE from the branch

# ...but not gone from the reflog:
git reflog
# c3d4e5f HEAD@{0}: reset: moving to HEAD~2
# a1b2c3d HEAD@{1}: commit: feat: add sort     <-- there they are

git reset --hard a1b2c3d       # (or: git reset --hard HEAD@{1})
git log --oneline
# a1b2c3d (HEAD) feat: add sort  -- fully restored, nothing lost
```

The commits were never destroyed — the branch pointer just stopped referencing them. Reflog remembered the SHA, and `reset --hard <sha>` moved the pointer back. **This is the single most valuable safety technique in Git.**

### Recover a deleted branch

Deleted the wrong branch? Its tip is still in the reflog.

```bash
git branch -D feature          # oops, deleted an un-merged branch
git reflog                     # find the commit that was feature's tip
# a1b2c3d HEAD@{3}: commit: feat: the work I just deleted
git switch -c feature a1b2c3d  # recreate the branch at that commit
```

Even simpler if you acted fast — Git prints the SHA when you delete: *"Deleted branch feature (was a1b2c3d)."* Copy that SHA straight into `git switch -c`.

### Recover from a bad rebase or bad merge

A rebase that went wrong is just HEAD moving. Before the rebase, HEAD pointed at your good state; the reflog has it.

```bash
git reflog
# ...
# 7a6b5c4 HEAD@{5}: rebase (finish): returning to refs/heads/feature
# 9f8e7d6 HEAD@{8}: rebase (start): checkout main   <-- HEAD before rebase began
# d4e5f6a HEAD@{9}: commit: the branch as it was, pre-rebase

git reset --hard HEAD@{9}       # branch restored to its pre-rebase state
```

Look for the `rebase (start)` line — the commit *just before* it is your branch as it was before you touched it.

## 3. How long does the reflog keep things?

The reflog is not forever, but it is generous. By default:

| Reflog entries for… | Kept for |
|---------------------|----------|
| Reachable commits | 90 days |
| Unreachable commits (orphaned by reset/rebase) | 30 days |

Entries expire and eventually `git gc` (garbage collection) can prune the truly-unreachable objects. So: recovery is reliable for *days to weeks*, not indefinitely. If you realise you lost something, recover it soon rather than "later."

Two important limits:

- **The reflog is local and per-repository.** It is never pushed or cloned. Your reflog protects *your* clone only. It does not help a teammate recover, and a fresh `git clone` has an empty reflog.
- **It only tracks things HEAD (or a branch ref) pointed at.** Changes you never committed at all — edits sitting only in the working tree when you ran `reset --hard` — are *not* in the reflog, because they were never a commit. Commit early; the reflog can only save what you committed.

## 4. `git fsck` — the deeper net

If a commit somehow isn't even in the reflog (rare), it may still exist as a "dangling" object in the database:

```bash
git fsck --lost-found          # list dangling commits/blobs
# dangling commit a1b2c3d...
git show a1b2c3d               # inspect it
git switch -c rescued a1b2c3d  # rescue it onto a branch
```

`fsck` walks the raw object database for anything unreferenced. It's the last resort after reflog, and it's saved many a panicked engineer.

## 5. Recovery decision guide

| "I just…" | Recover with |
|-----------|--------------|
| …reset --hard and lost commits | `git reflog` → `git reset --hard <sha>` |
| …deleted a branch | `git reflog` (or the printed SHA) → `git switch -c <name> <sha>` |
| …finished a rebase that went wrong | `git reflog` → find `rebase (start)` → `git reset --hard HEAD@{n}` |
| …amended and want the old commit back | `git reflog` → `git reset --hard <old-sha>` |
| …lost a commit not even in the reflog | `git fsck --lost-found` → `git switch -c rescued <sha>` |
| …lost uncommitted working-tree edits | Nothing in Git — they were never committed. (Editor local history / backups only.) |

## 6. Check yourself

- What does `cherry-pick` do to the *original* commit?
- Is the range `A..B` inclusive or exclusive of `A`? How do you include A?
- After `git reset --hard HEAD~3`, are those three commits recoverable? How?
- What does the `HEAD@{2}` notation refer to?
- Which line in the reflog marks your branch state before a rebase started?
- Name one thing the reflog **cannot** recover, and why.

When you can recover a deleted commit from memory in under a minute, [exercise 3](../exercises/exercise-03-cherry-pick-and-reflog-recovery.md) proves it.

## Further reading

- **Pro Git — "Data Recovery" (reflog + fsck):** <https://git-scm.com/book/en/v2/Git-Internals-Maintenance-and-Data-Recovery>
- **`git cherry-pick` reference:** <https://git-scm.com/docs/git-cherry-pick>
- **`git reflog` reference:** <https://git-scm.com/docs/git-reflog>
- **Atlassian — "git reflog" tutorial:** <https://www.atlassian.com/git/tutorials/rewriting-history/git-reflog>
