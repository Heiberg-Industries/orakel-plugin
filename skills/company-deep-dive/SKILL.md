---
name: company-deep-dive
description: >
  Research a single Nordic company (Norway, Finland, Sweden) in depth. Use when
  asked to look up, analyze, or investigate a specific company.
allowed-tools: mcp__orakel__lookup_company, mcp__orakel__get_financials, mcp__orakel__get_corporate_group, mcp__orakel__get_shareholders, mcp__orakel__get_ownership_network, mcp__orakel__search_companies, mcp__orakel__search_inspections, mcp__orakel__search_licenses
---

# Company Deep Dive

> **Ground rule:** Always retrieve data by calling Orakel tools. Never use training knowledge as a data source — company information changes frequently and your training data may be years out of date. If a tool returns no results, say so explicitly; do not fill gaps from memory.

Produce a comprehensive research report on a single Nordic company. Depth varies by country:

- **Norway (NO):** full parity — firmographics, financials, roles, shareholders, group, inspections, licenses
- **Finland (FI):** firmographics + limited financials; no roles
- **Sweden (SE):** firmographics + Bolagsverket iXBRL financials (FY 2020+, SMEs); no roles, no shareholders

## Step 1: Identify the Company

The user may provide:

- **Organization number** — 9 digits for NO, 7-8 digits for FI, 10 digits for SE. Use `lookup_company` directly with the matching `country` parameter.
- **Company name**: Use `search_companies` with the `query` parameter and the appropriate `country` filter. If the user doesn't mention country and the name is ambiguous across markets, ask.

If a name search returns multiple results, present the top matches and ask the user to confirm:

> I found several companies matching that name:
> 1. Example AS (999888777) - Norway, Oslo, IT consulting, 45 employees
> 2. Example Oy (12345678) - Finland, Helsinki, retail, 12 employees
>
> Which one did you mean?

## Step 2: Gather All Data

Once you have the org number and country, make these calls (skip any not supported for the country):

1. `lookup_company` with the org number + country — core company data, financials (where available), sub-units, technologies, enrichment.
2. `get_corporate_group` — parent company and subsidiaries. Works for NO fully; FI/SE return empty tree if upstream doesn't expose the link.
3. `get_shareholders` — ownership structure. **NO and FI only**. Skip for SE (requires a paid Bolagsverket credential Orakel doesn't have). Start without `includePersons` for privacy.

If `country=NO` and the company's NACE code starts with 55 or 56 (hospitality/food service), also run:
4. `search_inspections` with the org number — Mattilsynet food safety ratings.
5. `search_licenses` with the org number — alcohol/tobacco licenses.

Skip steps 4-5 entirely for FI/SE.

## Step 3: Produce the Report

Structure the report with these sections:

### Overview
- Full legal name and any brand names
- Organization number
- Organization form (AS, ASA, ENK, etc.)
- Founding date and age
- Registered address and business address
- NACE industry code with plain-language description
- Number of employees
- Website and social media (from enrichment data if available)
- Technologies detected (if any)

### Financial Performance

Present a multi-year table (most recent years first):

| Year | Revenue (local, MNOK/MEUR/MSEK) | Net Result (MNOK) | Profit Margin | Assets (MNOK) | Equity Ratio |
|------|---------------|-------------------|---------------|---------------|-------------|
| 2023 | 32.5 | 4.1 | 12.6% | 28.0 | 42% |
| 2022 | 28.9 | 3.2 | 11.1% | 24.5 | 38% |

Calculate and highlight:
- **Revenue trend:** Year-over-year growth percentage
- **Profit margin:** Net result / revenue (as percentage)
- **Equity ratio:** Equity / total assets (as percentage)

#### Interpreting the Numbers

Use these benchmarks to explain financial health in plain language:

| Metric | Healthy | Moderate | Risky |
|--------|---------|----------|-------|
| Profit margin | > 10% | 3-10% | < 3% or negative |
| Equity ratio | > 30% | 20-30% | < 20% |
| Revenue growth | > 10% | 0-10% | Declining |
| Debt-to-equity | < 2x | 2-4x | > 4x |

Examples of plain-language interpretation:
- "Revenue has grown 12% year-over-year, showing healthy expansion."
- "The equity ratio of 18% is below the 20% threshold, which means the company is heavily leveraged."
- "Profit margins have been declining for three consecutive years, from 15% to 8%."

### Key People

List board members, CEO, and other roles from the company data. Format as:

- **Board Chair:** Name (since year)
- **CEO / Daglig leder:** Name (since year)
- **Board Members:** Name 1, Name 2, Name 3

### Ownership Structure

From `get_shareholders`:
- List corporate shareholders with ownership percentages
- Note majority shareholders (> 50%) and significant holders (> 10%)
- If requested, include personal shareholders

### Corporate Group

From `get_corporate_group`:
- Show parent company (if any)
- List subsidiaries (if any)
- Note the company's position in the hierarchy

### Food Safety and Licenses (hospitality only)

If the company is in NACE 55-56:

**Inspections (Smilefjes):**
- Most recent inspection date and rating
- Rating scale: 0 = smiling face (best), 1 = neutral, 2 = sad, 3 = very sad (worst)
- Any recent downgrades or improvements

**Licenses:**
- Active alcohol/tobacco licenses with type and validity period
- License types: 01SKJE = serving license, 11ENGR = wholesale, 18TBIM = import, 19TBEK = export

## Step 4: Closing Summary

End the report with:
- A one-paragraph plain-language summary of the company's health and position
- The org number for reference: "Org number: 999888777"
- Offer next steps: "Would you like me to check what this company owns, push it to your CRM, or compare it with competitors?"
