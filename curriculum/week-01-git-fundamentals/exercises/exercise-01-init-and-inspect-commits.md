# Exercise 1 — Init a repo and inspect your commits

**Goal:** Create a repository from scratch, make three commits, and prove to yourself what each commit actually stored. By the end you can read a commit as an object, not a mystery.

**Estimated time:** 40 minutes. **Do it after:** Lecture 2.

## Setup

```bash
mkdir -p ~/c31-w1/exercise-01
cd ~/c31-w1/exercise-01
git init
```

`git init` created a `.git/` directory. Peek at it before you do anything else:

```bash
ls -a1 .git
cat .git/HEAD          # ref: refs/heads/main  (points at a branch that has no commit yet)
git status            # "No commits yet"
```

## Part A — Three commits

Make a first commit:

```bash
echo '# My Project' > README.md
git add README.md
git status            # README.md under "Changes to be committed"
git commit -m "docs: add README"
```

Add a source file in a subdirectory, then commit:

```bash
mkdir src
printf 'def main():\n    print("hello")\n' > src/app.py
git add src/app.py
git commit -m "feat: add app entry point"
```

Change the README and commit a third time:

```bash
printf '\nRun with: python src/app.py\n' >> README.md
git add README.md
git commit -m "docs: document how to run"
```

## Part B — Read the history

```bash
git log --oneline --graph          # three commits, newest on top
git log --stat                     # which files each commit touched
git show HEAD                      # the last commit + its diff
git show HEAD~2                    # your first commit + its diff
```

Answer in a file `answers.md`:

1. In `git log --oneline`, how many characters is each short SHA? Are they all unique?
2. In `git show HEAD~2`, what does the diff show being added — and what is the "parent" line? (Hint: the first commit has no parent.)

## Part C — Inspect what got stored

Now prove a commit is a pointer to a tree, which points at blobs:

```bash
git cat-file -t HEAD               # commit
git cat-file -p HEAD               # read the commit object
git cat-file -p HEAD^{tree}        # read its root tree
git cat-file -p HEAD^{tree}:src 2>/dev/null || git ls-tree HEAD src
```

Then read the README's contents straight out of history, without checking anything out:

```bash
git show HEAD:README.md            # current version
git show HEAD~2:README.md          # the very first version
```

In `answers.md`, record:

3. The SHA of the **root tree** of `HEAD`. (From the first line of `git cat-file -p HEAD`.)
4. The SHA of the **blob** for `src/app.py`. (From `git ls-tree HEAD src`.)
5. Confirm `git cat-file -p <that-blob-sha>` prints your Python file. Paste the output.

## Expected result

- A repo with exactly three commits, viewable as a clean graph.
- An `answers.md` containing the five answers above with real SHAs from *your* repo.
- You can state, in one sentence, what each of the three commits stored.

## Done when…

- [ ] `git log --oneline` shows three commits in teaching order.
- [ ] `answers.md` has all five answers with SHAs copied from your machine.
- [ ] You retrieved the first version of `README.md` with `git show HEAD~2:README.md`.
- [ ] You can explain why `git cat-file -p HEAD` shows a `tree` line but no file contents.

## Stretch

- Run `git count-objects -v`. How many objects does your three-commit repo hold? Reason about why (blobs + trees + commits).
- Create a fourth commit that only *renames* `src/app.py` to `src/main.py` (`git mv`). Does the blob SHA change? Why or why not? (Re-read Lecture 1 §3 if unsure.)

## Submission

Commit `answers.md` into the repo and note the final `git log --oneline` output in your portfolio under `c31-week-01/exercise-01/`.
