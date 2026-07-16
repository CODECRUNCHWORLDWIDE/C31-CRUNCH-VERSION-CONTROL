# Challenge 1 — Recover from a Rejected Push

**Time:** ~75 minutes. **Difficulty:** Medium.

## The scenario

You finish a feature, commit, and run `git push`. Instead of success, Git prints:

```
 ! [rejected]        main -> main (non-fast-forward)
error: failed to push some refs to 'https://github.com/you/repo.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
```

Someone (or a past you, from another machine) pushed to `main` while you were working. The server's `main` has a commit your local `main` doesn't. Git refuses the push because moving the server pointer to your commit would **orphan** the commit already there — silently losing it.

Your job: get *both* your work and the existing commit onto the server, safely. There is a tempting, wrong answer (`git push --force`) that makes the error vanish by throwing the other commit away. Avoiding it is the whole challenge.

## Reproduce it yourself (local, no network)

You can manufacture this exact rejection with a bare repo and two clones:

```bash
mkdir -p ~/c31-w3/chal01 && cd ~/c31-w3/chal01
git init --bare server.git

git clone ./server.git me
cd me
echo "shared line" > notes.txt
git add notes.txt && git commit -m "Base commit"
git push -u origin main
cd ..

# A "colleague" clones and pushes first
git clone ./server.git colleague
cd colleague
echo "colleague's important work" >> notes.txt
git commit -am "Colleague: add important line"
git push
cd ..

# Meanwhile YOU committed locally, unaware
cd me
echo "my new feature" >> notes.txt
git commit -am "Me: add my feature"
git push        # <-- REJECTED, non-fast-forward
```

That final `git push` reproduces the rejection. Now recover.

## What to do

Diagnose first, act second. Do not run anything destructive until you understand the state.

1. **See the divergence.** After fetching, both branches have a commit the other lacks:
   ```bash
   git fetch origin
   git log --oneline --graph --all
   git log --oneline main..origin/main    # what's on the server you don't have
   git log --oneline origin/main..main    # what you have that the server doesn't
   ```

2. **Choose an integration strategy** and apply it:
   - **Merge:** `git merge origin/main` — creates a merge commit tying both histories together. Simple, always works, leaves a visible merge.
   - **Rebase:** `git rebase origin/main` — replays *your* commit on top of the colleague's, giving a linear history. Cleaner log, but you're rewriting your local commit (fine, since it was never pushed).
   - Either way you may hit a conflict on `notes.txt` (both edited it). Resolve it, `git add notes.txt`, and finish (`git commit` for merge, `git rebase --continue` for rebase).

3. **Push again** — now it's a fast-forward and succeeds:
   ```bash
   git push
   ```

4. **Verify nothing was lost:**
   ```bash
   git clone ./server.git verify && cd verify
   git log --oneline --graph
   cat notes.txt
   ```
   Both the colleague's line and your feature must be present.

## Constraints

- **`git push --force` and `git push --force-with-lease` are banned** for this challenge. The colleague's commit must survive. (You'll learn *legitimate* force-push uses in Week 4 — this isn't one of them.)
- You may use merge or rebase — but you must be able to explain in your writeup *why* your choice was safe here.
- Resolve any conflict by keeping **both** contributions, not by deleting one side.

## Hints

<details>
<summary>Why does force-push "work" but destroy data?</summary>

`git push --force` tells the server "make `main` point at my commit, no matter what." Since your commit's history doesn't include the colleague's, the colleague's commit becomes unreachable — still in the object database briefly, but orphaned and eventually garbage-collected. The colleague's `git pull` would then either fail or silently lose their work. Integrating first (merge/rebase) makes your branch a *descendant* of theirs, so the push is a safe fast-forward.
</details>

<details>
<summary>Merge or rebase — how do I choose?</summary>

For shared branches like `main`, a merge is the conservative default: it never rewrites commits. Rebase gives a cleaner line but rewrites your local commits — perfectly safe as long as those commits were never pushed anywhere (they weren't). Many teams standardize on `git pull --rebase` for exactly this everyday case.
</details>

## How success is judged

Write `writeup.md` covering:

- [ ] The exact error you got and a one-paragraph explanation of *why* Git rejected the push (in terms of fast-forward and orphaned commits).
- [ ] The two `git log A..B` outputs proving the branches diverged.
- [ ] Which integration strategy you chose and **why it was safe**.
- [ ] Proof from the `verify` clone that both commits survived (paste the `git log --oneline --graph`).
- [ ] One sentence on when, if ever, `--force-with-lease` *would* be the right tool (foreshadowing Week 4).

## Submission

Commit `writeup.md` to your portfolio under `c31-week-03/challenge-01/`.
