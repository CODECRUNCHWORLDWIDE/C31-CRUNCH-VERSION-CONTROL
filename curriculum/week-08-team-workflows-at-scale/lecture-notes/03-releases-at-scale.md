# Lecture 3 — Releases at Scale

> **Duration:** ~2 hours. **Outcome:** You can version software with Semantic Versioning, write Conventional Commits that machines can read, and stand up a workflow that generates a changelog, bumps the version, tags a release, and publishes it — automatically, from commit history alone.

A "release" is the act of drawing a line and saying: *this exact state of the code is a thing people can depend on, and it has a name.* On a solo project you might do that by hand once a month. On a team shipping daily, doing it by hand is a source of errors and arguments — what's the new version number? what changed? did we tag it? did we forget to update the changelog? This lecture makes releases **mechanical**: the commit history is the single source of truth, and a workflow turns it into a versioned, documented, tagged release with no human deciding "is this a minor or a patch."

## 1. Semantic Versioning — the contract in the number

A version string like `2.4.1` is a **promise about compatibility**, not a marketing counter. Semantic Versioning (SemVer) defines `MAJOR.MINOR.PATCH`:

| Part | Bump when… | Signals to users |
|------|-----------|------------------|
| **MAJOR** (`2`.x.x) | You make an **incompatible** API change | "Upgrading may break your code. Read the migration notes." |
| **MINOR** (x.`4`.x) | You add functionality in a **backward-compatible** way | "Safe to upgrade; new features available." |
| **PATCH** (x.x.`1`) | You make a **backward-compatible bug fix** | "Safe to upgrade; just fixes." |

The whole point is that a consumer can read the number and know the risk of upgrading *without reading the code.* `1.4.2 → 1.5.0` is safe; `1.4.2 → 2.0.0` demands attention. That contract only works if you bump honestly — shipping a breaking change as a MINOR is how you break everyone who trusted the number.

Extra rules worth knowing:

- **`0.y.z` is the wild west.** Below `1.0.0`, anything may change at any time; SemVer's guarantees don't apply yet.
- **Pre-releases** hang off a hyphen: `2.0.0-rc.1`, `2.0.0-beta.3`. They sort *before* the final `2.0.0`.
- **Build metadata** hangs off a plus: `2.0.0+build.5` (ignored for precedence).
- The version is usually stored where the ecosystem expects it: `package.json` (Node), `pyproject.toml` / `__version__` (Python), `Cargo.toml` (Rust), or a `VERSION` file, and mirrored as a Git tag `v2.4.1`.

## 2. Conventional Commits — commit messages a machine can read

If SemVer says *how* to bump, Conventional Commits tells the machine *which* bump this change deserves — by putting the answer in the commit message in a structured way. The format:

```
<type>[optional scope][!]: <description>

[optional body]

[optional footer(s)]
```

Real examples:

```
feat(auth): add password-reset endpoint
fix: prevent crash when cart is empty
docs: clarify the install steps
refactor(api)!: drop the deprecated v1 routes
```

The **type** classifies the change; two types map directly to SemVer:

| Commit | Meaning | SemVer effect |
|--------|---------|---------------|
| `fix:` | a bug fix | **PATCH** bump |
| `feat:` | a new feature | **MINOR** bump |
| `feat!:` / `fix!:` / any `type!:` | a **breaking** change (note the `!`) | **MAJOR** bump |
| `BREAKING CHANGE:` in the footer | a breaking change | **MAJOR** bump |
| `docs:`, `chore:`, `test:`, `refactor:`, `ci:`, `style:`, `perf:`, `build:` | no user-facing behavior change | no bump (but may appear in the changelog) |

Two ways to mark a breaking change: a `!` after the type/scope (`refactor(api)!:`), or a `BREAKING CHANGE: <what broke>` line in the footer. Either one forces a MAJOR bump.

This is the linchpin. Once every commit declares its type, a tool can scan the commits since the last tag, find the highest-impact change (any breaking → MAJOR, else any `feat` → MINOR, else any `fix` → PATCH), and compute the next version with **zero human judgment.** The commit history *is* the release plan.

You can enforce the format locally with a commit-msg hook (`commitlint`) or in CI, so a malformed message is caught before it pollutes the history.

## 3. Changelogs — the human-readable diff

A `CHANGELOG.md` is the release's story for humans: what changed, grouped and dated, newest first. The community standard is **Keep a Changelog**, which groups entries under headings like `Added`, `Changed`, `Fixed`, `Removed`, `Deprecated`, `Security`:

```markdown
# Changelog

## [2.5.0] - 2026-07-15
### Added
- Password-reset endpoint (`feat(auth)`).
### Fixed
- Crash when the cart is empty.

## [2.4.1] - 2026-06-30
### Fixed
- Null user on the profile page.
```

