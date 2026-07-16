# Lecture 3 — Code Review: How to Review, Request Changes, and Not Be a Jerk

> **Duration:** ~2 hours. **Outcome:** You can review a PR in the right order, leave comments that improve the code *and* respect the author, use suggestions and the three review verdicts correctly, and know the etiquette that keeps a team reviewing each other's work happily for years.

## 1. What review is actually for

Before the mechanics, get the *purpose* straight, because it shapes every comment you leave. Code review exists to:

1. **Catch defects** a second pair of eyes sees and the author, too close to it, doesn't.
2. **Spread knowledge** — after review, at least two people understand this code. That's your bus-factor insurance.
3. **Keep the codebase coherent** — consistent patterns, naming, and structure so the repo reads like it was written by one careful person.
4. **Mentor, both ways** — juniors learn from senior reviews; seniors learn the codebase's corners from reviewing juniors.

Notice what's **not** on the list: proving you're smart, gatekeeping, or rewriting the author's code into the code you'd have written. Review is a collaboration toward a merged, better change — not a contest. Google's engineering guidance puts it bluntly: reviewers should favor **approving a PR once it definitely improves overall code health**, even if it isn't perfect. Perfect is the enemy of merged.

## 2. Read the diff in the right order

A common mistake is reading a diff top to bottom, file by alphabetical file. That's how the tool lists it, not how you understand it. A better order:

1. **Read the PR description first.** What is this, and why? If the description is empty or incomprehensible, that's your first comment — ask for context before reviewing code you don't understand the purpose of.
2. **Find the "main" file** — the one where the actual change lives — and read that first, so the rest has context.
3. **Follow the data / call flow**, not the file list. If the change adds an endpoint that calls a serializer, read the endpoint, then the serializer.
4. **Read tests as a spec.** The tests tell you what the author *thinks* the code should do. Compare that to what it *should* do.
5. **Skim the mechanical bits last** — generated files, lockfile changes, mass renames. Don't spend real attention there.

For large diffs, review **commit by commit** if the author structured the history well (`base...head` shows the whole thing; each commit is a chapter). GitHub's "Commits" tab lets you do this.

## 3. What to look for (and what to let go)

Aim your limited attention at what matters. Roughly in priority order:

| Priority | Look for | Example comment |
|---------:|----------|-----------------|
| **1. Correctness** | Bugs, edge cases, off-by-one, null/empty, race conditions | "What happens here if `items` is empty? Looks like this indexes `[0]` unconditionally." |
| **2. Security** | Injection, secrets in code, missing authz, unsafe input | "This interpolates user input straight into the query — parameterize it?" |
| **3. Design** | Right abstraction, not over/under-engineered, fits existing patterns | "Could this reuse `formatReport()` instead of a second formatter?" |
| **4. Tests** | Are the important paths covered? Do the tests actually assert? | "Nice tests. Could we add one for the empty-input case?" |
| **5. Readability** | Names, clarity, comments where the *why* is non-obvious | "`d` is hard to follow — maybe `daysElapsed`?" |
| **6. Style** | Formatting, conventions | *Ideally a linter's job, not yours — see below.* |

**Push style to machines.** Formatting, import order, and trailing commas should be enforced by a formatter (Prettier, `gofmt`, `black`, `ruff`) and a linter in CI (Week 6), not by humans in review. Every "add a space here" comment is attention stolen from finding a real bug. If you find yourself leaving style nits, the fix is a formatter, not more diligent nit-picking.

## 4. Leaving comments: line, multi-line, and general

GitHub gives you three comment scopes in the **Files changed** tab:

- **Line comment** — click the `+` in the gutter next to a line. Anchored to that exact line; the most common and most useful.
- **Multi-line comment** — click and drag across a range of line numbers, then comment. Use it when the issue spans several lines.
- **General / summary comment** — the big box when you finish a review. Use it for the overall verdict and cross-cutting notes ("great direction; three small things inline").

**Batch your comments into a single review.** Don't leave ten separate one-off comments — each fires a notification and the author starts reacting before you're done. Instead click **Start a review**, add all your comments as "pending," then **Finish your review / Submit review** once, with your verdict. The author gets one coherent review, not a trickle of pings.

## 5. Suggested changes — fix it for them in one click

The single most useful review feature you may not know about: a **suggestion**. Inside a line comment, use a fenced block with the language `suggestion`:

````markdown
```suggestion
    return items[0] if items else None
```
````

GitHub renders this as a proposed replacement for the line(s) you're commenting on, with an **Apply suggestion** button. The author clicks it and your exact change is committed to the PR — no context-switch to their editor, no "did they mean the whole line or just that word?" ambiguity.

Suggestions are perfect for small, unambiguous fixes: a typo, a rename, a missing guard, a clearer variable name. You can even **batch multiple suggestions** into one commit. What they're *not* for: large or architectural changes — those need a conversation, not a one-liner you've written for them.

