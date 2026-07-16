# Mini-Project — Rewrite a Feature Branch for Review

> Take a real, messy feature branch and rewrite it into a clean, review-ready history you would be proud to open a pull request from. This is the capstone of Week 4 — it uses every tool from all three lectures in one continuous flow.

**Estimated time:** 6–7 hours, spread across Friday–Saturday.

Unlike the exercises (which hand you a pre-baked mess), this project asks you to **create the mess yourself, naturally**, then clean it up — because that's how it happens in real life. You'll build a small feature the way you actually work (committing whenever, however), then step back and reshape the history into the story you *wish* you'd committed.

---

## The deliverable

A single Git repository (`c31-week-04/mini-project/`) containing:

1. A small but real feature built across **at least 10 commits** made "naturally" (messy is encouraged — wip commits, typos, a debug file, an out-of-order commit or two).
2. A `backup-original` branch preserving the messy history untouched, for comparison.
3. A rewritten `clean` branch with a tidy, logical history (target: **4–7 commits**, each one self-contained with a conventional-commit message).
4. A `WRITEUP.md` documenting the before, the after, and every rewrite decision.

---

## The feature to build

Build a tiny command-line **to-do list** in the language of your choice (Python, JS, Go, Bash — anything). It only needs to:

- add a task,
- list tasks,
- mark a task done,
- store tasks in a file,
- have a short `README.md`.

The feature is deliberately small so the *history work* is the focus, not the coding.

---

## Suggested workflow

### Phase 1 — Build it messily (1.5h)

Work naturally and **don't self-censor your commits.** Genuinely commit like a tired human:

```bash
mkdir -p /tmp/c31-w4/mini && cd /tmp/c31-w4/mini && git init -q
# ...build the feature over ~10+ commits...
# Encourage the mess: commit "wip" a few times, commit a stray debug print,
# add then delete a scratch file, fix a typo in a separate commit,
# commit the README update in the middle of feature work, etc.
```

Aim for at least **10 commits**, and make sure the mess includes at minimum:

- two or more `wip`-style commits that belong to a single logical change,
- one commit that should be dropped entirely (a debug file or stray print),
- one commit whose message needs rewording,
- one pair of commits that are in an illogical order.

### Phase 2 — Snapshot the mess (5 min)

Before touching anything, preserve the original:

```bash
git branch backup-original
git switch -c clean            # do all rewriting on 'clean'
git log --oneline > ../history-before.txt
```

### Phase 3 — Rewrite (2.5h)

On the `clean` branch, reshape the history. Reach for the right tool per situation:

- `git rebase -i` to squash the wip commits, drop the debug commit, reword, and reorder.
- `git commit --fixup` + `git rebase -i --autosquash` if you prefer to mark fixups first.
- `edit` + `git reset HEAD~1` to split any commit that does two unrelated things.
- `git rebase --abort` any time it gets away from you — then try a smaller range.

Target a history where each commit is one logical, reviewable change:

```
feat: scaffold todo CLI with file storage
feat: add 'add' and 'list' commands
feat: add 'done' command
docs: write README with usage examples
```

### Phase 4 — Verify nothing was lost (30 min)

Prove the rewrite only reorganised — it didn't drop real work:

```bash
git diff backup-original clean            # should show ONLY intended removals (debug/scratch)
git log --oneline                         # your clean 4–7 commits
```

The **tree** at the tip of `clean` should be functionally identical to `backup-original` minus the junk you intentionally removed. If `git diff` shows a real code difference you didn't intend, you lost something — recover it from `backup-original` or the reflog.

### Phase 5 — Write it up + ship (30 min)

Fill in `WRITEUP.md` (template below), export `git log --oneline` for both branches, and commit.

---

## `WRITEUP.md` template

```markdown
# Rewrite Write-up

## Before
<paste `git log --oneline` of backup-original — the mess>

## After
<paste `git log --oneline` of clean — the tidy history>

## Decisions
- Squashed commits X, Y, Z into "<final commit>" because ...
- Dropped commit W (debug file) because ...
- Reordered ... because ...
- Reworded ... from "<old>" to "<new>" because ...

## Proof nothing was lost
<output of `git diff backup-original clean` and a one-line explanation of every difference>

## What I'd do differently next time
<2–3 sentences on committing more cleanly from the start>
```

---

## Acceptance criteria

- [ ] `backup-original` branch preserves a messy history of **≥10 commits**.
- [ ] `clean` branch has a tidy history of **4–7 commits**, each a single logical change with a conventional message.
- [ ] No `wip`, `fix typo`, or debug commits survive on `clean`.
- [ ] `git diff backup-original clean` shows only the removals you *intended* (junk), no lost real work.
- [ ] `WRITEUP.md` explains every squash, drop, reorder, and reword.
- [ ] You never pushed or force-pushed during the rewrite (all local).

---

## Rubric

| Criterion | Weight | "Great" looks like |
|-----------|------:|--------------------|
| Clean final history | 30% | 4–7 commits, each logical, conventional messages, reads as a story |
| Nothing lost | 25% | `git diff` proves only intended removals; every feature present |
| Tool fluency | 20% | Right tool per job — squash, drop, reword, reorder, split |
| Write-up quality | 15% | Every rewrite decision justified; before/after clear |
| Safety discipline | 10% | Backup branch kept; comfortable with `--abort`/reflog; nothing pushed |

---

## Why this matters

This *is* the Week 5 pre-flight. Next week you'll open a real pull request, and the first thing a reviewer sees is your commit history. A clean branch gets reviewed fast and merged with confidence; a branch of twelve `wip` commits gets a "can you clean this up first?" and a day of delay. The skill you build here — treating the log as a draft you polish before sharing — is one of the clearest signals of a senior engineer.

---

When done: push your portfolio and continue to [Week 5 — Pull Requests & Review](../../week-05-pull-requests-and-review/), where this clean branch becomes a reviewed, merged PR.
