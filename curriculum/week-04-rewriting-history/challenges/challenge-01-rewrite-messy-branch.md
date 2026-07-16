# Challenge 1 — Rewrite a Messy Branch into Reviewable History

**Time:** ~75 minutes. **Difficulty:** Medium.

## Scenario

You've been hacking on a feature all week. It works, but your commit history is an embarrassment: twelve commits, half of them `wip`, a committed debug file, a secret you accidentally added and removed, commits in the wrong order, and one commit that mixes two unrelated changes. Tomorrow you open a pull request, and your team's culture is: **the diff tells a story, one commit per logical change.** Reshape this branch into something you'd be proud to send for review.

## Setup

Generate the mess with this script (read it — it's realistic):

```bash
mkdir -p /tmp/c31-w4/challenge-01 && cd /tmp/c31-w4/challenge-01
git init -q
echo "# Widget Service" > README.md && git add . && git commit -qm "chore: init"
git switch -c feature/widgets

echo "class Widget: pass"              > widget.py   && git add . && git commit -qm "start widgets"
echo "DEBUG = True"                    > debug.py    && git add . && git commit -qm "add debug file"
echo "def make(): return Widget()"    >> widget.py   && git add . && git commit -qm "wip"
echo "API_KEY = 'sk-live-oops-secret'" > config.py   && git add . && git commit -qm "add config"
git rm -q config.py && git commit -qm "remove secret oops"
echo "def color(): pass"              >> widget.py   && git add . && git commit -qm "wip2"
echo "## Usage"                       >> README.md   && git add . && git commit -qm "docs"
echo "def size(): pass"               >> widget.py   && git add . && git commit -qm "add size AND fix make()"
echo "def make(): return Widget()  # fixed" >> widget.py && git add . && git commit -qm "typo"
echo "print('temp trace')"            >> widget.py   && git add . && git commit -qm "temp trace remove later"
echo "### Examples"                   >> README.md   && git add . && git commit -qm "more docs"
echo "def destroy(): pass"            >> widget.py   && git add . && git commit -qm "wip3 destroy"

git log --oneline                      # twelve commits of chaos
```

## Your task

Rewrite `feature/widgets` so that its history — everything since it left `main`/`master` — reads as a small number of clean, logical commits. A good target is **three to five** commits, something like:

- `feat: add Widget class with make/color/size/destroy`
- `docs: document widget usage and examples`

You decide the exact grouping — that's the judgement being tested. Along the way you must:

1. **Drop** the `debug.py` / `print('temp trace')` noise entirely — that code should not exist in the final tree.
2. **Ensure the secret is truly gone.** The `config.py` with the API key was added and removed in separate commits — but the key still sits in the *history*. Your rewrite must remove both commits so the key exists in **no** commit at all. Prove it: `git log -p | grep -c "sk-live"` must return `0`.
3. **Squash the wip/typo commits** into the logical commits they belong to.
4. **Order** the commits sensibly (feature before docs, or grouped however tells the clearest story).
5. **Keep every real change** — all four widget methods and both doc sections must survive in the final files.

## Constraints

- Work only on local, un-pushed commits. Do not push at any point.
- Before you start, create a safety branch: `git branch backup-messy`. You'll diff against it at the end.
- The final working tree must contain `widget.py` (with `make`, `color`, `size`, `destroy`) and `README.md` (with Usage + Examples), and must **not** contain `debug.py`, `config.py`, or the `temp trace` line.

## Hints

<details>
<summary>Where to start</summary>

`git rebase -i main` (or `HEAD~12`) opens the whole branch. Delete or `drop` the lines for the debug, secret, and temp-trace commits; `fixup`/`squash` the wip and typo lines into the commits they belong with; `reword` the survivors into clean messages.
</details>

<details>
<summary>The secret is stubborn</summary>

Because the key was added in one commit and removed in another, *both* commits are in history. If you only drop the "remove secret" commit, the "add config" commit still contains the key. Drop **both** — or if the removal got tangled, `git rebase -i` and delete the `add config` line so it never happened. Verify with `git log -p | grep "sk-live"` (should print nothing).
</details>

<details>
<summary>A commit does two things at once</summary>

The `add size AND fix make()` commit mixes two changes. If you care about clean history, use `edit` on it during the rebase and `git reset HEAD~1` to split it into two commits (Lecture 2, §5). Optional but impressive.
</details>

<details>
<summary>If the rebase gets messy</summary>

`git rebase --abort` returns you to the start. Then try a smaller range or do it in two passes (drop the junk first, squash second).
</details>

## How success is judged

| Criterion | What "great" looks like |
|-----------|-------------------------|
| **Clean history** | 3–5 commits, each one logical change, conventional messages, no wip/typo/debug |
| **Secret eradicated** | `git log -p \| grep -c "sk-live"` returns `0` |
| **Nothing lost** | `git diff backup-messy -- widget.py README.md` shows only the intended removals (debug/trace), not real work |
| **Junk gone** | `debug.py`, `config.py`, and the temp-trace line are absent from the final tree |
| **Reasoning** | `RATIONALE.md` explains your grouping and how you removed the secret |

## Deliverable

Commit to your portfolio under `c31-week-04/challenge-01/`:

- The final `git log --oneline` output (paste or screenshot).
- `RATIONALE.md` — your grouping decisions and the secret-removal step.
- The proof that the secret is gone (`grep -c` result).
