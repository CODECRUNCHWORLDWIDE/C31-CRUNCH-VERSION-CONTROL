# Week 4 — Resources

Free, public, no signup. Everything here is either the official Git documentation or a high-quality free explainer. Read the "Required" section this week; keep the cheat sheets open in tabs.

## Required reading

- **Pro Git — "Git Tools: Rewriting History"** — the canonical chapter for this entire week: amend, rebase, filter, and squashing.
  <https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History>
  *Why this: it's the single best free source for everything Week 4 covers, written by the Git maintainers' community.*
- **Pro Git — "Reset Demystified"** — the definitive explanation of the three trees and what each reset mode does to them.
  <https://git-scm.com/book/en/v2/Git-Tools-Reset-Demystified>
  *Why this: Lecture 1's model comes straight from here; read it once and the resets stop being scary.*
- **Pro Git — "Data Recovery"** — reflog and fsck as a safety net.
  <https://git-scm.com/book/en/v2/Git-Internals-Maintenance-and-Data-Recovery>
  *Why this: the recovery techniques from Lecture 3, from the source.*

## Official command references

- **`git commit --amend`:** <https://git-scm.com/docs/git-commit#Documentation/git-commit.txt---amend>
  *Why this: the exact flags — `--no-edit`, `--reset-author` — you'll actually use.*
- **`git reset`:** <https://git-scm.com/docs/git-reset>
  *Why this: the mode tables in the man page are authoritative; bookmark them.*
- **`git rebase`** (see "interactive mode" and `--autosquash`): <https://git-scm.com/docs/git-rebase>
  *Why this: every to-do verb and the autosquash workflow, documented.*
- **`git cherry-pick`:** <https://git-scm.com/docs/git-cherry-pick>
  *Why this: range syntax (`A..B`, `A^..B`) and `--continue`/`--abort` behaviour.*
- **`git reflog`:** <https://git-scm.com/docs/git-reflog>
  *Why this: the `HEAD@{n}` syntax and expiry defaults spelled out.*
- **`git revert`:** <https://git-scm.com/docs/git-revert>
  *Why this: the safe, shared-history counterpart to reset.*

## Tutorials and explainers

- **Atlassian — "Rewriting history":** <https://www.atlassian.com/git/tutorials/rewriting-history>
  *Why this: clear diagrams of amend, rebase, and reflog; a gentle complement to Pro Git.*
- **Atlassian — "git rebase":** <https://www.atlassian.com/git/tutorials/rewriting-history/git-rebase>
  *Why this: the best free visual explanation of what "replay onto a new base" means.*
- **Atlassian — "Merging vs. Rebasing":** <https://www.atlassian.com/git/tutorials/merging-vs-rebasing>
  *Why this: settles the "when do I rebase vs. merge?" question with the Golden Rule.*
- **Julia Evans — "How git cherry-pick and revert use 3-way merge":** <https://jvns.ca/blog/2023/11/10/how-cherry-pick-and-revert-work/>
  *Why this: demystifies why cherry-pick and revert can conflict, from a beloved explainer.*
- **Julia Evans — "wizard zines: Oh shit, git!?!" (git comics):** <https://wizardzines.com/comics/>
  *Why this: short, memorable one-pagers — several are exactly the reset/reflog scenarios here.*

## Interactive practice (free, in-browser)

- **Learn Git Branching:** <https://learngitbranching.js.org/>
  *Why this: a visual sandbox where you can watch rebase and reset move the graph in real time. Do the "Rebase Introduction" and "Advanced Topics" levels this week.*
- **"Oh Shit, Git!?!" (the site):** <https://ohshitgit.com/>
  *Why this: a plain-English list of "I broke it, how do I fix it" recipes — most solved by reflog and reset.*

## Video (free)

- **MIT "Missing Semester" — Version Control (Git):** <https://missing.csail.mit.edu/2020/version-control/>
  *Why this: a rigorous, free lecture on Git's data model — reinforces why rewriting is safe.*

## Reference cards worth printing

| Command | Does | Danger |
|---------|------|--------|
| `git commit --amend` | Replace the last commit | Rewrites SHA — don't amend pushed commits |
| `git reset --soft HEAD~1` | Undo commit, keep changes staged | Safe |
| `git reset --mixed HEAD~1` | Undo commit, keep changes unstaged | Safe |
| `git reset --hard HEAD~1` | Undo commit, discard changes | Can lose uncommitted work |
| `git rebase -i <base>` | Reshape a range of commits | Rewrites SHAs — local only |
| `git rebase --abort` | Cancel an in-progress rebase | Safe — restores pre-rebase state |
| `git cherry-pick <sha>` | Copy one commit onto HEAD | Duplicates the change |
| `git revert <sha>` | New commit undoing an old one | Safe on shared branches |
| `git reflog` | List everywhere HEAD has been | Read-only — your undo map |
| `git push --force-with-lease` | Force-push, but refuse if remote moved | Safer than `--force`; solo branches only |

---

*Broken link? Open an issue or PR.*
