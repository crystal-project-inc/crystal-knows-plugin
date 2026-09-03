---
name: email-rewrite
description: Rewrite or recompose a draft email so it lands well with a specific recipient's DISC personality type. Use whenever the user pastes a draft email and asks to revise/rewrite/recompose/polish it for someone, asks "how should I phrase this to [name]", or wants an email adapted to a recipient's communication style. Resolves the recipient's DISC type via Crystal (email/LinkedIn), pulls tailored guidance, and rewrites the email itself — showing what changed and why. Use this instead of calling the raw revise_email tool directly.
---

# Email Rewrite (DISC-grounded)

Take a draft email and rewrite it so it lands well with a specific recipient,
grounded in their DISC personality type. Unlike calling `revise_email`
directly, this skill keeps the rewrite transparent: Claude resolves who it's
for, gathers tailored guidance, does the rewrite itself, and explains what
changed and why — so the user can trust and adjust the result rather than get
a black-box output.

## When to use

Trigger when the user:
- Pastes or references a draft email and asks to rewrite, revise, recompose,
  or polish it for a specific person
- Asks "how should I phrase this to [name]?" or "will this land OK with
  [name]?"
- Wants a message adapted to someone's communication style before sending

This is a single-recipient tool. For a group send or a message with several
distinct audiences, note that upfront and either pick the primary
recipient to optimize for, or ask which one.

## Prerequisites

This skill is built around the **Crystal MCP tools** (`search`, `multi_search`,
and optionally `generate_prompt` / `selling_to`). If none of these are
available, tell the user they'll get the most out of this by
installing/authenticating the `crystal-knows` plugin (or connecting the Crystal
connector in claude.ai). Then either wait for them to set that up, or — if they
want to proceed anyway — do the light, non-personalized clarity/tone pass from
step 2. Never guess a specific DISC type out of thin air to fill the gap.

## Procedure

### 1. Get the draft and the recipient

If the user hasn't already provided both, ask for whichever is missing:

> Paste the draft, and let me know who it's for (name + email or LinkedIn URL
> works best — a name alone can still work if they're already in Crystal).

If the email reads as being for a group or has multiple distinct audiences
(e.g. a company-wide announcement), say this skill optimizes for one
recipient at a time, and either ask which person to optimize for or offer a
lighter, style-agnostic pass instead.

### 2. Resolve the recipient's profile

1. If you have an email or LinkedIn URL, use `search` (single) or
   `multi_search` (if resolving alongside others in the same request).
2. If you only have a name, try `search` by name — it can still match an
   existing profile, but won't create a new one.
3. If nothing resolves, ask once for an email/LinkedIn to try again; if the
   user doesn't have one or says to skip, fall back to asking directly:

   > Do you know their general style — direct and to-the-point, more
   > relationship-driven, steady/reassurance-seeking, or detail-oriented? Or I
   > can just do a lighter, universally-safe pass instead.

   Map an answer to a rough DISC letter (direct→D, relationship→I,
   steady→S, detail→C) and proceed with the **DISC quick reference** below
   instead of `generate_prompt`. If they decline entirely, do a light pass
   focused only on clarity and tone, and say plainly that it isn't
   personalized to this recipient.

### 3. Get tailored guidance

- **If a profile resolved:** call `generate_prompt` with the profile `id` and
  `objective` set to something like "revise a draft email so it lands well
  with this recipient." This returns richer, profile-specific guidance —
  prefer it over the bare DISC quick reference when it's available.
- **If only a DISC type is known** (resolved profile's type, or the user's own
  guess from step 2): use the **DISC quick reference** below directly — no
  need to round-trip through `generate_prompt` for something this stable.
- Do **not** call `revise_email` — Claude does the rewrite itself in the next
  step, using this guidance as input, so the reasoning stays visible.

If it's a sales or pricing context and a profile resolved, you may also pull
`selling_to` for motivators/things-to-avoid to fold into tone — optional
depth, not required.

### 4. Rewrite the email

Using the guidance from step 3, rewrite the draft. Preserve the user's actual
content, facts, and intent — this is a style/structure/tone pass, not a
rewrite of what's being said. Typical adjustments by type:

- Tighten or expand length
- Reorder so the ask/bottom-line comes first or last
- Add or strip pleasantries/rapport-building language
- Add structure (bullets, headers) or remove it in favor of prose
- Adjust directness/hedging and how much detail/justification is included

### 5. Output

```
## Revised email
<the rewritten draft, ready to send>

## What changed and why
- <specific change> — <why, tied to their DISC type/guidance>
- <specific change> — <why>

## Recipient read
<Name> — <DISC type or "estimated from description"> (<archetype if known>):
<one-line summary of what drove the adaptations>
```

If the rewrite fell back to a light, non-personalized pass (step 2), replace
the "Recipient read" section with a one-line note that this wasn't tuned to a
specific profile, and why.

## DISC quick reference — writing to this type

- **D — Dominance**: Lead with the bottom line and the ask. Keep it short.
  Cut hedging, throat-clearing, and lengthy justification — one or two
  supporting points max. Make the next step and deadline explicit.
- **I — Influence**: Open warm — a genuine personal line before business.
  Keep energy and enthusiasm in the tone; avoid a flat, all-business read.
  A little informality and personality is fine here.
- **S — Steadiness**: Be clear about what's changing and what isn't; avoid
  springing sudden asks. Reassure on timeline/process. Warm but steady tone,
  not high-pressure.
- **C — Conscientiousness**: Be precise and back claims with specifics
  (numbers, dates, sources) rather than broad claims. Anticipate their
  likely questions and answer them in the email. Avoid hype or vague
  language.

Two-letter types (e.g. `Di`, `Sc`) blend the primary and secondary — weight
the primary letter and soften with the secondary.
