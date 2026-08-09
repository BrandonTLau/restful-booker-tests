# Restful Booker API - Manual QA & API Automation Project

![CI](https://github.com/BrandonTLau/restful-booker-tests/actions/workflows/api-tests.yml/badge.svg)

Manual and automated API testing project for the [restful-booker](https://restful-booker.herokuapp.com/apidoc/index.html) practice API — a booking service with Create, Read, Update, Partial Update, and Delete endpoints.

## Overview

This project combines structured manual test design with an automated regression suite:

- **Manual QA:** 56-case test plan across 5 endpoints, built using equivalence partitioning and boundary value analysis, covering auth token validation, path parameter boundaries, field-level data type / required-field validation, and state verification via chained request sequences (create → update → verify, create → delete → verify).
- **Automation:** The confirmed-passing scenarios from that manual pass were converted into a Postman collection, run via Newman, and wired into a GitHub Actions CI pipeline that triggers on every push and pull request.

## Defects found

Testing surfaced **11 documented defects** across the API, including:

- Inconsistent type coercion (numeric strings silently converted; non-numeric strings rejected)
- Missing server-side validation on required fields, value ranges, and cross-field date logic (checkin/checkout)
- Incorrect HTTP status codes (e.g. `405` instead of `404` for a non-existent resource; `200` instead of `404` on invalid/missing IDs)
- Unhandled server crashes (`500`) on missing/empty request bodies
- A data-integrity issue where a deleted booking remained retrievable via `GET` afterward

Full defect log with steps to reproduce, expected vs. actual results, and severity/priority ratings is included in the test plan.

## Tech stack

- **Postman** — request design, assertions, dynamic test data generation
- **Newman** — CLI test runner for CI execution
- **GitHub Actions** — automated test run on every push/PR to `main`
- **Node.js / npm** — dependency and script management

## Project structure

```
restful-booker-tests/
├── postman/
│   ├── restful-booker.postman_collection.json
│   └── restful-booker.postman_environment.json
├── .github/
│   └── workflows/
│       └── api-tests.yml
├── package.json
└── README.md
```

## Running the tests locally

```bash
npm install
npm run test:api
```

This runs the full Postman collection against the live restful-booker API using Newman and prints a pass/fail summary to the console.

## Notes

This project tests against a shared, public demo API rather than a private/mocked instance. As a result, some test data (e.g. fixed booking IDs) isn't guaranteed to persist across runs, since other users of the same public API can create, modify, or delete records independently. Test scope was adjusted accordingly — assertions tied to a fixed record's exact field values were scoped down to structural/type checks where appropriate, and this is noted in the test plan.
