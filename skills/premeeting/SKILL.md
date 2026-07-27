---
name: premeeting
description: Prep for an upcoming call or meeting using DISC personality intelligence — resolve your and each attendee's DISC via Crystal, pin down the meeting goal, and produce a DISC-tailored prep note (approach, pacing, agenda). Use before a call; hands off to transcript-analysis afterward for a plan-vs-actual debrief.
---

# Pre-meeting Prep (DISC-grounded)

Help the user walk into a call prepared: know who's in the room (DISC-wise), know
the goal, and have a plan/agenda shaped to how those specific people communicate.
Personality data comes from the **Crystal MCP server** — never guess a person's
DISC type; resolve it with the tools below.

## When to use

Trigger when the user wants to prepare for an upcoming call or meeting: "help me
prep for my call with...", "I've got a meeting with [name] tomorrow", "draft an
agenda for this meeting", "what should I know before I talk to [name]?", etc.

This skill is the counterpart to `transcript-analysis` — that one debriefs a call
after it happens; this one preps for it beforehand.

## Prerequisites

This skill depends on the **Crystal MCP tools** being available (`multi_search`,
`search`, and optionally `generate_prompt` / `selling_to`) — see
`transcript-analysis`'s SKILL.md for details on each. If the tools aren't
available, tell the user to install/authenticate the `crystal-knows` plugin (or
connect the Crystal connector in claude.ai), then stop.

## Procedure

### 1. Get the meeting basics

First, check your **own available tools** for a calendar connector — look for
tool names containing `calendar`, `gcal`, `outlook`, `google_calendar`, or
`microsoft`/`365`. Do not assume one exists; only rely on it if you actually see
it in your current tool list.

- **If a calendar connector is available:** tell the user, then use it to find
  the relevant upcoming event (ask which one if there's ambiguity — by title,
  time, or attendee) and pull the title, time, and attendee list from it. Still
  confirm the pulled details back to the user before proceeding — invite data is
  often incomplete (missing agenda, stale attendee list).
- **If no calendar connector is available:** ask the user directly:

  > Who are you meeting with (name + email or LinkedIn URL for each), and what's
  > the meeting about?

  Add one line noting the alternative, but don't belabor it:

  > Tip: if you connect a Google Calendar or Outlook connector (Settings →
  > Connectors in claude.ai, or an MCP server in Claude Code), I can pull invite
  > details directly next time instead of you typing them out.

### 2. Identify attendees

List the distinct people the user will be meeting with, separate from the user
themselves. Present the list back for confirmation, same as you would with
transcript participants.

### 3. Resolve DISC types

1. Gather an **email or LinkedIn URL** per attendee (`multi_search` only matches
   on those, not names). Ask the user to fill in gaps:

   > To pull DISC types I need an email or LinkedIn URL for each person. I have:
   > - Jane Doe → (need email or LinkedIn)
   > - John Smith → john@acme.com
   > Please fill in the missing ones (or say "skip" for anyone).

2. Call `multi_search` with everything collected (batch, max 25). Fall back to
   `search` by name for anyone only identifiable by name.
3. Also resolve the **user's own** DISC type if you have their email/LinkedIn
   (their own message signature, or ask) — their natural style is the baseline
   they'll need to adapt from.
4. Note anyone who couldn't be resolved; continue with whoever did, and flag
   unresolved people as DISC-blind rather than guessing.

### 4. Get the meeting goal

Ask explicitly if it isn't already clear:

> What's the one outcome that would make this meeting a win?

If the user skips this, proceed but keep the prep more general — say so rather
than inventing a goal.

### 5. Build the prep

Synthesize attendee DISC types (call out the dominant type in the room, e.g. "this
is mostly a high-C room"), the user's own DISC, and the goal into:

- **Your adaptation plan** — how the user's natural style should flex for this
  specific room. E.g. a high-D user meeting high-C attendees needs to slow down
  and bring specifics, not just conclusions.
- **Recommended pacing/structure** — per the quick reference below.
- **A draft agenda, if warranted** — not every meeting needs one. Lean toward
  drafting one when: attendees skew **C** (structure signals credibility) or
  **D** (keeps things moving, respects time), the meeting is high-stakes, or the
  user asked for one outright. Skip or keep it loose for **I**/**S**-heavy,
  relationship-first meetings where a rigid agenda can feel cold.
- **Prep checklist** — concrete things to bring or do beforehand (data/specifics
  for C attendees, a tight opening line for D attendees, rapport-building context
  for I attendees, reassurance about pace/change for S attendees).
- **Watch-outs** — likely DISC mismatches to avoid, phrased as specific pitfalls
  for this room, not generic advice.

If it's a sales context and the tools are available, you may pull in
`generate_prompt` or `selling_to` for extra angle — treat as optional depth, not
a required step.

### 6. Output

```
## Meeting: <title/context> — <date/time if known>

## Attendees & DISC
- <Name> — <DISC type> (<archetype>): <one-line read>
- <Name> — unknown (couldn't resolve)

## You — <DISC type or "unknown">
<one-line note on how their natural style maps to this room>

## Goal
<stated goal, or "not specified">

## Approach
<the adaptation plan + pacing/structure recommendation>

## Agenda
<drafted agenda with rough time allocations, or "not needed for this one — here's
why" if skipped>

## Prep checklist
- <item>

## Watch-outs
- <specific DISC mismatch to avoid>
```

Close with a handoff note:

> After the call, run the transcript-analysis skill with the transcript — I'll
> check what actually happened against the goal and plan above. Keep this prep
> note (or stay in this chat) so I have it to compare against.

## DISC quick reference — running a meeting

- **D — Dominance**: lead with the bottom line and the ask. Time-box strictly.
  Give them control over next steps. Avoid long preamble or open-ended
  brainstorming.
- **I — Influence**: open with genuine rapport, not straight into business. Keep
  energy up, leave room for tangents but steer back. Avoid a cold, agenda-only
  read of bullet points.
- **S — Steadiness**: signal the plan and pace up front so there are no
  surprises. Avoid springing big asks or sudden pivots; give them room to
  process before pushing for a decision.
- **C — Conscientiousness**: send a detailed agenda beforehand if possible; bring
  data and specifics, not just conclusions. Avoid hype, vagueness, or rushing
  past their questions.

Two-letter types (e.g. `Di`, `Sc`) blend the primary and secondary — weight the
primary letter and soften with the secondary.
