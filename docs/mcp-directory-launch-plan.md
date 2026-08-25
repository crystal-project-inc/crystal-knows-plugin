# Getting Crystal's MCP server into directories/marketplaces — audit & plan

Written 2026-08-24 while scoping PR #4 (`mcp-registry-server-json`). Captures what
was actually verified (live curl/fetch checks, not assumptions) so this survives
a session restart. Re-verify anything with a date on it before treating it as
still true.

## TL;DR

"Publish `server.json` to the official registry" and "get Crystal listed
everywhere marketing wants" are not the same project. The registry PR (#4) is
small and ~ready. Getting into the ChatGPT and Claude.ai *connector directories*
(as opposed to third-party marketplaces that mirror the registry) is a separate,
bigger effort with real gaps below. Recommend shipping #4 now, treating the
directory submissions as follow-up work with owners assigned.

## The landscape

Three distinct layers, easy to conflate:

1. **Registry** (`registry.modelcontextprotocol.io`) — canonical machine-readable
   index. Publish once with `mcp-publisher`. This is all PR #4 does.
2. **Marketplaces** — discovery layers that read the registry. No submission
   needed once you're in the registry (PulseMCP is fully automatic; Glama
   auto-indexes public GitHub repos independently of the registry).
3. **Connector directories** — Claude.ai's and ChatGPT's own first-party app
   stores. Each has its own review process, technical requirements, and
   reviewer test account. Being in the registry does **not** get you into
   these. Crystal is currently reachable in Claude *only* as a self-published
   Claude Code plugin (no gatekeeper — that's already live) and as a manual
   "add custom connector" in Claude.ai/ChatGPT (user has to know the URL).
   Neither is the same as showing up in the built-in directory/picker.

## Current state (verified 2026-08-24)

| Surface | Status |
|---|---|
| Claude Code plugin (`crystal-knows-plugin` repo, self-published marketplace) | **Live.** No submission needed — this is your own repo. |
| `api.crystalknows.com/_mcp` (the server itself) | **Live.** Returns 401 without auth (expected), OAuth discovery metadata present at `/.well-known/oauth-authorization-server` (200). |
| `data.crystalknows.com/mcp` (connection-setup dashboard) | **Live**, confirmed via screenshot — this is where OAuth clients get created for Claude/ChatGPT/Copilot. |
| Official MCP registry entry | **Not yet published.** `server.json` + `docs/mcp-registry.md` drafted in PR #4, not run through `mcp-publisher` yet. |
| PulseMCP / Glama listings | Follow automatically once registry entry exists (PulseMCP) or independently via GitHub crawl (Glama) — no separate action. |
| Smithery listing | Not started — separate submission, see below. |
| ChatGPT app/plugin directory | Not started — separate submission, real gaps found below. |
| Claude.ai connector directory | Not started — separate submission, real gaps found below. |

## Track 1 — Official MCP registry (PR #4)

This is the small, mostly-done piece.

- [x] `server.json` drafted, validates against 2025-12-11 schema
- [x] `docs/mcp-registry.md` runbook drafted
- [x] Confirmed no private key committed
- [x] Fixed the doc's incorrect "make the tool description match the site"
      note — that was backwards (see Content gap below); corrected in
      `docs/mcp-registry.md`
- [x] Re-checked `llms-full.txt` (`data.crystalknows.com/llms-full.txt`) —
      the "not yet publicly available" line the doc warned about isn't there
      as of 2026-08-24. Looks resolved; give it one more look right before
      publishing in case it was a different page than what got re-checked here.
- [ ] **Decision needed:** namespace verification path. Doc recommends Option
      A (`com.crystalknows`, DNS TXT on the apex `crystalknows.com` via
      Cloudflare) over Option B (GitHub org `io.github.crystal-project-inc`,
      requires an org **Owner**, not just a member — I could not verify who
      currently holds Owner on `crystal-project-inc` from here, `gh api`
      needs `admin:org` scope I don't have).
- [ ] Someone with Cloudflare access adds the TXT record
- [ ] Generate Ed25519 keypair, store private key in the team password manager
- [ ] `mcp-publisher login dns` + `mcp-publisher publish` from repo root
- [ ] Confirm via `curl "https://registry.modelcontextprotocol.io/v0.1/servers?search=com.crystalknows/personality"`

**Owner needed:** whoever holds/can get Cloudflare DNS access for
`crystalknows.com`. Not a marketing-solo task.

## Track 2 — PulseMCP / Glama

No action beyond Track 1. PulseMCP mirrors the registry automatically. Glama
crawls public GitHub repos independently — `crystal-project-inc/crystal-knows-plugin`
is already public, so it may pick this repo up regardless of registry status.
Worth a spot-check on glama.ai after Track 1 ships, no dedicated work item.

## Track 3 — Smithery

Separate from the registry. Two paths, per Smithery's docs:
- `smithery mcp publish <url> -n <org/server>` against the live server URL —
  Smithery scans it for tools/prompts, no manifest needed, **or**
- a `smithery.yaml` + `icon.svg` in a repo Smithery indexes directly.

Given Crystal is a hosted remote server (not something Smithery would deploy
itself), the scan-a-live-URL path is almost certainly the right one. Needs a
Smithery publisher account. **Not started; nobody currently owns this.**

## Track 4 — Claude.ai Connectors Directory

This is Anthropic's first-party in-product directory — distinct from both the
registry and from the Claude Code plugin. Requirements, per Anthropic's public
submission docs (checked 2026-08-24):

- Submission happens inside Claude.ai itself, and **requires a Team or
  Enterprise org**; only org Owners/Primary owners can submit.
- Remote server over Streamable HTTP — **have this.**
- OAuth 2.0 with a proper user-consent flow — **have this** (dynamic client
  registration, confirmed live).
- **A reviewer test account** — need to provision one Anthropic's reviewers
  can log in with. Not set up.
- **Every tool needs a `title` plus the correct read-only/destructive
  annotation hint.** I could not verify from this repo or the exposed tool
  schemas whether `search`/`fetch`/`get_myself` etc. are annotated
  `readOnlyHint: true` and whether `add_profile_tags` / `remove_profile_tags`
  / `assign_disc_types` are annotated as mutating. **This needs a check
  against the actual server code** (not in this repo) — if missing, it's a
  real blocker for submission, not paperwork.
- Tool handlers must return within 300s — almost certainly fine, no evidence
  otherwise.
- No interactive UI (MCP Apps) in Crystal's tools as far as I can tell, so the
  screenshot requirement likely doesn't apply — confirm before submitting.

**Not started. Gaps: Team/Enterprise org + submitter who's an Owner, reviewer
test account, tool-annotation audit.**

## Track 5 — ChatGPT app/plugin directory

OpenAI's directory (merged with the Codex plugin directory as of 2026-07-09).
Requirements, per OpenAI's developer docs (checked 2026-08-24):

- Tools need `readOnlyHint`, `destructiveHint`, `openWorldHint` set — same
  annotation gap as Track 4, needs a server-code check.
- **Domain-ownership verification endpoint at
  `/.well-known/openai-apps-challenge`** — checked live, `api.crystalknows.com`
  returns **404** for this path today. Concrete, confirmed gap; needs to be
  built and deployed before submission.
- A demo/reviewer account, same as Claude's requirement.

**Not started. Gap: verification endpoint doesn't exist yet, needs a reviewer
account, same annotation audit as Track 4.**

## Content gap: DISC-only messaging vs. what the API actually returns

Not a registry blocker, but worth marketing's attention. Pulled Paul's own
profile via `get_myself` live: the API returns `assessments.disc`,
`.enneagram`, and `.mbti` as independent, populated fields when a person has
taken each one (his profile has all three). But:

- `www.crystalknows.com/personality-mcp` (the page `server.json`'s
  `websiteUrl` points at) — fetched live 2026-08-24 — talks exclusively about
  DISC, no mention of MBTI/Enneagram.
- `data.crystalknows.com/llms-full.txt` — same, DISC-only in its API
  description.

So the site undersells what the server does. Given Crystal's brand is
deliberately DISC-first, this may be intentional simplification rather than
an oversight — flagging as a decision for marketing, not assuming it needs to
change.

## Open decisions (need a person, not more research)

1. Namespace verification path for the registry (Track 1) — who has Cloudflare
   access?
2. Does anyone want to own Smithery (Track 3) — low effort, no blockers found.
3. Do we actually want Claude.ai / ChatGPT directory listings (Tracks 4–5)?
   Both need: an engineering pass to confirm/add tool annotations, a reviewer
   test account, and (ChatGPT only) a new verification endpoint. If yes,
   this is an eng-scoped follow-up, not something marketing can do solo —
   worth saying that explicitly back to Luke.
4. Whether to update `personality-mcp` / `llms-full.txt` copy to mention
   MBTI/Enneagram alongside DISC.

## Sources

- https://blog.modelcontextprotocol.io/posts/2025-09-08-mcp-registry-preview/
- https://mcpblog.dev/blog/2026-03-17-mcp-registry-guide
- https://modelcontextprotocol.info/tools/registry/faq/
- https://smithery.ai/docs/build/publish
- https://claude.com/docs/connectors/building/submission
- https://sunpeak.ai/blogs/claude-connector-directory-submission/
- https://support.anthropic.com/en/articles/11697096-anthropic-mcp-directory-policy
