# LinkedIn Jobs Feed MCP Server

[![Smithery](https://smithery.ai/badge/mambabuilt/mcp-linkedin-jobs-feed)](https://smithery.ai/servers/mambabuilt/mcp-linkedin-jobs-feed) [![Glama score](https://glama.ai/mcp/servers/mambalabsdev/mcp-linkedin-jobs-feed/badges/score.svg)](https://glama.ai/mcp/servers/mambalabsdev/mcp-linkedin-jobs-feed) [![MCP Registry](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fregistry.modelcontextprotocol.io%2Fv0%2Fservers%3Fsearch%3Dcom.mambabuilt%252Fmcp-linkedin-jobs-feed%26limit%3D1&query=%24.servers%5B0%5D._meta%5B%22io.modelcontextprotocol.registry%2Fofficial%22%5D.status&label=mcp%20registry&color=blue)](https://registry.modelcontextprotocol.io/v0/servers?search=com.mambabuilt/mcp-linkedin-jobs-feed&limit=1) [![npm version](https://img.shields.io/npm/v/@mambalabsdev/mcp-linkedin-jobs-feed)](https://www.npmjs.com/package/@mambalabsdev/mcp-linkedin-jobs-feed) [![npm downloads](https://img.shields.io/npm/dm/@mambalabsdev/mcp-linkedin-jobs-feed)](https://www.npmjs.com/package/@mambalabsdev/mcp-linkedin-jobs-feed) [![license](https://img.shields.io/github/license/mambalabsdev/mcp-linkedin-jobs-feed)](https://github.com/mambalabsdev/mcp-linkedin-jobs-feed/blob/main/LICENSE) [![mcpservers.org](https://img.shields.io/badge/mcpservers.org-listed-blue)](https://mcpservers.org/servers/mambalabsdev/mcp-linkedin-jobs-feed)

An MCP server for the Mamba Labs LinkedIn Jobs Feed actor on Apify. Give it job searches and locations. It returns one row per job from LinkedIn's public job search, with the employer's own website domain attached.

## What's Inside

- [What it does](#what-it-does)
- [Quick start](#quick-start)
- [Prerequisites](#prerequisites)
- [Example prompts](#example-prompts)
- [Tool](#tool)
- [Inputs](#inputs)
- [Pricing](#pricing)
- [Your keys](#your-keys)
- [Full actor documentation](#full-actor-documentation)
- [License](#license)

## What it does

- **Source**: LinkedIn's public guest job search and job posting pages only. No login, no cookie, no profile or company page reads.
- **Employer domain**: a company name autocomplete, then a web search, once per employer and cached; returned only when the domain clearly matches the company name (`domain_status` `resolved`), otherwise empty and labeled.
- **Rows**: one `job` row per job with the posting details, and one free `search` row per search and location with `jobs_found` and `jobs_returned`.
- **Filters and schedules**: posted within, workplace type, experience level, job type, excluded companies, and `only_new` with a `state_key`.

All of the work runs on Apify. This package is a thin client that starts the actor run, waits for it, and hands back the dataset rows.

## Quick start

You need Node.js 18 or newer and an Apify account with an API token.

Add this to your Claude Desktop config (`claude_desktop_config.json`), Cursor, or any MCP client:

```json
{
  "mcpServers": {
    "mamba-linkedin-jobs-feed": {
      "command": "npx",
      "args": [
        "-y",
        "@mambalabsdev/mcp-linkedin-jobs-feed"
      ],
      "env": {
        "APIFY_TOKEN": "your-apify-token"
      }
    }
  }
}
```

Restart the client. The `search_linkedin_jobs` tool appears.

## Prerequisites

- Node.js 18 or newer.
- An Apify API token from [console.apify.com/account/integrations](https://console.apify.com/account/integrations), set as `APIFY_TOKEN`. The server lists its tool without a token; a call needs one.

## Example prompts

- "Find account executive jobs posted in Germany this week and give me the hiring companies' domains."
- "Which companies in London posted a head of sales or SDR manager job in the last 24 hours?"
- "Every morning, list new remote RevOps jobs in the United States with the company domain."

## Tool

`search_linkedin_jobs`. One tool. Example arguments:

```json
{
  "keywords": [
    "account executive"
  ],
  "locations": [
    "Germany"
  ],
  "max_jobs_per_search": "5"
}
```

## Inputs

Every buyer facing input of the actor, unchanged. Omitted inputs take the actor's own defaults. Numbers and booleans are sent as strings.

| Input | Type | Description |
|---|---|---|
| `keywords` | array | One job search per line, as you would type it in LinkedIn's job search, for example account executive, or sdr manager saas. Up to 50 searches per run (keywords times locations) |
| `locations` | array | Optional. One location per line, as LinkedIn's job search takes it: a country (Germany), a city (London, England, United Kingdom), or a region (European Union). Each search runs once per location. Empty searches worldwide |
| `posted_within` | string | Only jobs posted in this window, as LinkedIn dates them |
| `workplace_types` | array | Optional. Any of: on_site, remote, hybrid |
| `experience_levels` | array | Optional. Any of: internship, entry, associate, mid_senior, director, executive |
| `job_types` | array | Optional. Any of: full_time, part_time, contract, temporary, internship, volunteer, other |
| `max_jobs_per_search` | string | Job rows returned per search, newest first. Default 100, up to 1000 (LinkedIn shows guests about 1,000 results per search) |
| `exclude_companies` | array | Optional. Drop jobs whose company name contains one of these, ignoring case, for example staffing agencies you do not want |
| `include_details` | string | true reads each job's public posting page. No extra charge |
| `find_company_domain` | string | true looks up each employer's own website domain by name with a web search, and returns it only when the domain clearly matches the name. No extra charge |
| `only_new` | string | true returns only jobs that an earlier run with the same state key did not return. The first run returns every job in the window |
| `state_key` | string | Names the saved state for only new jobs. Use one key per schedule, for example ae-germany-daily |
| `reset_state` | string | true forgets the saved state for this state key |
| `max_charge_usd` | string | Optional. The run stops before it would charge more than this |

## Pricing

Pay per event on Apify, billed to your Apify account. Prices in USD.

| Event | Free plan | Bronze | Silver | Gold |
|---|---|---|---|---|
| `job-row`, one job returned, with details and the employer domain | $0.0015 | $0.001425 | $0.00135 | $0.001275 |
| `apify-actor-start`, per run start | $0.001 | $0.001 | $0.001 | $0.001 |

Search rows and the row for a refused input are free. Posting details and the domain lookup carry no extra charge.

## Your keys

The actor needs no key and no LinkedIn account. It reads public job pages and a public search results page and holds no key of any kind. Mamba Labs pays the platform and proxy costs of the run.

## Full actor documentation

[apify.com/mambalabs/linkedin-jobs-feed](https://apify.com/mambalabs/linkedin-jobs-feed)

## License

MIT. See [LICENSE](LICENSE).
