---
name: premeeting
description: Prep for an upcoming call or meeting using DISC personality intelligence and account context — resolve your and each attendee's DISC via Crystal (weighted toward external attendees), pin down the meeting type/goal, and produce a tailored prep note (approach, pacing, agenda). Use before a call; hands off to transcript-analysis afterward for a plan-vs-actual debrief.
---

# Pre-meeting Prep (DISC-grounded)

Help the user walk into a call prepared: know who's in the room (DISC-wise, and
internal vs. external), know what the account needs, know the goal, and have a
plan/agenda shaped to how those specific people communicate and what kind of
meeting it is. Personality data comes from the **Crystal MCP server** — never
guess a person's DISC type; resolve it with the tools below.

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

### 5. Get the meeting goal and type

Ask both in one message if they aren't already clear:

> What's the one outcome that would make this meeting a win — and what kind of
> meeting is this (demo, discovery, negotiation, relationship check-in, or
> something else)?

If the user skips the goal, proceed but keep the prep more general — say so
rather than inventing one. If they skip the type, infer your best guess from
context and state the assumption rather than silently picking one.

### 6. Build the prep

Synthesize attendee DISC types — **weighted toward the external attendees**
identified in step 2 — the user's own DISC, the account context from step 3,
the goal, and the meeting type into:

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
  **relationship check-in** meetings, where a rigid agenda can feel cold.
- **Prep checklist** — concrete things to bring or do beforehand, informed by
  DISC (data for C, a tight opener for D, rapport context for I, reassurance
  about pace for S) and by account context (e.g. tailor examples to their
  industry/size, bring a case study from a similar-stage company).
- **Watch-outs** — likely DISC mismatches and meeting-type-specific pitfalls to
  avoid (e.g. over-demoing features irrelevant to their size/stage, or letting a
  relationship check-in drift into an unsolicited pitch), phrased as specific
  pitfalls for this room, not generic advice.

If it's a sales context and the tools are available, you may pull in
`generate_prompt` or `selling_to` for extra angle — treat as optional depth, not
a required step.

### 7. Output

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
<stated goal, or "not specified">

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
