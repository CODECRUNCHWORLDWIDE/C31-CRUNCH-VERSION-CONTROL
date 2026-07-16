# Exercise 2 — Force a three-way merge and inspect the merge commit

**Goal:** Make two branches genuinely diverge (both move), merge them, and then dissect the resulting **merge commit** — proving to yourself it has two parents.

**Estimated time:** 45 minutes.

## Setup

```bash
mkdir -p ~/git-w2/ex02 && cd ~/git-w2/ex02
git init
printf "line one\n" > notes.txt
git add notes.txt
git commit -m "Initial commit"
```

## Steps

1. **Branch off, and commit on the feature branch — editing a *different* file so there's no conflict:**

   ```bash
   git switch -c feature
   printf "feature work\n" > feature.txt
   git add feature.txt
   git commit -m "Add feature.txt"
   ```

2. **Now go back to `main` and commit there too.** This is the key move — it makes `main` advance *after* you branched, forcing divergence:

   ```bash
   git switch main
   printf "line two\n" >> notes.txt
   git add notes.txt
   git commit -m "Extend notes.txt on main"

   git lg
   ```

   You should now see the graph fork: `main` and `feature` each have a commit the other doesn't, sharing a common ancestor (the initial commit). Neither is an ancestor of the other — so a fast-forward is **impossible**.

3. **Find the merge base yourself** before merging, so you can predict what Git will use:

   ```bash
   git merge-base main feature
   # <hash of the initial commit>
   git log --oneline
   ```

   That common-ancestor commit is the "base" of the three-way merge.

4. **Predict, then merge.** Write down: *fast-forward or three-way?* (Both branches moved, so…). Then, from `main`:

   ```bash
   git merge feature
   ```

   Git opens your editor with a default message like `Merge branch 'feature'`. Save and close it. That editor step is your signal that a **merge commit is being created**.

5. **Inspect the merge commit's two parents:**

   ```bash
   git cat-file -p HEAD
   ```

   Look for **two `parent` lines**. Record both short hashes. The first parent is `main`'s previous tip ("ours"); the second is `feature`'s tip ("theirs"). Cross-check:

   ```bash
   git log --oneline           # note the tips before merging (do this earlier too)
   git rev-parse HEAD^1        # first parent  -> main's old tip
   git rev-parse HEAD^2        # second parent -> feature's tip
   ```

6. **See the shape and the merge-only view:**

   ```bash
   git lg                      # the two lines visibly rejoin at the merge commit
   git log --merges            # shows ONLY the merge commit
   git log --no-merges         # shows only the real-work commits, merge hidden
   git show HEAD               # the combined diff the merge introduced
   ```

## Expected result

- `git cat-file -p HEAD` shows **exactly two `parent` lines**.
- `git rev-parse HEAD^1` and `HEAD^2` resolve to the pre-merge tips of `main` and `feature` respectively.
- `git lg` shows a fork that rejoins at the merge commit.

## Done when…

- [ ] You forced a divergence by committing on **both** `main` and `feature`.
- [ ] You identified the merge base with `git merge-base` *before* merging.
- [ ] The merge opened an editor for a message (i.e., it was a real merge commit, not a fast-forward).
- [ ] You located both parents of the merge commit and can say which is "ours" and which is "theirs."
- [ ] You can explain why this merge could not fast-forward.

## Stretch

- Run `git log --first-parent --oneline`. How does following only first parents change the story the log tells?
- Undo the merge with `git reset --hard HEAD^` (this drops the merge commit; you're back to pre-merge `main`). Now re-merge with `git merge --no-ff feature` — is the result different from the plain merge? Why or why not, given the histories already diverged?
