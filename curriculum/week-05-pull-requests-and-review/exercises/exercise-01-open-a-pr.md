# Exercise 1 — Open a PR from a Feature Branch

**Goal:** Take a change from a fresh topic branch to an open, well-formed pull request. By the end you'll have a PR with a real title, a what/why/how-to-test description, a linked issue, and a checklist.

**Estimated time:** 35 minutes.

## Setup

Create a tiny public repo to work in. From the CLI:

```bash
# Create a local repo with one file and a main branch
mkdir pr-practice && cd pr-practice
git init -b main

cat > report.py <<'EOF'
def total(numbers):
    result = 0
    for n in numbers:
        result += n
    return result


def average(numbers):
    return total(numbers) / len(numbers)
EOF

git add report.py
git commit -m "Add total() and average() helpers"

# Create the GitHub repo and push (gh will offer to push for you)
gh repo create pr-practice --public --source=. --push
```

No `gh`? Create an empty repo named `pr-practice` on github.com, then:

```bash
git remote add origin https://github.com/<your-username>/pr-practice.git
git push -u origin main
```

## Step 1 — Open an issue to link

A PR is stronger when it closes something. Create one:

```bash
gh issue create \
  --title "average() crashes on an empty list" \
  --body "average([]) raises ZeroDivisionError. It should return 0 for an empty list."
```

Note the issue number it prints (say `#1`). Web UI alternative: **Issues → New issue**.

## Step 2 — Branch and make the change

```bash
git switch -c fix-empty-average
```

Edit `report.py` so `average` handles the empty case:

```python
def average(numbers):
    if not numbers:
        return 0
    return total(numbers) / len(numbers)
```

Commit it with a clear message:

```bash
git add report.py
git commit -m "fix: return 0 from average() on empty input"
```

## Step 3 — Push the branch

```bash
git push -u origin fix-empty-average
```

GitHub prints a URL you could click to open a PR. We'll use `gh` instead so you control the fields.

## Step 4 — Open the PR with a real description

Write the body to a file so it's multi-line and deliberate:

```bash
cat > /tmp/pr-body.md <<'EOF'
## What
`average()` now returns `0` for an empty list instead of crashing.

## Why
Closes #1. `average([])` raised `ZeroDivisionError`; callers had to guard
every call site. This handles it once, in the function.

## How to test
```
python3 -c "from report import average; print(average([]), average([2,4]))"
# expected: 0 3.0
```

## Checklist
- [x] Handles the empty case
- [x] Self-reviewed the diff
- [ ] Add a unit test (follow-up)
EOF

gh pr create \
  --title "fix: return 0 from average() on empty input" \
  --body-file /tmp/pr-body.md \
  --base main \
  --head fix-empty-average
```

Web UI alternative: click the URL from Step 3, fill the same title and body, click **Create pull request**.

## Step 5 — Self-review

Open the PR in your browser (`gh pr view --web`). Go to **Files changed** and read your own diff as if you were the reviewer. Is the change minimal? Does the description explain it? If a line needs context, leave a comment on it yourself.

## Expected result

- A branch `fix-empty-average` exists on the remote.
- An **open PR** targets `main`, with your title and a what/why/how-to-test body.
- The body contains `Closes #1`, so the issue will auto-close when the PR merges (you can see "Closes #1" linked in the sidebar under *Development*).
- The diff is a single, small, focused change.

## Done when…

- [ ] `gh pr view` shows your PR as **OPEN** against `base: main`.
- [ ] The description answers what, why, and how to test.
- [ ] The linked issue appears in the PR sidebar (Development → Closes #1).
- [ ] You self-reviewed the Files changed tab.

## Stretch

- Add a `.github/pull_request_template.md` to the repo, then open a *second* throwaway PR and watch the template pre-fill the description.
- Open the PR as a **draft** instead (`gh pr create --draft ...`), then `gh pr ready` to flip it. Notice the merge button is disabled while it's a draft.

## Submission

Keep this repo — Exercises 2 and 3 and the mini-project all reuse it. Record the PR URL in a scratch file; you'll need it next.
