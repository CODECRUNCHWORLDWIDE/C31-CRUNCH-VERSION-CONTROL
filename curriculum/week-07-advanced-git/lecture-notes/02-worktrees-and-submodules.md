# Lecture 2 — Worktrees and Submodules

> **Duration:** ~2 hours. **Outcome:** You can use `git worktree` to work on multiple branches at once without stashing or re-cloning, and you understand submodules well enough to use them — and, more importantly, to know when *not* to.

Lecture 1 gave you hooks and bisect: tools for automating checks and finding regressions. This lecture is about **working on more than one thing at once** and **composing repositories out of other repositories**. Both solve real problems. One of them (worktrees) you should reach for often. The other (submodules) you should reach for rarely and carefully — so we spend as much time on the "when not to" as the "how."

## 1. The problem worktrees solve

You're deep in a feature branch, editor full of half-finished changes. A production bug lands. You need to fix it on `main`, now. Your options up to this week:

1. **`git stash`** your work, `git checkout main`, fix, come back, `git stash pop` — and pray the stash applies cleanly.
2. **Commit a "WIP"** you'll have to unpick later.
3. **Clone the repo again** into a second directory — wasteful, and now you have two `.git/` copies to keep in sync.

Every one of these has friction. **`git worktree`** removes it: it lets a single repository have **multiple working directories**, each on its own branch, all sharing **one** object database.

## 2. What a worktree is

A **worktree** is a checkout of your repo in its own directory. When you `git init` or `git clone`, you get one worktree (the "main" worktree) plus the `.git/` database. `git worktree add` creates *additional* working directories that point back at the same `.git/` — same objects, same refs, same stashes — but each sitting on a different branch.

The payoff: switching "branches" becomes switching *directories*. No stashing. No rebuilding. Your feature branch stays exactly as you left it, editor state and all, while you work in a second window on the fix.

```bash
# From inside your repo, create a worktree for a hotfix branch:
git worktree add ../myproject-hotfix -b hotfix/crash main
```

That command does three things at once: creates the directory `../myproject-hotfix`, creates a new branch `hotfix/crash` starting from `main`, and checks it out there. Open that folder in a second editor window and you have a clean, independent workspace.

## 3. The worktree workflow

```bash
# List every worktree this repo has:
git worktree list
# /home/you/myproject          a1b2c3d [feature/exporter]
# /home/you/myproject-hotfix   d4e5f6a [hotfix/crash]

# Do the fix in the hotfix worktree:
cd ../myproject-hotfix
# ...edit, test, commit, push, open PR...

# When the branch is merged and you're done, remove the worktree:
cd ../myproject
git worktree remove ../myproject-hotfix

# If a worktree's directory was deleted manually, prune the stale record:
git worktree prune
```

Common real uses:

| Situation | Worktree play |
|-----------|---------------|
| Urgent hotfix mid-feature | `worktree add` a hotfix dir from `main`; feature branch untouched |
| Review a colleague's PR while building | `worktree add ../review pr-branch`; run their code without disturbing yours |
| Long build on one branch | build in one worktree while you keep coding in another |
| Compare two branches side by side | two worktrees, two editor windows, real diffing by eye |
| Run tests on `main` while working on a branch | dedicated `../myproject-test` worktree |

## 4. Worktree rules and gotchas

Sharing one object database is the strength and the constraint:

- **A branch can be checked out in only one worktree at a time.** Try to check out `main` in two worktrees and Git refuses: `fatal: 'main' is already checked out at ...`. This is a feature — it stops two directories from fighting over the same branch pointer.
- **Detached HEAD is allowed anywhere.** Need the same commit in two places without a branch? `git worktree add --detach ../tmp <commit>`.
- **Stashes and the reflog are shared** (one object DB), but each worktree has its **own** index and `HEAD`.
- **Don't nest worktrees inside the repo** if you can avoid it — put them in sibling directories (`../myproject-hotfix`) so build tools and `.gitignore` don't get confused.
- **Removing** a worktree: use `git worktree remove`, not `rm -rf`. If you already `rm`'d it, run `git worktree prune` to clear the bookkeeping.

Worktrees are cheap, safe, and underused. Make them your default answer to "I need to touch another branch right now."

## 5. Submodules — the why (and the warning)

A **submodule** embeds one Git repository inside another as a subdirectory, pinned to a **specific commit** of the inner repo. The outer (super)project doesn't store the inner repo's files — it stores a *pointer*: "at this path, check out that exact commit of that other repo."

