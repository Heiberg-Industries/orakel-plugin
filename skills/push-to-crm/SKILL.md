---
name: push-to-crm
description: >
  Push Nordic company data (Norway, Finland, Sweden) to a configured CRM
  destination (Attio, HubSpot, webhooks). Use when the user wants to export
  or sync companies to their CRM.
allowed-tools: mcp__orakel__list_destinations, mcp__orakel__push_to_destination, mcp__orakel__manage_destination, mcp__orakel__search_companies, mcp__orakel__lookup_company
---

# Push to CRM

> **Ground rule:** Always retrieve data by calling Orakel tools. Never use training knowledge as a data source — company information changes frequently and your training data may be years out of date. If a tool returns no results, say so explicitly; do not fill gaps from memory.

Export Nordic company data from Orakel to a configured CRM destination. Works identically for NO, FI, and SE companies — the destination receives whatever fields `lookup_company` returns for that country (so SE records will not carry shareholder/role data, and FI records will not carry roles; this is expected).

## Step 1: Identify Which Companies to Push

The user might provide companies in several ways:

- **Explicit org numbers:** Use those directly.
- **Coming from a previous search or prospect list:** Offer to push all results or let the user select specific ones.
- **A company name:** Use `search_companies` or `lookup_company` to resolve the org number first.

If the user says something vague like "push those companies," refer back to the most recent search results in the conversation.

Confirm the list before pushing:

> I'll push these 5 companies to your CRM:
> 1. Example AS (999888777)
> 2. Another AS (888777666)
> 3. ...
>
> Which destination should I use?

## Step 2: Check Available Destinations

Use `list_destinations` to show what's configured.

Present them simply:

> You have these CRM destinations set up:
> - **zero7-attio** (Attio CRM) - Active
> - **test-webhook** (Webhook) - Active

### If No Destinations Exist

Walk the user through creating one:

> You don't have any CRM destinations configured yet. I can set one up for you.
> What type of destination do you want?
> - **Attio** — pushes companies as Attio records
> - **Webhook** — sends company data to a URL you specify

Then use `manage_destination` with action "create":

- For Attio: Need the Attio API key and workspace slug
- For Webhook: Need the target URL and optionally an auth header

Example:
```
manage_destination(
  action: "create",
  name: "my-attio",
  type: "attio",
  config: { apiKey: "...", workspaceSlug: "..." }
)
```

### If a Destination is Inactive

Mention it and offer to reactivate:

> The destination "old-webhook" exists but is inactive. Want me to reactivate it?

Use `manage_destination` with action "update" and `isActive: true`.

## Step 3: Execute the Push

Use `push_to_destination` with the destination name and array of org numbers.

**Important limits:**
- Push in batches if there are more than 50 companies (the API may time out on large batches).
- Each push is idempotent — pushing the same company twice will update rather than duplicate.

## Step 4: Report Results

The push response includes counts of created, updated, and failed records. Report clearly:

> Push complete:
> - **Created:** 3 new records in Attio
> - **Updated:** 2 existing records refreshed
> - **Failed:** 0
>
> All 5 companies are now in your CRM.

If any failed, show the org numbers and error messages so the user can investigate.

## Important Notes

- **Attio preserves brand names:** When pushing to Attio, company names from Orakel (legal names from Brreg) will not overwrite brand names that have been manually set in Attio. Only empty name fields get populated.
- **Data freshness:** Orakel pushes the latest data it has. Financial data may be up to a year old (depends on when the company filed).
- **Destination management:** Use `manage_destination` to create, update config, activate/deactivate, or delete destinations.
