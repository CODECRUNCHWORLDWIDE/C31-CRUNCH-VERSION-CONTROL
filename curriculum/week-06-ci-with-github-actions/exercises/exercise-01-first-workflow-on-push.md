# Exercise 1 — Your First Workflow, Running on Push

**Goal:** Give a small project a real test suite, then a workflow that runs it automatically on every push and pull request. When you finish, a green ✓ appears next to your commits without you ever running the tests by hand.

**Estimated time:** 45 minutes.

## Setup — a tiny project with a failing-capable test

Use the `cc-ci-week6` repo from the exercises README. Create a minimal Node project with one function and one test.

```bash
cd cc-ci-week6
npm init -y

# The unit under test
cat > sum.js <<'EOF'
function sum(a, b) { return a + b; }
module.exports = { sum };
EOF

# A test using Node's built-in test runner (no dependencies to install)
mkdir -p test
cat > test/sum.test.js <<'EOF'
const { test } = require('node:test');
const assert = require('node:assert');
const { sum } = require('../sum');

test('adds two numbers', () => {
  assert.strictEqual(sum(2, 3), 5);
});
EOF
```

Point the `test` script at Node's test runner by editing `package.json` so its `scripts` block reads:

```json
"scripts": { "test": "node --test" }
```

Verify it passes locally, then commit:

```bash
npm test          # should report 1 passing test
git add -A && git commit -m "Add sum() with a test"
```

> **Python instead?** Make `sum.py` with `def add(a, b): return a + b`, a `test_sum.py` using `assert add(2, 3) == 5`, and use `pytest -q` as your test command. Everything below is identical apart from the setup action and install line.

## The task

Write a workflow that runs `npm test` on every push and pull request.

1. Create the workflow directory and file:

```bash
mkdir -p .github/workflows
```

2. Write `.github/workflows/ci.yml`:

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
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      - run: npm test
```

3. Commit and push:

```bash
git add .github/workflows/ci.yml
git commit -m "Add CI workflow: test on push and PR"
git push -u origin main
```

## Observe the run

Watch it happen from the terminal:

```bash
gh run watch          # live-tails the run that just started
gh run view --log     # full log once it finishes
```

Or open the repo's **Actions** tab in the browser. You should see a `CI` run, one `test` job, four steps, and a green check on your latest commit.

## Prove it reacts to a PR

1. Branch, break the test on purpose, push, open a PR:

```bash
git checkout -b break-it
sed -i.bak 's/sum(2, 3), 5/sum(2, 3), 6/' test/sum.test.js && rm -f test/sum.test.js.bak
git commit -am "Break the test deliberately"
git push -u origin break-it
gh pr create --fill
```

2. Look at the PR (in the browser or `gh pr checks`). The `test` check should be **red**.
3. Fix it, push, watch it go green:

```bash
git checkout test/sum.test.js   # discard the break (revert the edit)
# re-apply from a clean state if needed, then:
git commit -am "Fix the test" && git push
gh pr checks           # should now show the check passing
```

## Expected result

- A `CI` workflow that runs on every push to `main` and on every PR.
- A green check on passing commits, a red check on the broken one.
- You never ran `npm test` by hand for the PR — the runner did.

## Done when…

- [ ] `.github/workflows/ci.yml` exists and is committed to `main`.
- [ ] The Actions tab shows at least one **successful** run and one **failed** run (from the deliberate break).
- [ ] `gh pr checks` on your PR reports the `test` check status.
- [ ] You can explain, in one sentence each, what `on:`, `runs-on:`, and the `checkout` step do.

## Stretch

- Add a second step that runs before the tests and prints the Node version (`- run: node --version`). Confirm it appears in the logs.
- Change the trigger to also run on `workflow_dispatch`, then trigger a run manually with `gh workflow run CI`.
- Set `permissions` to nothing at all (`permissions: {}`) and confirm the test job still passes — proving it needs no write access.
