# Week 2 — Resources

Free, public, no signup. Curated for branching, merging, and conflicts specifically — save the general Git firehose for later weeks.

## Required reading

- **Pro Git, Ch. 3 — "Git Branching"** (the canonical free book, by Scott Chacon & Ben Straub): <https://git-scm.com/book/en/v2/Git-Branching-Branches-in-a-Nutshell>
  *Why:* The clearest published explanation of branches-as-pointers, with the exact diagrams this week's lectures echo. Read "Branches in a Nutshell" and "Basic Branching and Merging."
- **Pro Git — "Basic Merge Conflicts":** <https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging#_basic_merge_conflicts>
  *Why:* Walks a real conflict end to end; matches Lecture 3's workflow.

## Visual, interactive learning (do at least one)

- **Learn Git Branching** — an in-browser game where you type real Git commands and *watch* the commit graph move: <https://learngitbranching.js.org/>
  *Why:* The single best way to internalize pointers and merges this week. Do the "Introduction Sequence" and "Ramping Up." Non-negotiable if the graph still feels abstract.
- **Git Visualizer / `git-sim`** — render Git operations as diagrams before you run them: <https://github.com/initialcommit-com/git-sim>
  *Why:* Lets you *preview* what a merge or rebase would do to your graph. Great for the challenges.

## Official docs (bookmark these)

- **`git switch`:** <https://git-scm.com/docs/git-switch>
- **`git merge`:** <https://git-scm.com/docs/git-merge>
- **`git mergetool`:** <https://git-scm.com/docs/git-mergetool>
- **`git rerere`** (reuse recorded resolution): <https://git-scm.com/docs/git-rerere>
  *Why:* Turn to these when a flag's exact behavior matters. The `EXAMPLES` sections are gold.

## Deeper explainers

- **Atlassian — "Merging vs. Rebasing":** <https://www.atlassian.com/git/tutorials/merging-vs-rebasing>
  *Why:* The best free write-up of the decision you previewed in Lecture 2. Read it before Challenge 2.
- **Atlassian — "Git Branch":** <https://www.atlassian.com/git/tutorials/using-branches>
  *Why:* Clean, diagram-heavy reinforcement of create/switch/delete.
- **GitHub Docs — "Resolving a merge conflict using the command line":** <https://docs.github.com/en/get-started/using-git/resolving-a-merge-conflict-using-the-command-line>
  *Why:* The exact conflict workflow, from the platform you'll use in Week 3.
- **Julia Evans — "How git merges work":** <https://jvns.ca/blog/2024/01/05/do-we-think-of-git-commits-as-diffs--snapshots--or-histories-/> and her *"Oh sh*t, git!"* stories: <https://wizardzines.com/comics/>
  *Why:* Short, friendly, mental-model-first explainers. The wizardzines comics on merge/rebase are especially good.

## Tools to set up this week

- **A three-way merge tool.** Pick one and configure it once:
  - VS Code: `git config --global merge.tool vscode` (built-in three-way editor).
  - Meld (free GUI): <https://meldmerge.org/> — friendliest for beginners.
  - KDiff3 (free): <https://kdiff3.sourceforge.net/> — strong on big conflicts.
- **Recommended one-time config** (paste all of it):

  ```bash
  git config --global alias.lg "log --oneline --graph --all --decorate"
  git config --global merge.conflictStyle zdiff3    # show the base in conflicts
  git config --global mergetool.keepBackup false    # no .orig litter
  git config --global rerere.enabled true           # remember conflict resolutions
  ```

## Glossary

| Term | Definition |
|------|------------|
| **Branch** | A movable pointer to a commit. A 41-byte file under `.git/refs/heads/`. |
| **`HEAD`** | A pointer to your current branch (usually a symbolic ref); a raw hash when "detached." |
| **Detached HEAD** | `HEAD` points straight at a commit, not a branch. Commits made here aren't on any branch. |
| **Fast-forward** | A merge that just slides the branch pointer forward; no merge commit. Possible only when the destination is an ancestor of the source. |
| **Three-way merge** | Combines two diverged branches using their common ancestor (base); produces a merge commit with two parents. |
| **Merge base** | The most recent common ancestor of two branches — the "third" input to a three-way merge. |
| **Merge commit** | A commit with two (or more) parents, recording where two lines of history joined. |
| **Conflict** | Both branches changed the same region of the same file differently; Git refuses to guess and asks you. |
| **Ours / Theirs** | In a merge, "ours" = the branch you're on (`HEAD`, top of the marker); "theirs" = the branch being merged in (bottom). |
| **`rerere`** | "Reuse recorded resolution" — Git remembers how you resolved a conflict and replays it if it recurs. |

---

*Broken link? Open an issue.*
