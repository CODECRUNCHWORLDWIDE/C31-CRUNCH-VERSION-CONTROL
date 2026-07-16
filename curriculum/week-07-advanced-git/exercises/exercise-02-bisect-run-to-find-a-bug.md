# Exercise 2 — Find a Bug with `git bisect run`

**Goal:** Build a repo with a run of commits, plant a regression partway through, then let `git bisect run` drive a test script to the exact commit that broke it — without you eyeballing a single diff.

**Estimated time:** 50 minutes.

## Setup — build a history with a planted bug

We'll write a tiny shell "app" (`total.sh`) that sums numbers, make a series of commits, and break it in the middle. Copy this whole block; it creates 15 commits and plants the bug at commit 9.

```bash
mkdir -p ~/c31-w7/exercise-02
cd ~/c31-w7/exercise-02
git init
git config user.name  "Your Name"
git config user.email "you@example.com"

# A correct summing program:
cat > total.sh <<'EOF'
#!/usr/bin/env bash
# Sums its integer arguments and prints the total.
sum=0
for n in "$@"; do sum=$((sum + n)); done
echo "$sum"
EOF
chmod +x total.sh
git add total.sh
git commit -q -m "Add total.sh"

# 7 harmless commits (a changelog growing over time):
for i in $(seq 1 7); do
  echo "- change $i" >> CHANGELOG.md
  git add CHANGELOG.md
  git commit -q -m "Changelog entry $i"
done

# THE REGRESSION: someone "optimises" the loop and breaks it
# (starts sum at 1 instead of 0 — every total is now off by one).
cat > total.sh <<'EOF'
#!/usr/bin/env bash
# Sums its integer arguments and prints the total.
sum=1
for n in "$@"; do sum=$((sum + n)); done
echo "$sum"
EOF
git add total.sh
git commit -q -m "Refactor total.sh loop"

# 6 more harmless commits on top, hiding the bug in history:
for i in $(seq 8 13); do
  echo "- change $i" >> CHANGELOG.md
  git add CHANGELOG.md
  git commit -q -m "Changelog entry $i"
done

git log --oneline
```

Confirm the bug exists at `HEAD`:

```bash
./total.sh 2 2      # prints 5, should print 4 — BROKEN
```

## Part A — Find the endpoints

You need a known-good and a known-bad commit.

- **Bad** is easy: `HEAD` is broken.
- **Good:** the very first commit ("Add total.sh") was correct. Grab its SHA:

```bash
git log --oneline | tail -1        # note the SHA of "Add total.sh"
FIRST=$(git rev-list --max-parents=0 HEAD)   # SHA of the root commit
echo "$FIRST"
```

## Part B — Write the test the way bisect wants it

Put the check **outside** the repo so it doesn't disappear as bisect checks out old commits. It must exit `0` when good and non-zero when bad:

```bash
cat > /tmp/check-total.sh <<'EOF'
#!/usr/bin/env bash
# Good if `total.sh 2 2` prints exactly 4.
result=$(./total.sh 2 2)
[ "$result" = "4" ] && exit 0 || exit 1
EOF
chmod +x /tmp/check-total.sh
```

## Part C — Run the automated bisect

```bash
git bisect start
git bisect bad HEAD
git bisect good "$FIRST"
git bisect run /tmp/check-total.sh
```

Watch the output. Bisect will check out midpoints, run the script, read the exit code, and converge. It should finish with something like:

```
<sha> is the first bad commit
    Refactor total.sh loop
```

That's the commit you planted. Bisect found it in ~4 steps instead of you reading 14 diffs.

## Part D — Clean up (do NOT skip)

```bash
git bisect reset      # return to the branch you started from
git log --oneline | head -1   # confirm you're back at HEAD, not detached
```

Record the culprit's commit message and SHA in `found.md`, plus the number of steps bisect took (count the "Bisecting:" lines).

## Expected result

- Bisect names **"Refactor total.sh loop"** as the first bad commit.
- It reaches that answer in roughly `log2(14) ≈ 4` steps, not 14.
- After `git bisect reset` you're back on your branch at `HEAD`.

## Done when…

- [ ] `git bisect run` identified the correct planted commit.
- [ ] Your check script lived **outside** the repo and used correct exit codes (0 good / 1 bad).
- [ ] You ran `git bisect reset` and confirmed you're no longer in detached HEAD.
- [ ] `found.md` records the culprit SHA, its message, and the step count.

## Stretch

- Re-run the bisect **by hand** (no `run` script): `git bisect start`, mark bad/good, then at each stop type `./total.sh 2 2`, judge it, and enter `git bisect good` or `git bisect bad` yourself. Compare the number of steps to the automated run.
- Add a `git bisect skip` scenario: introduce one commit in the range that doesn't build (e.g. `total.sh` temporarily has a syntax error), make your check script `exit 125` when the script can't run, and confirm bisect skips it instead of blaming it.

## Submission

Commit `found.md` to your portfolio under `c31-week-07/exercise-02/`. You do not need to commit the practice repo itself — the write-up is the artifact.
