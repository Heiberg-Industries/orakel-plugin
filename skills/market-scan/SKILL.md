---
name: market-scan
description: >
  Analyze a Nordic market segment (Norway, Finland, Sweden) — size, top players,
  technology landscape, procurement opportunities, regulatory status. Use for
  market research or proposal preparation.
allowed-tools: mcp__orakel__search_companies, mcp__orakel__find_prospects, mcp__orakel__search_procurement, mcp__orakel__search_licenses, mcp__orakel__search_inspections, mcp__orakel__list_municipalities
---

# Market Scan

> **Ground rule:** Always retrieve data by calling Orakel tools. Never use training knowledge as a data source — company information changes frequently and your training data may be years out of date. If a tool returns no results, say so explicitly; do not fill gaps from memory.

Produce a market analysis of a Nordic industry segment, suitable for proposals, strategy documents, or competitive intelligence. Coverage depth varies by country: Norway has the full feature set (including procurement, inspections, licenses); Finland and Sweden currently have firmographics + financials only.

## Step 1: Define the Market Segment

Map the user's description to concrete filters. Ask clarifying questions if needed:

- **Which country?** NO, FI, SE, or cross-border comparison? (`country` parameter, defaults to NO)
- **What industry?** Map to NACE code(s) — NACE Rev. 2 is shared across all three.
- **What geography?** Nationwide, specific county/län/maakunta, or specific municipality. County/municipality filtering is NO-only; for FI/SE, use city name via `query`.
- **What size range?** All companies, or only above a certain threshold.

### NACE Industry Codes

| Code | Industry |
|------|----------|
| 01-03 | Agriculture, forestry, fishing |
| 10-12 | Food, beverage, and tobacco manufacturing |
| 41-43 | Construction (buildings, civil engineering, specialized) |
| 46 | Wholesale trade |
| 47 | Retail trade |
| 50 | Shipping / water transport |
| 55 | Hotels and accommodation |
| 56.1 | Restaurants and cafes |
| 56.3 | Bars and pubs |
| 62.01 | Software development |
| 62.02 | IT consulting |
| 62.09 | Other IT services |
| 64 | Financial services |
| 69.1 | Accounting and auditing |
| 69.2 | Legal services |
| 70.22 | Management consulting |
| 73.1 | Advertising agencies |
| 85 | Education |
| 86 | Health services |

### Norwegian County Codes (country=NO only)

| Code | County |
|------|--------|
| 03 | Oslo |
| 11 | Rogaland |
| 15 | Møre og Romsdal |
| 18 | Nordland |
| 30 | Viken |
| 34 | Innlandet |
| 38 | Vestfold og Telemark |
| 42 | Agder |
| 46 | Vestland |
| 50 | Trøndelag |
| 54 | Troms og Finnmark |

Use `list_municipalities` if the user names a specific Norwegian city. For FI/SE, the `county`/`municipality` filters and `list_municipalities` tool are not available — use company-name `query`, NACE, employee count, and revenue to narrow the list.

## Step 2: Market Overview

Run `search_companies` with the NACE and region filters. Set `limit: 100` and `sort: employeeCount`, `order: desc` to get the biggest players first.

Produce these metrics from the results:

### Segment Size
- Total number of companies matching the criteria
- Total employees across the segment (sum of employeeCount)
- Estimated total revenue (sum of latest revenue from financials, where available)

### Size Distribution
Categorize companies by employee count:

| Size Class | Employees | Count |
|------------|-----------|-------|
| Micro | 1-4 | |
| Small | 5-19 | |
| Medium | 20-99 | |
| Large | 100-249 | |
| Enterprise | 250+ | |

### Top 10 by Revenue

| # | Company | Org Number | Location | Employees | Revenue (MNOK) |
|---|---------|-----------|----------|-----------|----------------|
| 1 | ... | ... | ... | ... | ... |

Format revenue in the local currency (MNOK for NO, MEUR for FI, MSEK for SE), rounded to one decimal.

## Step 3: Technology Landscape

If tech stack data is available, summarize:
- Most common technologies detected (WordPress, Shopify, HubSpot, etc.)
- Percentage of companies running digital ads (hasAnyAdPixel)
- This reveals the digital maturity of the segment.

## Step 4: Procurement Opportunities (Norway only)

**Skip this section if `country` is FI or SE** — Orakel doesn't integrate HILMA (FI) or Upphandlingsmyndigheten (SE) yet.

Use `search_procurement` to find active Norwegian public tenders relevant to the sector.

Map NACE codes to CPV codes for procurement search:

| NACE sector | CPV code prefix | Description |
|-------------|----------------|-------------|
| 62 (IT) | 72 | IT services |
| 41-43 (Construction) | 45 | Construction work |
| 85 (Education) | 80 | Education services |
| 86 (Health) | 85 | Health services |
| 73 (Advertising) | 79 | Business services |
| 55-56 (Hospitality) | 55 | Hotel and restaurant services |

Filter with `status: "ACTIVE"` and `deadlineAfter` set to today's date to show only open tenders.

Present as:

### Active Tenders
| Title | Contracting Authority | Deadline | Est. Value (MNOK) |
|-------|-----------------------|----------|-------------------|

If no active tenders are found, say so explicitly — that's useful information too.

## Step 5: Regulatory Landscape (Norway, NACE 55-56 hospitality/food only)

**Skip this section unless `country=NO`** — Mattilsynet (Smilefjes) and Helsedirektoratet (TBR) are Norwegian registries with no FI/SE equivalent in Orakel.

For Norwegian NACE 55-56 segments, add:

### Food Safety Overview
Use `search_inspections` to get recent inspections in the region. Summarize:
- Distribution of ratings (how many 0/1/2/3)
- Average rating for the segment
- Rating scale: 0 = best (smiling face), 3 = worst (very sad face)

### License Landscape
Use `search_licenses` to check the segment. Summarize:
- Number of active alcohol serving licenses (01SKJE)
- Number of wholesale licenses (11ENGR)
- Import/export licenses (18TBIM / 19TBEK)

## Step 6: Format for Output

Structure the final report under clear headings. Use tables wherever possible. The output should be ready to copy-paste into a proposal or strategy document.

### Suggested Report Structure

```
# Market Analysis: [Industry] in [Region]

## Executive Summary
[2-3 sentence overview of the market]

## Market Size
[Segment size metrics and size distribution table]

## Top Players
[Top 10 table by revenue]

## Technology Adoption
[Tech landscape summary]

## Public Procurement
[Active tenders or note that none exist]

## Regulatory Landscape (if applicable)
[Inspection and license summary]

## Key Takeaways
[3-5 bullet points summarizing the most important findings]
```

If the user mentions this is for a specific proposal or client, adapt the framing accordingly.
