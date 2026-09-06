---
name: dimensions-authenticate-and-query
description: >-
  Authenticate against the Dimensions Analytics API and run a Dimensions Search
  Language (DSL) query. This is the base flow every other Dimensions skill builds
  on — get a JWT, send DSL, read the result envelope, handle the documented errors.
api: Dimensions Analytics API
operations:
  - authenticate
  - dslQuery
source: https://docs.dimensions.ai/dsl/api.html
generated: '2026-09-06'
method: generated
---

# Authenticate and query Dimensions

The Dimensions Analytics API has exactly two operations. Everything else is
expressed in the query language carried in the request body.

## Before you start

- Access is **subscription-only**. There is no free tier and no self-serve signup.
  If you do not have a key, stop and tell the user to request one — do not attempt
  to guess or reuse a key.
- Get the key from the "My Account" section of the Dimensions web application.
- If the user logs in at a host other than `https://app.dimensions.ai` (for example
  `https://your-institution.dimensions.ai`), use **that** host as the base URL. A
  key from a custom instance returns `401` against `app.dimensions.ai`.

## Step 1 — mint a token (`authenticate`)

`POST {base}/api/auth` with `Content-Type: application/json`:

```json
{"key": "<the API key>"}
```

The response is `{"token": "<JWT>"}`. The token lasts **about two hours**. There is
no refresh token — re-run this step when it expires.

`/api/auth.json` is an equivalent path.

## Step 2 — run a query (`dslQuery`)

`POST {base}/api/dsl/v2` with the **raw DSL string as the body** — not JSON — and:

```
Authorization: JWT <token>
```

The keyword is `JWT`, not `Bearer`. Sending `Bearer` fails.

Working examples straight from the Dimensions docs:

```
search publications for "AIDS" return publications
search publications for "HIV" where year in [2005:2015] return publications
search grants for "malaria" return grants[id + title + start_date]
search grants return funders return active_year aggregate funding
```

## Reading the response

The body is a JSON object keyed by the source you asked for, plus a `_stats` block:

```json
{"researchers": [ ... ], "_stats": {"total_count": 8735}}
```

Read `_stats.total_count` **before** paging. If it exceeds 10,000, say so and
narrow the query rather than paging through it.

## Limits you must respect

| Limit | Value |
|---|---|
| Requests per IP per minute | 30 |
| Rows per call | 1,000 |
| Records per search via `limit`/`skip` | 50,000 |
| Facet buckets | 1,000 (no pagination) |
| Items in an `in` clause | 400 |
| Boolean filter conditions | 100 |
| Boolean full-text clauses | 100 |

The API returns **no** `Retry-After` and **no** `X-RateLimit-*` headers. You cannot
discover your remaining budget from a response — throttle yourself to 30 requests
per minute on a sliding window and back off exponentially with jitter on `429`.

## Errors

| Status | Meaning | What to do |
|---|---|---|
| 400 | DSL query is not valid, or too long/complex | Fix the syntax; reduce filters |
| 401 | Token expired or invalid, or wrong instance host | Re-run step 1; check the host |
| 408 | Query timed out | Narrow it; avoid multiple `UNNEST` clauses |
| 429 | More than 30 requests/IP/minute | Back off; no header tells you how long |
| 500 | Evaluation/data/timeout error | Simplify and retry; then email supportapi@dimensions.ai |

Successful responses can still carry warnings — deprecated-field warnings and the
multiple-`UNNEST` performance warning. Surface them to the user; do not swallow them.

## Reasonable use — this is a licence condition, not a suggestion

The API is licensed for analytical tasks, not for building a local copy of
Dimensions data, not as a substitute for the platform, and **not for model
training**. Do not mirror the corpus. Pull what the task needs and stop.

Reference: https://docs.dimensions.ai/dsl/usagepolicy.html
