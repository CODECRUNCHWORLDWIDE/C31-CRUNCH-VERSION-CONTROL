# Week 1 — Resources

Free, public, no signup. Read the "Required" section this week; treat the rest as a reference you return to.

## Required reading

- **Pro Git, Ch. 10 — "Git Internals: Git Objects"** — the canonical explanation of blobs, trees, commits, and content-addressing. If you read one thing, read this:
  <https://git-scm.com/book/en/v2/Git-Internals-Git-Objects>
- **Pro Git, Ch. 10 — "Git References"** — how branches, HEAD, and tags are just files pointing at SHAs:
  <https://git-scm.com/book/en/v2/Git-Internals-Git-References>
- **Pro Git, Ch. 7.7 — "Reset Demystified"** — the definitive three-trees walkthrough; pairs directly with Lecture 2:
  <https://git-scm.com/book/en/v2/Git-Tools-Reset-Demystified>

## Deep dives (the "aha" material)

- **"Git from the inside out" — Mary Rose Cook** — builds the object graph up from first principles; the best long-form explainer of the model:
  <https://maryrosecook.com/blog/post/git-from-the-inside-out>
- **"Building Git" — James Coglan (book site)** — reimplements Git in Ruby; even just the sample chapters cement the object model:
  <https://shop.jcoglan.com/building-git/>
- **"Git internals" section of the official docs (`git help`):** run `git help cat-file`, `git help rev-parse`, `git help ls-tree`, `git help glossary` — short, authoritative, offline.

## Interactive practice

- **"Learn Git Branching"** — visualizes commits, trees, and refs as you type commands; the sandbox is perfect for Week 1 intuition (branch content is Week 2, but the object graph view helps now):
  <https://learngitbranching.js.org/>
- **"Oh My Git!"** — a free open-source game that renders the real object graph as you play:
  <https://ohmygit.org/>

## Cheat sheets (keep open in a tab)

- **GitHub Git cheat sheet (PDF):** <https://education.github.com/git-cheat-sheet-education.pdf>
- **`git help everyday`** — the "everyday Git" command set, built into your install: run `git help everyday`.

## Reference / official

- **The official Git documentation home:** <https://git-scm.com/doc>
- **Git reference manual (every command):** <https://git-scm.com/docs>
- **SHA-1 collision + Git's mitigation (background on why SHA-256 exists):** <https://git-scm.com/docs/hash-function-transition>

## Videos (free)

- **MIT "Missing Semester" — "Version Control (Git)"** — a single lecture that covers the model plus daily use; excellent companion to this week:
  <https://missing.csail.mit.edu/2020/version-control/>

## Install / setup

- **Install Git (all platforms):** <https://git-scm.com/downloads>
- **GitHub CLI (`gh`)** — used from Week 3 onward; install it now: <https://cli.github.com/>
- **First-time setup** (identity + editor): <https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup>

## Glossary

| Term | Definition |
|------|------------|
| **Object** | A blob, tree, commit, or tag stored in `.git/objects/`, named by the SHA of its content. |
| **Blob** | The raw bytes of a file — no name, no metadata. |
| **Tree** | A directory listing: `mode type sha name` per entry; points at blobs and other trees. |
| **Commit** | A snapshot: points at one root tree, zero or more parent commits, plus author/message. |
| **Content-addressing** | Naming an object by the hash of its content, so identical content shares one object. |
| **SHA-1 / SHA-256** | The hash functions Git uses to name objects (40 / 64 hex chars). |
| **Working tree** | The actual files on disk you edit. |
| **Index (staging area)** | `.git/index` — the draft of your next commit. |
| **HEAD** | A symbolic ref for "the commit my next commit builds on," usually via a branch. |
| **Ref** | A name pointing at a commit SHA (branch, tag, HEAD). |
| **Reflog** | A local log of where each ref used to point — the safety net for recovery. |
| **Porcelain / plumbing** | High-level everyday commands vs. low-level scriptable ones. |

---

*Broken link? Open an issue or PR.*
