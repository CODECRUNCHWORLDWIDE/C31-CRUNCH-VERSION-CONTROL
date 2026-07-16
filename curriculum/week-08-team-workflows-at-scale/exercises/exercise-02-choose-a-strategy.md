# Exercise 2 — Choose a Strategy

**Goal:** Practice the senior skill from Lecture 1 — reading a team's real constraints and choosing a branching strategy you can *defend with trade-offs*, not preference. You'll write a short decision memo for each of three scenarios.

**Estimated time:** 45 minutes.

## The task

For each scenario below, write a **decision memo** in `decisions.md` with exactly these four parts:

1. **Choice** — trunk-based, GitHub flow, or GitFlow (or a named hybrid).
2. **Why** — 3–5 sentences tying the choice to the scenario's specific constraints (release cadence, team size, rollback cost, parallel versions, CI maturity).
3. **What you'd give up** — the strongest argument *against* your choice; every choice has one.
4. **First config step** — the one concrete repo/protection change you'd make on day one.

Use the decision questions from Lecture 1 §6 as your checklist. There is no single right answer — you're graded on the reasoning, not on matching a key.

## The three scenarios

### Scenario A — SaaS web app

A 6-engineer team ships a web dashboard. They deploy to production via an automated pipeline several times a day. Rolling back is a one-click redeploy of the previous build. They have a solid test suite and CI that runs in ~4 minutes. Only one version is ever "live" — whatever's in production. Occasionally a feature takes two weeks to finish and they don't want a half-built UI visible to users.

*Hint to consider: what makes the long feature safe to merge early? (Lecture 1 §2.)*

### Scenario B — Firmware for a medical device

A 20-engineer team builds firmware for an insulin pump. Releases go through regulatory review and ship on a **scheduled** cadence — roughly quarterly. Once a version is certified and in the field, it must be patchable **independently** of the next version under development (a v3.2 field fix while v3.3 is being built). A bad release is catastrophic and cannot be "rolled back" from a patient's device. CI exists but a full validation run takes hours.

*Hint to consider: which strategy has native branches for "patch an old shipped version while building the next one"? (Lecture 1 §4.)*

### Scenario C — A popular open-source library

A library with 3 core maintainers and ~150 occasional external contributors. Contributors fork, work for a few days, and open pull requests. Every change must be reviewed by a maintainer and pass CI. Releases happen "when there's enough to release," a few times a month, and there's only ever one supported major line at a time. Contributors are not on the team and can't be trusted with direct push.

*Hint to consider: which strategy centers on the fork → short branch → PR → merge loop with a mandatory review checkpoint? (Lecture 1 §3.)*

## Then: pressure-test one answer

Pick the scenario you found **hardest**. Add a section `stress-test.md` where you argue the *opposite* choice as convincingly as you can, then explain what would have to change about the scenario to make that opposite choice correct. (E.g., "GitFlow for Scenario A would be right *if* they had to support three self-hosted customer versions in parallel.")

This is the real skill: knowing not just what you'd pick, but the conditions under which you'd pick differently.

## Expected result

Three defensible memos plus one stress-test. A reader who disagrees with a choice should still say "but I see why you chose it."

## Done when…

- [ ] `decisions.md` has all four required parts for each of the three scenarios.
- [ ] Each "Why" references at least two *specific* constraints from that scenario (not generic).
- [ ] Each memo names a real trade-off you'd be giving up.
- [ ] `stress-test.md` argues the opposite of one choice and states what would flip the decision.

## Stretch

- Add Scenario D from your own life — a repo or team you actually work on — and write its memo. Then go check: does the repo's *current* setup match what you'd choose? If not, why not?
- For Scenario B, sketch the branch diagram (`main`, `develop`, `release/3.2`, `hotfix/3.2.1`) and mark where the certified v3.2 field-patch flows.

## Submission

Commit `decisions.md` and `stress-test.md` to your portfolio under `c31-week-08/exercise-02/`.