The beautiful part: **if your commits are Conventional Commits, the changelog writes itself.** `feat:` commits become "Added," `fix:` commits become "Fixed," breaking changes get flagged. You stop hand-maintaining a file that always drifts out of sync with reality, because the reality (the commits) generates it.

## 4. Automating the release

Now assemble it. The pipeline, conceptually:

1. Read all commits since the last release tag.
2. Determine the next version from their types (SemVer bump).
3. Generate/update `CHANGELOG.md`.
4. Bump the version in the manifest (`package.json`, etc.) and commit it.
5. Create and push a Git tag `vX.Y.Z`.
6. Create a GitHub Release with the changelog as its notes (and attach build artifacts if any).
7. Optionally publish to a registry (npm, PyPI, container registry).

You do **not** write this from scratch. Battle-tested tools do steps 1–7:

- **semantic-release** — fully automated: on every push to `main`, it decides if a release is warranted and does the whole pipeline. Zero manual version decisions.
- **release-please** (Google) — opens and maintains a "Release PR" that accumulates the next version + changelog; you merge it when you're ready to release. A nice middle ground: automated computation, human "go" button.
- **changesets** — popular for monorepos publishing multiple packages; contributors add intent files describing their change's bump.
- **git-cliff / standard-version** — changelog/version generators you run yourself.

### A minimal release-please workflow

`release-please` illustrates the "automated computation, human trigger" model cleanly. Drop this in `.github/workflows/release.yml`:

```yaml
name: release
on:
  push:
    branches: [main]

permissions:
  contents: write        # to push tags, the release commit, and the GitHub Release
  pull-requests: write   # to open/maintain the Release PR

jobs:
  release-please:
    runs-on: ubuntu-latest
    steps:
      - uses: googleapis/release-please-action@v4
        with:
          release-type: node   # reads/writes package.json version
```

How it behaves: every push to `main` gets scanned. `release-please` maintains a **Release PR** titled like "chore: release 2.5.0" that contains the computed version bump and the generated `CHANGELOG.md`. It keeps updating that PR as more `feat`/`fix` commits land. When you're ready, you **merge the Release PR** — that merge tags `v2.5.0`, updates the changelog on `main`, and creates the GitHub Release. Nobody picked the number; the commits did.

### The fully-automatic variant

With **semantic-release**, there's no Release PR — a push to `main` that contains a releasable commit *is* the release:

```yaml
name: release
on:
  push:
    branches: [main]
permissions:
  contents: write
jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with: { fetch-depth: 0 }   # semantic-release needs full history + tags
      - uses: actions/setup-node@v4
        with: { node-version: 20 }
      - run: npx semantic-release
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

`fetch-depth: 0` matters: the tool must see the whole history and all tags to know where the last release was. This is the trunk-based, continuous-delivery end of the spectrum — merge a `feat:`, and minutes later `v2.5.0` exists.

## 5. Choosing an approach

| You want… | Reach for |
|-----------|-----------|
| A human "go" button, computed version + changelog | release-please |
| Every releasable merge to ship instantly, no button | semantic-release |
| Many packages in one monorepo, per-package versions | changesets |
| Just a changelog/version bump you run manually | git-cliff / standard-version |

All of them depend on the same foundation: **Conventional Commits.** Adopt the commit convention first; the tooling choice is secondary and swappable.

## 6. Anti-patterns

- **Hand-editing the version number and the changelog.** They drift, they conflict, and someone always forgets. Automate.
- **Lying with SemVer** — shipping a breaking change as a MINOR because "it's a small break." There is no small break; MAJOR exists for exactly this.
- **`chore:`-ing a real feature** to dodge a version bump. The commit type is a contract; gaming it corrupts the automation.
- **Tagging by hand and forgetting `fetch-depth: 0`.** The automation then can't find the last tag and either re-releases or does nothing.
- **A changelog nobody reads because it's raw commit hashes.** Group by type, write for humans; the tools do this if you let them.

## 7. Self-check

- Given commits `fix: …`, `feat: …`, and `refactor(api)!: …` since the last `v1.4.0`, what's the next version and why?
- What's the difference in *when a release happens* between release-please and semantic-release?
- Why must the release workflow check out the full history (`fetch-depth: 0`)?
- How does a `feat(auth): …` commit end up as an "Added" line in `CHANGELOG.md` with no human writing it?
- Below `1.0.0`, what does SemVer promise about compatibility?

That's the release engine. In the mini-project you'll wire branching, protection, CI gates, review rules, and this release pipeline into one team workflow you actually run.

## Further reading

- **Semantic Versioning 2.0.0 (the spec):** <https://semver.org/>
- **Conventional Commits 1.0.0:** <https://www.conventionalcommits.org/>
- **Keep a Changelog:** <https://keepachangelog.com/>
- **release-please:** <https://github.com/googleapis/release-please> · **semantic-release:** <https://semantic-release.gitbook.io/>
