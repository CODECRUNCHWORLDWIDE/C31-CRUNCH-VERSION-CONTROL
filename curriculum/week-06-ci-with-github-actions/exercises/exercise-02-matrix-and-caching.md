# Exercise 2 — Build Matrix and Dependency Caching

**Goal:** Take the single-job workflow from Exercise 1 and (a) run it across several runtime versions and operating systems with a **matrix**, and (b) speed up repeat runs with **dependency caching**. You'll see one job definition fan out into many parallel jobs, and a second run finish faster off a warm cache.

**Estimated time:** 50 minutes.

## Setup — add a real dependency

Caching only matters when there's something to install. Add one dependency and a test that uses it so the install step does real work.

```bash
cd cc-ci-week6
npm install lodash          # creates node_modules + package-lock.json
```

Add a test that imports it:

```bash
cat > test/lodash.test.js <<'EOF'
const { test } = require('node:test');
const assert = require('node:assert');
const _ = require('lodash');

test('lodash chunk works', () => {
  assert.deepStrictEqual(_.chunk([1, 2, 3, 4], 2), [[1, 2], [3, 4]]);
});
EOF

git add -A && git commit -m "Add lodash dependency and a test"
```

Because you now have a lockfile, switch the install to the deterministic `npm ci` in the workflow (below).

> **Python?** `pip install requests` (or any package), freeze it with `pip freeze > requirements.txt`, and use `cache: 'pip'` on `setup-python`. The matrix and caching structure is identical.

## Task A — add a matrix

Rewrite `.github/workflows/ci.yml` so the `test` job runs across two operating systems and three Node versions:

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
        node-version: ['18', '20', '22']
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
      - run: npm ci
      - run: npm test
```

Commit and push:

```bash
git commit -am "Add OS + Node version matrix"
git push
```

Open the Actions tab. The single `test` job should have expanded into **6 jobs** (2 OS × 3 versions), each named like `test (ubuntu-latest, 20)`, running in parallel.

## Task B — add caching

Add `cache: 'npm'` to the setup step so each job restores npm's download cache instead of fetching from scratch. Change the `setup-node` step to:

```yaml
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
          cache: 'npm'
```

Commit and push. On this first run the cache is *saved* (a cache miss). Push a trivial commit again (e.g. touch the README) and compare the `npm ci` step's duration and log on the second run — you should see a **cache hit** near the top of the setup step, and a faster install.

```bash
git commit -am "Cache npm dependencies" && git push
echo "trigger" >> README.md && git commit -am "Second run to warm cache" && git push
```

## Verify the cache

- In the Actions tab, open a job → the `setup-node` step. On the warm run it logs something like `Cache restored from key: ...`.
- Repo → **Actions** → **Caches** (left sidebar) lists your saved caches with their sizes and keys.

## Expected result

- One job definition rendered as 6 parallel jobs across OS × version.
- A distinct, named check per combination.
- A measurable install speedup on the second run from the restored cache.

## Done when…

- [ ] The Actions tab shows 6 matrix jobs from a single `test` definition.
- [ ] `fail-fast: false` is set (break one version's test and confirm the *other* five still run to completion).
- [ ] The second run logs a cache hit for npm.
- [ ] You can explain why the cache key is tied to `package-lock.json` and what happens to the cache when you add a new dependency.

## Stretch

- Add an `exclude:` entry to drop `windows-latest` + Node `18`, and confirm the job count drops to 5.
- Add an `include:` entry that adds a single `macos-latest` + Node `20` combination.
- Replace the built-in `cache: 'npm'` with an explicit `actions/cache@v4` step (path `~/.npm`, key `npm-${{ hashFiles('package-lock.json') }}`, with a `restore-keys: npm-` fallback) and confirm it behaves the same.
