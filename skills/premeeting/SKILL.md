---
name: premeeting
description: Prep for an upcoming call or meeting using DISC personality intelligence and account context — resolve your and each attendee's DISC via Crystal (weighted toward external attendees), help pick a concrete goal (tailored options, not a blank prompt) and meeting type, and produce a prep note actually curated by that goal (approach, pacing, agenda). Use before a call; hands off to transcript-analysis afterward for a plan-vs-actual debrief.
---

# Pre-meeting Prep (DISC-grounded)

Help the user walk into a call prepared: know who's in the room (DISC-wise, and
internal vs. external), know what the account needs, land on a concrete goal,
and have a plan/agenda that's actually curated by that goal — not just DISC and
meeting-type templates with the goal mentioned in passing. Personality data
comes from the **Crystal MCP server** — never guess a person's DISC type;
resolve it with the tools below.

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

### 2. Identify attendees — internal vs. external

List the distinct people the user will be meeting with, separate from the user
themselves. For each, tag whether they're **internal** (a teammate — same
company/email domain as the user, or the user says so) or **external** (a
prospect, customer, partner, etc.). Present the list back for confirmation,
same as you would with transcript participants — ask if a tag is unclear rather
than assuming from domain alone (e.g. a contractor on a shared domain).

This split drives step 6: the room-read and adaptation plan should weight
toward the **external** attendees — they're who the user actually needs to
adapt to. Note internal teammates' DISC if useful for coordinating during the
call, but don't let them dilute the read on the room.

### 3. Account context (external meetings only)

If there are external attendees, identify the account/company they're from
(email domain, or ask). Ask one line:

> Anything about [Company]'s industry, size, or stage that should shape this —
> e.g. a fast-growing DTC brand, an enterprise IT buyer, cost-conscious right
> now, early-stage vs. established, etc.?

If web search is available to you, you may do a quick sanity check on
firmographics (industry, rough size, funding/growth stage) to sharpen the
question or fill gaps the user doesn't know offhand — but treat that as a
starting point to confirm with the user, not a substitute for their read on the
account. Skip this step entirely for purely internal meetings.

### 4. Resolve DISC types

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

### 5. Tag the meeting type

Ask if it isn't already clear:

> What kind of meeting is this — demo, discovery, negotiation, relationship
> check-in, or something else?

If skipped, infer your best guess from context (a first call with a new
prospect → discovery; a signature/pricing call → negotiation) and state the
assumption rather than silently picking one. This feeds the goal options in
the next step.

### 6. Get the meeting goal

A blank "what's your goal?" is a hard question to cold-start on. Instead of
just asking, **propose 3-4 candidate goals** tailored to what you already
know — the meeting type, the account context from step 3, and the attendees'
DISC/roles — then let the user pick one, edit one, or write their own:

> Here's what a good goal might look like for this one — pick one, tweak it, or
> tell me your own:
> 1. <goal tailored to this specific meeting>
> 2. <goal tailored to this specific meeting>
> 3. <goal tailored to this specific meeting>

