# Week 3 — Homework

Six problems, ~5 hours total. Commit each to your portfolio under `c31-week-03/homework/`. Type the commands; don't paste.

---

## Problem 1 — Decode a clone (30 min)

Clone any small public repo (e.g. `gh repo clone octocat/Hello-World`, or use HTTPS). Then, **without looking at Lecture 1**, answer in `clone.md`:

```bash
git remote -v
git branch -a
git branch -vv
git config --get-regexp '^remote\.'
```

**Acceptance:**

- `clone.md` contains the output of all four commands.
- A short paragraph naming the five things `git clone` set up for you (remote, tracking branches, local branch, checkout, objects), each mapped to a line in the output above.
- One sentence explaining what the `fetch = +refs/heads/*:refs/remotes/origin/*` line means.

---

## Problem 2 — fetch vs. pull, in writing (45 min)

Using two clones of a local bare repo (as in Exercise 2), produce a demonstration in `fetch-vs-pull.md`:

1. Push a commit from clone A.
2. In clone B, run `git status` (should say up to date), then `git fetch`, then `git status` again (should say behind).
3. Capture all three outputs.

**Acceptance:**

- The three `git status` outputs are pasted in order.
- A one-paragraph explanation of *why* the first status was stale and the second was accurate.
- A precise statement of what `git pull` equals, in two commands.

---

## Problem 3 — Set up authentication for real (45 min)

Configure GitHub authentication on your machine, if you haven't already, using **either** a personal access token (HTTPS) **or** an SSH key.

**Acceptance:**

- `auth.md` describing which method you chose and why.
- For SSH: the output of `ssh -T git@github.com` showing successful authentication (the "Hi USERNAME!" line). For HTTPS: confirmation that a `git push` succeeded without typing a password (because a credential helper or `gh` cached it).
- **Do not paste any secret** — no tokens, no private keys. Just the success confirmation.
- One sentence on why you should never use your account password on the command line.

---

## Problem 4 — Push a real project to GitHub (45 min)

Create a small project locally (a few files — a README and any script), then get it onto GitHub in as few steps as you can.

**Acceptance:**

- The GitHub repo URL in `push-project.md`.
- The commands you ran (ideally including `gh repo create ... --source=. --push` for the one-shot version).
- `git branch -vv` output showing your `main` tracks the remote.
- One sentence contrasting the manual `git remote add` + `git push -u` route with the `gh repo create --push` shortcut.

---

## Problem 5 — Fork, sync, and inspect (60 min)

Fork a public repo (Spoon-Knife or another), clone it, add `upstream`, and sync.

**Acceptance:**

- `fork.md` with `git remote -v` showing `origin` + `upstream`.
- The full four-command sync (`fetch upstream` → `switch main` → `merge upstream/main` → `push origin main`) with output.
- Two sentences: what `origin` points at, what `upstream` points at, and which you can push to.
- One sentence explaining why a fork stops receiving upstream's commits automatically after it's created.

---

## Problem 6 — Rejected-push drill (75 min)

Reproduce a non-fast-forward rejection using two clones of a local bare repo (the setup is in Challenge 1), then recover **without force-pushing**.

**Acceptance:**

- `rejected.md` containing the exact rejection message you triggered.
- The `git log --oneline main..origin/main` and `origin/main..main` outputs proving the branches diverged.
- The integration strategy you used (merge or rebase) and the resulting `git log --oneline --graph`.
- Proof, from a fresh clone, that **both** commits survived.
- One sentence on why `--force` would have "worked" but been wrong.

---

## Submission checklist

- [ ] Six files committed under `c31-week-03/homework/`.
- [ ] No secrets anywhere (tokens, private keys) — grep your files before committing.
- [ ] Each file answers every "Acceptance" bullet, not just the commands.
- [ ] Pushed to your portfolio repo.

If a problem took you far longer than its estimate, note where you got stuck in a `reflections.md` — that's the most useful thing to bring to Week 4.
