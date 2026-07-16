# Lecture 2 — Writing a Workflow

> **Duration:** ~2 hours. **Outcome:** You can author a production-quality workflow file that triggers on push and pull request, sets up a runtime, installs and caches dependencies, tests across a matrix of versions, and uses secrets safely — and you can debug it from its logs.

Lecture 1 gave you the vocabulary. This one is hands-on syntax: the YAML that turns the model into a working pipeline. We build it up in layers — trigger, checkout, runtime, dependencies, tests — then add the two things that make a workflow fast and real-world: a **matrix** and **caching**. We finish with **secrets**, because sooner or later a job needs a token.

## 1. Anatomy of the file

A workflow lives at `.github/workflows/<name>.yml`. The filename is yours; the path is not. The top-level keys:

| Key | Purpose |
|-----|---------|
| `name` | Human-readable name shown in the Actions tab |
| `on` | The event(s) that trigger the workflow |
| `permissions` | The token scopes the workflow's jobs get (least-privilege) |
| `jobs` | A map of job-id → job definition |

YAML is whitespace-significant: **two spaces per level, never tabs.** A single misindented line is the most common reason a workflow silently fails to appear. When in doubt, run it through a YAML linter (see `resources.md`).

## 2. Triggering: `on`

The `on` key decides *when* the workflow runs. For CI you almost always want two triggers: **push** (so the branch itself is verified) and **pull_request** (so the PR shows a check).

```yaml
on:
  push:
    branches: [main]        # only pushes to main
  pull_request:             # every PR, any branch
```

Why both? A pull_request run tests the *merge result* — what the code would look like once merged — which is what you actually care about before merging. A push run to `main` catches anything that lands directly on the trunk. Restricting `push` to `main` avoids running the same tests twice for branch pushes that also have an open PR (the PR trigger already covers those).

You can scope further with **path filters** so the workflow only runs when relevant files change:

```yaml
on:
  pull_request:
    paths:
      - 'src/**'
      - 'package.json'
      - '.github/workflows/**'
```

Be careful combining `paths` filters with required checks (Lecture 3): if the filter skips the workflow, the required check never reports, and the PR can get stuck. Keep the CI workflow that produces your *required* check unfiltered, or use `paths-ignore` only for genuinely irrelevant files like `docs/**`.

## 3. The core job, step by step

Here is the smallest useful test job, for a Node.js project. We'll dissect it.

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4        # 1. get the code
      - uses: actions/setup-node@v4      # 2. install a runtime
        with:
          node-version: '20'
      - run: npm ci                      # 3. install dependencies
      - run: npm test                    # 4. run the suite
```

1. **`actions/checkout@v4`** clones your repository into the runner's working directory. Without it, the runner is an empty machine — no code. This is step one of almost every job.
2. **`actions/setup-node@v4`** installs the requested Node.js version and puts it on `PATH`. There are sibling actions for every major language: `setup-python`, `setup-java`, `setup-go`, `setup-dotnet`, and so on.
3. **`npm ci`** (not `npm install`) installs *exactly* the versions in your lockfile, failing if the lockfile and `package.json` disagree. In CI you always want the deterministic, lockfile-driven install. The Python equivalent is `pip install -r requirements.txt`; for a locked install, tools like `pip-tools` or `uv` give you the same guarantee.
4. **`npm test`** runs whatever your `package.json`'s `test` script defines. The job passes if this command exits `0` and fails otherwise. **Exit code is everything** — CI reads success/failure purely from the exit status of each `run` step. A step that prints "FAIL" but exits `0` is a *passing* step. Make sure your test runner exits non-zero on failure (they all do by default).

### The Python version, for comparison

The concepts are identical; only the setup and install commands change:

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.12'
      - run: pip install -r requirements.txt
      - run: pytest -q
```

Pick the stack you know. Everything from here — matrix, caching, secrets, gating — works the same regardless of language.

## 4. Matrix builds: test many versions at once

Real software must work on more than one version of a runtime and often more than one operating system. A **matrix** runs the same job once per combination of values, in parallel.

```yaml
jobs:
  test:
    runs-on: ${{ matrix.os }}
    strategy:
      fail-fast: false
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
        node-version: ['18', '20', '22']
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
      - run: npm ci
      - run: npm test
```

This single job definition expands into **3 × 3 = 9 parallel jobs** — every OS crossed with every Node version. Notes:

- **`${{ matrix.<key> }}`** is expression syntax; it substitutes the value for the current combination. `runs-on: ${{ matrix.os }}` makes the runner itself vary.
- **`fail-fast: false`** tells GitHub *not* to cancel the other combinations the moment one fails. The default (`true`) cancels siblings on first failure — fast, but you lose the full picture. For a test matrix you usually want `false` so you see *all* the failures at once.
- You can **exclude** or **include** specific combinations:

```yaml
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest]
        node-version: ['18', '20', '22']
        exclude:
          - os: windows-latest
            node-version: '18'      # skip this one combination
        include:
          - os: ubuntu-latest
            node-version: '22'
            experimental: true      # add an extra variable to one combo
```

Each matrix combination is a **separately named check** — e.g. `test (ubuntu-latest, 20)`. That matters in Lecture 3: you can require *all* of them, or just the one representative combination, as a merge gate.

## 5. Caching dependencies

A fresh runner downloads every dependency from scratch, every run. For a real project that can add minutes. **Caching** stores a directory (your dependency cache) keyed on a hash of your lockfile, and restores it on the next run when the lockfile hasn't changed.

The easiest path: the `setup-*` actions have caching built in.