Use the **goal starting points** reference near the bottom of this file for the
*shape* of each option, but write the actual options with specifics from this
meeting (the account, the attendees, what's known so far) — don't paste the
generic examples verbatim.

If the user skips this entirely, proceed but say plainly that the agenda and
checklist below will default to generic **<meeting-type>** best practices
instead of being curated toward a specific outcome — step 7 depends on having a
goal to curate against.

### 7. Build the prep

Before drafting anything, **derive what the goal actually requires**: write out
2-4 concrete requirements — what has to happen, be learned, or be said in this
room for the stated goal to be true afterward. This is the step that makes the
goal actually curate the prep instead of sitting in the output as an unused
label. Then synthesize attendee DISC types — **weighted toward the external
attendees** identified in step 2 — the user's own DISC, the account context
from step 3, and the meeting type into:

- **Your adaptation plan** — how the user's natural style should flex for this
  specific room, focused on the external/decision-making side. E.g. a high-D
  user meeting high-C external stakeholders needs to slow down and bring
  specifics, not just conclusions.
- **Recommended pacing/structure** — combine the DISC quick reference with the
  meeting-type reference below. Where they'd pull in different directions (e.g.
  a high-D attendee on a discovery call still needs room for open-ended
  questions despite D's preference for brevity), lead with the meeting type's
  structural needs and use DISC to tune tone and pacing within that structure.
- **A draft agenda, if warranted** — not every meeting needs one. Lean toward
  drafting one when: attendees skew **C** or **D**, the meeting is a **demo** or
  **negotiation** (both benefit from clear structure), it's high-stakes, or the
  user asked for one outright. Skip or keep it loose for **I**/**S**-heavy or
  **relationship check-in** meetings, where a rigid agenda can feel cold. Every
  agenda item should trace back to one of the goal requirements above, or exist
  only because the meeting type/DISC structurally needs it (e.g. a few minutes
  of rapport for an I-heavy room even if it isn't goal-critical) — don't include
  something just because a generic template would have it.
- **Prep checklist** — concrete things to bring or do beforehand, each tied to a
  goal requirement, DISC (data for C, a tight opener for D, rapport context for
  I, reassurance about pace for S), or account context (e.g. tailor examples to
  their industry/size, bring a case study from a similar-stage company).
- **Watch-outs** — likely DISC mismatches and meeting-type-specific pitfalls
  that would specifically derail the stated goal (e.g. over-demoing features
  irrelevant to their size/stage when the goal is a next-step commitment, or
  letting a relationship check-in drift into an unsolicited pitch), phrased as
  specific pitfalls for this room, not generic advice.

If the goal was skipped in step 6, skip the requirements-derivation above and
fall back to generic meeting-type/DISC structure — say so in the output rather
than inventing a goal to curate against.

If it's a sales context and the tools are available, you may pull in
`generate_prompt` or `selling_to` for extra angle — treat as optional depth, not
a required step.

### 8. Output

```
## Meeting: <title/context> — <date/time if known>
Type: <demo / discovery / negotiation / relationship check-in / other>

## Attendees & DISC
- <Name> (external, <Company>) — <DISC type> (<archetype>): <one-line read>
- <Name> (internal) — <DISC type or "unknown">
- <Name> — unknown (couldn't resolve)

## You — <DISC type or "unknown">
<one-line note on how their natural style maps to this room>

## Account context
<one-line account read, or "not applicable — internal meeting">

## Goal
<the chosen/written goal, or "not specified">

## What this requires
- <derived requirement>
(omit this section entirely if the goal was skipped)

## Approach
<the adaptation plan + pacing/structure recommendation, weighted toward the
external attendees>

## Agenda
<drafted agenda with rough time allocations, or "not needed for this one — here's
why" if skipped>

## Prep checklist
- <item>

## Watch-outs
- <specific DISC or meeting-type pitfall to avoid>
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

## Meeting-type quick reference

- **Demo**: structure matters — walk in with a clear flow (context → show → tie
  back to their goal). Favor a drafted agenda. Anchor it to what this specific
  account cares about (see account context), not a generic feature tour.
- **Discovery**: structure serves the questions, not a pitch. Favor open-ended
  questions and listening time over a tight agenda — even high-D attendees need
  room to talk here. A short list of questions to cover beats a time-boxed
  agenda.
- **Negotiation**: know the goal and walk-away points before the call. Favor
  clear structure and explicit next steps; ambiguity costs more here than in
  other meeting types. Watch for DISC-driven pressure tactics (e.g. a high-D
  counterpart pushing for a fast close) and plan how to hold the line.
- **Relationship check-in**: keep it light — skip or loosen the agenda, lead
  with rapport, and resist turning it into a pitch unless the other side steers
  there first.

## Goal starting points by meeting type

Use these as a starting shape for the options offered in step 6 — adapt them
with this meeting's actual specifics rather than presenting them as-is.

- **Demo**: get a next-step commitment (trial, pilot, second call); validate
  technical fit with a specific system/integration; expand the conversation to
  the economic buyer.
- **Discovery**: map their current stack/process and where it's painful;
  qualify budget, timeline, and decision process; find and enlist a champion.
- **Negotiation**: resolve a specific named blocker (security review, legal
  terms, pricing); get verbal commitment before a deadline; land on final terms
  both sides can sign.
- **Relationship check-in**: surface anything brewing before it becomes a
  problem; identify an expansion or referral opportunity; no ask — just
  maintain the relationship.
