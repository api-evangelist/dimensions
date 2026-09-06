---
name: dimensions-funding-and-impact-analytics
description: >-
  Answer aggregate questions with Dimensions — who funds a topic, how funding and
  citations move over time, which organizations and researchers dominate a field —
  using DSL facets and aggregations rather than pulling rows.
api: Dimensions Analytics API
operations:
  - authenticate
  - dslQuery
mcp_tools:
  - facet_query
  - aggregate_query
  - citation_trend
  - funding_trend
  - search_grants
  - search_researchers
  - search_organizations
  - extract_affiliations
  - extract_grants
  - describe_schema
source: >-
  https://docs.dimensions.ai/dsl/examples.html +
  https://docs.dimensions.ai/dsl/usagepolicy.html
generated: '2026-09-06'
method: generated
---

# Funding and impact analytics with Dimensions

Aggregate first, retrieve later. Almost every "who / how much / what trend"
question is answerable with a facet or aggregation that returns a few hundred
buckets instead of thousands of rows — which is both faster and what the
Reasonable Use policy asks for.

## Facets — who is in this space

```
search grants return funders
search grants return funders[name + acronym + country_name] return research_orgs[basics] return research_org_countries[basics]
search grants for "malaria" return research_orgs
```

Facets return up to **1,000 buckets and cannot be paginated**. If you need more,
narrow the query — do not try to page.

## Aggregations — how much

```
search grants return funders return active_year aggregate funding
```

`aggregate` applies a metric (funding totals, counts) over the facet buckets.
Via MCP, `aggregate_query` does the same with `sum`/`avg`/`count` plus grouping.

## Trends over time

- `funding_trend` — funding awarded per year for a topic or entity.
- `citation_trend` — citations per year for a topic or entity.

Both are year-bucketed aggregations. Prefer them over pulling every record and
counting client-side.

## Resolving messy inputs

- `extract_affiliations` turns raw affiliation strings into **GRID/ROR**
  organization identifiers. Use it before filtering by organization — matching on
  `research_orgs.name~"..."` is fuzzy and will under-count.
- `extract_grants` turns grant numbers into full grant records.

## Know the schema before you guess a field

Call `describe_schema` (or read `dimensions://schema/summary`) rather than
guessing field names. Field names change: 2.15 deprecated
`research_org_country_names` for `research_org_countries`,
`research_org_state_names` for `research_org_states`, `acronym` for `acronyms`,
and `funder_org_acronym` for `funders`. Deprecated fields still work and return a
warning — if you see one, switch to the replacement rather than ignoring it.

Classification systems available for slicing: ANZSRC FOR, HRCS Health Categories
and Research Activity Codes, ICRP CSO, RCDC, UoA, and the UN SDGs.

## Reporting the answer

State the query you ran and the `_stats.total_count` it covered. An aggregate with
no denominator is not an answer a researcher can check. Cite records with the
canonical profile URL, never a hand-built one.

## Limits

30 requests per IP per minute, no rate-limit headers. A trend across twenty years
is still ONE query — build the aggregation, do not loop a query per year.
