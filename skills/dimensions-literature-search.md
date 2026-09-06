---
name: dimensions-literature-search
description: >-
  Find publications, patents, clinical trials and datasets in Dimensions, resolve
  a DOI or PubMed ID to a full record, and find topic-similar work from an
  abstract — via the DSL or the Dimensions Analytics MCP tools.
api: Dimensions Analytics API
operations:
  - authenticate
  - dslQuery
mcp_tools:
  - search_publications
  - search_patents
  - search_clinical_trials
  - search_datasets
  - get_by_doi
  - get_by_pmid
  - get_by_id
  - similar_documents
  - construct_profile_url
source: >-
  https://docs.dimensions.ai/dsl/examples.html +
  https://github.com/digital-science/dimensions-analytics-mcp/blob/main/docs/USAGE.md
generated: '2026-09-06'
method: generated
---

# Search the research literature in Dimensions

Follow `dimensions-authenticate-and-query` first if you are calling the REST API
directly. If the Dimensions Analytics MCP server is connected, use its tools —
they compose the same DSL and add the guardrails described below.

## Full-text search

```
search publications for "AIDS" return publications
search publications in title_abstract_only for "AIDS" return publications
search publications for "HIV" where year<=2010 return publications
```

`for "..."` is full-text. `in <index>` narrows which index the full text hits —
`title_abstract_only`, `authors`, `funding`, and for patents
`title_abstract_claims`.

## Filtering

```
search publications for "HIV" where year in [2005:2015] return publications
search publications for "women" where (research_orgs.country_name="Senegal" or research_orgs.country_name="Gambia") and (year>=2000 and year<=2015) return publications
search grants where research_orgs.name~"National Blood" return research_orgs[name]
```

`~` is a fuzzy match. Keep to 100 boolean conditions and 400 items in an `in` list.

## Choosing fields

```
search grants for "malaria" return grants[basics]
search grants for "malaria" return grants[basics + extras]
search grants for "malaria" return grants[id + title + start_date]
```

Named fieldsets (`basics`, `extras`, …) and explicit `field + field` projections
both work. Ask for the narrowest projection that answers the question — it is
faster and it respects reasonable use.

## Lookups by identifier

| You have | Use |
|---|---|
| A DOI | MCP `get_by_doi`, or DSL `where doi = "..."` |
| A PubMed ID | MCP `get_by_pmid`, or DSL `where pmid = "..."` |
| A Dimensions ID | MCP `get_by_id`, or DSL `where id = "..."` |

Dimensions IDs are prefixed: `pub.` publications, `grant.` grants, `ur.`
researchers, `grid.` organizations, `jour.` source titles. The prefix tells you
which source to search.

## Topic-similar work from an abstract

`similar_documents` takes prose — an abstract or description — and returns
concept-similar publications or grants. Longer text works better than keywords.
The concept extraction limit defaults to 250 and cannot exceed 2,000 (DSL 2.15).

## Linking back

Never hand-build a dimensions.ai URL. Use MCP `construct_profile_url`, or the
`dimensions://schema/profile-urls` templates, so the link you give the user
actually resolves. Since MCP 1.4.0 the search tools also return `id` and `doi`
alongside results specifically so agents can cite without inventing links.

## Paging

`limit` up to 1,000 per call, `skip` for the offset, 50,000 records maximum per
search. Check `_stats.total_count` first. Above 10,000 results the MCP server
raises `largeResultWarning`; above `skip` 5,000 or page 5 it requires
`confirmLargeFetch: true`. Treat both as a prompt to narrow the query, not as a
box to tick.

## Do not

- Do not page to 50,000 to "have the data". Bulk extraction breaches the licence.
- Do not cache and re-serve Dimensions records as your own dataset.
- Do not use retrieved records to train a model.
