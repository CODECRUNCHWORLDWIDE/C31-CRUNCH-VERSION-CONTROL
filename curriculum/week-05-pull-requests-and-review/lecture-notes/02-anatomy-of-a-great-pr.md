# Lecture 2 — Anatomy of a Great Pull Request

> **Duration:** ~2 hours. **Outcome:** You can write a PR that a busy reviewer approves in ten minutes because the title, description, and links did the explaining for you — and you know when to open it as a draft.

## 1. The PR is a document, not just a diff

A weak PR is a pile of code with the title "fix stuff" and an empty description. The reviewer has to reverse-engineer *what* changed, *why*, and *how to check it*. That's slow and error-prone, and it puts the burden on the wrong person.

A great PR **does the reviewer's homework for them.** Your goal is to make approval the path of least resistance: the reviewer opens it, the description tells them exactly what they're looking at, the diff is small, and they can say "yes" with confidence in ten minutes. A PR is a piece of writing. Treat it like one.

There are five parts to get right: the **title**, the **description**, the **links**, the **checklist**, and the **metadata** (reviewers, labels, draft state). Let's take them in order.

## 2. The title

The title is what shows up in the PR list, in notifications, and — if you squash-merge — in the `main` commit history forever. Make it earn its place.

Write it in the **imperative mood**, like a good commit subject: "Add CSV export to reports," not "Added CSV export" or "CSV stuff." The imperative reads as *what merging this PR will do* to the codebase.

| Weak title | Better title |
|------------|--------------|
| `fix` | `Fix off-by-one in pagination offset` |
| `Updated the parser` | `Handle empty input in the CSV parser` |
| `WIP` | `[Draft] Add rate limiting to the login endpoint` |
| `changes requested by review` | `Address review: rename cols, add null guard` |

Keep it under ~50–70 characters where you can. If your team uses **Conventional Commits** (you met these in Week 1), prefix accordingly: `feat: add CSV export`, `fix: guard against empty CSV input`, `docs: document the export format`. This makes changelogs and release tooling trivial later.

## 3. The description: what / why / how to test

The description is the heart of the PR. A reliable template answers three questions:

1. **What** does this change do? One or two sentences, plain language.
2. **Why** is it needed? The problem, the ticket, the user request. This is the part that's gone forever if you don't write it down.
3. **How do I test it?** The exact steps a reviewer follows to see it work — or a note that automated tests cover it.

Here's a description that does its job:

```markdown
## What
Adds a CSV export button to the Reports page. Clicking it downloads the
current report as `report-<date>.csv`.

## Why
Closes #142. Finance has been copy-pasting the HTML table into Excel every
Monday; three people asked for a real export. This unblocks that.

## How to test
1. `npm run dev`, open http://localhost:3000/reports
2. Pick any date range, click **Export CSV**
3. Open the file — columns should match the on-screen table, dates in ISO format

## Notes
- Serializer is a pure function in `lib/csv.ts` with unit tests.
- Deliberately did NOT add Excel (.xlsx) export — out of scope, see #143.
```

Note what the last two lines do. **"Notes" pre-empts questions.** By calling out that `.xlsx` is deliberately excluded, you stop a reviewer from leaving a "shouldn't this also do Excel?" comment that costs a round-trip. Anticipating objections in the description is a senior move.

> **Reviewer's tip you should exploit as an author:** if a diff hunk needs explaining, *you* comment on your own PR's line first — "this looks odd; it's because the upstream API returns dates as strings." Self-review before you request review. It catches your own mistakes and answers questions before they're asked.

## 4. Linking issues — and closing them automatically

If your repo tracks work as GitHub Issues, **link the issue in the description.** Certain keywords, followed by an issue number, make GitHub *auto-close* that issue when the PR merges into the default branch:

| Keyword forms | Effect |
|---------------|--------|
| `Closes #142`, `Close #142`, `Closed #142` | Closes issue 142 on merge |
| `Fixes #142`, `Fix #142`, `Fixed #142` | Same — closes on merge |
| `Resolves #142`, `Resolve`, `Resolved` | Same — closes on merge |
| `#142` (bare, no keyword) | Links but does **not** auto-close |
| `Closes org/other-repo#142` | Closes an issue in another repo |

Use the closing keyword when the PR fully resolves the issue; use a bare `#142` reference when it's only related. This wiring means your issue tracker stays honest for free: merge the PR, the ticket closes, the board updates. One PR can close several issues (`Closes #142, closes #143`).

## 5. Checklists

A markdown checklist in the description turns "did you remember everything?" into a visible, tickable list:

```markdown
## Checklist
- [x] Tests added/updated
- [x] Ran the linter locally
- [ ] Updated the docs (follow-up in #144)
- [x] Self-reviewed the diff
```

