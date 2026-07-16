# Mini-Project — Merge three feature branches into `main`, cleanly

> Build a small project across **three parallel feature branches**, then integrate all three into `main` — resolving the conflicts that arise — and end with a history a reviewer would happily approve.

**Estimated time:** 3 hours, Friday–Saturday.

This is the week's capstone. Everything you practiced — branching, fast-forward vs. three-way, and conflict resolution — comes together in one realistic integration. You'll deliberately create branches that *overlap* on some files (so real conflicts happen) and stay clean on others (so you see automatic merges too).

---

## The project: a tiny "quotes" CLI

You're building a trivial command-line app. The code barely matters — the **integration** is the exercise. Three features are being developed in parallel by "three developers" (all you, on three branches).

### Deliverable

A repo `quotes-app/` in your portfolio containing:

1. The finished code on `main` with **all three features integrated**.
2. A `SOLUTION.md` documenting your branch plan, the conflicts you hit, how you resolved each, and your final `git log --oneline --graph`.
3. A clean `main` history where each feature's integration is legible.

---

## Phase 0 — Scaffold `main` (15 min)

```bash
mkdir -p ~/git-w2/quotes-app && cd ~/git-w2/quotes-app
git init

cat > quotes.py <<'EOF'
QUOTES = [
    "Simplicity is the soul of efficiency.",
]

def get_quote(i=0):
    return QUOTES[i % len(QUOTES)]

if __name__ == "__main__":
    print(get_quote())
EOF

cat > README.md <<'EOF'
# Quotes CLI
Prints a quote.
EOF

git add . && git commit -m "Scaffold quotes CLI"
```

This shared starting point is the **merge base** all three features will diverge from.

## Phase 1 — Build three feature branches (60 min)

Create each branch **from `main`** (not from each other), so all three share the same base. Two of them will edit `quotes.py` — that overlap is where your conflicts will come from.

**Branch 1 — `feature-more-quotes`:** add two more quotes to the `QUOTES` list, and a line to the README.

```bash
git switch -c feature-more-quotes main
# edit quotes.py: add two entries to QUOTES
# edit README.md: add a "Quotes" section
git commit -am "Add more quotes + README section"
```

**Branch 2 — `feature-random`:** add a `random` import and a `get_random_quote()` function; also touch the README.

```bash
git switch -c feature-random main
# edit quotes.py: import random; add get_random_quote()
# edit README.md: document --random
git commit -am "Add random quote selection"
```

**Branch 3 — `feature-cli-args`:** make `__main__` read an index from `sys.argv`.

```bash
git switch -c feature-cli-args main
# edit quotes.py: parse sys.argv for an index
git commit -am "Accept quote index as CLI arg"
```

Three branches, all rooted at the scaffold commit, several of them editing `quotes.py` and `README.md`. Verify the fan-out:

```bash
git log --oneline --graph --all --decorate
```

## Phase 2 — Integrate, one branch at a time (60 min)

**Merge deliberately, one at a time** — not an octopus merge — so you can resolve each conflict with full attention. Order matters less than care; a reasonable order is smallest/least-conflicting first.

```bash
git switch main
git merge feature-cli-args      # likely clean (touched only __main__)
git merge feature-more-quotes   # may conflict on quotes.py / README.md
git merge feature-random        # will likely conflict on quotes.py
```

For each merge:

1. If it's clean, note whether it fast-forwarded or made a merge commit, and why.
2. If it conflicts, `git status`, open the file, read the markers (top = `main` so far = "ours"; bottom = the incoming feature = "theirs"), and **combine both features' intent** — usually you want *all three* features present, so hand-merge rather than taking one side.
3. `git add` the resolved files, then `git commit` to finish the merge.
4. After each merge, run the app (`python quotes.py`, `python quotes.py --random`, `python quotes.py 1`) to confirm nothing broke.

> **The trap to avoid:** when `quotes.py` conflicts, it's tempting to `git checkout --ours` or `--theirs` and move on. That throws away one feature. The whole point is to keep *both* — read the base with `zdiff3` (`git config merge.conflictStyle zdiff3`) and reconstruct a version that has all the intended changes.

## Phase 3 — Verify and document (45 min)

```bash
python quotes.py            # prints a quote
python quotes.py --random   # prints a random one
python quotes.py 1          # prints quote at index 1
grep -rn '<<<<<<<\|>>>>>>>' .    # MUST print nothing
git log --oneline --graph --all --decorate
```

Then write `SOLUTION.md` (see rubric). Delete the merged feature branches (`git branch -d …`) once you've confirmed everything is on `main`.

---

## Requirements

- [ ] All three features work on `main` after integration (all three commands run correctly).
- [ ] You merged **one branch at a time**, not with a single octopus merge.
- [ ] At least one merge was a genuine three-way merge with a resolved conflict.
- [ ] **No conflict markers** remain anywhere (`grep` proves it).
- [ ] `SOLUTION.md` records your branch plan, each conflict and its resolution, and the final graph.
- [ ] Merged feature branches are deleted; `main` is the single source of truth.

## Milestones

| Milestone | You're here when… |
|-----------|-------------------|
| M1 — Scaffold | `main` has the base commit; three branches exist off it |
| M2 — First merge | One feature is on `main`; you can say if it FF'd or not |
| M3 — First conflict resolved | You hand-merged a `quotes.py` conflict keeping both features |
| M4 — All integrated | Three features live on `main`; app runs three ways |
| M5 — Documented | `SOLUTION.md` written; branches cleaned up |

## Rubric

| Criterion | Weight | "Great" looks like |
|-----------|-------:|--------------------|
| Correct integration | 30% | All three features present and working on `main` |
| Conflict handling | 25% | Conflicts resolved by keeping *both* sides' intent, not by discarding a feature |
| History readability | 20% | `git log --graph` reads cleanly; deliberate merge choices |
| No residue | 15% | Zero conflict markers; branches cleaned up; app verified to run |
| Documentation | 10% | `SOLUTION.md` explains each conflict and the resolution reasoning |

## Why this matters

Integrating multiple parallel features into a trunk *is* the daily job on any real team. The mechanical skills (branch, merge, resolve) are necessary but not sufficient — the judgment is in resolving conflicts so **no one's work is silently dropped**. Nail this and you've got the core competency the whole rest of C31 builds on.

---

When done: push, then start [Week 3 — Remotes & GitHub](../../week-03-remotes-and-github/), where these branches learn to live on more than one machine.
