# Week 4 — Challenges

Two open-ended problems. Unlike the exercises, there is **no single right answer** — the point is judgement: choosing the right tool, sequencing your rewrites sanely, and keeping a way back at every step.

1. **[Challenge 1 — Rewrite a messy branch into reviewable history](challenge-01-rewrite-messy-branch.md)** — you're handed a real-looking branch of a dozen chaotic commits. Reshape it into a history a reviewer would approve. *(~75 min, Medium.)*
2. **[Challenge 2 — Recover a repo after a bad rebase](challenge-02-recover-after-bad-rebase.md)** — a rebase went wrong and the branch looks wrecked. Get it back to a known-good state and explain what happened. *(~60 min, Medium-Hard.)*

## How these are judged

There's no autograder. Judge yourself — and have a peer judge you — against these principles, which are exactly what a senior engineer looks for in a real review:

- **Every change is preserved.** Rewriting history reorganises work; it never silently deletes it. Diff the final tree against the original tip to prove nothing was lost.
- **The final history tells a story.** Each commit is one logical, self-contained change with a clear conventional message. No `wip`, no `fix typo`, no debug commits.
- **You never rewrote shared history.** Everything happens on local, un-pushed commits (or a solo branch you force-push *with lease*).
- **You always had a way back.** A safety branch or a noted SHA before each risky step; comfort reaching for `git reflog` and `git rebase --abort`.
- **You can explain what you did.** A short write-up of the before/after and the reasoning matters as much as the result.

## Ground rules

- Work in a **scratch repo**. Never practise destructive rewrites on anything you can't afford to lose.
- Keep a `RATIONALE.md` as you go — a running log of what you tried, what broke, and what worked. It's part of the deliverable and it's how you'll answer "why did you do it that way?" in a real review.
