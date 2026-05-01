# Dimensions (dimensions)

Dimensions is a research data platform from Digital Science providing access to publications, grants, patents, clinical trials, datasets, and policy documents. The Dimensions Analytics API offers programmatic access to this research data via the Dimensions Search Language (DSL), enabling citation analysis, researcher discovery, organization benchmarking, and topic identification. The API is subscription-only and is not intended for bulk data extraction or to power dashboards or other derivative products.

**URL:** [Visit APIs.json URL](https://raw.githubusercontent.com/api-evangelist/dimensions/refs/heads/main/apis.yml)

## Scope

- **Type:** Index
- **Position:** Consumer
- **Access:** 3rd-Party

## Tags:

 - Analytics, Research, Publications, Grants, Patents, Clinical Trials, Jupyter Notebooks

## Timestamps

- **Created:** 2025-02-06
- **Modified:** 2026-04-28

## APIs

### Dimensions Analytics API

The Dimensions Analytics API provides programmatic access to the Dimensions research data platform via the Dimensions Search Language (DSL). It supports queries against publications, grants, patents, clinical trials, datasets, organizations, and researchers. Authentication uses an API key exchanged for a JWT token via POST to /api/auth, with query requests sent to /api/dsl/v2 carrying the JWT in the Authorization header. The API is subscription-only and rate limited to 30 requests per IP per minute.

**Human URL:** [https://docs.dimensions.ai/dsl/](https://docs.dimensions.ai/dsl/)

**Base URL:** https://app.dimensions.ai/api

#### Tags:

 - Analytics, Research, Publications, Grants, Patents, Clinical Trials

#### Properties

- [Documentation](https://docs.dimensions.ai/dsl/)
- [API Access](https://docs.dimensions.ai/dsl/api.html)
- [DSL Reference](https://docs.dimensions.ai/dsl/language.html)
- [API Lab](https://api-lab.dimensions.ai/)
- [SourceCode](https://github.com/digital-science/dimcli)

## Common Properties

- [Website](https://www.dimensions.ai/)
- [Documentation](https://docs.dimensions.ai/dsl/)
- [Support](https://plus.dimensions.ai/support/)
- [Blog](https://www.dimensions.ai/blog/)

## Maintainers

**FN:** Kin Lane

**Email:** kin@apievangelist.com