The legitimate use case is narrow: you depend on another repository's source *as source* (not as a published package), you need to **pin an exact commit**, and you occasionally need to change both repos together. Vendoring a library you also maintain, or sharing a firmware blob repo across products, are the classic fits.

Before you reach for submodules, read the warning: **submodules are the sharpest tool in this course.** They introduce a second layer of state that every collaborator must understand, and the failure mode is silent — someone clones your repo and the submodule directory is empty, or they commit the superproject without updating the pointer, and now history lies about which version was used. Most teams that adopt submodules eventually remove them. Use them only when the alternatives genuinely don't fit.

## 6. Using submodules

```bash
# Add a submodule at path libs/parser, tracking a remote:
git submodule add https://github.com/example/parser libs/parser
git commit -m "Add parser as a submodule pinned to current commit"
```

This creates a `.gitmodules` file (tracked; records URL + path) and stores the inner repo's current commit as the pointer. Now the critical part — **cloning**:

```bash
# A plain clone leaves submodule dirs EMPTY:
git clone https://github.com/example/superproject
# You must then initialise and fetch them:
git submodule update --init --recursive

# Or do it all at clone time:
git clone --recurse-submodules https://github.com/example/superproject
```

Updating a submodule to a newer commit of the inner repo:

```bash
cd libs/parser
git fetch
git checkout v2.1.0          # move the inner repo to the commit you want
cd ../..
git add libs/parser          # stage the NEW pointer in the superproject
git commit -m "Bump parser to v2.1.0"
```

Read that last block carefully. Changing the submodule is **two commits**: one conceptually inside the inner repo (moving its `HEAD`) and one in the superproject that records the new pointer. Forget the second and your teammates get a different version than you're running.

Pulling others' submodule bumps:

```bash
git pull
git submodule update --init --recursive   # move submodules to the pointers you just pulled
```

## 7. The traps that bite everyone

| Trap | What happens | Avoid by |
|------|--------------|----------|
| Empty submodule dirs | Cloner forgets `--recurse-submodules` | Document it; add `submodule update --init` to setup |
| Detached HEAD inside submodule | Submodules check out a *commit*, not a branch | Know it's expected; branch explicitly if you'll commit there |
| Forgotten pointer bump | You update the inner repo but not the superproject | Always `git add <submodule-path>` after moving it |
| "Modified" that won't go away | `git status` shows the submodule dirty | You changed the inner repo; commit or reset *inside* it |
| Painful history rewrites | Submodule pointers complicate rebase/bisect | Keep submodule changes in their own commits |

## 8. When to avoid submodules — and what to use instead

Reach for an alternative first:

| You want to… | Prefer | Over submodules because… |
|--------------|--------|--------------------------|
| Use a published library | your language's **package manager** (npm, pip, cargo…) | versioning, resolution, and updates are solved problems |
| Combine repos into one | **`git subtree`** or just merging them | code is present in every clone; no second state layer |
| Manage many related repos | a **meta-tool** (`meta`, `mu`, or a monorepo) | tooling designed for the multi-repo case |
| Vendor a snapshot you won't update | **copy the files in** (vendoring) | simplest possible; no live link to maintain |

**`git subtree`** deserves a specific mention as the usual replacement: it merges another repo's history into a subdirectory of yours. Clones "just work" (no init step, no empty dirs), the files are really there, and you can still pull upstream updates — at the cost of a fatter history. For most "I need this other repo's code in mine" situations, subtree hurts less than submodules.

The rule of thumb: **if a package manager can do it, use the package manager. If you only need a snapshot, vendor it. Reach for submodules only when you need a live, pinned, mutually-editable link between two source repos — and everyone on the team understands the workflow.**

## 9. Check yourself

- What does a worktree share with the main worktree, and what does it keep separate?
- Why can't the same branch be checked out in two worktrees at once?
- What's the right way to delete a worktree, and what do you run if you deleted its folder by hand?
- A submodule pointer stores what, exactly — a branch or a commit?
- Why do submodule directories come up empty after a plain `git clone`, and what fixes it?
- Updating a submodule to a new version takes two commits. What are they?
- Name two alternatives to submodules and when each is the better call.

When those land, the [exercises](../exercises/README.md) put you in two worktrees at once.

## Further reading

- **`git worktree` — official docs:** <https://git-scm.com/docs/git-worktree>
- **Pro Git — "Submodules":** <https://git-scm.com/book/en/v2/Git-Tools-Submodules>
- **`git submodule` — official docs:** <https://git-scm.com/docs/git-submodule>
- **`git subtree` — official docs:** <https://git-scm.com/docs/git-subtree>
