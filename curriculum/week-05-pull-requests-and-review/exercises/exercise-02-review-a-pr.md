# Exercise 2 — Review a PR with Line Comments and a Suggestion

**Goal:** Do a real review. Read a diff, leave line-anchored comments, propose a one-click **suggestion**, praise something, and submit a single review with an honest verdict.

**Estimated time:** 40 minutes.

## What you're reviewing

Ideally, a **partner's** PR from Exercise 1. Swap repo URLs and review each other's. Solo? Review your *own* PR — GitHub blocks self-*approval*, but line comments and suggestions all work, which is what you're practicing here.

To make the review meaty, the author should push one more commit with a deliberate rough edge. If you're setting up your own repo to review, add this on the `fix-empty-average` branch:

```bash
cat >> report.py <<'EOF'


def summarize(numbers):
    # returns a one-line summary string
    return "total=" + str(total(numbers)) + " avg=" + str(average(numbers))
EOF
git commit -am "Add summarize() helper"
git push
```

Now the PR has something worth commenting on: string concatenation that could be an f-string, no empty-list handling in the summary, and no tests.

## Step 1 — Read before you type

Open the PR's **Files changed** tab (`gh pr view <number> --web`, then click *Files changed*). Before leaving a single comment:

1. Read the PR **description**. What's the goal?
2. Read the **main change** first (the `average` fix), then the new `summarize`.
3. Ask yourself the priority questions: correctness → security → design → tests → readability.

## Step 2 — Start a review (don't fire single comments)

Click **Start a review** (not the standalone "Add single comment"). Every comment you add is now *pending* and won't notify the author until you submit the whole review at once.

## Step 3 — Leave line comments

Click the blue `+` in the gutter next to a line and write a comment. Leave at least:

- **One correctness/design comment.** Example, on the `summarize` return line:
  > "Does `summarize([])` do the right thing? `average([])` is 0 now, so this won't crash — but is `avg=0` the summary we want for no data, or should it say `avg=n/a`?"
- **One praise comment.** Example, on the `average` fix:
  > "Nice — guarding the empty case inside the function instead of at every call site is the right call."

## Step 4 — Propose a suggestion (the key skill)

Find the string-concatenation line in `summarize`. Leave a comment, and inside it use a **`suggestion`** block to rewrite the line for the author:

````markdown
nit: an f-string reads cleaner here and the author can apply it in one click:

```suggestion
    return f"total={total(numbers)} avg={average(numbers)}"
```
````

When rendered, this shows an **Apply suggestion** button. Prefix it `nit:` to signal it's non-blocking — the author's call.

> Make sure the indentation *inside* the suggestion block matches the file exactly (four spaces here). GitHub replaces the commented line(s) verbatim, so a wrong indent produces a broken apply.

## Step 5 — Choose a verdict honestly

Click **Finish your review**. Pick one:

- **Comment** — you have notes but aren't gating (or you're reviewing your own PR and can't approve).
- **Approve** — the change improves the code and you'd be fine merging it.
- **Request changes** — there's a real must-fix problem.

For this exercise, the empty-summary question is a genuine design point but not a bug — **Comment** or **Approve with nits** is honest; **Request changes** would be too heavy. Write a one-line summary in the box: e.g. "Good fix. One design question on empty summaries, one nit you can one-click. Not blocking."

Submit the review. Now — and only now — the author gets one notification with everything.

## Expected result

- The PR's **Conversation** tab shows one review from you containing multiple line comments, not a scatter of separate comments.
- At least one comment contains a `suggestion` block with an **Apply suggestion** button.
- Your verdict matches the severity (Comment/Approve, not a punitive Request changes).

## Done when…

- [ ] You used **Start a review** and submitted once (comments arrived batched).
- [ ] At least two line comments exist, anchored to specific lines.
- [ ] One comment is genuine praise.
- [ ] One comment is a working `suggestion` the author can apply in one click.
- [ ] Your verdict is defensible in one sentence.

## Stretch

- Leave a **multi-line** comment: click-drag across a range of line numbers, then comment on the whole block.
- Use the `gh` CLI to review from the terminal: `gh pr review <number> --comment --body "..."` (or `--approve` / `--request-changes`). Notice you can't `--approve` your own PR.
- Read the diff **commit by commit** via the PR's *Commits* tab and notice how much clearer a well-split history is to review.

## Submission

Leave the review live on the PR. Exercise 3 picks up as the *author* responding to exactly these comments.
