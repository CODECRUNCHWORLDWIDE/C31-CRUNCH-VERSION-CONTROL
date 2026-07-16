# Week 5 — Quiz

Twelve questions. Lectures closed. Aim for 10/12 before Week 6. Mostly multiple-choice, a couple short-answer.

---

**Q1.** A pull request points at two Git refs. What are they called?

- A) `origin` and `upstream`
- B) `base` and `head`
- C) `main` and `feature`
- D) `local` and `remote`

---

**Q2.** You push a new commit to a branch that already has an open PR. What must you do to update the PR?

- A) Close the PR and open a new one.
- B) Click "Re-submit."
- C) Nothing — the PR updates automatically.
- D) Force-push and re-request review from scratch.

---

**Q3.** Which is the *best* reason small PRs catch more bugs than large ones?

- A) Small PRs have fewer lines, so there are fewer bugs to find.
- B) Reviewer attention is finite; past a few hundred lines reviewers skim and miss defects.
- C) GitHub disables review on large PRs.
- D) Small PRs are always written by better engineers.

---

**Q4.** In a PR description, what does `Closes #142` do that a bare `#142` does not?

- A) Nothing — they're identical.
- B) It links to the issue but doesn't close it.
- C) It auto-closes issue 142 when the PR merges into the default branch.
- D) It assigns the issue to you.

---

**Q5.** When should you open a **draft** PR?

- A) When the code is finished and you want it merged fast.
- B) When you want early feedback or CI on work that isn't ready for formal review.
- C) Never — drafts can't run CI.
- D) Only for hotfixes.

---

**Q6.** You're reviewing a diff. Which order is best?

- A) Alphabetically by filename, top to bottom.
- B) Description first, then the main change, following the data/call flow.
- C) Tests first, then never look at the implementation.
- D) Largest file first, always.

---

**Q7.** A `suggestion` block in a review comment is for…

- A) Rewriting the author's entire architecture.
- B) A small, unambiguous change the author can apply in one click.
- C) Leaving a general summary of the PR.
- D) Requesting changes without saying why.

---

**Q8.** Your only comments on a PR are "I'd have named this differently" and "I'd structure it another way." The honest verdict is:

- A) Request changes — block the merge.
- B) Comment or Approve-with-nits — taste isn't a blocker.
- C) Refuse to review.
- D) Approve and say nothing.

---

**Q9.** What does the `nit:` prefix on a review comment signal?

- A) The PR is rejected.
- B) A critical, must-fix bug.
- C) A minor, non-blocking point — the author's call.
- D) The comment is directed at the author personally.

---

**Q10.** Which review comment critiques the *code*, not the *coder*?

- A) "Why would you ever write it like this?"
- B) "Did you even test this?"
- C) "I found this function hard to follow — could we split it?"
- D) "This is sloppy."

---

**Q11.** As the author, a reviewer leaves a comment you think is *wrong*. Best move?

- A) Silently ignore it and merge.
- B) Reply explaining your reasoning; resolve the thread after the discussion.
- C) Delete the comment.
- D) Request changes on your own PR.

---

**Q12.** You merge a small PR made of 6 messy "wip" commits and want `main` to show one clean commit. Which merge strategy?

- A) Merge commit
- B) Rebase and merge
- C) Squash and merge
- D) Cherry-pick

---

**Q13 (short answer).** In one sentence each, name three distinct reasons (beyond "fewer lines") that small PRs are better than large ones.

---

**Q14 (short answer).** Write a `suggestion` block that would replace a line with `return items[0] if items else None`.

---

**Q15 (short answer).** A branch protection rule on `main` requires a PR + one approval. In one sentence, what does that *prevent* someone from doing?

---

## Answer key

<details>
<summary>Reveal after attempting</summary>

1. **B** — `base` (merge into) and `head` (merge from). The diff is `base...head`.
2. **C** — nothing; the PR's diff is computed live against the base, so pushing updates it automatically.
3. **B** — finite reviewer attention. Past ~200–400 lines, defect-detection density collapses because reviewers skim.
4. **C** — a closing keyword (`Closes`/`Fixes`/`Resolves`) auto-closes the linked issue on merge to the default branch. A bare `#142` only links.
5. **B** — early feedback or CI on unfinished work. Mark it **Ready for review** when it's done.
6. **B** — read the description, then the main change, following the flow; skim mechanical bits last.
7. **B** — small, unambiguous, one-click applicable changes. Big/architectural changes need a conversation.
8. **B** — Comment or Approve-with-nits. Don't block a merge over taste; reserve *Request changes* for real problems.
9. **C** — a minor, non-blocking nit — the author decides. (Popularized by Conventional Comments.)
10. **C** — critiques the code and invites collaboration. The others attack the author.
11. **B** — reply with your reasoning, discuss, then resolve. Never silently ignore a reviewer.
12. **C** — Squash and merge collapses the branch into one clean commit on `main`.
13. Any three of: gets reviewed faster (small = quick to pick up); catches more bugs (attention stays high); smaller merge-conflict surface (shorter-lived branch); easier to revert precisely; kinder to reviewer time; easier to understand as one logical change.
14. ````
    ```suggestion
        return items[0] if items else None
    ```
    ```` (indentation must match the file exactly).
15. It prevents anyone from pushing changes directly to `main` — every change must go through a pull request that gets at least one approval before merging.

</details>

If you scored 12+: ship the mini-project. 9–11: re-read the lecture section behind each miss. <9: re-read Lectures 1 and 3 from the top before continuing.
