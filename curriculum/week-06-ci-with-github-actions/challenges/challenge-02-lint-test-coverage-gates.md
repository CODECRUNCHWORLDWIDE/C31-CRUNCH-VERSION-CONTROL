# Challenge 2 — Lint + Test + Coverage Gates on a PR Pipeline

**Type:** Open-ended. No single right answer.
**Estimated time:** ~1.5 hours.

## Scenario

Passing tests are the floor, not the ceiling. Mature teams gate merges on *more than one* signal: code must be **linted** (style/quality), **tested** (correctness), and hit a **coverage threshold** (the tests actually exercise the code). Your job is to design a PR pipeline where **each of these is an independent, required gate** — a PR can't merge unless all three are green.

The design questions are the interesting part: How do you structure the jobs? Should lint and test run in parallel or in sequence? Where does coverage get measured, and what threshold is honest rather than vanity?

## Your task

Extend your repo's CI into a three-gate pipeline:

1. **Lint gate** — a linter runs and fails the job on any violation.
2. **Test gate** — the suite runs and fails on any failing test.
3. **Coverage gate** — coverage is measured and the job **fails if coverage drops below a threshold you set** (pick a defensible number and justify it).

Then make **all three** required status checks in branch protection.

## Constraints

- Each gate is a **separately named check** so branch protection can require each independently. (Separate jobs, or a matrix — your call, but the checks must be distinguishable.)
- The coverage gate must **fail the build** when under threshold — not merely print a number. A coverage tool that reports 61% but exits `0` is not a gate.
- Use free tooling. Suggested per stack (use what you know):

| Stack | Lint | Test + coverage |
|-------|------|-----------------|
| Node.js | `eslint` | `c8` or `nyc` wrapping `node --test`, with `--check-coverage --lines=<N>` |
| Python | `ruff` (or `flake8`) | `pytest` + `pytest-cov` with `--cov-fail-under=<N>` |

- Keep total wall-clock time reasonable — parallelize where it's safe.

## Design decisions to make (and defend in `NOTES.md`)

- **Parallel vs. sequential:** Running lint, test, and coverage as three parallel jobs is fast but re-installs dependencies three times. Running them as sequential steps in one job is cheaper but slower to signal. Or: separate jobs sharing a cache. Which did you pick and why?
- **Coverage threshold:** 100% is usually dishonest (it incentivizes testing trivial getters). Too low is meaningless. What number did you choose, and what's your reasoning? Does it ratchet (never allowed to drop) or hold a fixed floor?
- **Where coverage runs:** Coverage needs the tests to run *with instrumentation*. Do you run tests twice (once plain, once instrumented) or fold coverage into the test job? Trade-off?
- **Signal quality:** If lint fails, should the test job still run? Argue for or against `fail-fast`.

## Hints

<details>
<summary>Getting coverage to actually gate</summary>

- Node: `npx c8 --check-coverage --lines 80 node --test` exits non-zero when line coverage is under 80%. That non-zero exit is what fails the job and blocks the merge.
- Python: `pytest --cov=. --cov-fail-under=80` does the same — the `--cov-fail-under` flag makes pytest exit non-zero below the threshold.
- The whole trick is: the *tool* must exit non-zero. CI only knows pass/fail from exit codes (Lecture 2 §3). A gate that doesn't exit non-zero isn't a gate.
</details>

<details>
<summary>Naming the checks for branch protection</summary>

Give each job a clear `name:` (`lint`, `test`, `coverage`) so branch protection's required-checks list shows three distinct entries you can tick. Job id and display name both matter here.
</details>

## How success is judged

- **Three independent gates (35%):** Lint, test, and coverage each block merge on their own. Prove it by breaking each *one at a time* and showing only that gate goes red while the PR stays blocked.
- **Coverage truly gates (20%):** Dropping coverage below your threshold fails the build (not just prints a lower number).
- **Sound design (25%):** `NOTES.md` defends your parallel/sequential structure, threshold, and coverage placement with real reasoning, not hand-waving.
- **Reproducible + free (20%):** All config in the repo; all tooling free and installed by the workflow.

## Deliverable

A PR pipeline with three named, required checks. Include in `NOTES.md`: three screenshots or `gh pr checks` transcripts showing each gate independently blocking a bad PR, plus your written design rationale.
