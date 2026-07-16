# Exercise 3 — Parallel Work with Worktrees

**Goal:** Simulate the classic interruption — you're mid-feature when an urgent bug lands on `main` — and handle it with `git worktree` instead of stashing. By the end you'll have two working directories on two branches, sharing one repo, and you'll feel why worktrees beat stashing.

**Estimated time:** 40 minutes.

## Setup — a repo with a feature in progress

```bash
mkdir -p ~/c31-w7/exercise-03/app
cd ~/c31-w7/exercise-03/app
git init
git config user.name  "Your Name"
git config user.email "you@example.com"

cat > app.py <<'EOF'
def greet(name):
    return f"Hello, {name}!"

if __name__ == "__main__":
    print(greet("world"))
EOF
git add app.py
git commit -m "Initial app"

# Start a feature branch and leave work HALF DONE (unstaged, on purpose):
git switch -c feature/goodbye
cat >> app.py <<'EOF'

def farewell(name):
    return f"Goodbye, {name"   # <-- intentionally unfinished / broken syntax
EOF
```

You now have a dirty working tree on `feature/goodbye` with a syntax error you're in the middle of writing. **Do not commit it, do not stash it.** That half-typed line is the state we want to preserve.

## Part A — The interruption arrives

An urgent bug is reported on `main`: `greet` should handle an empty name. Normally you'd stash your broken feature work first. Instead, spin up a worktree:

```bash
# From the app/ directory:
git worktree add ../hotfix -b hotfix/empty-name main
git worktree list      # see both worktrees and their branches
```

You now have a **second** directory, `../hotfix`, checked out on a new `hotfix/empty-name` branch off `main` — while `app/` still sits on your untouched, half-broken feature.

## Part B — Fix the bug in the hotfix worktree

```bash
cd ../hotfix
cat > app.py <<'EOF'
def greet(name):
    if not name:
        return "Hello, there!"
    return f"Hello, {name}!"

if __name__ == "__main__":
    print(greet("world"))
EOF
python3 app.py            # prints: Hello, world!
git add app.py
git commit -m "Handle empty name in greet"
```

The hotfix is committed on its own branch. In a real repo you'd push it and open a PR here.

## Part C — Confirm your feature work is untouched

```bash
cd ../app
cat app.py               # your half-typed, broken farewell() is EXACTLY as you left it
git status               # still on feature/goodbye, still dirty
```

This is the whole point: you context-switched to an urgent fix and back, and your in-progress work never moved. No stash, no "WIP" commit, no merge anxiety.

Now finish the feature normally:

```bash
# Fix the broken line:
cat > app.py <<'EOF'
def greet(name):
    return f"Hello, {name}!"

def farewell(name):
    return f"Goodbye, {name}!"

if __name__ == "__main__":
    print(greet("world"))
    print(farewell("world"))
EOF
python3 app.py
git add app.py
git commit -m "Add farewell()"
```

## Part D — Clean up the worktree

```bash
git worktree remove ../hotfix     # proper removal
git worktree list                  # only app/ remains
```

Answer in `notes.md`, two or three sentences: *what did worktrees give you here that `git stash` would not?* Mention at least the fact that your editor state and unfinished, un-committable code survived the interruption in place.

## Expected result

- Two worktrees existed simultaneously on two branches, sharing one `.git/`.
- The hotfix was committed without disturbing the feature branch's dirty tree.
- After `git worktree remove`, only the original worktree remains.

## Done when…

- [ ] `git worktree list` showed two worktrees during Part B.
- [ ] The hotfix commit exists on `hotfix/empty-name`, made from the `../hotfix` directory.
- [ ] Your `feature/goodbye` working tree was provably unchanged during the hotfix.
- [ ] `git worktree remove ../hotfix` succeeded and `notes.md` explains the advantage over stashing.

## Stretch

- Try to `git worktree add ../second main` while `main` is already checked out somewhere, and read the error. Explain in `notes.md` why Git refuses to check out the same branch in two worktrees.
- Create a `--detach` worktree at a specific commit (`git worktree add --detach ../inspect HEAD~1`), poke around, then remove it. When is a detached worktree the right tool?

## Submission

Commit `notes.md` to your portfolio under `c31-week-07/exercise-03/`.
