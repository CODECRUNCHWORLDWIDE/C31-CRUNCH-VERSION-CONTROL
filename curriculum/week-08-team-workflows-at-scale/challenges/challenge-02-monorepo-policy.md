# Challenge 2 — Design a Monorepo Contribution Policy

**Time:** ~90 minutes. **Difficulty:** Hard. **No single right answer.**

## The scenario

`acme-platform` is a monorepo four teams share:

```
acme-platform/
├── services/
│   ├── payments/      # owned by Team Payments (high-risk, PCI-adjacent)
│   ├── auth/          # owned by Team Security
│   └── catalog/       # owned by Team Catalog
├── web/               # owned by Team Frontend
├── packages/
│   ├── ui/            # shared component library, everyone depends on it
│   └── sdk/           # shared client SDK, everyone depends on it
└── .github/
```

Reality today:

- One CI workflow runs **every test in the repo on every push** — a 25-minute wall even for a one-line docs change. People are furious.
- There are no clear ownership rules, so PRs sit unreviewed because nobody knows whose job it is.
- A change to `packages/ui` recently broke `web` and `catalog` and nobody noticed until production.
- Teams want to release **independently** where possible, but shared packages make that fuzzy.

Your job: write the **contribution policy** that makes this monorepo scale — fast CI, clear ownership, safe shared-code changes, and a sane release model.

## Constraints

- **CI must be scoped.** A docs-only or single-service change must not run the whole 25-minute suite. Design path-based CI (Lecture 2 §5).
- **Ownership must be explicit and enforced.** Use `CODEOWNERS` so the right team is a *required* reviewer for its paths, and shared packages get extra scrutiny.
- **Shared code (`packages/ui`, `packages/sdk`) is special.** A change there can break every consumer. Your policy must say how such changes are reviewed, tested against consumers, and released.
- **Releases:** decide how independent releases work in one repo. Per-service tags? A tool like `changesets`? Justify it.

## Deliverable

A `contribution-policy.md` that a new engineer could read on day one, covering:

1. **CODEOWNERS design** — the actual file contents (paths → teams), including how `packages/*` gets stricter ownership. Show you understand last-match-wins.
2. **CI strategy** — how you scope builds to changed paths (describe the trigger filters and/or a change-detection step), and what the *required* checks are per area. What must be green to merge a `payments` change vs. a `docs` change?
3. **Shared-code rule** — the explicit policy for changing `packages/ui`/`packages/sdk`: who reviews, how consumers are tested, how it's versioned/released, and how you avoid the "silently broke `web`" incident.
4. **Branching + protection** — the branching model (likely trunk-based or GitHub flow; Lecture 1) and the protection rules on `main`.
5. **Release model** — how each service/package ships, and how you handle a shared-package bump that forces consumer updates.

## Hints

<details>
<summary>How do you scope CI to changed paths?</summary>

Two common patterns:
- **`paths:` filters on the workflow trigger** — a workflow that only runs when its area changed (`on: pull_request: paths: ['services/payments/**']`). Simple, but "required status checks" get awkward when a check doesn't run (it can't be both required and skipped) — you often need a "always-passes if not applicable" shim or path-filter action.
- **A change-detection job** (e.g., `dorny/paths-filter`) that computes which areas changed, then conditionally runs downstream jobs. More flexible, keeps a single required "gate" job that depends on the relevant ones.

Name the trade-off you're accepting.
</details>

<details>
<summary>Why are shared packages the crux?</summary>

`packages/ui` has many internal consumers. A breaking change there is a breaking change to `web` and `catalog` *simultaneously*, in the same repo. That's actually a monorepo *superpower*: you can change `ui` and all its callers in **one atomic PR**, so nothing is ever half-migrated — but only if CI runs the consumers' tests against the change. Your policy should exploit this, not fear it.
</details>

<details>
<summary>Independent releases in one repo?</summary>

`changesets` is built for exactly this: contributors declare per-package version intents, and it computes independent version bumps and changelogs per package. Alternatively, per-directory tags (`payments-v1.4.0`) with a release workflow triggered by path. Pick one and justify.
</details>

## How success is judged

- **CI is genuinely scoped** — your design makes a small change cheap, and you honestly address the "required check that didn't run" gotcha.
- **Ownership is unambiguous** — the CODEOWNERS file is real and correct (last-match-wins respected), and shared packages get stricter rules.
- **The shared-code policy prevents the incident** — a `packages/ui` change would now run consumer tests and require the right reviewers.
- **Releases are coherent** — independent where it makes sense, coordinated where shared code forces it, with a named tool/approach.
- **A newcomer could follow it** — it reads like a policy, not a brainstorm.

## Submission

Commit `contribution-policy.md` (and a sample `CODEOWNERS` + a sketch CI workflow) to your portfolio under `c31-week-08/challenge-02/`.
