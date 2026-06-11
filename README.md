# orakel

**Nordic company data, directly in Claude.**

The Nordic business-data layer for CRMs and AI agents — 5M+ companies from Norway, Finland, and Sweden, sourced straight from public registries. Look up financials, map ownership networks, find prospects, push to your CRM.

[Sign up](https://orakel.cloud) · [Docs](https://orakel.cloud/docs) · [MCP setup](https://orakel.cloud/docs/mcp-setup)

---

## Install

```bash
claude plugin install https://github.com/Heiberg-Industries/orakel-plugin
```

Claude Code opens a browser to sign in with your Orakel account. No API key to copy or paste. If you don't have an account, a 7-day free trial is created automatically.

---

## What you get

**MCP connector** — authenticated connection to `https://orakel.cloud/api/mcp`. Gives Claude access to 15 tools covering company lookup, search, financials, shareholders, corporate groups, procurement, inspections, and CRM push.

**Skills** — five multi-step guided workflows, surfaced as slash commands in Claude Code:

| Command | What it does |
|---|---|
| `/orakel:prospect` | Find and qualify Nordic sales leads by industry, region, and size |
| `/orakel:company-deep-dive` | Full research report on a single company |
| `/orakel:market-scan` | Analyze a market segment — size, top players, regulatory landscape |
| `/orakel:ownership-map` | Trace ownership networks and corporate hierarchies |
| `/orakel:push-to-crm` | Export companies to Attio, HubSpot, or a webhook |

---

## Coverage

| Country | Companies | Financials | Roles |
|---|---|---|---|
| **Norway** | ~1.16M (full Brreg) | Regnskapsregisteret XBRL — full | Full Brreg board and leadership |
| **Finland** | ~458K (YTJ/PRH) | Best-effort XBRL (~30K filers) | — |
| **Sweden** | ~2.8M (Bolagsverket + SCB) | iXBRL for SMEs, 2020+ | — |

---

## Example prompts

```
Find 15 IT consultancies in Bergen with more than 10 employees
```
```
Look up Equinor's revenue and profit for the past three years
```
```
Map the ownership structure of Aker ASA
```
```
Find restaurants in Oslo with revenue over 10M NOK
```
```
Push my prospect list to Attio
```

---

## Data

All data is sourced from public registries under open licences:

- **Brønnøysundregistrene** — Norwegian company register, roles, financials (NLOD 2.0)
- **PRH / YTJ** — Finnish company register (CC BY 4.0)
- **Bolagsverket / SCB** — Swedish company register and statistics (PSI)
- **Doffin** — Norwegian public procurement notices (NLOD 2.0)
- **Mattilsynet / Helsedirektoratet** — Food safety and license data (NLOD 2.0)

EU-sovereign infrastructure. All processing and storage on Hetzner Helsinki.

---

Built by [heiberg industries](https://heiberg.co).