> **Author-side note (you'll use this in Exercise 3):** when you receive suggestions, you can apply them one at a time or batch several into a single commit via **Add suggestion to batch → Commit suggestions**. Applied suggestions become real commits on your branch and the diff updates.

## 6. The three verdicts — and choosing honestly

When you submit a review, GitHub makes you pick one of three:

| Verdict | Meaning | Use when |
|---------|---------|----------|
| **Comment** | Feedback, no explicit judgment | You have thoughts but aren't gating the merge, or you're not the deciding reviewer. |
| **Approve** | "Ship it." | The change improves the codebase and you'd be comfortable with it merging as-is (or with the trivial nits you noted). |
| **Request changes** | "Do not merge until we resolve this." | There's a real problem — a bug, a security hole, a design issue — that *must* be addressed before merge. |

Two disciplines here:

- **Don't withhold approval over taste.** If your only comments are "I'd have named this differently" and "personally I'd structure it another way," that's a **Comment** or an **Approve with nits**, not a **Request changes**. Blocking a merge is a strong action; reserve it for things that are actually wrong.
- **Approve with confidence.** "Approve" means you'd be comfortable being on the hook if it breaks. If you skimmed a 900-line PR and clicked Approve to be nice, you've made the review worthless. If it's too big to review honestly, say *that* (and point the author at splitting it — Lecture 1).

**Request changes** blocks the merge under branch protection. It's powerful and, used carelessly, it stings — so pair it with specifics and a path forward: *what* must change and *why*.

## 7. Review etiquette — the part that keeps teams together

Mechanics are easy. The reason review goes bad is almost always tone. A few rules that pay off for years:

**Comment on the code, not the coder.** "This function is confusing" → "I found this function hard to follow; could we split it?" The first sounds like a verdict on the author; the second is about the code and invites collaboration. Never "why would you do this?" — it reads as an attack even when you meant it literally.

**Ask, don't command, when you're not certain.** "Could we handle the empty case here?" invites a reply ("good catch" *or* "it's handled upstream, see X"). "Handle the empty case." shuts the door. You are often missing context; phrasing as a question leaves room for the author to teach you.

**Prefix non-blocking nits.** A widely used convention: start optional comments with **`nit:`** ("nit: `d` → `days`"). It signals "this is minor, your call, I won't block on it." Some teams extend this with labels like `nit:` / `question:` / `suggestion:` / `blocking:` so the author instantly knows what's a request and what's a musing. (This vocabulary is popularized by Conventional Comments — see resources.)

**Praise the good.** Review isn't only for finding faults. "Nice — this is much cleaner than the old version" costs you three seconds and makes the next twenty comments land better. Genuine praise is not filler; it calibrates the author on what to keep doing.

**Explain the why.** "Use a `Map` here" is a command. "A `Map` would make lookups O(1) instead of scanning the array each time — matters because this runs per request" teaches. The author (and everyone reading later) learns something, and is far more likely to agree.

**Assume competence and good faith.** The author is a smart colleague who made a reasonable choice with the information they had. If something looks wrong, lead with curiosity ("is there a reason for X?") rather than a correction. You are frequently the one missing something.

**Reply to what you didn't change.** As the author, if you *disagree* with a comment, say so and why — don't silently ignore it. As the reviewer, when the author pushes back with a good reason, resolve the thread gracefully: "ah, makes sense, thanks."

## 8. Turnaround time is part of etiquette

A perfect review that arrives three days late blocked a colleague for three days. Treat review requests as **high-priority interrupts**: aim to respond within a business day, even if the response is "I'll get to this properly this afternoon." Fast, kind reviews are the lubricant of a team that ships. Slow reviews teach people to make bigger, rarer PRs — exactly the wrong lesson.

## 9. Putting it together — a review, start to finish

```text
1. Open the PR. Read the description. (No description? Comment: "what's the goal here?")
2. Files changed → Start a review.
3. Read the main file first, follow the flow, read the tests.
4. Leave line comments as pending. Suggest fixes for the trivial ones.
   Prefix nits with "nit:". Praise one thing you liked.
5. Decide the verdict honestly:
     - real problem → Request changes, with specifics + a path forward
     - good enough, minor notes → Approve (with nits)
     - just thoughts, not deciding → Comment
6. Write a short summary comment. Submit the whole review once.
7. When the author responds, re-review the delta — not the whole thing again.
```

Do that, and you'll be the reviewer people *want* on their PRs: fast, sharp on the things that matter, and never a jerk about it.

## 10. Check yourself

- In what order should you read a diff, and why not top-to-bottom by filename?
- What are the top two things to prioritize when reviewing, and what should you push onto machines instead?
- Write a `suggestion` block that replaces a line to add a null guard.
- Give the honest rule for choosing between Comment, Approve, and Request changes.
- Rewrite "why would you write it like this?" as a comment that critiques the code, not the coder.
- What does the `nit:` prefix signal, and why is it useful?

If you can answer all six, you're ready for the exercises. Go open a PR, review one, and merge one.

## Further reading

- **How to do a code review — Google Engineering Practices (Reviewer's Guide):** <https://google.github.io/eng-practices/review/reviewer/>
- **Conventional Comments (the `nit:` / `suggestion:` / `blocking:` vocabulary):** <https://conventionalcomments.org/>
- **Incorporating feedback & suggested changes (GitHub Docs):** <https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/reviewing-changes-in-pull-requests/incorporating-feedback-in-your-pull-request>
