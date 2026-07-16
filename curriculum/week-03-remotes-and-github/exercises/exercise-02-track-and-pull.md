# Exercise 2 — Track and Pull

**Goal:** Feel the difference between `git fetch` and `git pull` in your fingers. You'll set up branch tracking, simulate a teammate pushing, and watch how a stale remote-tracking branch updates. Everything is local — no network required.

**Estimated time:** 40 minutes.

## Setup — a shared "server" and two clones

This models two developers pushing to the same GitHub repo. `server.git` is GitHub; `alice` and `bob` are two laptops.

```bash
mkdir -p ~/c31-w3/ex02 && cd ~/c31-w3/ex02
git init --bare server.git

# Alice clones and seeds the repo
git clone ./server.git alice
cd alice
echo "line 1 — alice" > log.txt
git add log.txt
git commit -m "Start the log"
git push -u origin main
cd ..

# Bob clones the now-populated repo
git clone ./server.git bob
```

You now have two working copies (`alice`, `bob`) that both track the same `server.git`.

## The seven steps

Run these in order and **read every status message** — the whole exercise is about noticing what changes and when.

1. **Confirm Bob's tracking setup:**
   ```bash
   cd ~/c31-w3/ex02/bob
   git branch -vv
   ```
   Note that `main` tracks `origin/main`.

2. **Alice pushes a new commit** (simulating a teammate working ahead of you):
   ```bash
   cd ~/c31-w3/ex02/alice
   echo "line 2 — alice" >> log.txt
   git commit -am "Add line 2"
   git push
   ```

3. **Back as Bob, check status — Bob does NOT know yet:**
   ```bash
   cd ~/c31-w3/ex02/bob
   git status
   ```
   It says **"up to date."** This is the key moment: Bob's `origin/main` is *stale*. The server has moved; Bob's cached view has not.

4. **Fetch — download without touching Bob's files:**
   ```bash
   git fetch
   git status
   ```
   Now it says **"behind by 1 commit."** Notice `log.txt` did **not** change — `fetch` only updated the `origin/main` pointer.

5. **See exactly what's incoming before integrating:**
   ```bash
   git log --oneline main..origin/main    # commits on the server you don't have
   git diff main origin/main              # the actual line differences
   ```

6. **Integrate with a pull (this one is a clean fast-forward):**
   ```bash
   git pull
   cat log.txt        # now shows both lines
   ```

7. **Now cause a divergence** so you can see when `pull` does more than fast-forward. Both sides commit without the other knowing:
   ```bash
   # Alice commits
   cd ~/c31-w3/ex02/alice
   echo "line 3 — alice" >> log.txt
   git commit -am "Alice line 3"
   git push

   # Bob commits a DIFFERENT change, then pulls
   cd ~/c31-w3/ex02/bob
   echo "line 3 — bob" >> log.txt
   git commit -am "Bob line 3"
   git pull        # fetch + merge — this may open a merge or a conflict
   ```
   Because both edited the same line, expect a **merge conflict**. Resolve it (you learned this in Week 2): edit `log.txt`, `git add log.txt`, `git commit`, then `git push`.

## Expected result

- After step 4, `git status` reports "behind by 1" and `log.txt` is unchanged — proving `fetch` is non-destructive.
- After step 6, `log.txt` has both Alice's lines — proving `pull` integrated.
- Step 7 produces a real conflict you resolve by hand, showing that `pull` = `fetch` + `merge`, and merges can conflict.

## Done when…

- [ ] You observed `git status` say "up to date" while the server was actually ahead (step 3).
- [ ] You confirmed `git fetch` changed `origin/main` but **not** your working files (step 4).
- [ ] You listed the incoming commits with `git log main..origin/main` before integrating.
- [ ] You resolved the divergence conflict from step 7 and pushed the merge.
- [ ] You can explain, in one sentence, why step 3 lied and step 4 told the truth.

## Stretch

- Redo step 7 with `git pull --rebase` instead of the default. How does the resulting history differ? (Compare `git log --graph --oneline` in each case.)
- Set `git config pull.ff only` in Bob's repo, then trigger a divergent pull. What does Git refuse to do, and what does it tell you to run instead?
- Delete Bob's `origin/main` tracking ref knowledge with `git fetch --prune` after deleting a branch on the server. What does `--prune` clean up?

## Submission

Commit a `notes.md` to your portfolio under `c31-week-03/exercise-02/` with: the `git status` output from steps 3 and 4 side by side, and one sentence explaining why they differ.
