---
name: transcript-analysis
description: >-
  Analyze a call or meeting transcript through the lens of each participant's
  DISC personality type. Use when the user wants to debrief a call, understand
  why a meeting went well or poorly, review a sales/customer conversation, or
  get personality-grounded feedback on how people communicated. The skill
  prompts for the transcript, resolves attendees' DISC types via the Crystal
  MCP server (multi_search / search), then explains what landed and what
  didn't — and why — based on DISC.
---

# Transcript Analysis (DISC-grounded)

Analyze a call/meeting transcript and explain **what went well and why**, and
**what went poorly and why**, grounded in each participant's **DISC** personality
type. Personality data comes from the **Crystal MCP server** — never guess a
person's DISC type; resolve it with the tools below.

## When to use

Trigger when the user wants to debrief, review, or get feedback on a
conversation: "here's my call transcript", "why did this meeting go badly?",
"review this sales call", "how did I do on this demo?", etc.

## Prerequisites

This skill depends on the **Crystal MCP server** being connected (it ships with
the `crystal-knows` plugin). It uses these tools:

- `multi_search` — resolve up to 25 people at once by **email or LinkedIn URL**.
  Returns per person: `disc_type`, `disc_archetype`, `traits`, `name`, `id`.
- `search` — single lookup that also supports **name-only** queries among
  profiles that already exist in Crystal. Use as a fallback when you only have a
  name, not an email/LinkedIn URL.
- `generate_prompt` / `selling_to` — optional depth if the user asks for
  follow-up guidance or a sales angle after the analysis.

If the tools aren't available, tell the user to install and authenticate the
`crystal-knows` plugin (OAuth) first, then stop.

## Procedure

### 1. Get the transcript

If the user hasn't already pasted a transcript, prompt for it explicitly:

> Paste the full call/meeting transcript below. Include speaker labels/names if
> you have them — that's how I'll map lines to each person.

Do not proceed without a transcript. If it's very long, work from the whole
thing; do not truncate silently.

### 2. Identify participants

Extract the list of distinct speakers/participants from the transcript (speaker
labels, names, "me"/"rep" vs. "client", etc.). Present the list back to the user
so they can confirm and correct it.

### 3. Resolve DISC types (the critical step)

`multi_search` matches on **email or LinkedIn URL only** — it does **not** accept
names. So:

1. For each participant, gather an **email or LinkedIn URL**. Ask the user to
   fill in any you don't have:

   > To pull DISC types I need an email or LinkedIn URL for each person. I have:
   > - Jane Doe → (need email or LinkedIn)
   > - John Smith → john@acme.com
   > Please fill in the missing ones (or say "skip" for anyone).

2. Call `multi_search` with the collected emails/LinkedIn URLs (batch, max 25).
3. For anyone you only have a **name** for, fall back to the `search` tool with
   the name — it can resolve profiles that already exist in Crystal.
4. Note anyone who couldn't be resolved (in `multi_search`'s `failed` array or a
   `search` miss). Continue with whoever resolved; call out the unresolved
   people so the user knows their analysis is DISC-blind.

Never invent a DISC type. If a person can't be resolved and the user can't
supply an identifier, analyze their contributions descriptively but label the
DISC read as "unknown."

### 4. Analyze the transcript against DISC

For the conversation as a whole and for key moments, connect **observed
behavior in the transcript** to **each participant's DISC type**. Use the
`traits` and `disc_archetype` returned by Crystal as your evidence base, plus
the DISC reference below.

Assess, per participant and for the interaction overall:

- **What landed well, and why** — moments where communication matched the other
  person's DISC preferences (e.g. giving a **D** the bottom line up front; giving
  a **C** data and specifics; giving an **S** reassurance and a steady pace;
  giving an **I** energy and social connection).
- **What fell flat, and why** — DISC mismatches (e.g. burying the ask in small
  talk with a **D**; rushing a **S**; hand-waving specifics with a **C**;
  being cold/transactional with an **I**). Quote the transcript line that shows
  it.
- **What to do differently next time** — concrete, DISC-specific adjustments.

Always tie claims to **evidence**: cite the actual transcript line, then the DISC
reason. Avoid generic advice that isn't anchored in both the transcript and a
resolved DISC type.

### 5. Output

Produce a structured debrief:

```
## Participants & DISC
- <Name> — <DISC type> (<archetype>): <one-line read>
- <Name> — unknown (couldn't resolve)

## What went well
- <transcript evidence> → <why it worked, in DISC terms>

## What didn't land
- <transcript evidence> → <why it missed, in DISC terms>

## Do differently next time
- <person/moment> → <DISC-specific adjustment>
```

Keep it tight and evidence-led. If the user asks for a follow-up email or a
sales angle afterward, hand off to `revise_email`, `generate_prompt`, or
`selling_to`.

## DISC quick reference

Use as interpretation scaffolding — the authoritative traits come from Crystal's
`traits` / `disc_archetype` per person.

- **D — Dominance** (direct, results-driven, fast, impatient with detail): wants
  the bottom line, options, and control. Lands well: brevity, confidence,
  outcomes. Falls flat: long preamble, hedging, excessive detail.
- **I — Influence** (social, enthusiastic, optimistic, relationship-first):
  wants energy, rapport, big-picture vision. Lands well: warmth, stories,
  collaboration. Falls flat: cold/transactional tone, drowning them in data.
- **S — Steadiness** (patient, supportive, dislikes pressure and sudden change):
  wants reassurance, a steady pace, and clear next steps. Lands well: empathy,
  no rush, stability. Falls flat: high pressure, rushing, abrupt shifts.
- **C — Conscientiousness** (analytical, precise, skeptical, detail-oriented):
  wants accuracy, evidence, and logical structure. Lands well: specifics, data,
  precision. Falls flat: vagueness, hype, unsupported claims.

Two-letter types (e.g. `Di`, `Sc`) blend the primary and secondary — weight the
primary letter and soften with the secondary.
