# Exercise 1 — Branch and fast-forward

**Goal:** Create a branch, switch to it, commit on it, and merge it back with a **fast-forward** — then explain, out loud, why no merge commit appeared.

**Estimated time:** 40 minutes.

## Setup

```bash
mkdir -p ~/git-w2/ex01 && cd ~/git-w2/ex01
git init
git config user.name "Your Name"        # if not already set globally
git config user.email "you@example.com"

echo "# Task Tracker" > README.md
git add README.md
git commit -m "Initial commit"
```

Make sure the log alias from Lecture 1 exists (you'll use it constantly):

```bash
git config --global alias.lg "log --oneline --graph --all --decorate"
git lg
# * 9f4c2b1 (HEAD -> main) Initial commit
```

## Steps

1. **Create and switch to a feature branch in one command:**

   ```bash
   git switch -c add-tasks
   git lg
   ```

   Observe: `HEAD -> add-tasks` now, and `main` still points at the same commit. **Nothing was copied.** Prove it: `cat .git/refs/heads/add-tasks` and `cat .git/refs/heads/main` — same hash.

2. **Make two commits on `add-tasks`:**

   ```bash
   echo "- [ ] Buy milk" > tasks.md
   git add tasks.md
   git commit -m "Add first task"

   echo "- [ ] Walk dog" >> tasks.md
   git add tasks.md
   git commit -m "Add second task"

   git lg
   ```

   `add-tasks` is now two commits ahead of `main`. Note the two hashes — you'll watch `main` catch up to the tip.

3. **Switch back to `main` and confirm the divergence in the working tree:**

   ```bash
   git switch main
   ls
   ```

   `tasks.md` is **gone** — it only exists on `add-tasks`. This is not deletion; it's that `main`'s snapshot never had it. Confirm with `git lg` that `main` hasn't moved.

4. **Predict, then merge.** Before running the merge, write down: *will this be a fast-forward or a three-way merge?* (`main` hasn't moved since you branched, so…). Then:

   ```bash
   git merge add-tasks
   ```

   Read the output. You should see `Fast-forward` and **no editor opening for a merge message**.

5. **Confirm the result:**

   ```bash
   git lg
   ls                    # tasks.md is back
   git log --oneline     # a straight line — no merge commit
   ```

   Both `main` and `add-tasks` now point at the same tip commit. History is perfectly linear.

6. **Clean up the merged branch:**

   ```bash
   git branch -d add-tasks       # safe delete: works because it's fully merged
   git lg
   ```

   `-d` succeeds precisely because every `add-tasks` commit is now reachable from `main`. Try to imagine when `-d` would *refuse*.

## Expected result

- `git log --oneline` shows **three commits in a straight line**, no merge commit.
- The merge output said `Fast-forward`.
- `git branch` lists only `main` after cleanup.

## Done when…

- [ ] You created `add-tasks` with `git switch -c` and made two commits on it.
- [ ] You observed `tasks.md` disappear on `main` and reappear after the merge.
- [ ] Your merge produced a **fast-forward** (you can state *why*: `main` was a direct ancestor of `add-tasks`).
- [ ] `git branch -d add-tasks` succeeded without needing `-D`.
- [ ] You can explain in one sentence why no merge commit was created.

## Stretch

- Redo the whole exercise but this time merge with `git merge --no-ff add-tasks`. What's different in `git lg`? Why might a team *want* that extra commit?
- Run `git merge --ff-only add-tasks` in a fresh copy. When would this fail?
