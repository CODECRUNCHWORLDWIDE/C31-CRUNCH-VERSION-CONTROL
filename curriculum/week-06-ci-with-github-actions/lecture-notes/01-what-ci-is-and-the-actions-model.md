# Lecture 1 — What CI Is, and the GitHub Actions Model

> **Duration:** ~2 hours. **Outcome:** You can explain what Continuous Integration is and why teams adopt it, and you can name and describe every layer of the GitHub Actions model — events, workflows, jobs, steps, and runners — well enough to read any workflow file and predict what it does.

For five weeks you've been the one running the tests. You edit code, you type `npm test`, you eyeball the output, you push. That works right up until it doesn't: you forget to run them, you run them against stale dependencies, your machine has a tool your teammate's machine lacks, or a reviewer approves a branch they never actually executed. Continuous Integration removes the human from that loop. This lecture explains the idea, then builds the vocabulary you need to make GitHub do it for you.

## 1. What "Continuous Integration" actually means

The term predates GitHub Actions by two decades. In the old world, developers worked on separate copies of a codebase for weeks, then went through a painful "integration phase" where everyone's changes were merged and everything broke at once. **Continuous Integration** is the discipline of integrating *often* — every developer merges small changes into a shared trunk many times a day — and, crucially, **automatically verifying each integration** with a build and a test run.

The automation is the part people mean when they say "CI" today. A CI system:

1. **Watches** your repository for changes (a push, a pull request).
2. **Checks out** the exact code of that change into a clean, disposable environment.
3. **Runs** a defined sequence of commands — install dependencies, build, lint, test.
4. **Reports** pass or fail back to the commit and the pull request.

The value is not the running of tests — you could do that yourself. The value is that it happens **every time, in a clean environment, whether or not anyone remembers.** "Works on my machine" stops being an argument, because the machine is a fresh, identical container every run.

| Without CI | With CI |
|------------|---------|
| Tests run when someone remembers | Tests run on every push, automatically |
| "Works on my machine" | Runs in a clean, standardized environment |
| Broken code discovered days later | Broken code flagged within minutes |
| Reviewers trust that tests passed | Reviewers *see* the green (or red) check |
| Merge, then find out it broke `main` | Merge is *blocked* until checks pass (Lecture 3) |

CI is a prerequisite for the payoff at the end of this week: if a machine reliably tells you whether a change is good, you can make that verdict a **merge requirement**, and then broken code cannot reach your trunk.

## 2. Why GitHub Actions

There are many CI systems — Jenkins, CircleCI, GitLab CI, Travis. We teach **GitHub Actions** because:

- It's **built into GitHub**, where your code already lives. No separate service to configure.
- It's **free for public repositories** — unlimited minutes — and generous on private ones.
- Workflows are **files in your repo** (`.github/workflows/*.yml`), so your CI configuration is version-controlled, reviewed, and branched exactly like code.
- The **marketplace of reusable actions** means most setup steps (`checkout`, `setup-node`, `cache`) are one line, not a shell script.

The last point matters: because the workflow is a file in the repo, changing your CI *is a pull request*, subject to the same review and the same protection rules as any other change.

## 3. The object model, top to bottom

Everything in Actions is one of five things. Learn these five nouns and you can read any workflow.

| Concept | What it is | Analogy |
|---------|-----------|---------|
| **Event** | Something that happens in the repo that can trigger automation | A doorbell ringing |
| **Workflow** | An automated process, defined in one YAML file, triggered by events | A recipe that runs when the bell rings |
| **Job** | A group of steps that run together on one runner | One dish in the recipe |
| **Step** | A single task: run a command, or use an action | One instruction in the dish |
| **Runner** | The virtual machine that executes a job | The kitchen the dish is cooked in |

Read that table twice. The rest of the lecture is just detail on each row.

### 3.1 Events

An **event** is a repository activity that can start a workflow. The most common:

| Event | Fires when… |
|-------|-------------|
| `push` | Commits are pushed to the repo |
| `pull_request` | A PR is opened, updated, or reopened |
| `schedule` | A cron timer elapses (e.g. nightly) |
| `workflow_dispatch` | You click "Run workflow" manually |
| `release` | A release is published |

A workflow declares which events it listens for at the top, under `on:`. You can scope events — for example, only pushes to `main`, or only PRs that touch certain files. We'll do exactly that next lecture.

### 3.2 Workflows

A **workflow** is a single YAML file in `.github/workflows/`. The directory name is not optional — GitHub only looks there. Each file is independent; a repo can have many (one for tests, one for deploys, one for nightly security scans). The minimal skeleton:

```yaml
name: CI                    # shows up in the Actions tab
on: [push, pull_request]    # events that trigger this workflow
jobs:
  test:                     # a job id you choose
    runs-on: ubuntu-latest  # which runner
    steps:
      - uses: actions/checkout@v4   # a step that USES an action
      - run: echo "hello from CI"   # a step that RUNS a command
```

Push that file and GitHub will, on your next push, spin up an Ubuntu machine, check out your code, and echo a greeting. That's a complete, working workflow.