GitHub renders `- [ ]` as an interactive checkbox you can tick right in the PR. Checklists do two jobs: they remind *you* not to forget the boring-but-important steps, and they signal to the reviewer what you've already handled. Many teams keep a default checklist in a **PR template** so it appears automatically.

### PR templates

Drop a file at `.github/pull_request_template.md` in the repo and its contents pre-fill the description box for every new PR. A minimal one:

```markdown
## What

## Why (link the issue)
Closes #

## How to test

## Checklist
- [ ] Tests added/updated
- [ ] Docs updated if needed
- [ ] Self-reviewed the diff
```

Now every PR starts with the right skeleton and nobody ships an empty description. This is a five-minute change that raises the quality floor of every PR in the repo forever.

## 6. Draft PRs — open early, mark ready later

You do **not** have to wait until code is finished to open a PR. A **draft PR** is a PR that explicitly cannot be merged yet and doesn't request review by default. Open one when you want to:

- Share direction early and get feedback *before* you've built the whole thing (cheaper to change course at 20% than at 100%).
- Run CI on your work-in-progress.
- Have a visible place to discuss an approach.

```bash
# Open a draft from the CLI
gh pr create --draft --fill

# When it's ready for real review
gh pr ready
```

In the web UI, click the dropdown next to "Create pull request" and choose **Create draft pull request**; later, click **Ready for review**. The etiquette: **draft = "not asking for your time yet, but you can peek."** Ready = "please review." Don't request a formal review on a draft, and don't leave something in draft for a week with no note about what it's waiting on.

## 7. Metadata: reviewers, assignees, labels

The right-hand sidebar of a PR is not decoration:

| Field | Use it for |
|-------|-----------|
| **Reviewers** | Who you're asking. Request specific people, not "anyone." A `CODEOWNERS` file can auto-request the right people for touched paths. |
| **Assignees** | Who *owns* getting this merged (usually you, the author). |
| **Labels** | `bug`, `feature`, `needs-design`, `breaking-change` — machine- and human-readable triage. |
| **Milestone / Project** | Which release or board column this belongs to. |

Request review from someone who actually knows the area. Requesting review from five people so "someone" does it is an anti-pattern — everyone assumes someone else will, and nobody does. Pick one or two.

## 8. Keeping the PR small and legible

Everything from Lecture 1 applies here at authoring time:

- **One logical change per PR.** Resist "while I was in here, I also…". That belongs in its own PR.
- **Separate mechanical from meaningful.** If you're renaming a variable across 40 files *and* fixing a bug, do the rename in its own PR. A reviewer can approve a pure rename in seconds but needs to actually think about the bug fix; mixing them buries the important 10 lines under 400 boring ones.
- **Order your commits so the diff tells a story.** A reviewer often reads commit by commit. `git rebase -i` (Week 4) to tidy the history *before* you open the PR is a courtesy, not vanity.

## 9. A great PR, assembled

Putting it together, here's the full lifecycle from the author's side:

```bash
git switch -c add-csv-export main
# ... build the change in small commits ...
git push -u origin add-csv-export

gh pr create \
  --title "feat: add CSV export to the Reports page" \
  --body-file .github/pr-body.md \
  --reviewer teammate1 \
  --label feature \
  --draft                       # open as draft while CI runs and you self-review

# self-review the diff on the web, add explanatory comments to tricky lines
gh pr ready                     # flip to ready; reviewers get notified
```

The reviewer opens it to: a clear imperative title, a what/why/how-to-test description, a linked issue that'll auto-close, a ticked checklist, a small focused diff, and a couple of author comments pre-empting the obvious questions. That PR gets approved fast — not because the reviewer is lazy, but because you removed every reason to hesitate.

## 10. Check yourself

- Rewrite the title "fixed the thing" for a PR that stops the parser crashing on empty input.
- What three questions must every PR description answer?
- What's the difference between `Closes #142` and a bare `#142` in a description?
- When would you open a **draft** PR instead of a normal one? How do you mark it ready?
- Why is mixing a 40-file rename with a 10-line bug fix in one PR a bad idea?
- Where does a PR template file live, and what does it do?

If you can answer all six, move to Lecture 3 — how to be the reviewer on the other side.

## Further reading

- **About pull requests (GitHub Docs):** <https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests>
- **Linking a pull request to an issue (closing keywords):** <https://docs.github.com/en/issues/tracking-your-work-with-issues/linking-a-pull-request-to-an-issue>
- **Creating a pull request template for your repository:** <https://docs.github.com/en/communities/using-templates-to-encourage-useful-issues-and-pull-requests/creating-a-pull-request-template-for-your-repository>
