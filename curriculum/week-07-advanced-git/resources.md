# Week 7 — Resources

Free, official-first, no signup unless noted. Read the official docs before the blog posts.

## Hooks

- **Git hooks reference (official)** — every hook, when it fires, what it's passed:
  <https://git-scm.com/docs/githooks> — the normative list; bookmark it.
- **Pro Git — "Customizing Git: Git Hooks"** — the readable introduction with examples:
  <https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks>
- **`pre-commit` framework** — the most popular way to share hooks across a team via one config file:
  <https://pre-commit.com/> — start here if you don't want to hand-roll scripts.
- **lefthook** — a fast, language-agnostic hook manager (Go binary, no runtime dependency):
  <https://github.com/evilmartians/lefthook> — worth comparing against `pre-commit`.

## Bisect

- **`git bisect` reference (official)** — the full command surface including `run`, `skip`, `log`, `replay`:
  <https://git-scm.com/docs/git-bisect> — read the `run` section carefully for the exit-code contract.
- **Pro Git — "Debugging with Git: Binary Search"** — the concept explained with a worked example:
  <https://git-scm.com/book/en/v2/Git-Tools-Debugging-with-Git> — the clearest short intro.

## Worktrees

- **`git worktree` reference (official)** — `add`, `list`, `remove`, `prune`, and the locking rules:
  <https://git-scm.com/docs/git-worktree> — the source of truth for the "one branch, one worktree" rule.

## Submodules (and alternatives)

- **Pro Git — "Git Tools: Submodules"** — the honest, thorough walkthrough including the pitfalls:
  <https://git-scm.com/book/en/v2/Git-Tools-Submodules> — read before adopting submodules anywhere.
- **`git submodule` reference (official)**:
  <https://git-scm.com/docs/git-submodule> — command details for `add`, `update`, `--recurse-submodules`.
- **`git subtree` reference (official)** — the usual, less-painful alternative to submodules:
  <https://git-scm.com/docs/git-subtree> — why cloners never hit "empty directory" surprises.

## Tags, SemVer, and releases

- **Semantic Versioning 2.0.0 (the spec)** — the exact rules for MAJOR/MINOR/PATCH, pre-release, and build metadata:
  <https://semver.org/> — short enough to read in full; do so once.
- **Pro Git — "Git Basics: Tagging"** — lightweight vs. annotated, creating, pushing, deleting:
  <https://git-scm.com/book/en/v2/Git-Basics-Tagging> — clears up why `git push` ignores tags.
- **`git tag` reference (official)**:
  <https://git-scm.com/docs/git-tag>
- **`gh release` manual (GitHub CLI)** — `create`, `--generate-notes`, uploading artifacts, drafts, pre-releases:
  <https://cli.github.com/manual/gh_release> — the command you'll use to actually ship.
- **GitHub docs — "Managing releases in a repository"** — the web-UI side and how releases relate to tags:
  <https://docs.github.com/en/repositories/releasing-projects-on-github/managing-releases-in-a-repository>
- **Keep a Changelog** — a sane, human-readable changelog convention that pairs with SemVer:
  <https://keepachangelog.com/>

## Videos and deep dives (free)

- **MIT "Missing Semester" — Version Control (Git)** — a fast, dense tour that touches bisect and internals:
  <https://missing.csail.mit.edu/2020/version-control/> — supplementary, not a replacement.

## Glossary

| Term | Definition |
|------|------------|
| **Hook** | An executable Git runs at a lifecycle event (e.g. `pre-commit`). Lives in `.git/hooks/`; not cloned. |
| **Blocking hook** | A hook whose non-zero exit **aborts** the operation (e.g. `pre-commit`, `commit-msg`, `pre-push`, `pre-receive`). |
| **`--no-verify`** | Flag that skips client-side commit hooks. Why unskippable rules belong in CI. |
| **`git bisect`** | Binary search over history to find the first commit exhibiting a property (usually a bug). |
| **`bisect run`** | Automated bisect driven by a script whose exit code judges each commit (0 good, 125 skip, else bad). |
| **Worktree** | An additional working directory of one repo, on its own branch, sharing the object database. |
| **Submodule** | A pointer to a specific commit of another repo, embedded at a path in the superproject. |
| **`.gitmodules`** | Tracked file recording each submodule's URL and path. |
| **Lightweight tag** | A bare name pointing at a commit; no metadata. |
| **Annotated tag** | A full tag object with tagger, date, message, and optional signature. Use for releases. |
| **SemVer** | `MAJOR.MINOR.PATCH` versioning where the number encodes the risk of upgrading. |
| **Release** | A GitHub layer on a tag: notes plus downloadable artifacts. Built on a tag; not every tag is one. |
| **`git describe`** | Names a commit by its distance from the nearest tag, e.g. `v1.2.0-14-g2414bc4`. |

---

*Broken link? Open an issue or PR.*
