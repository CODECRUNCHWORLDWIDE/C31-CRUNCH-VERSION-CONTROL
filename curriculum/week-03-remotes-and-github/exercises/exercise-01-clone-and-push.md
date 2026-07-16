# Exercise 1 — Clone and Push

**Goal:** Clone a repository and push a commit back to its remote. You'll do it against a *local* bare repo first (so it works with no network and no account), then repeat against a real GitHub repo you own.

**Estimated time:** 40 minutes.

## Part A — A local "server" (no network needed)

A bare repository is exactly the shape GitHub hosts for you: a repo with no working tree, meant to be pushed to and pulled from. We'll use one as a stand-in for GitHub so you can see push/clone with zero moving parts.

### Setup

```bash
mkdir -p ~/c31-w3/ex01 && cd ~/c31-w3/ex01

# Create the "server" — a bare repo
git init --bare origin-server.git

# Clone it as if it lived on GitHub
git clone ./origin-server.git app
cd app
```

`git clone` printed something like "warning: You appear to have cloned an empty repository." That's expected — the server has no commits yet.

### Steps

1. **Confirm the remote wiring the clone set up for you:**
   ```bash
   git remote -v
   ```
   You should see `origin` pointing at `origin-server.git` for both fetch and push.

2. **Create some history:**
   ```bash
   echo "# My App" > README.md
   echo "print('hello')" > app.py
   git add .
   git commit -m "Initial commit: README and app"
   ```

3. **Push it, setting upstream tracking:**
   ```bash
   git push -u origin main
   ```
   Read the output. Note the line creating `main` on the remote and setting up tracking.

4. **Verify the tracking link:**
   ```bash
   git branch -vv
   ```
   You should see `[origin/main]` next to `main`.

5. **Make a second commit and push again — this time with no arguments:**
   ```bash
   echo "## Usage" >> README.md
   git commit -am "Add usage heading"
   git push
   ```
   Because upstream is set, a bare `git push` works.

6. **Prove the server actually received it** by cloning a second, fresh copy:
   ```bash
   cd ~/c31-w3/ex01
   git clone ./origin-server.git verify
   cd verify
   git log --oneline
   ```
   Both commits should be present in this brand-new clone.

### Expected result

The `verify` clone's `git log --oneline` shows **two** commits, identical to the ones you made in `app/`. That proves your pushes reached the "server."

## Part B — A real GitHub repo (needs an account)

Now the real thing. You'll need a GitHub account and either a personal access token (HTTPS) or an SSH key set up — see Lecture 2 if you haven't.

1. **Create an empty repo on GitHub** named `c31-week-03` (via the website's **New repository** button, or `gh repo create c31-week-03 --public`). Do **not** add a README from the UI — keep it empty so there's nothing to conflict with.

2. **Point a local repo at it and push:**
   ```bash
   cd ~/c31-w3/ex01/app
   git remote add github https://github.com/YOUR-USERNAME/c31-week-03.git
   git push -u github main
   ```
   (Authenticate with your token or SSH key when prompted. If you used `gh auth login`, you won't be prompted.)

3. **Open the repo in your browser** and confirm your two commits are there.

## Done when…

- [ ] Part A: a fresh `verify` clone shows both commits — proving your pushes landed.
- [ ] `git branch -vv` shows `main` tracking a remote branch (`[origin/main]`).
- [ ] You pushed once with `-u` and once with a bare `git push`, and can explain the difference.
- [ ] Part B: your two commits are visible on GitHub in a repo you own.
- [ ] You can state in one sentence what a *bare* repository is and why servers use it.

## Stretch

- Add a third commit, then delete the remote branch with `git push github --delete main` and observe what happens to the GitHub repo. Push it back.
- Run `git remote show origin` in `app/` and read every line of the report. What does "local out of date" mean when it appears?
- Change the `github` remote from HTTPS to SSH (or vice versa) with `git remote set-url` and push again.

## Submission

Commit a `notes.md` to your portfolio under `c31-week-03/exercise-01/` recording: the output of `git remote -v` and `git branch -vv` from Part A, and one sentence on what "bare repository" means.
