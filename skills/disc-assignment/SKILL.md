---
name: disc-assignment
description: Assign or review DISC types for several people at once in an interactive table. Use when setting/correcting DISC across a group — not a single person's profile (use search for that).
---

# DISC Assignment (group)

Open an interactive table to assign a DISC type to each of several people at
once, then use those assignments for whatever comes next (drafting outreach,
analyzing a call, comparing approaches).

## When to use

Trigger when the user wants to **assign, review, set, or correct DISC types
across a group of people** — e.g. "set DISC types for these prospects", "let me
tag everyone on this list", "review the DISC types for my team".

**Do not** use this for a single person's profile lookup ("what's Jane's DISC?")
— that's what `search` / `fetch` are for. This skill is specifically for
handling **multiple** people together in the table view.

## Prerequisites

Requires the **`assign_disc_types`** tool (enable the Crystal connector, or
install the `crystal-knows` plugin), which renders the table as an MCP App view.
Optionally uses `search` / `multi_search` to look up existing DISC types first.

## Procedure

The input is **open-ended** — do whatever fits the request:

1. **Gather the people.** They might be plain names, people you already resolved
   earlier in the conversation, or a list the user pastes.
2. **Optionally pre-fill existing DISC types.** If the user wants known types
   pre-selected, resolve the people first with `search` / `multi_search` and
   carry each person's `disc_type` forward. If they just want a blank table to
   fill in, skip this — names alone are fine.
3. **Call `assign_disc_types`** with the people. Pass either plain names, or
   objects `{name, email, disc_type}` (include `disc_type` only when you already
   know it). Anyone with a known type is pre-selected; everyone else starts at
   "Not set".
4. The user assigns types and clicks **Done**; their selections come back to you
   as a readable summary — use them for whatever's next.

This skill is **standalone and chainable**: it works on its own with a bare list
of names, downstream of `search` / `multi_search`, or feeding a later step
(drafting outreach, analyzing a call).

## Read-only — important

The table is **read-only**. Assignments are **conversational scratch that apply
to this conversation only** — they are **not** written back to Crystal and do not
change anyone's profile on crystalknows.com. Never tell the user their Crystal
profile has been updated, created, or changed. If the user wants to permanently
change a profile, direct them to crystalknows.com — this skill cannot do that.

## Taxonomy

Use only the DISC codes the table provides (the canonical Crystal set with real
archetype names). Do not invent or blend archetype labels.
