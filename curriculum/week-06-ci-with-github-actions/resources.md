# Week 6 — Resources

Free, official-first. Read the "required" set before the exercises; the rest is reference and depth.

## Required reading (official GitHub docs)

- **Understanding GitHub Actions** — the object model in GitHub's own words; read alongside Lecture 1:
  <https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions>
- **Workflow syntax for GitHub Actions** — the reference for every key you'll write in a `.yml`:
  <https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions>
- **About protected branches** — what branch protection can enforce and how:
  <https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches>

## Core how-tos

- **Building and testing your code** — starter workflows per language (Node, Python, Go, Java, Ruby, .NET):
  <https://docs.github.com/en/actions/automating-builds-and-tests>
- **Using a matrix for your jobs** — the definitive matrix reference (include/exclude, fail-fast):
  <https://docs.github.com/en/actions/using-jobs/using-a-matrix-for-your-jobs>
- **Caching dependencies to speed up workflows** — keys, restore-keys, limits, eviction:
  <https://docs.github.com/en/actions/using-workflows/caching-dependencies-to-speed-up-workflows>
- **Using secrets in GitHub Actions** — creating, referencing, masking, and the fork-PR rule:
  <https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions>
- **About rulesets** — the modern successor to branch protection rules; use for new repos:
  <https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/about-rulesets>

## Gating and scale (for the challenges + mini-project)

- **Managing a branch protection rule** — the exact click-path for required status checks:
  <https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/managing-a-branch-protection-rule>
- **Managing a merge queue** — how large repos keep required checks honest against a moving `main`:
  <https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/configuring-pull-request-merges/managing-a-merge-queue>
- **Automatic token authentication (`GITHUB_TOKEN`)** — the built-in secret and how to scope it:
  <https://docs.github.com/en/actions/security-guides/automatic-token-authentication>

## Reference cards and marketplace

- **Actions used this week** — read each action's own README for its inputs:
  - `actions/checkout` — <https://github.com/actions/checkout>
  - `actions/setup-node` — <https://github.com/actions/setup-node>
  - `actions/setup-python` — <https://github.com/actions/setup-python>
  - `actions/cache` — <https://github.com/actions/cache>
- **GitHub Actions Marketplace** — where reusable actions live: <https://github.com/marketplace?type=actions>
- **Workflow commands cheat-sheet** (grouping logs, masking, outputs): <https://docs.github.com/en/actions/using-workflows/workflow-commands-for-github-actions>

## Validate before you push

- **`actionlint`** — a static linter for workflow files; catches YAML and expression errors locally before you waste a run: <https://github.com/rhysd/actionlint>
- **`act`** — run GitHub Actions workflows locally in Docker for fast iteration (not a perfect mirror, but close): <https://github.com/nektos/act>
- **`gh` CLI Actions commands** — `gh run`, `gh workflow`: <https://cli.github.com/manual/gh_run>

## Concept and background (free)

- **Martin Fowler — "Continuous Integration"** — the canonical essay on *why* CI exists, tool-agnostic:
  <https://martinfowler.com/articles/continuousIntegration.html>
- **GitHub Actions — quickstart** — a 10-minute hands-on if you want a second angle before the exercises:
  <https://docs.github.com/en/actions/quickstart>

## Tools to have installed

- `git` 2.40+ and the **GitHub CLI** `gh` (`gh auth status` should be green).
- Your language runtime locally (Node 18+ or Python 3.11+) so you can reproduce CI failures on your own machine.
- Optionally `actionlint` and `act` from the "validate" section above.

---

*Broken link? Open an issue. Docs URLs occasionally move — search the doc title if a link 404s.*
