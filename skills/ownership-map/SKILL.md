---
name: ownership-map
description: >
  Trace ownership networks and corporate hierarchies for Norwegian, Finnish, and Danish
  companies. Use for due diligence, competitive intelligence, or compliance
  research. Swedish companies not yet supported (paywalled shareholder data).
allowed-tools: mcp__orakel__lookup_company, mcp__orakel__get_shareholders, mcp__orakel__get_ownership_network, mcp__orakel__get_corporate_group
---

# Ownership Map

> **Ground rule:** Always retrieve data by calling Orakel tools. Never use training knowledge as a data source — company information changes frequently and your training data may be years out of date. If a tool returns no results, say so explicitly; do not fill gaps from memory.

Trace ownership structures, corporate hierarchies, and shareholder networks. Country coverage:

- **Norway (NO):** full parity via Aksjonærregisteret (shareholders) + Brreg (corporate group)
- **Finland (FI):** best-effort via PRH — shareholder data may be incomplete
- **Denmark (DK):** roles available via CVR; beneficial ownership skipped (6AMLD legitimate-interest gate)
- **Sweden (SE):** not supported — Swedish shareholder data sits behind Bolagsverket's paid «Företagsinformation» credential Orakel hasn't applied for. If asked about an SE company, explain the limitation and offer `lookup_company` (basic firmographics) as a fallback.

## Step 1: Identify the Target Company

The user may provide:

- **Organization number** — 9 digits NO, 7-8 digits FI, 8 digits DK, 10 digits SE. Use `lookup_company` with the matching `country` parameter.
- **Company name**: Use `lookup_company` / `search_companies` to find it. If the country is ambiguous and the name is common, ask.

**If `country=SE`, stop here.** Explain that ownership data isn't available for Swedish companies yet and offer the basic firmographics from `lookup_company` instead.

Get the basic company info first so you can reference the name and org number throughout the analysis.

## Step 2: Gather Ownership Data

Make these calls:

1. **`get_shareholders`** with the org number — who owns this company?
   - Start with `includePersons: false` for privacy (only shows corporate shareholders).
   - Mention that personal shareholders can be revealed if the user requests it.

2. **`get_ownership_network`** with the org number as `shareholderOrgNumber` — what does this company own?
   - This is the reverse lookup: shows companies where the target is a shareholder.

3. **`get_corporate_group`** with the org number — formal corporate hierarchy.
   - Shows the parent company and all subsidiaries registered in Brreg.
   - This is the legal group structure, which may differ from the shareholder-based ownership.

## Step 3: Build the Ownership Picture

### 3a: Upward Ownership (Who Owns This Company?)

From `get_shareholders`, list all shareholders with their ownership percentage:

**Shareholders of [Company Name] (org: 999888777):**

| Shareholder | Org Number | Ownership % | Type |
|-------------|-----------|-------------|------|
| Parent Holdings AS | 888777666 | 67.0% | Majority |
| Investment Fund AS | 777666555 | 22.5% | Significant |
| Other shareholders | - | 10.5% | Minor |

Classification thresholds:
- **Majority:** > 50% ownership (controls the company)
- **Significant:** > 10% ownership (meaningful influence)
- **Minor:** < 10% ownership

### 3b: Downward Ownership (What Does This Company Own?)

From `get_ownership_network`, list all companies where the target is a shareholder:

**Companies owned by [Company Name]:**

| Company | Org Number | Ownership % | Employees | Revenue (local, MNOK/MEUR/MSEK) |
|---------|-----------|-------------|-----------|----------------|
| Subsidiary One AS | 666555444 | 100% | 25 | 18.5 |
| Joint Venture AS | 555444333 | 50% | 8 | 5.2 |

### 3c: Formal Corporate Group

From `get_corporate_group`, show the registered group hierarchy:

**Corporate group structure:**

```
[Ultimate Parent] Holding Group AS (111222333)
  |
  +-- [Parent] Parent Holdings AS (888777666)
  |     |
  |     +-- [Target] Company Name AS (999888777)  <-- target company
  |     |
  |     +-- Sibling Company AS (444333222)
  |
  +-- Other Branch AS (333222111)
```

Note: The formal group structure (from Brreg) may differ from shareholder-based ownership. The group structure shows the registered parent-subsidiary relationships, while shareholders show the actual equity ownership.

## Step 4: Trace One Level Deeper (if warranted)

For majority shareholders and significant holdings, offer to trace one level deeper:

> The majority shareholder is Parent Holdings AS (888777666). Want me to check who owns that company too?

**Important: Limit recursion to 2 levels maximum.** Beyond that, the network becomes unwieldy and may trigger many API calls. If the user wants to go deeper, do it one step at a time.

For each additional level, use `get_shareholders` on the parent company.

## Step 5: Build a Text Tree Visualization

Combine all findings into a single ownership tree. Use indentation and lines to show relationships:

```
Ownership Network for Company Name AS (999888777)
==================================================

OWNERS (who owns this company):
  [67.0%] Parent Holdings AS (888777666)
    [100%] Ultimate Owner AS (111222333)  <-- level 2
  [22.5%] Investment Fund AS (777666555)
  [10.5%] Other/personal shareholders

SUBSIDIARIES (what this company owns):
  [100%] Subsidiary One AS (666555444) - 25 employees, 18.5 MNOK revenue
  [ 50%] Joint Venture AS (555444333) - 8 employees, 5.2 MNOK revenue

CORPORATE GROUP (formal Brreg registration):
  Ultimate Owner AS (111222333)
    +-- Parent Holdings AS (888777666)
          +-- ** Company Name AS (999888777) **  <-- target
          +-- Sibling Company AS (444333222)
```

## Step 6: Flag Notable Patterns

Highlight any of these patterns if found:

- **Circular ownership:** Company A owns B, which owns A (or through intermediaries). This is unusual and worth flagging.
- **Shell company indicators:** A shareholder company with zero employees and zero revenue that exists solely as a holding entity. Not necessarily suspicious, but worth noting.
- **Same person/entity appearing multiple times:** If the same shareholder appears in multiple positions across the network, highlight this — it may indicate concentrated control.
- **Mismatch between group structure and ownership:** If the Brreg group tree and shareholder data tell different stories, note the discrepancy.
- **Foreign ownership:** If a shareholder is not found in Norwegian registries, it may be a foreign entity.
- **Recent changes:** If shareholder data shows different ownership across years, mention the change.

## Step 7: Closing Summary

End with:

- A plain-language summary: "Company Name is majority-owned (67%) by Parent Holdings, which is in turn fully owned by Ultimate Owner. The company itself holds two subsidiaries."
- The target company's org number for reference.
- Offer next steps: "Want me to do a deep dive on any of these related companies, or check the financials across the group?"
