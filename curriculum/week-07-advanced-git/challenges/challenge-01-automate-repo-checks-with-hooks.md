# Challenge 1 — Automate Repo Checks with Hooks

**Type:** Open-ended. No single right answer. ~1–1.5 hours.

## Scenario

You've joined a small team whose repo is a mess of preventable problems: commit messages like "fix stuff", secrets pasted into config files, unformatted code, and the occasional 20 MB binary someone dragged in. Nobody *means* to do these things — "please remember" just doesn't scale. Your job: make the repo enforce its own standards so mistakes get caught **before** they land, and make that enforcement **shareable** so every teammate gets it, not just you.

## Your task

Design and build a hook setup for a sample repo that enforces at least **four** distinct quality gates, and make it survive a fresh `git clone`.

## Constraints

- At least **two** gates must be **blocking** (`pre-commit` and/or `commit-msg` — exit non-zero to abort).
- At least one gate must validate the **commit message** (e.g. enforce a Conventional Commits prefix like `feat:`, `fix:`, `docs:`).
- The setup must be **shareable**: a teammate who clones the repo and runs a single documented setup step gets all the hooks. `.git/hooks/` alone does not count — it isn't cloned.
- Hooks must be **fast** (a `pre-commit` that runs in under ~2 seconds on the sample repo). Slow, thorough checks belong in `pre-push` or CI — say so if you split them.
- Everything must be free/open tooling. Rolling your own scripts, or using `pre-commit` / `lefthook` / `husky`, are all acceptable — justify your pick.

## Suggested gates to choose from

Pick at least four; mix blocking and reactive:

| Gate | Hook | Blocking? |
|------|------|:---------:|
| Reject `DO NOT COMMIT` / `DEBUG` markers | `pre-commit` | yes |
| Block files over a size limit | `pre-commit` | yes |
| Reject obvious secrets (`AWS_SECRET`, `-----BEGIN ... KEY-----`) | `pre-commit` | yes |
| Enforce Conventional Commits message format | `commit-msg` | yes |
| Run a formatter check on staged files | `pre-commit` | yes |
| Run the fast unit tests before push | `pre-push` | yes |
| Print a friendly summary after commit | `post-commit` | no |

## Hints

<details>
<summary>Making hooks shareable</summary>

Two common routes. Either commit a `hooks/` directory and have each dev run `git config core.hooksPath hooks` once (document it in your README), or adopt a manager: commit a `.pre-commit-config.yaml` (for the `pre-commit` framework) or `lefthook.yml`, and have devs run the tool's one-time `install`. Both mean the config is versioned in the repo; the hooks themselves are regenerated on each machine.
</details>

<details>
<summary>Enforcing the message format</summary>

The `commit-msg` hook receives the path to the message file as `$1`. Read the first line, test it against a regex like `^(feat|fix|docs|refactor|test|chore)(\(.+\))?: .+`, and `exit 1` with a helpful error if it doesn't match.
</details>

## How success is judged

| Criterion | What "great" looks like |
|-----------|-------------------------|
| Coverage | ≥4 distinct gates, ≥2 blocking, including a message-format check |
| Shareability | A fresh clone + one documented command activates every hook |
| Speed | `pre-commit` stays fast; heavy checks are moved to `pre-push`/CI with a rationale |
| Robustness | Hooks act on **staged** state, write errors to stderr, and use correct exit codes |
| Honesty about limits | Your writeup acknowledges `--no-verify` bypasses client hooks, and names CI as the unskippable backstop |

## Deliverable

A repo (or a directory in your portfolio) containing the hook config/scripts, a `README.md` documenting the one-command setup, and a short `DESIGN.md` (~200 words) explaining which gates you chose, why, which are blocking, and where you drew the line between "hook" and "CI". Include a transcript showing at least two gates blocking a bad commit and one clean commit passing.
