# Week 8 — Resources

Free, public, no signup unless noted. Each link has a one-line "why this."

## Branching strategies

- **Trunk-Based Development (canonical site):** <https://trunkbaseddevelopment.com/>
  — The reference for what trunk-based dev actually is, including the scaled-trunk and short-lived-branch variants.
- **"A successful Git branching model" — Vincent Driessen:** <https://nvie.com/posts/a-successful-git-branching-model/>
  — The original GitFlow post; read the note at the top where the author himself says it's overkill for continuous-delivery web apps.
- **GitHub flow (official):** <https://docs.github.com/en/get-started/using-github/github-flow>
  — The six-step loop, straight from the source.
- **Martin Fowler, "Patterns for Managing Source Code Branches":** <https://martinfowler.com/articles/branching-patterns.html>
  — The deepest free treatment of branch lifetime, integration frequency, and the trade-offs behind every strategy.

## Protection, ownership, and rulesets

- **GitHub — About protected branches:** <https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches>
  — Every protection rule and exactly what it enforces.
- **GitHub — About code owners:** <https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners>
  — CODEOWNERS syntax, matching rules, and the last-match-wins behavior.
- **GitHub — About rulesets:** <https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/about-rulesets>
  — The newer org-level system for applying protection across many repos and branch patterns.
- **GitHub REST — Branch protection API:** <https://docs.github.com/en/rest/branches/branch-protection>
  — What the `gh api ... /branches/main/protection` calls in the exercises are hitting.

## Monorepo vs multi-repo

- **monorepo.tools:** <https://monorepo.tools/>
  — A balanced overview of the monorepo trade-off and the build tools (Nx, Turborepo, Bazel) that make it viable.
- **"Advantages and disadvantages of a monorepo" and rebuttals** — read one pro and one con; the monorepo.tools "understanding" section links both sides.

## Versioning and releases

- **Semantic Versioning 2.0.0:** <https://semver.org/>
  — The short, normative spec. Read it once in full; it's under 15 minutes.
- **Conventional Commits 1.0.0:** <https://www.conventionalcommits.org/>
  — The commit-message convention that drives automated versioning.
- **Keep a Changelog:** <https://keepachangelog.com/>
  — The human-readable changelog format the tools generate.
- **release-please:** <https://github.com/googleapis/release-please>
  — Google's Release-PR-based automation, used in Exercise 3.
- **semantic-release:** <https://semantic-release.gitbook.io/>
  — Fully automatic release-on-push; the trunk-based end of the spectrum.
- **changesets:** <https://github.com/changesets/changesets>
  — Per-package versioning for monorepos publishing multiple packages.

## Tooling to have installed

- **GitHub CLI (`gh`):** <https://cli.github.com/> — scripts every protection/release step in this week's exercises.
- **`jq`:** <https://jqlang.github.io/jq/> — parse the JSON that the protection API returns.
- **`commitlint`:** <https://commitlint.js.org/> — enforce Conventional Commits in a hook or CI.

## Glossary

| Term | Definition |
|------|------------|
| **Trunk** | The single shared mainline branch (usually `main`) everyone integrates into. |
| **Merge debt** | The growing cost of merging a branch back, proportional to how long it drifted from the trunk. |
| **Feature flag** | A runtime toggle that lets incomplete code be merged (off by default) instead of living on a long branch. |
| **Branch protection** | Platform-enforced rules on a branch (require PR, reviews, checks) that replace "we should" with "you can't unless." |
| **CODEOWNERS** | A file mapping paths to owning users/teams who are auto-requested (and can be required) as reviewers. |
| **Required status check** | A named CI check that must be green before the merge button is enabled. |
| **Monorepo** | One repository holding many projects/services/packages. |
| **SemVer** | `MAJOR.MINOR.PATCH` versioning where the number encodes compatibility. |
| **Conventional Commits** | A structured commit-message format (`feat:`, `fix:`, `feat!:`) machines read to compute version bumps. |
| **Runbook** | An operating manual for a system — the procedures a teammate follows to run it. |

---

*Broken link? Open an issue.*
