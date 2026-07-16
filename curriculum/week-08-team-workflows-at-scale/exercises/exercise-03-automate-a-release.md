# Exercise 3 — Automate a Release

**Goal:** Stand up an automated release. You'll write Conventional Commits, add a `release-please` workflow, watch it compute a version and changelog into a Release PR, and merge that PR to cut a tagged GitHub Release — without ever choosing the version number yourself.

**Estimated time:** 55 minutes.

## Why release-please for this exercise

We use `release-please` (Google) because it makes the automation *visible*: it opens a Release PR you can read before anything is tagged. That's the best way to learn the mechanism. Section "Stretch" points at the fully-automatic `semantic-release` once you trust it.

## Setup

```bash
gh repo create w8-release-demo --public --clone
cd w8-release-demo
printf '{\n  "name": "w8-release-demo",\n  "version": "0.1.0",\n  "private": true\n}\n' > package.json
git add package.json
git commit -m "chore: bootstrap package.json at 0.1.0"
git push -u origin main
```

## Steps

### 1. Add the release workflow

Create `.github/workflows/release.yml`:

```yaml
name: release
on:
  push:
    branches: [main]

permissions:
  contents: write
  pull-requests: write

jobs:
  release-please:
    runs-on: ubuntu-latest
    steps:
      - uses: googleapis/release-please-action@v4
        with:
          release-type: node
```

```bash
git add .github/workflows/release.yml
git commit -m "ci: add release-please workflow"
git push
```

### 2. Land some Conventional Commits

Make a few changes, each with a properly typed message. The **types drive the version bump** (Lecture 3 §2).

```bash
# A feature → should push a MINOR bump
printf "export const greet = (n) => `hi ${n}`;\n" > greet.js
git add greet.js && git commit -m "feat: add greet helper"

# A fix → PATCH-level, but the feat above dominates
printf "export const greet = (n) => `hi, ${n}!`;\n" > greet.js
git add greet.js && git commit -m "fix: punctuation in greeting"

git push
```

### 3. Read the Release PR

Within a minute, `release-please` opens a PR titled like **"chore(main): release 0.2.0"** (0.1.0 + a `feat` → 0.2.0). Open it:

```bash
gh pr list
gh pr view --web
```

Study what it contains **without merging yet**:

- The computed **version bump** in `package.json` (`0.1.0 → 0.2.0`).
- A generated **`CHANGELOG.md`** with your `feat` under "Features" and your `fix` under "Bug Fixes."
- Note: *you never typed "0.2.0."* The `feat:` commit chose it.

### 4. Add one more commit and watch the PR update

```bash
printf "\n# w8 release demo\n" >> README.md
git add README.md && git commit -m "docs: add a readme heading"
git push
```

Refresh the Release PR. It updates in place. A `docs:` commit doesn't change the version (still 0.2.0) but may appear in the changelog's "Documentation" section depending on config.

### 5. Cut the release

Merge the Release PR (`gh pr merge --squash --auto` or via the web button). Merging it triggers `release-please` to:

- Tag `v0.2.0`.
- Commit the updated `CHANGELOG.md` and `package.json` to `main`.
- Create a **GitHub Release** with the changelog as its notes.

Verify:

```bash
git fetch --tags
git tag                    # v0.2.0 present
gh release list            # the release exists
gh release view v0.2.0     # notes = your changelog
```

### 6. Trigger a MAJOR bump

Prove you understand breaking changes. Land a breaking commit:

```bash
printf "export const greet = (n, lang) => `hi ${n} (${lang})`;\n" > greet.js
git add greet.js
git commit -m "feat!: greet now requires a lang argument

BREAKING CHANGE: greet(name) callers must pass greet(name, lang)."
git push
```

A new Release PR should propose **1.0.0** (a breaking change from 0.x jumps as configured; the `!`/`BREAKING CHANGE` is what forces the major-level bump). Read it, then merge it.

## Expected result

Two automated releases whose version numbers were computed entirely from commit types — a MINOR from a `feat`, then a breaking bump — each with a generated changelog and a GitHub Release, and not one manually chosen version.

## Done when…

- [ ] `release.yml` exists and opens/updates a Release PR on pushes to `main`.
- [ ] You merged a Release PR and `v0.2.0` (tag + GitHub Release + CHANGELOG) exists.
- [ ] A `feat!:` / `BREAKING CHANGE:` commit produced a major-level bump in a later Release PR.
- [ ] `notes.md` explains how a commit *type* became a *version number* with no human deciding it.

## Stretch

- Swap in **`semantic-release`** (Lecture 3 §4) and observe the difference: no Release PR — a releasable push to `main` ships immediately. Note in `notes.md` which model you'd pick for a 10-deploys-a-day team vs. a library, and why.
- Add a `commitlint` check in CI so a badly-formatted commit message fails the PR before it can pollute history.

## Submission

Commit `notes.md`, your generated `CHANGELOG.md`, and links to the two releases to your portfolio under `c31-week-08/exercise-03/`.
