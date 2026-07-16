# Exercise 3 — Cherry-pick + Reflog Recovery

**Goal:** Copy a single fix commit from a feature branch onto `main` with `cherry-pick`, then deliberately "destroy" a commit with `reset --hard` and bring it back from the dead with `reflog`. Two of Git's most reassuring tools, back to back.

**Estimated time:** 45 minutes.

## Setup

```bash
mkdir -p /tmp/c31-w4/ex3 && cd /tmp/c31-w4/ex3
git init -q
echo "v1" > app.txt && git add . && git commit -qm "chore: release v1"

# A feature branch with several commits, one of which is an urgent bug fix
git switch -c feature
echo "feature work A" >> app.txt && git add . && git commit -qm "feat: start feature"
echo "CRITICAL FIX"   >> app.txt && git add . && git commit -qm "fix: patch security hole"
echo "feature work B" >> app.txt && git add . && git commit -qm "feat: more feature work"

git log --oneline --all --graph
```

The `fix: patch security hole` commit needs to ship on `main` **now**, but the surrounding feature work is unfinished. You cannot merge the whole branch.

## Part A — Cherry-pick the fix onto main

1. Find the SHA of the fix commit:

   ```bash
   git log --oneline feature | grep "patch security hole"
   ```

2. Switch to `main` and cherry-pick just that commit:

   ```bash
   git switch main
   git cherry-pick <sha-of-the-fix>
   ```

3. If a conflict appears (both branches edited `app.txt`), resolve it, then:

   ```bash
   git add app.txt
   git cherry-pick --continue
   ```

4. Verify the fix is on `main` **and** that the surrounding feature work did NOT come along:

   ```bash
   git log --oneline main        # shows the fix, but NOT "start feature" / "more feature work"
   cat app.txt                   # contains "CRITICAL FIX", not the feature lines
   ```

   In `answers.md`: note the SHA of the fix on `feature` and the SHA of its copy on `main`. Are they the same? Explain in one sentence.

## Part B — Destroy a commit, then recover it

1. Back on `feature`, confirm the tip:

   ```bash
   git switch feature
   git log --oneline             # note the SHA of "feat: more feature work"
   ```

2. Simulate a disaster — hard-reset two commits back, wiping the two most recent:

   ```bash
   git reset --hard HEAD~2
   git log --oneline             # "more feature work" and the fix are GONE from feature
   ```

3. Recover using the reflog. Find where HEAD was before the reset:

   ```bash
   git reflog                    # look for the line just before "reset: moving to HEAD~2"
   git reset --hard HEAD@{1}     # or use the SHA you noted in step 1
   git log --oneline             # feature fully restored
   ```

   In `answers.md`: paste the `git reflog` output and mark which line you used to recover, and why.

## Part C — Recover a deleted branch (stretch into the real skill)

1. Create and then delete a branch:

   ```bash
   git switch -c throwaway
   echo "important" >> app.txt && git add . && git commit -qm "feat: work I will 'lose'"
   git switch feature
   git branch -D throwaway       # Git prints: Deleted branch throwaway (was <sha>)
   ```

2. Bring it back:

   ```bash
   git reflog                    # or use the SHA Git printed on deletion
   git switch -c throwaway <sha> # recreate the branch at its old tip
   git log --oneline throwaway   # the "lost" commit is back
   ```

## Expected result

- `main` carries the security fix as a **new commit** (different SHA from the original) and none of the feature work.
- `feature` is fully restored after the hard reset, via reflog.
- The deleted `throwaway` branch is recreated at its original tip.

## Done when…

- [ ] The fix commit is on `main` with a different SHA than on `feature`, and no feature commits leaked over.
- [ ] You recovered `feature` after `reset --hard` using a `HEAD@{N}` reference or the reflog SHA.
- [ ] You recreated the deleted `throwaway` branch.
- [ ] `answers.md` (with the SHAs and the reflog line you used) is committed under `c31-week-04/exercise-03/`.

## Stretch

- Cherry-pick a **range** of two commits at once using `A^..B` syntax, and confirm both land.
- Run `git fsck --lost-found` and find a dangling commit. Inspect it with `git show`.
- Explain, in `answers.md`, one thing the reflog **cannot** recover (hint: something never committed).
