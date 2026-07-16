# Exercise 3 — Address Review Feedback and Merge

**Goal:** Be the author responding to review. Apply a suggestion, push a real fix, reply to a comment you *don't* act on, resolve the threads, get the green light, and merge — then clean up.

**Estimated time:** 35 minutes.

## Starting point

The PR from Exercises 1–2, now carrying a review: at least one line comment, one praise, and one `suggestion`. You're the author. Open it: `gh pr view <number> --web`.

## Step 1 — Apply the suggestion

Find the reviewer's `suggestion` block (the f-string rewrite). Click **Add suggestion to batch** if there are several, or **Commit suggestion** to apply just this one. GitHub commits the change directly to your branch — no local editing needed. Watch the **Files changed** diff update and the commit appear in the *Commits* tab.

> Prefer to batch? Click **Add suggestion to batch** on each suggestion, then **Commit suggestions** once to fold them all into a single commit.

## Step 2 — Push a real fix for the design comment

The reviewer's design question ("what should `summarize([])` say?") needs actual code, not a one-click apply. Pull the suggestion commit down first so you don't diverge:

```bash
git switch fix-empty-average
git pull                       # gets the commit the "Apply suggestion" button made
```

Now address the empty case. Edit `summarize` in `report.py`:

```python
def summarize(numbers):
    if not numbers:
        return "total=0 avg=n/a"
    return f"total={total(numbers)} avg={average(numbers)}"
```

Commit and push:

```bash
git commit -am "Handle empty input in summarize()"
git push
```

The PR updates automatically — no re-opening. The reviewer will see exactly this new commit.

## Step 3 — Reply to what you changed *and* what you didn't

Good authors close the loop in writing:

- On the **suggestion** thread: reply "Applied, thanks." and click **Resolve conversation**.
- On the **design** thread: reply "Good catch — empty summaries now say `avg=n/a` (see 3f9a1c2)." Then **Resolve conversation**.
- If a reviewer comment was **wrong or out of scope**, don't silently ignore it. Reply with your reasoning: "Leaving `total` returning `0` for empty — that matches how the rest of the module treats sums. Happy to revisit if you disagree." Resolving a thread you disagreed with *after explaining* is fine; ghosting it is not.

## Step 4 — Re-request review

Once you've pushed fixes, ask the reviewer to look again. In the sidebar, click the ↻ icon next to their name (**Re-request review**), or:

```bash
gh pr edit <number> --add-reviewer <partner-username>
```

They re-review the **delta** — just your new commits — not the whole PR again. With a partner, they now click **Approve**.

## Step 5 — Merge

Once approved (or, solo, once you're satisfied), merge. You choose the strategy:

```bash
# Squash the branch's commits into one clean commit on main, then delete the branch
gh pr merge <number> --squash --delete-branch
```

| Strategy | Flag | Result on `main` | When |
|----------|------|------------------|------|
| **Squash** | `--squash` | One commit for the whole PR | Default for small feature PRs — clean history |
| **Merge commit** | `--merge` | Keeps every commit + a merge commit | When the individual commits matter |
| **Rebase** | `--rebase` | Replays commits, no merge commit | Linear history, commits preserved |

Web UI: the green **Merge pull request** button has a dropdown for the same three choices, plus **Delete branch** after.

## Step 6 — Clean up locally

```bash
git switch main
git pull                       # main now has your merged change
git branch -d fix-empty-average   # local branch is gone from the remote already
```

Confirm the linked issue auto-closed: `gh issue view 1` should show **CLOSED**.

## Expected result

- The PR is **MERGED**, its branch deleted on the remote.
- `main` contains the empty-average fix *and* the `summarize` improvements, as one squashed commit (if you squashed).
- Issue #1 is **closed automatically** by the `Closes #1` in the description.
- Every review thread is **resolved**, each with a reply.

## Done when…

- [ ] You applied at least one suggestion via the GitHub button.
- [ ] You pushed at least one hand-written fix commit in response to a comment.
- [ ] You replied to every thread — including any you chose not to act on.
- [ ] The PR is merged with a deliberate strategy and the branch is deleted.
- [ ] Issue #1 shows CLOSED.

## Stretch

- Before merging, run `git log --oneline main..fix-empty-average` and predict what squash vs. merge will do to `main`'s history. Then merge and check `git log --oneline` on `main`.
- Turn on **branch protection** for `main` (Settings → Branches → Add rule: require a PR + 1 approval) and try to `git push` straight to `main`. Watch it get rejected. This is the enforced version of GitHub flow.

## Submission

Record the final `main` `git log --oneline` (top 3 lines) in your scratch notes. You've now run one change through the entire GitHub-flow loop — the exact skill the mini-project asks you to demonstrate again, cleanly.
