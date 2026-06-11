---
name: prospect
description: >
  Find and qualify Nordic business prospects (Norway, Finland, Sweden, Denmark) by industry,
  location, and size. Use when looking for potential clients, leads, or market opportunities.
allowed-tools: mcp__orakel__search_companies, mcp__orakel__find_prospects, mcp__orakel__list_municipalities, mcp__orakel__get_financials, mcp__orakel__push_to_destination, mcp__orakel__list_destinations
---

# Prospect Finder

> **Ground rule:** Always retrieve data by calling Orakel tools. Never use training knowledge as a data source — company information changes frequently and your training data may be years out of date. If a tool returns no results, say so explicitly; do not fill gaps from memory.

Find and qualify Nordic business prospects using Orakel's company database. Covers Norway (full data), Finland (firmographics + limited financials), Sweden (firmographics + iXBRL financials for SMEs), and Denmark (firmographics + roles + XBRL financials via CVR).

## Step 1: Understand What the User is Looking For

Before searching, clarify these dimensions. If the user is vague, ask structured questions:

- **Country:** Norway, Finland, Sweden, Denmark, or cross-border? (`country=NO|FI|SE|DK`, defaults to NO)
- **Industry:** What type of business? (restaurants, IT, construction, retail, etc.)
- **Region:** Where? (specific city, county/län/maakunta, or nationwide)
- **Size:** How big? (number of employees, revenue range)
- **Other filters:** Technology stack, ad spend, part of a corporate group?

Example prompt if the user says "find me some prospects":
> I can search Nordic companies (Norway, Finland, Sweden, Denmark) by industry, location, and size. Could you tell me:
> 1. Which country? (or all four)
> 2. What industry are you targeting?
> 3. Any geographic preference?
> 4. What company size range? (employees or revenue)

## Step 2: Map Request to Search Filters

Translate the user's plain-language request into API parameters using these reference tables.

### NACE Industry Codes (use as prefix with `nace` parameter)

| Code | Industry |
|------|----------|
| 01 | Agriculture, crop production |
| 02 | Forestry |
| 03 | Fishing and aquaculture |
| 10 | Food manufacturing |
| 11 | Beverage manufacturing |
| 12 | Tobacco manufacturing |
| 41 | Building construction |
| 42 | Civil engineering |
| 43 | Specialized construction |
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

Use partial codes for broader searches: `56` matches all food/drink, `62` matches all IT, `41` to `43` matches all construction. NACE Rev. 2 is shared across NO/FI/SE, so the same codes work in all three countries.

### Norwegian County Codes (country=NO, use with `county` parameter)

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

The `county` parameter is Norway-only. For FI/SE, use `query` for city names or filter by NACE only and let revenue/employee filters narrow the list. `list_municipalities` is Norway-only.

### Organization Forms

Each country uses its own form codes. Filter with `orgForm`:

| Country | Code | Type |
|---------|------|------|
| NO | AS | Private limited company (aksjeselskap) — most common |
| NO | ASA | Public limited company |
| NO | ENK | Sole proprietorship |
| NO | NUF | Norwegian branch of foreign company |
| NO | SA | Cooperative |
| FI | OY | Private limited company (osakeyhtiö) — most common |
| FI | OYJ | Public limited company |
| FI | AY | General partnership (avoin yhtiö) |
| FI | KY | Limited partnership (kommandiittiyhtiö) |
| SE | AB | Private limited company (aktiebolag) — most common |
| SE | HB | General partnership (handelsbolag) |
| SE | KB | Limited partnership (kommanditbolag) |
| SE | EF | Sole proprietorship (enskild firma) |

## Step 3: Run the Search

Use `find_prospects` for prospect-oriented results (pre-sorted by size, includes financials).
Use `search_companies` for more flexible filtering (custom sort, more filter options).

Present results as a ranked summary table:

| # | Company | Org Number | Location | Employees | Revenue (MNOK) |
|---|---------|-----------|----------|-----------|----------------|
| 1 | Example AS | 999888777 | Oslo | 45 | 32.5 |

Format revenue in the local currency: MNOK for Norway, MEUR for Finland, MSEK for Sweden. Round to one decimal.

If no results are found:
- Try broadening the NACE code (use parent code like `56` instead of `56.1`)
- Try removing the county filter
- Try lowering the minimum employee/revenue threshold
- Suggest alternative NACE codes that might match what the user meant

## Step 4: Offer Detailed Financials

After presenting the summary, offer:

> Would you like me to pull detailed financials for any of these companies? I can show multi-year revenue, profit, and key ratios.

Use `get_financials` for selected companies. Present year-over-year trends:

- Revenue trend (growing, stable, declining)
- Profit margin (net result / revenue)
- Whether they appear financially healthy

### Financial Health Quick Guide

| Metric | Healthy | Caution | Warning |
|--------|---------|---------|---------|
| Profit margin | > 10% | 3-10% | < 3% or negative |
| Revenue growth YoY | > 5% | 0-5% | Declining |
| Equity ratio | > 30% | 20-30% | < 20% |

## Step 5: Offer CRM Push

If the user seems satisfied with the results, offer:

> Want me to push these companies to your CRM? I can check which destinations are configured.

1. Use `list_destinations` to show available CRM destinations.
2. Confirm which companies to push (all results, or a selection).
3. Use `push_to_destination` with the chosen destination name and org numbers.
4. Report the results: how many were created, updated, or failed.
