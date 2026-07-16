# Exercise 3 — Create and resolve a real conflict

**Goal:** Deliberately manufacture a merge conflict — the same lines of the same file changed differently on two branches — then resolve it three ways: by hand, by taking one side, and with the `zdiff3` view showing the base.

**Estimated time:** 50 minutes.

## Setup

```bash
mkdir -p ~/git-w2/ex03 && cd ~/git-w2/ex03
git init

cat > config.py <<'EOF'
DEBUG = False
TIMEOUT = 30
RETRIES = 3
EOF

git add config.py
git commit -m "Initial config"
```

## Part A — Manufacture the conflict

1. **Branch off and change `TIMEOUT` on the feature branch:**

   ```bash
   git switch -c tune-timeout
   sed -i.bak 's/TIMEOUT = 30/TIMEOUT = 60/' config.py && rm config.py.bak
   git add config.py
   git commit -m "Raise timeout to 60"
   ```

2. **Go back to `main` and change the SAME line, differently:**

   ```bash
   git switch main
   sed -i.bak 's/TIMEOUT = 30/TIMEOUT = 45/' config.py && rm config.py.bak
   git add config.py
   git commit -m "Raise timeout to 45"
   ```

   Both branches changed `TIMEOUT`, to different values, from the same base of `30`. This is the exact recipe for a conflict.

3. **Merge and watch it conflict:**

   ```bash
   git merge tune-timeout
   # CONFLICT (content): Merge conflict in config.py
   # Automatic merge failed; fix conflicts and then commit the result.

   git status                 # config.py under "Unmerged paths" -> "both modified"
   ```

## Part B — Resolve by hand

4. **Open `config.py`.** You'll see:

   ```
   <<<<<<< HEAD
   TIMEOUT = 45
   =======
   TIMEOUT = 60
   >>>>>>> tune-timeout
   ```

   Confirm you can read it: **top (`HEAD`) is `main`'s `45` ("ours"); bottom is `tune-timeout`'s `60` ("theirs").**

5. **Decide the final value** — say you want `60`. Edit the file so the whole block becomes a single correct line and **all three markers are gone**:

   ```python
   TIMEOUT = 60
   ```

6. **Verify no markers remain, stage, and complete the merge:**

   ```bash
   grep -n '<<<<<<<\|=======\|>>>>>>>' config.py    # should print nothing
   git add config.py
   git status                                        # no more unmerged paths
   git commit                                        # completes the merge
   git lg
   ```

## Part C — Redo it taking one side wholesale

7. **Reset and reproduce the conflict**, then resolve by taking "theirs" in one command:

   ```bash
   git reset --hard HEAD~1        # drop the merge commit you just made
   git merge tune-timeout         # conflict again
   git checkout --theirs config.py    # take tune-timeout's entire version
   git add config.py
   git commit                     # done — no hand-editing
   ```

   Try it once with `--ours` too, on a fresh reproduce, and confirm which value survives.

## Part D — Turn on the base view (`zdiff3`)

8. **Enable the three-way conflict style, reproduce, and look for the base:**

   ```bash
   git config merge.conflictStyle zdiff3
   git reset --hard HEAD~1
   git merge tune-timeout
   cat config.py
   ```

   Now the conflict includes a `||||||| base` section showing the original `TIMEOUT = 30`. Seeing that both sides changed it *from 30* is exactly the context you need to make a good call. Resolve however you like and finish the merge.

9. **Rescue button drill:** reproduce the conflict one more time, then instead of resolving, run:

   ```bash
   git merge --abort
   git status                     # clean — as if you never merged
   ```

   Internalize that you're never trapped in a conflict.

## Expected result

- You produced the conflict on purpose and can point to the two commits that caused it.
- You resolved it (a) by hand, (b) with `--theirs`, and (c) after enabling `zdiff3`.
- You aborted a conflict cleanly with `git merge --abort`.

## Done when…

- [ ] You can point to which line is "ours" and which is "theirs" in a marker block, and name the branch each belongs to.
- [ ] You resolved a conflict by editing the file until `grep` finds no markers, then staging and committing.
- [ ] You used `git checkout --theirs` (or `--ours`) to take one side in one command.
- [ ] You enabled `zdiff3` and identified the base value in a conflict.
- [ ] You aborted a conflicted merge with `git merge --abort` and returned to a clean state.

## Stretch

- Turn on `git config rerere.enabled true`, resolve this conflict, `reset --hard HEAD~1`, and re-merge. Did Git remember your resolution? What did it print?
- Configure a GUI mergetool (`git config merge.tool meld` or `vscode`), reproduce the conflict, and resolve it with `git mergetool`. Compare the experience to editing markers by hand.
