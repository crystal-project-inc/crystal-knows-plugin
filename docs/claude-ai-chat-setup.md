# Add Crystal to Claude (claude.ai chat)

Use Crystal's DISC personality intelligence directly inside **claude.ai** — including
the **call transcript analysis** workflow. This takes ~2 minutes and works on Free,
Pro, Max, Team, and Enterprise plans.

There are two pieces:

1. **The Crystal connector** — gives Claude the tools to resolve people's DISC profiles.
2. **The Transcript skill** — teaches Claude the guided "analyze this call" workflow.

You need both for the transcript feature; the connector alone is enough for lookups.

---

## Step 1 — Connect Crystal (the connector)

1. In claude.ai, go to **Settings → Connectors**.
2. Click **Add custom connector**.
3. Enter:
   - **Name:** `Crystal`
   - **URL:** `https://api.crystalknows.com/_mcp`
4. Click **Add**, then **Connect** and complete the Crystal sign-in (OAuth) when prompted.

Once connected, Claude can look up DISC profiles by email or LinkedIn URL in any chat.

> **Tip:** Ask *"What's the DISC type for jane@example.com?"* to confirm it's working.

---

## Step 2 — Add the Transcript skill

1. Go to **Settings → Customize → Skills**.
2. Make sure **code execution** is enabled (Skills require it).
3. Click **+ Create skill → Upload a skill**.
4. Upload **`transcript-analysis.zip`** (provided by Crystal).

The skill activates automatically when you ask Claude to analyze a call.

---

## Step 3 — Use it

In a new chat, say something like:

> *Analyze this call transcript.*

Then paste the transcript. Claude will:

1. Identify the participants.
2. Ask you for an **email or LinkedIn URL** for each (needed to resolve DISC types).
3. Resolve everyone's DISC via the Crystal connector.
4. Return a debrief: **what landed, what didn't, and why** — grounded in each
   person's DISC type — plus what to do differently next time.

---

## For Team / Enterprise admins — roll it out org-wide

Instead of asking every member to upload the skill:

1. Go to **Organization settings → Skills**.
2. Upload **`transcript-analysis.zip`** there.
3. It's enabled by default for all members (each can toggle it off).

Members still connect the Crystal connector individually (Step 1), or you can
provision the connector through your organization's connector settings.

---

## Troubleshooting

- **"I can't resolve that person"** — the connector needs an **email or LinkedIn URL**,
  not just a name. Names only match people already in your Crystal account.
- **The skill isn't triggering** — confirm it's enabled under Settings → Customize →
  Skills, and that code execution is on.
- **Lookups fail** — re-check the connector is **Connected** (not just added) under
  Settings → Connectors, and that you completed the Crystal sign-in.
