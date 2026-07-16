# Exercise 3 — Fork and Sync

**Goal:** Fork a real GitHub repository, wire up the `upstream` remote, and sync your fork when the original moves ahead. This is the exact setup you'll build the challenges and mini-project on top of.

**Estimated time:** 45 minutes.

## Prerequisites

- A GitHub account with auth set up (token or SSH key — Lecture 2).
- The `gh` CLI installed and logged in (`gh auth status` shows you're authenticated). You can do this entirely in the web UI + plain `git` too; `gh` just makes it shorter.

## A safe repo to fork

Practice on a repo built for exactly this. **`octocat/Spoon-Knife`** is GitHub's official "learn to fork" repository — it exists to be forked and experimented with. (Any small public repo works; Spoon-Knife is the traditional choice.)

## Part A — Fork and clone

### With `gh` (one command)

```bash
cd ~/c31-w3
gh repo fork octocat/Spoon-Knife --clone
cd Spoon-Knife
```

`gh` forks the repo to your account, clones your fork, **and** adds an `upstream` remote automatically.

### Or manually (understand what `gh` did)

1. On GitHub, open `github.com/octocat/Spoon-Knife` and click **Fork**.
2. Clone *your* fork:
   ```bash
   cd ~/c31-w3
   git clone https://github.com/YOUR-USERNAME/Spoon-Knife.git
   cd Spoon-Knife
   ```
3. Add the original as `upstream`:
   ```bash
   git remote add upstream https://github.com/octocat/Spoon-Knife.git
   ```

## Part B — Inspect the two-remote setup

```bash
git remote -v
```

You should see **two** remotes:

```
origin    https://github.com/YOUR-USERNAME/Spoon-Knife.git   (your fork — you can push)
upstream  https://github.com/octocat/Spoon-Knife.git         (the original — read only)
```

Confirm the difference in your own words in `notes.md`: which remote can you push to, and which is read-only?

## Part C — Sync your fork with upstream

Even if `upstream` hasn't changed today, run the full sync so the muscle memory is real:

```bash
git fetch upstream                 # download the original's latest into upstream/*
git switch main                    # (Spoon-Knife's default branch may be 'main')
git merge upstream/main            # fast-forward your local main to match
git push origin main               # update YOUR fork on GitHub too
```

If Git complains the branch is `master` rather than `main`, substitute the name `git branch` shows — Spoon-Knife historically used `master`. Adapt the branch name; the mechanism is identical.

Then confirm the shortcut route works too:

```bash
gh repo sync YOUR-USERNAME/Spoon-Knife    # one-command sync of your fork
```

## Expected result

- `git remote -v` shows exactly two remotes named `origin` (yours) and `upstream` (the original).
- The four-command sync completes cleanly (a fast-forward or "Already up to date").
- Your fork's page on GitHub shows it's even with `octocat/Spoon-Knife`.

## Done when…

- [ ] You forked Spoon-Knife and cloned **your fork** (not the original).
- [ ] `git remote -v` shows both `origin` (your fork) and `upstream` (the original).
- [ ] You can state which remote you push to and which you only fetch from.
- [ ] You ran the four-command sync (`fetch upstream` → `switch main` → `merge upstream/main` → `push origin main`) and it succeeded.
- [ ] You tried the `gh repo sync` shortcut and got the same end state.

## Stretch

- Create a branch (`git switch -c my-change`), edit `README.md`, commit, and `git push -u origin my-change`. Do **not** open a PR yet — that's the mini-project. Just confirm the branch appears on your fork.
- Explore what "Sync fork" looks like in the GitHub web UI on your fork's page. When would you use the button vs. the command line?
- Add a second unrelated public repo as another fork and set up its `upstream`. Practicing the wiring twice cements it.

## Submission

Commit a `notes.md` to your portfolio under `c31-week-03/exercise-03/` with: the output of `git remote -v`, and two sentences on the roles of `origin` vs. `upstream`.
