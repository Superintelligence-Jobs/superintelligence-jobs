# Superintelligence.jobs

**The job market an agent can read.**

A live, hourly-refreshed index of open roles at frontier AI labs and platform
companies — 2,400+ requisitions across ~48 organizations, each traced to the
employer's own applicant tracking system (Greenhouse, Ashby, Lever, Workable).

Everything is public, keyless and machine-readable by design:

| Interface | URL | What it is |
|---|---|---|
| Careers feed | [`/api/careers`](https://superintelligence.jobs/api/careers) | Full JSON index: jobs, orgs, coverage, generation timestamps |
| MCP server | [`/mcp`](https://superintelligence.jobs/mcp) | Streamable HTTP MCP — `search_jobs`, `get_job`, `list_orgs`, `diff_since`, `market_stats` (listed on the official MCP registry, DNS-verified) |
| Integration contract | [`skill.md`](https://superintelligence.jobs/skill.md) | The one-page contract for agents: endpoints, schema, etiquette |
| LLM entry point | [`llms.txt`](https://superintelligence.jobs/llms.txt) | Answer-engine orientation file |
| A2A card | [`/.well-known/agent-card.json`](https://superintelligence.jobs/.well-known/agent-card.json) | Agent-to-Agent discovery card |
| JobPosting schema | every `/jobs/<id>` page | schema.org JobPosting, server-rendered |

## Quickstart

```bash
# The entire index, no auth:
curl https://superintelligence.jobs/api/careers | jq '.jobs | length'
```

```python
# From Python:
import requests
feed = requests.get("https://superintelligence.jobs/api/careers").json()
newest = sorted(feed["jobs"], key=lambda j: j["firstSeenAt"])[-10:]
```

```typescript
// MCP client (any spec-compliant client):
// url: https://superintelligence.jobs/mcp   (streamable HTTP, no auth)
```

## Why this exists

Job markets are human UIs bolted onto databases. Frontier AI work is
increasingly done by agents — and agents don't scroll infinite grids of job
cards. So we built the market the other way round: an honest, attributed,
continuously-verified index with a real API underneath, a real contract on
top, and a human-friendly site in the middle.

Humans: [superintelligence.jobs](https://superintelligence.jobs)
Data reports: [/pulse](https://superintelligence.jobs/pulse)

## Use policy

Read it, cite it, build on it. Free with attribution to
superintelligence.jobs; don't present the aggregate as your own data; don't
hammer the feed (hourly generation — one fetch per hour is the refresh rate;
be polite between cycles). We are an independent index, not the employer —
`sourceUrl` on every row links to the employer's own posting.

---

*Org profile verified against the superintelligence.jobs domain. This repo is
the public integration surface; the site codebase is proprietary.*
