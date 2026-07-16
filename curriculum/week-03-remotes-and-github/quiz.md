# Week 3 — Quiz

Twelve questions. Lectures closed. Aim for 10/12 before Week 4.

---

**Q1.** A remote in Git is best described as:

- A) A copy of your working tree on another disk.
- B) A short name bound to a URL, plus remote-tracking branches caching the server's state.
- C) A branch that only exists on GitHub.
- D) The `.git` directory compressed for transport.

---

**Q2.** The name `origin` is:

- A) A reserved keyword Git treats specially.
- B) Required for `git push` to work.
- C) Just a conventional default name for the remote you cloned from.
- D) The name of your default branch.

---

**Q3.** `git clone URL` does **not**:

- A) Create a remote named `origin`.
- B) Push your local commits to the server.
- C) Create remote-tracking branches like `origin/main`.
- D) Check out the default branch.

---

**Q4.** `origin/main` is:

- A) A branch you can check out and commit to directly.
- B) The live state of `main` on the server, always current.
- C) A read-only local pointer, updated only when you fetch.
- D) The same object as your local `main`.

---

**Q5.** `git fetch`:

- A) Downloads new objects and updates remote-tracking branches, but does not change your working tree.
- B) Downloads and merges in one step.
- C) Uploads your commits to the server.
- D) Deletes local branches that no longer exist on the server.

---

**Q6.** `git pull` is equivalent to:

- A) `git fetch` followed by `git push`.
- B) `git fetch` followed by `git merge` (by default).
- C) `git clone` followed by `git checkout`.
- D) `git merge` followed by `git fetch`.

---

**Q7.** After committing on a new branch, `git push` errors with "no upstream branch." The fix is:

- A) `git push --force`.
- B) `git commit --amend`.
- C) `git push -u origin <branch>`.
- D) `git remote add origin <url>`.

---

**Q8.** You should authenticate to GitHub over HTTPS using:

- A) Your GitHub account password.
- B) A personal access token.
- C) Your SSH private key pasted into the terminal.
- D) No authentication is needed for pushing.

---

**Q9.** Which key do you upload to GitHub when setting up SSH?

- A) The private key (`id_ed25519`).
- B) The public key (`id_ed25519.pub`).
- C) Both keys.
- D) Neither; you paste a token instead.

---

**Q10.** In a fork workflow, `upstream` conventionally points at:

- A) Your fork, which you push to.
- B) The original repository you forked from, which you fetch from.
- C) A backup mirror of your fork.
- D) Your local `main` branch.

---

**Q11.** Your push is rejected as a "non-fast-forward." The safe response is:

- A) `git push --force` to overwrite the server.
- B) Delete the remote branch and re-push.
- C) Fetch, integrate the remote changes (merge or rebase), then push again.
- D) Clone a fresh copy and start over.

---

**Q12.** Why must you periodically sync a fork with upstream?

- A) GitHub deletes forks that aren't synced.
- B) A fork stops receiving upstream's new commits automatically after it's created.
- C) Syncing is required before every commit.
- D) Otherwise your SSH key expires.

---

## Answer key

<details>
<summary>Reveal after attempting</summary>

1. **B** — a remote is a name + URL + the remote-tracking branches that cache the server's state.
2. **C** — `origin` is just the conventional default; you can rename or replace it freely.
3. **B** — clone downloads and sets everything up, but it does **not** push; there's nothing local to push yet.
4. **C** — `origin/main` is a read-only local cache, moved only by `git fetch` (or `pull`, which fetches).
5. **A** — `fetch` downloads and updates `origin/*` without touching your working tree or local branches.
6. **B** — `git pull` = `git fetch` + `git merge` by default (or `+ rebase` with `--rebase`).
7. **C** — `git push -u origin <branch>` sets the upstream so future pushes need no arguments.
8. **B** — a personal access token. GitHub removed password auth for Git operations in 2021.
9. **B** — the **public** key (`.pub`). The private key never leaves your machine.
10. **B** — `upstream` is the original repo you fetch from; `origin` is your fork you push to.
11. **C** — fetch and integrate first, then push. `--force` would orphan the other person's commit.
12. **B** — a fork is a point-in-time copy; it doesn't track upstream automatically, so you sync it yourself.

</details>

If you scored 10+: move to the mini-project and Week 4. 7–9: re-read the lecture sections behind your misses. <7: re-read all three lectures from the top before continuing.