### 3.3 Jobs

A **job** is a named collection of steps that all run on the **same** runner, in order, sharing a filesystem. Key facts:

- **Jobs run in parallel by default.** If you define `test` and `lint` jobs, GitHub runs them at the same time on separate machines.
- **Jobs are isolated from each other.** They do *not* share a filesystem. Anything one job builds is gone unless you explicitly pass it along (with artifacts or caching).
- **You can order jobs** with `needs:`. `deploy` can declare `needs: [test]` so it only runs after `test` succeeds.

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - run: echo "run the tests"
  deploy:
    needs: test              # deploy waits for test to pass
    runs-on: ubuntu-latest
    steps:
      - run: echo "ship it"
```

The mental model: **jobs = parallel machines; steps = sequential commands on one machine.**

### 3.4 Steps

A **step** is the smallest unit. Every step is one of two things:

1. **`run:`** — execute a shell command on the runner.
2. **`uses:`** — invoke a reusable **action** (a packaged unit of behaviour published on the marketplace or in a repo).

```yaml
steps:
  - uses: actions/checkout@v4        # action: check out the repo
  - uses: actions/setup-node@v4      # action: install Node.js
    with:
      node-version: '20'             # inputs to the action
  - run: npm ci                      # command: install deps
  - run: npm test                    # command: run tests
```

Steps in a job run **top to bottom on the same machine**, sharing the working directory. If step 3 installs dependencies, step 4 can use them. That's why `checkout` is almost always the first step — nothing else can see your code until it's there.

The `@v4` after an action name is a **version ref** — a Git tag, branch, or commit SHA in the action's own repository. Pinning a version protects you from an action changing behaviour underneath you. For security-sensitive workflows, teams pin to a full commit SHA rather than a tag.

### 3.5 Runners

A **runner** is the machine that executes a job. `runs-on: ubuntu-latest` asks GitHub for a fresh, GitHub-hosted Ubuntu virtual machine. Your options:

| `runs-on` value | Gives you |
|-----------------|-----------|
| `ubuntu-latest` | Latest Ubuntu LTS (the default choice, cheapest, fastest) |
| `windows-latest` | Latest Windows Server |
| `macos-latest` | Latest macOS (more expensive minutes) |
| `self-hosted` | A machine *you* provide and register |

The critical property of a GitHub-hosted runner is that it is **ephemeral and clean**: every job gets a brand-new VM that is destroyed when the job ends. Nothing persists between runs unless you deliberately cache or upload it. This is exactly what makes CI trustworthy — there is no "leftover state from last time" to hide a bug.

## 4. Reading a real workflow

Put it all together. Here is a complete, realistic Node.js CI workflow. Read it and narrate what each layer is:

```yaml
name: CI
on:
  push:
    branches: [main]
  pull_request:

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      - run: npm ci
      - run: npm test
```

Narration: *"On a push to `main` or on any pull request (**events**), run the `CI` **workflow**. It has one **job**, `test`, which runs on a fresh Ubuntu **runner**. The job has four **steps**: check out the code, install Node 20, install dependencies cleanly, and run the tests."* If you can narrate a workflow like that, you understand the model.

## 5. Where the results show up

When a workflow runs, the result attaches to the commit that triggered it as a **check run** — the little green ✓ or red ✗ you see next to a commit and, importantly, on a pull request. Each job becomes a separate check with a name (by default the job id). Those named checks are the exact things you will mark **required** in Lecture 3 to gate merges. For now, just know the chain: *event → workflow run → job → a named check on the commit/PR.*

You watch runs live in the repository's **Actions** tab, or from the terminal with the GitHub CLI:

```bash
gh run list                 # recent workflow runs
gh run watch                # live-tail the latest run
gh run view --log-failed    # jump straight to the failed step's logs
```

## 6. What CI is *not*

- **CI is not a deploy system.** Running tests on every push is CI. Automatically shipping to production is *Continuous Deployment* (CD) — a separate, later concern. Actions can do both, but this week is CI only.
- **CI does not fix your tests.** If your test suite is weak, green means little. CI enforces that whatever tests you *do* have keep passing.
- **CI is not free of cost on private repos.** Minutes are metered there. On public repos it's unlimited, which is why our examples are public.

## 7. Check yourself

- In one sentence, what problem does Continuous Integration solve that running tests locally does not?
- Name the five layers of the Actions model, from the trigger down to the machine.
- Do two jobs in the same workflow share a filesystem? Do two steps in the same job?
- What is the significance of a runner being *ephemeral*?
- Where must a workflow file live for GitHub to find it?
- What does `@v4` mean in `uses: actions/checkout@v4`, and why pin it?
- What is the chain of objects between "someone pushed" and "a green check appears on the PR"?

When all seven are easy, move to [Lecture 2](./02-writing-a-workflow.md) and write a workflow that does real work.

## Further reading

- **Understanding GitHub Actions (official):** <https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions>
- **Events that trigger workflows:** <https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows>
- **About GitHub-hosted runners:** <https://docs.github.com/en/actions/using-github-hosted-runners/about-github-hosted-runners>
