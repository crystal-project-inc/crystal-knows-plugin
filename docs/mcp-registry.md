# Publishing Crystal to the official MCP Registry

Registry: https://registry.modelcontextprotocol.io. Schema: 2025-12-11 (current). The `server.json` next to this file validates against it.

## Decide the namespace (the only real decision)

| Option | Name in server.json | Who can publish | Verification |
|---|---|---|---|
| **A. Domain (recommended)** | `com.crystalknows/personality` | Anyone holding the private key | One DNS TXT record on the apex `crystalknows.com` (Cloudflare) |
| B. GitHub org | `io.github.crystal-project-inc/personality` | Only an **Owner** of the `crystal-project-inc` GitHub org (member is not enough) | GitHub OAuth in the CLI |

Pick A: it's brand-owned, permanent, and the server URL is on the same domain. Pick B only if nobody can touch Cloudflare DNS this week; you can re-publish under A later, but the old entry stays.

If you pick B, change `"name"` in `server.json` to `io.github.crystal-project-inc/personality` and skip the DNS steps.

## Steps (Option A)

1. Install the CLI: `brew install mcp-publisher`
2. Generate a key (macOS ships LibreSSL, which lacks Ed25519; use Homebrew OpenSSL 3):
   ```bash
   brew install openssl@3
   OPENSSL=/opt/homebrew/opt/openssl@3/bin/openssl
   $OPENSSL genpkey -algorithm Ed25519 -out crystal-mcp-registry.pem
   PUBLIC_KEY="$($OPENSSL pkey -in crystal-mcp-registry.pem -pubout -outform DER | tail -c 32 | base64)"
   echo "crystalknows.com. IN TXT \"v=MCPv1; k=ed25519; p=${PUBLIC_KEY}\""
   ```
   Keep `crystal-mcp-registry.pem` in the team password manager; it is the publishing credential from now on.
3. Add that TXT record in Cloudflare on the **apex** (`crystalknows.com`, not a subdomain or `_mcp-*` selector). Wait a few minutes.
4. Put `server.json` in the root of the `crystal-knows-plugin` repo and commit it (the registry links the repo; it doesn't need to build anything).
5. From that repo directory:
   ```bash
   mcp-publisher login dns --domain crystalknows.com --private-key "$(cat crystal-mcp-registry.pem)"
   mcp-publisher publish
   ```
6. Confirm: `curl "https://registry.modelcontextprotocol.io/v0.1/servers?search=com.crystalknows/personality"`

Re-publishing later (new description, URL change): bump `"version"`, commit, `mcp-publisher publish` again.

## Steps (Option B)

1. `brew install mcp-publisher`
2. Edit `"name"` to `io.github.crystal-project-inc/personality`, commit `server.json` to the plugin repo root.
3. `mcp-publisher login github` (must be signed in as an org Owner), then `mcp-publisher publish`.

## Notes on what the file declares

- Validated against the 2025-12-11 schema: required fields present, name matches the reverse-DNS pattern, and `description` is under the 100-character cap (the longer pitch lives on the website page, not here).

- One remote, `streamable-http`, `https://api.crystalknows.com/_mcp`. The registry has no first-class auth field for remotes; OAuth is discovered by clients from the server's own metadata, and the `_meta` block documents it for humans. Everything under `_meta` except the `publisher-provided` key is dropped by the registry, so that's where the categories, tool list, and plugin pointer live (4 KB max).
- `websiteUrl` is the new `/personality-mcp` page; that's what directories that sync from the registry will link.
- The token-in-URL variant is documented in `_meta`, not as a second remote: the validator rejects templated remote URLs.

## Before you hit publish

- `llms-full.txt` still says "not yet publicly available". Remove that line first; directory reviewers read it.
- The server's own `search` tool description says "DISC/MBTI/Enneagram". Fix server-side so the listed tools match the site.
