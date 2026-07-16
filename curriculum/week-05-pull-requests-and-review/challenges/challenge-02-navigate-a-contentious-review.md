# Challenge 2 — Navigate a Contentious Review to a Good Outcome

**Estimated time:** ~1.5 hours.

## Scenario

You opened a PR you're proud of. The reviewer left this:

> "I don't think we should do it this way at all. This whole approach is wrong — you're reinventing what the `stdlib` already does, and the naming is confusing. I'd rewrite this from scratch. **Request changes.**"

Your pulse jumps. Maybe they're right. Maybe they skimmed and missed *why* you did it this way. Maybe they're technically right about the stdlib but the naming complaint is just taste. The thread is one careless reply away from becoming a standoff that poisons the next month of working together.

This challenge is about the **human** half of review — the part no `git` command touches. There's no single correct answer; you're judged on how you'd steer this to a merged, better change *and* an intact relationship.

## Set it up (role-play or real)

Do this with a partner if you can. One person plays the author, one the reviewer; the reviewer leaves a genuinely blunt (but not abusive) review on a real PR. No partner? Write it out: paste the reviewer's comment above onto one of your own PRs and write your responses as real PR comments, then reflect.

You'll produce **written responses** — actual PR comments — plus a short reflection. The words are the deliverable.

## Your task

Take the review to a good outcome. That means working through, in writing:

1. **Separate correctness from taste.** The reviewer made (at least) two claims: "reinventing the stdlib" (a *correctness/design* claim — checkable against facts) and "the naming is confusing" (a *taste* claim). Handle them differently. Facts you resolve with facts; taste you negotiate or defer to convention.
2. **Assume good faith and de-escalate.** Reply in a way that lowers the temperature, not raises it. Thank them for the scrutiny. Ask a clarifying question if the objection is vague.
3. **Get to a decision.** Either you're convinced (change the approach, and say so gracefully), or you're not (make your case with evidence), or you're stuck (escalate — see below). The thread must not just die.

## Constraints

- **Every reply critiques the code or the argument, never the person.** No "you clearly didn't read it," even if they clearly didn't.
- **You must respond to the strongest version of their point**, not the weakest. If "reinventing the stdlib" is *right*, concede it — fast and cleanly. Digging in on a point you're wrong about is how reviews rot.
- **Resolve the taste dispute with something other than "because I like it."** Point to a convention, a style guide, existing code in the repo, or offer to defer to the reviewer since it's minor.
- **If you and the reviewer genuinely can't agree on a substantive point, escalate — don't stall.** Loop in a third person or a team norm. State the trade-off neutrally so the tiebreaker can decide on facts.

## Hints

<details>
<summary>A de-escalating opening reply</summary>

> "Thanks for the careful look — appreciate you flagging the stdlib overlap, I want to get this right. Two things I want to separate: (1) the stdlib point, and (2) the naming. On (1): can you point me at the stdlib function you're thinking of? If it covers our empty-input and locale cases I'll happily rip mine out. On (2): totally open to renames — got specific suggestions?"

Notice: thanks, splits the two issues, asks for the *specific* stdlib function (turning a vague verdict into a checkable fact), and yields on naming immediately because it's cheap.
</details>

<details>
<summary>When they're right about the big thing</summary>

Concede clearly and without drama: "You're right — `itertools.groupby` does exactly this and handles the edge case I hand-rolled. I'll rewrite on top of it. Good call." Conceding fast when you're wrong *builds* your credibility; it doesn't spend it.
</details>

<details>
<summary>When you're right and they skimmed</summary>

Don't say "you skimmed." Supply the missing context and make it easy to verify: "The stdlib version doesn't handle the streaming case — see the comment on line 40 and the test `test_large_input`. That's the reason for the custom pass. Does that address the concern, or is there a stdlib path I'm missing?" You're inviting them to be convinced, leaving them a face-saving exit.
</details>

<details>
<summary>When it's a true deadlock</summary>

"We've each made our case and still disagree on X. It's a real trade-off — mine is simpler to read, yours is faster. Rather than go back and forth, let's get @lead's read, or check if we have a team convention. I'm fine either way once we decide." Escalation isn't losing; unresolved stalling is.
</details>

## How success is judged

| Criterion | What "great" looks like |
|-----------|-------------------------|
| Separates fact from taste | Handles the stdlib claim with evidence; handles naming by convention/deferral |
| Tone | Every reply is about the code/argument; temperature goes down, not up |
| Steel-mans the reviewer | Engages the strongest version of the objection, concedes cleanly when wrong |
| Reaches a decision | Thread ends in merge, a rewrite, or an escalation — never a silent stall |
| Relationship intact | You'd both happily review each other's next PR |

## Deliverable

`review-navigation.md` containing: the reviewer's original comment, your written replies (the real PR thread, copied in), the outcome (what you changed, conceded, or escalated), and a 150–200 word reflection on the hardest moment — where you felt defensive, and what you did with that feeling. That last part is the real lesson: review skill is 20% Git and 80% managing your own ego under critique.
