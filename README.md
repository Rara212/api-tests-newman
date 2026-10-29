# Simple Books API: Postman + Newman Test Suite

![API Tests](https://github.com/Rara212/api-tests-newman/actions/workflows/api-tests.yml/badge.svg)

An API test suite for the public [Simple Books API](https://github.com/vdespa/introduction-to-postman-course/blob/main/simple-books-api.md), built in Postman and run from the command line with Newman. It runs on every push through GitHub Actions.

## What it covers
- 12 requests, 16 assertions
- **CRUD on orders:** create, read (list and single), update (PATCH), delete
- **Auth flow:** register a client, save the token, use it on `/orders`
- **Error responses:** 404 (unknown book), 400 (invalid book id), 401 (no token)
- **Contract check:** JSON schema validation on `GET /books/:id` (field names and types)

## How to run
```bash
git clone https://github.com/Rara212/api-tests-newman.git
cd api-tests-newman
npm install
npm test
```
The HTML report is written to `reports/report.html`.

## Structure
- `collections/`: the Postman collection (what is tested)
- `environments/`: environment files (where it is tested)
- `.github/workflows/`: CI

## Coverage decisions
**Why these endpoints:** TODO: orders cover full CRUD plus auth, which most APIs depend on; books cover reads, filtering and the contract check)

**What I de-prioritized:**
- Pagination and the `limit` parameter
- Rate limiting, concurrency and load: needs a dedicated tool like k6, and a shared public demo API shouldn't be hammered
- Unicode and very long input

**What a real-world version would include:**
- Schema validation against the OpenAPI spec instead of hand-written schemas
- A data seeding and cleanup strategy so tests don't depend on shared state
- Per-environment configs (dev, staging) with secrets stored in CI variables
- Contract tests running on every deploy

## What broke and why
1. **Register returned 409 and every order request returned 401.** TODO: hardcoded email was already registered, so no token was saved. Fixed with `{{$randomEmail}}` and saving the token in the test script.
2. **Error-case tests failed even though the API behaved correctly.** TODO: they asserted success codes. Changed them to expect 404, 400 and 401.
3. **Contract check proof.** TODO: changed `available` from boolean to string; the contract test failed while the 200 status test stayed green.

## Notes
State is passed between requests with collection variables, not Postman globals, so the run is self-contained. No secrets are stored in the repo; the token is generated on every run.