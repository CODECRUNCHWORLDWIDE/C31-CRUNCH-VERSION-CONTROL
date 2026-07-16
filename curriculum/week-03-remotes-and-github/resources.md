# Week 3 — Resources

Free, public, no signup unless noted. Official docs first, then the best supplementary material.

## Required reading

- **Pro Git — "Working with Remotes"** — the canonical explanation of remotes, `origin`, fetch, pull, and push. Free, book-quality:
  <https://git-scm.com/book/en/v2/Git-Basics-Working-with-Remotes>
  *Why this:* it's the reference the whole lecture is built on; read it once end to end.
- **Pro Git — "Remote Branches"** — remote-tracking branches, tracking, and the ahead/behind model:
  <https://git-scm.com/book/en/v2/Git-Branching-Remote-Branches>
  *Why this:* the clearest treatment of the `origin/main` vs. `main` distinction anywhere.
- **GitHub Docs — "About forks"** — what a fork is and how the two-remote model works:
  <https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/about-forks>
  *Why this:* GitHub's own words on the model you'll use for the challenges and mini-project.

## Authentication (do this once, benefit all course)

- **GitHub Docs — "Managing your personal access tokens"** — how to create and scope a PAT:
  <https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens>
  *Why this:* the HTTPS auth path, straight from the source, with the fine-grained token flow.
- **GitHub Docs — "Connecting to GitHub with SSH"** — generate a key, add it, test it:
  <https://docs.github.com/en/authentication/connecting-to-github-with-ssh>
  *Why this:* the SSH auth path; the "Generating a new SSH key" and "Adding a new SSH key" sub-pages are all you need.
- **GitHub Docs — "About authentication to GitHub"** — the overview that helps you choose HTTPS vs. SSH:
  <https://docs.github.com/en/authentication>
  *Why this:* decision-level guidance before you commit to a method.

## The `gh` CLI

- **GitHub CLI — install:** <https://github.com/cli/cli#installation>
  *Why this:* per-OS install commands (`brew install gh`, `winget install GitHub.cli`, apt/dnf repos).
- **GitHub CLI manual:** <https://cli.github.com/manual/>
  *Why this:* the full command reference — `gh repo`, `gh pr`, `gh auth` are the ones you'll live in this week.
- **`gh auth login`:** <https://cli.github.com/manual/gh_auth_login>
  *Why this:* the single command that sets up both auth and Git's credential helper.

## Command references (keep open in tabs)

- **`git remote`:** <https://git-scm.com/docs/git-remote> — managing remotes and their URLs.
- **`git fetch`:** <https://git-scm.com/docs/git-fetch> — read the "refspec" section once.
- **`git pull`:** <https://git-scm.com/docs/git-pull> — note the `--rebase` and `--ff-only` options.
- **`git push`:** <https://git-scm.com/docs/git-push> — the `-u`, `--delete`, and `--force-with-lease` flags.
- **`git clone`:** <https://git-scm.com/docs/git-clone> — the `--depth`, `--branch`, and `--bare` options.

## Forks and pull requests

- **GitHub Docs — "Fork a repo":** <https://docs.github.com/en/get-started/quickstart/fork-a-repo>
  *Why this:* the step-by-step fork + `upstream` setup, mirroring Lecture 3.
- **GitHub Docs — "Syncing a fork":** <https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/syncing-a-fork>
  *Why this:* the web-UI, `gh`, and command-line ways to keep a fork current, side by side.
- **GitHub Docs — "Creating a pull request from a fork":** <https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-a-pull-request-from-a-fork>
  *Why this:* exactly the flow the mini-project asks for.

## Practice repositories

- **`octocat/Spoon-Knife`** — GitHub's official fork-practice repo: <https://github.com/octocat/Spoon-Knife>
  *Why this:* built to be forked; safe to experiment on.
- **`firstcontributions/first-contributions`** — a guided first-PR repo: <https://github.com/firstcontributions/first-contributions>
  *Why this:* your practice PR is expected and welcome; ideal for Challenge 2.

## Deeper / optional

- **Atlassian — "Syncing with `git fetch`"** and the "Comparing workflows" series: <https://www.atlassian.com/git/tutorials/syncing>
  *Why this:* clear diagrams of fetch/pull/push if the prose version hasn't clicked yet.
- **MIT Missing Semester — "Version Control (Git)":** <https://missing.csail.mit.edu/2020/version-control/>
  *Why this:* a fast, opinionated tour that covers remotes among much else — good supplementary video.
- **`git-scm` — "Git on the Server – The Protocols":** <https://git-scm.com/book/en/v2/Git-on-the-Server-The-Protocols>
  *Why this:* if you're curious *how* HTTPS and SSH actually move objects under the hood.

## Glossary

| Term | Definition |
|------|------------|
| **Remote** | A named URL bound to another copy of the repo, plus its remote-tracking branches. |
| **`origin`** | The conventional name for the remote you cloned from (or your fork). Not special. |
| **`upstream`** | The conventional name for the original repo you forked from. Fetch-only in practice. |
| **Remote-tracking branch** | A read-only local pointer (`origin/main`) caching the server's branch as of your last fetch. |
| **Tracking / upstream branch** | The link between a local branch and the remote branch it pushes/pulls against. |
| **Fetch** | Download new objects and update remote-tracking branches; leaves your files untouched. |
| **Pull** | `fetch` + integrate (`merge` by default, or `rebase`). |
| **Push** | Upload local commits and advance the server's branch — only if fast-forward (unless forced). |
| **Fast-forward** | Moving a branch pointer straight forward because the target is a direct descendant. |
| **Fork** | A server-side copy of a repo under your account that you can push to. |
| **Pull request (PR)** | A request to merge one branch/fork into another repo's branch, with review. |
| **Personal access token (PAT)** | A scoped, revocable secret used as your password for HTTPS Git operations. |
| **SSH key pair** | A private key (kept secret) + public key (uploaded to GitHub) used to authenticate. |

---

*Broken link? Open an issue.*