```yaml
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'            # cache ~/.npm keyed on package-lock.json
```

That one line — `cache: 'npm'` — handles the whole thing: it caches npm's download cache, keyed on your `package-lock.json`, and restores it automatically. `setup-python` accepts `cache: 'pip'`, `setup-java` accepts `cache: 'maven'`, and so on.

For full control, use the generic `actions/cache` action:

```yaml
      - uses: actions/cache@v4
        with:
          path: ~/.npm                                   # what to cache
          key: npm-${{ hashFiles('package-lock.json') }} # exact-match key
          restore-keys: |
            npm-                                          # fallback prefix
```

How it works:

| Piece | Meaning |
|-------|---------|
| `path` | The directory to save and restore |
| `key` | The exact cache identity. `hashFiles(...)` changes when the lockfile changes, so a dependency change → a new key → a fresh cache |
| `restore-keys` | Ordered fallback prefixes. If no exact `key` match, restore the newest cache whose key starts with `npm-` |

The logic: an **exact key hit** restores instantly and skips re-download. A **miss** falls back to a `restore-keys` prefix (a *partial* cache — better than nothing), then, at job end, saves a new cache under the current `key`. **Caches are immutable** — a key is written once and never overwritten, which is why the key must include a hash that changes when contents change. Stale caches expire automatically after 7 days of no access, and each repo has a total cache size budget.

Caching is an optimization, never a correctness tool. A build must produce the same result cache-hit or cache-miss. Never cache anything whose staleness could hide a bug (e.g. compiled test output).

## 6. Secrets

Some jobs need credentials — a token to publish a package, an API key to hit a staging service. You must never commit those. GitHub stores them as **encrypted secrets** (repo Settings → Secrets and variables → Actions) and injects them at runtime.

```yaml
      - run: ./deploy.sh
        env:
          API_TOKEN: ${{ secrets.API_TOKEN }}
```

Rules that keep secrets safe:

- **Reference, never hardcode.** `${{ secrets.NAME }}` pulls the value at runtime; the value never appears in the file.
- **Actions masks secret values in logs.** If a secret's value would be printed, the log shows `***` instead. This masking is best-effort — do not deliberately transform and print a secret, and it can still leak if you base64/echo it.
- **`GITHUB_TOKEN` is automatic.** Every workflow run gets a built-in `${{ secrets.GITHUB_TOKEN }}` scoped to that repo — you don't create it. Restrict its power with the top-level `permissions:` key:

```yaml
permissions:
  contents: read           # least privilege: can read code, nothing else
```

Default to `contents: read` and add scopes only as a job needs them. A test workflow rarely needs write access to anything.

- **Secrets are not passed to workflows triggered by `pull_request` from forks.** This is deliberate — otherwise a stranger could open a PR whose workflow prints your secrets. Keep secret-using steps out of the fork-PR path.

## 7. Debugging a run

Workflows fail. Reading the failure is a skill:

```bash
gh run list                    # find the failed run
gh run view <run-id>           # summary: which job/step failed
gh run view <run-id> --log-failed   # only the failing step's log
gh run watch                   # live-tail the current run
```

A checklist when a run misbehaves:

1. **Did the workflow even trigger?** If nothing appears in the Actions tab, the file is likely misindented, in the wrong directory, or filtered out by `on:`.
2. **Which step failed?** The red ✗ marks it. The exit code and last lines of that step's log are the story.
3. **Is it the code or the workflow?** Reproduce the failing command locally in a clean checkout. If it fails locally too, it's your code (CI did its job). If it only fails in CI, suspect environment: a missing dependency, an env var you have locally but didn't set, or an OS difference.
4. **Re-run to check for flakiness.** `gh run rerun <run-id>`. A test that passes on re-run without a code change is *flaky* — a real bug, just a different kind. Don't paper over it by re-running until green.

## 8. Putting it together

A complete, real workflow with everything from this lecture — trigger, least-privilege permissions, matrix, caching, and a clear install/test split:

```yaml
name: CI
on:
  push:
    branches: [main]
  pull_request:

permissions:
  contents: read

jobs:
  test:
    runs-on: ${{ matrix.os }}
    strategy:
      fail-fast: false
      matrix:
        os: [ubuntu-latest, windows-latest]
        node-version: ['20', '22']
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
          cache: 'npm'
      - run: npm ci
      - run: npm test
```

That is a workflow you could drop into a real project today. Next lecture, we make its check a *requirement*.

## 9. Check yourself

- Why prefer `npm ci` over `npm install` (or a locked install over a loose one) in CI?
- CI reads pass/fail from what, exactly, about each `run` step?
- A matrix of `os: [A, B]` and `version: [1, 2, 3]` produces how many jobs?
- What does `fail-fast: false` change, and why is it usually right for a test matrix?
- What makes a cache `key` correct? What is the role of `restore-keys`?
- Why must a cache never affect correctness?
- How do you reference a secret, and why don't fork PRs receive secrets?
- Your workflow doesn't appear in the Actions tab at all. Name two likely causes.

When these are automatic, do [Exercise 1](../exercises/exercise-01-first-workflow-on-push.md).

## Further reading

- **Workflow syntax for GitHub Actions:** <https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions>
- **Using a matrix for your jobs:** <https://docs.github.com/en/actions/using-jobs/using-a-matrix-for-your-jobs>
- **Caching dependencies to speed up workflows:** <https://docs.github.com/en/actions/using-workflows/caching-dependencies-to-speed-up-workflows>
- **Using secrets in GitHub Actions:** <https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions>
