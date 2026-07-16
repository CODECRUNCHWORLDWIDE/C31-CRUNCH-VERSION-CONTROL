# Week 6 — Homework

Practice tasks that reinforce each lecture. Do them in your `cc-ci-week6` repo (or a fresh scratch repo). Budget **~4 hours** total. Commit your work; where a task asks for a written answer, keep it in a `homework/` folder as markdown.

Each task lists the lecture it reinforces so you know where to look if you're stuck.

---

## 1. Narrate a workflow (Lecture 1) — ~20m

Find any real workflow file in the wild (search GitHub for `filename:ci.yml`, or use a popular open-source repo's `.github/workflows/`). Copy it into `homework/found-workflow.yml` and, beneath it in `homework/narration.md`, **narrate it in the five-noun vocabulary**: which events trigger it, what workflow(s) it defines, what jobs run and whether in parallel, what the notable steps do, and what runners it uses. One paragraph is plenty.

**Done when:** your narration correctly identifies every event, job, and runner in the file.

---

## 2. Predict, then verify (Lecture 1) — ~20m

Before pushing, **write down** in `homework/predictions.md` what you expect to happen when you push a small change to your repo: how many jobs run, on what runners, and roughly how long. Then push and compare against the Actions tab. Note any surprise.

**Done when:** you've recorded a prediction and the actual outcome, and explained any gap.

---

## 3. From `install` to `ci` (Lecture 2) — ~25m

If your workflow uses `npm install` (or a loose `pip install`), switch it to a **lockfile-deterministic** install (`npm ci`, or a pinned requirements install). In `homework/why-ci.md`, explain in 3–4 sentences *why* CI should use the deterministic form — what specifically could go wrong with the loose form in a shared pipeline.

**Done when:** the workflow uses the deterministic install and your explanation names the concrete risk (drift from the lockfile).

---

## 4. Break the matrix on purpose (Lecture 2) — ~30m

Add a matrix of at least 2 runtime versions if you don't have one. Then introduce a bug that fails on **only one** version (for example, use a syntax or API only available in a newer runtime). Set `fail-fast: false`. Push, and confirm in the Actions tab that the other matrix legs **still complete** while the one leg fails. Screenshot the run into `homework/`.

**Done when:** exactly one matrix leg is red and the others finished, proving you understand `fail-fast: false`.

---

## 5. Measure the cache (Lecture 2) — ~25m

With caching enabled, do two consecutive pushes and record the duration of the dependency-install step on each, plus whether the log said "cache hit" or "cache miss." Put a two-row table in `homework/cache-timing.md`. Then answer: **what would invalidate the cache?** (Change a dependency and confirm the next run is a miss.)

**Done when:** you have real before/after timings and correctly explain what changes the cache key.

---

## 6. Secrets, safely (Lecture 2) — ~20m

Create a repo secret named `DEMO_SECRET` (any value). Add a workflow step that uses it as an `env:` var and runs `echo "len is ${#DEMO_SECRET}"` (print its *length*, never its value). Confirm in the logs that the length prints but the value is **masked** if you accidentally echo it. In `homework/secrets.md`, explain in two sentences why fork PRs don't receive secrets.

**Done when:** you've seen masking work and can explain the fork-PR restriction. *(Delete the demo secret after.)*

---

## 7. Configure a gate from scratch (Lecture 3) — ~30m

On a fresh branch pattern or a second scratch repo, set up branch protection **entirely from the CLI** (`gh api ... /branches/main/protection`). Then verify it with `gh api ... | jq`. In `homework/protection.md`, paste the command you used and the resulting JSON, and annotate what each `required_status_checks` field means.

**Done when:** protection is applied via CLI and you can explain `strict`, `contexts`, and `enforce_admins`.

---

## 8. Diagnose a real failure (Lecture 3) — ~30m

Deliberately create a workflow that **fails to appear at all** (misindent the YAML, or put the file outside `.github/workflows/`). Observe that nothing runs. Then fix it. In `homework/debugging.md`, list the checklist you used to diagnose "my workflow didn't run" and which item was the culprit.

**Done when:** you reproduced a silent no-run, fixed it, and wrote down the diagnostic checklist.

---

## Submission

Commit the `homework/` folder to your repo under `c31-week-06/`. Each task's artifact (markdown, screenshot, or config) should be present and named clearly. Skim the [quiz](./quiz.md) once homework is in — it draws on all three lectures.
