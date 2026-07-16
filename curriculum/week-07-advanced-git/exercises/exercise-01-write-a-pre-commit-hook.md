# Exercise 1 — Write a Pre-Commit Hook

**Goal:** Build a working `pre-commit` hook that blocks two classes of bad commit, and prove — by trying to make a bad commit — that it fires. By the end you'll trust hooks because you watched one stop you.

**Estimated time:** 45 minutes.

## Setup

```bash
mkdir -p ~/c31-w7/exercise-01
cd ~/c31-w7/exercise-01
git init
git config user.name  "Your Name"      # if not set globally
git config user.email "you@example.com"

echo "# Hook lab" > README.md
git add README.md
git commit -m "Initial commit"
```

You now have a fresh repo to protect.

## Part A — Write the hook

Create `.git/hooks/pre-commit` with the content below. This hook does two things: it blocks any staged change containing the marker `DO NOT COMMIT`, and it blocks staged files larger than 500 KB.

```bash
cat > .git/hooks/pre-commit <<'EOF'
#!/usr/bin/env bash
set -euo pipefail

# Files being committed (added, copied, or modified):
staged=$(git diff --cached --name-only --diff-filter=ACM)

# 1. Block a "DO NOT COMMIT" marker anywhere in the staged changes.
if git diff --cached -G'DO NOT COMMIT' --name-only | grep -q .; then
  echo "pre-commit BLOCKED: a staged change contains 'DO NOT COMMIT'." >&2
  exit 1
fi

# 2. Block files larger than 500 KB.
for f in $staged; do
  [ -f "$f" ] || continue
  size=$(wc -c < "$f")
  if [ "$size" -gt 512000 ]; then
    echo "pre-commit BLOCKED: $f is $((size/1024)) KB (> 500 KB)." >&2
    exit 1
  fi
done

echo "pre-commit checks passed."
exit 0
EOF

chmod +x .git/hooks/pre-commit
```

The `chmod +x` is not optional — Git silently ignores a hook that isn't executable.

## Part B — Prove the marker check fires

```bash
printf 'const key = "abc"  // DO NOT COMMIT\n' > config.js
git add config.js
git commit -m "Add config"        # should be BLOCKED
```

You should see `pre-commit BLOCKED: a staged change contains 'DO NOT COMMIT'.` and **no commit created** (`git log --oneline` still shows only the initial commit).

Now fix the file and watch it pass:

```bash
printf 'const key = process.env.KEY\n' > config.js
git add config.js
git commit -m "Add config"        # should PASS
```

## Part C — Prove the size check fires

```bash
# Make a 600 KB file:
dd if=/dev/zero of=big.bin bs=1024 count=600 2>/dev/null
git add big.bin
git commit -m "Add big file"      # should be BLOCKED
```

You should see the size message and no new commit.

## Part D — Prove the escape hatch works

Client-side hooks are conveniences, not security. Show that `--no-verify` bypasses the hook:

```bash
git commit -m "Force the big file in" --no-verify   # succeeds, hook skipped
```

Then undo it so your repo stays clean:

```bash
git reset --hard HEAD~1
rm -f big.bin
```

Write one sentence in `notes.md` answering: *given that `--no-verify` exists, where must a rule live if it truly cannot be bypassed?* (Hint: Week 6.)

## Expected result

- The hook blocks the marker commit and the big-file commit.
- The clean commit succeeds.
- `--no-verify` bypasses the hook.
- `notes.md` explains where an unskippable rule belongs.

## Done when…

- [ ] `.git/hooks/pre-commit` exists, is executable, and contains both checks.
- [ ] You have watched the hook **block** at least two bad commits (marker + size).
- [ ] You have watched a clean commit **pass**.
- [ ] You demonstrated `--no-verify` bypassing the hook, then reset the repo.
- [ ] `notes.md` names CI / a server-side hook as the home for unskippable rules.

## Stretch

- Add a third check: reject commit if any staged file has a `.env` extension.
- Move the hook out of `.git/hooks/` into a tracked `hooks/` directory and wire it up with `git config core.hooksPath hooks`. Explain in `notes.md` why that makes the hook shareable when `.git/hooks/` is not.

## Submission

Commit `config.js`, `notes.md`, and (if you did the stretch) `hooks/pre-commit` to your portfolio under `c31-week-07/exercise-01/`. Do **not** commit `big.bin`.
