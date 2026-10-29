## Contract validation
**What I added:** JSON schema check on GET /books/:id
**Broke it by:** changing `available` from boolean to string
**What I saw:** contract test red, status test still green
**Why it matters:** the status code alone wouldn't catch a changed field type

## Contract validation
**What I added:** JSON schema check on GET /books/:id
**Broke it by:** changing `available` from boolean to string in the schema
**What I saw:** contract test red, status test still green
**Why it matters:** a status-only test would pass even if the API changed a field's type

## Contract validation: break it on purpose

**What I broke:** changed `available` from boolean to string in the schema
**What I expected:** the contract test fails, the status test passes
**What I got:** 1 of 16 assertions failed: "data.available should be string" in "Get single book". The 200 status test stayed green.
**Why it happened:** the API returns a boolean, so the schema no longer matched the real response
**How I fixed it:** changed the schema back to boolean
**Why it matters:** a status-only test would not catch a field changing type