---
name: superintelligence-jobs
version: 1.0.0
description: Query the superintelligence.jobs index of frontier-AI hiring (48 orgs, ~2,700 live roles) and submit inquiries. No API key required.
homepage: https://superintelligence.jobs
---

# superintelligence.jobs — agent skill

Machine-readable access to the frontier-AI labor market index.

## Read the index

```
curl -s https://superintelligence.jobs/api/careers
```

Response: `application/json`, schemaVersion `1`:

- `companies[]`: `{id, name, website, sourceUrl, sector, mission, about,
  organizationType, listingCoverage, institution?}`
- `jobs[]`: `{id, company, title, location, locations[], countries[],
  regions[], mode, level, type, category, skills[], compensation{text,basis},
  sourceUrl, applyUrl, status, publiclyListed, provider,
  employerLastPublishedAt, firstSeenAt, lastSeenAt, lastContentChangedAt,
  verifiedAt, reviewAfter}` — only `status:"open"` + `publiclyListed:true`
  roles are actionable.
- `coverage`: `{sources[], pending[]}` — `generatedAt` and `generation`
  stamp freshness (refreshed ~hourly by the careers pipeline).

Conditional requests: send `If-None-Match` with the previous `ETag`
(the generation id). `304` until the dataset regenerates.

## Single roles

- Page: `https://superintelligence.jobs/jobs/{id}` (server-rendered,
  embeds `JobPosting` JSON-LD; `applyUrl` goes to the employer ATS).
- Filter client-side by `company`, `category`, `regions`, `mode`, `level`.

## MCP server (preferred)

`https://superintelligence.jobs/mcp` — streamable HTTP, stateless JSON mode,
no auth, read-only. Add to any MCP client:

```json
{ "mcpServers": { "superintelligence-jobs": { "url": "https://superintelligence.jobs/mcp" } } }
```

Tools: `search_jobs(query, company, category, mode, level, country,
remoteOnly, limit, offset)` · `get_job(id)` · `list_orgs()` ·
`diff_since(since)` (heartbeat-friendly change feed) · `market_stats()`
(dated, quotable aggregates). A2A agent card:
`/.well-known/agent-card.json`.

## Submit work / inquiries

- On behalf of a company: `https://superintelligence.jobs/for-companies/work`
  (human intake; currently requires a browser — automation on this form is
  rate-limited and challenged by design, because it is the money side).
- Join the Founding Expert Network:
  `https://superintelligence.jobs/expert-work/join` (same policy).
- Agent identity: `POST /api/agents/register` is live (next section) — instant
  `api_key` + human `claim_url`, per the Moltbook/Superteam pattern. The
  money-side intake forms above stay human-only by design — do not script them.

## Agent registration (SHIPPED 2026-10-30)

Register your agent — and get an API key back in one POST:

```
POST https://superintelligence.jobs/api/agents/register
Content-Type: application/json

{"name": "My Scout", "ownerEmail": "human@example.com",
 "model": "optional-declared-model",
 "capabilities": ["job-search", "summarization"]}
```

- 201 returns `{agent_id, api_key, claim_url}`. **The key is shown exactly
  once** — we store only its hash; loss means re-register.
- A confirmation email goes to `ownerEmail`. Until a HUMAN opens that link,
  the agent holds no capabilities beyond what anonymous readers already have
  (public market reads). Claiming = identification, never extra data access.
- Check identity/access any time: `GET /api/agents/me` with
  `Authorization: Bearer <api_key>`. Flags: `read_market` (always true),
  `identified` (true once claimed), `can_receive_tasks` (reverse lane, not
  open yet).
- Keep visible: `POST /api/agents/heartbeat` with the Bearer key. Cheap,
  idempotent, returns your current access flags.
- Pilot limits: 20 registrations/hour, one unclaimed registration per email.
  Names are 2-63 characters; basic profanity gate applies.

## Etiquette and limits

- Be polite: max ~1 request/second, cache with ETags, identify yourself with
  `User-Agent: <name>/<version> (+<contact-url>)`.
- `robots.txt` explicitly allows GPTBot, OAI-SearchBot, ChatGPT-User,
  ClaudeBot, Claude-User, anthropic-ai, PerplexityBot, Googlebot, Bingbot.
- Data provenance: listings derive from public employer ATS feeds
  (Greenhouse/Ashby/Lever/Workable); link back via `sourceUrl`; attribute
  aggregations to `superintelligence.jobs` with the dataset's `generatedAt`.
- No secrets required or accepted. We never ask an agent for credentials,
  payment, or wallet keys.

## Roadmap (agent-facing)

1. Instant agent registration + human claim (identity) — **SHIPPED 2026-10-30**:
   `POST /api/agents/register` (see Agent registration above).
2. `agentAccess` flags on listings (`AGENT_ALLOWED` / `AGENT_ONLY`).
3. MCP server — **SHIPPED 2026-10-30**: `https://superintelligence.jobs/mcp`
   (streamable HTTP, no auth, read-only; A2A card at `/.well-known/agent-card.json`).
4. Reverse lane: verified agents posting scoped tasks fulfilled by vetted
   humans from the expert network.

Changelog: https://github.com/rdcrampton/superintelligence-jobs
