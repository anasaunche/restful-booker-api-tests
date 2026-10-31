# Restful-Booker API – Postman Test Suite

API test suite for the public [Restful-Booker](https://restful-booker.herokuapp.com/apidoc/index.html) booking API, built with Postman and run in CI with Newman.

**20 requests · 50 assertions · chained CRUD flow · JSON-schema validation · 7 documented defects**

## What it covers

| Folder | What it checks |
|---|---|
| 00 Health | Service availability |
| 01 Auth | Token generation; token is saved and reused automatically by later requests |
| 02 Booking lifecycle | End-to-end chained flow: create → get → search → update (PUT) → partial update (PATCH) → delete → confirm 404 |
| 03 Security & error handling | Writes without a token or with an invalid token are rejected; missing resources return 404 |
| 04 Known defects | Tests that assert the *correct* behaviour and currently fail. Each one is tagged RB-01…RB-07 and has a full bilingual (EN/ES) bug report |

## Techniques shown

- Dynamic test data per run (random names, prices, future dates) so runs never collide
- Request chaining with collection variables (`token`, `bookingId`)
- JSON-schema validation of the booking response
- Collection-level response-time check (< 3 s on every request)
- Positive, negative, boundary and authorisation tests
- CI pipeline (GitHub Actions): the regression folders gate the build; known defects run as a non-blocking, tracked job; HTML reports are uploaded as build artifacts
- Weekly scheduled run to catch regressions in the live API

## Defects found

| ID | Severity | Summary |
|---|---|---|
| RB-01 | High | `POST /booking` with missing required fields returns **500** instead of 400 |
| RB-02 | High | Invalid dates are accepted and stored as `"0NaN-aN-aN"` |
| RB-03 | High | A booking with checkout **before** checkin is accepted |
| RB-04 | Medium | A non-numeric `totalprice` is silently saved as `null` |
| RB-05 | Medium | A negative `totalprice` is accepted |
| RB-06 | Medium | `GET /booking?checkin=notadate` returns **500** instead of 400 |
| RB-07 | Low | `POST /auth` with wrong credentials returns **200** instead of 401 |

## Run it

**Postman:** Import `restful-booker.postman_collection.json` and `restful-booker.postman_environment.json`, select the *Restful-Booker* environment, then *Run collection*.

**CLI:**
```bash
npm install -g newman newman-reporter-htmlextra
newman run restful-booker.postman_collection.json -e restful-booker.postman_environment.json -r cli,htmlextra
```

Expected result: folders 00–03 pass; the 8 assertions in folder 04 fail until the defects are fixed.

> Note: Restful-Booker is a public practice API that resets periodically and can be slow on first request.

---
Author: Ana Sanchez – QA Automation Engineer (API · Playwright · Cypress) · EN/ES
