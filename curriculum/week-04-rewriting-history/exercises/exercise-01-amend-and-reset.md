# Exercise 1 — Amend and Reset Scenarios

**Goal:** Fix a commit with `--amend`, then run all three resets against the same starting point so you *feel* the difference between soft, mixed, and hard. By the end, the three-trees model is not a diagram — it's a reflex.

**Estimated time:** 40 minutes.

## Setup

```bash
mkdir -p /tmp/c31-w4/ex1 && cd /tmp/c31-w4/ex1
git init -q
printf 'line one\n'   > notes.txt && git add . && git commit -qm "add first line"
printf 'line two\n'  >> notes.txt && git add . && git commit -qm "add secnd line"   # typo on purpose
printf 'line three\n'>> notes.txt && git add . && git commit -qm "add third line"
git log --oneline
```

You now have three commits, and commit two has a deliberate typo in its message ("secnd").

## Part A — Amend

1. **Fix the last commit's message.** The third commit is fine, but pretend you want it to say `add third line (final)`:

   ```bash
   git commit --amend -m "add third line (final)"
   ```

   Run `git log --oneline`. Note the SHA of the top commit **before and after** — record both in `answers.md`. They differ. Explain in one sentence *why* amending changed the SHA.

2. **Amend to add a forgotten file.** You forgot to include a `README`:

   ```bash
   echo "# Notes project" > README.md
   git add README.md
   git commit --amend --no-edit
   git show --stat HEAD          # README.md is now part of the third commit
   ```

   Confirm `README.md` appears in the commit, and that no separate "add readme" commit exists in `git log`.

## Part B — The three resets

Reset the same distance three times, restoring between each so you compare fairly. Watch `git status` after every reset.

1. **Soft.** Undo the last commit but keep its changes staged:

   ```bash
   git reset --soft HEAD~1
   git status                    # changes "to be committed"
   git log --oneline             # one fewer commit
   ```

   In `answers.md`: where are the changes now — staged, unstaged, or gone?

   Restore before the next step:

   ```bash
   git reset --soft HEAD@{1}     # move HEAD back using the reflog
   ```

2. **Mixed** (the default). Undo the last commit, keep the changes but unstaged:

   ```bash
   git reset HEAD~1              # no flag = --mixed
   git status                    # changes "not staged for commit"
   ```

   Record where the changes are now. Then restage and recommit to restore:

   ```bash
   git add . && git commit -qm "add third line (final)"
   ```

3. **Hard** — the destructive one. First look at the file, then reset:

   ```bash
   cat notes.txt                 # three lines
   git reset --hard HEAD~1
   cat notes.txt                 # the third line is GONE
   git status                    # clean
   ```

   In `answers.md`: what happened to "line three", and why can `--hard` lose work when `--soft` and `--mixed` cannot?

## Part C — Recover what the hard reset "destroyed"

You just discarded a commit. Get it back — the reflog kept it.

```bash
git reflog                       # find the commit you hard-reset away
git reset --hard HEAD@{1}        # or use the SHA the reflog shows
cat notes.txt                    # three lines again — fully recovered
```

Note in `answers.md` which `HEAD@{N}` (or SHA) you used and how you identified it.

## Expected result

- `answers.md` records the before/after SHAs from Part A and a one-line "why."
- You can state, in your own words, where changes land after soft vs. mixed vs. hard.
- `notes.txt` ends with all three lines, recovered via reflog after the hard reset.

## Done when…

- [ ] You amended both a message and a forgotten file into the last commit — no extra "fix" commits in `git log`.
- [ ] You ran all three resets and recorded where the changes went for each.
- [ ] You explained why `--hard` can lose work and the other two can't.
- [ ] You recovered the hard-reset commit using the reflog, and `notes.txt` has three lines again.
- [ ] `answers.md` is committed under `c31-week-04/exercise-01/`.

## Stretch

- Use `git reset --soft HEAD~2` then a single `git commit` to squash two commits into one. Confirm no changes were lost.
- Run `git reflog` and count how many entries a single amend + three resets produced. Explain what each line means.
