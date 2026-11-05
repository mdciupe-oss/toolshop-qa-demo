# Toolshop API & UI Test Automation


## What this project covers
- API test suite (Postman/Newman) — N requests, N assertions
- Contract testing against an OpenAPI specification
- Negative and boundary test coverage
- UI automation (Playwright)
- Performance testing (JMeter)
- CI pipeline (GitHub Actions)

## Application under test
Toolshop — links to app, API, Swagger.

## Project structure
(the folder tree)

## Required variable (values injected at runtime; never committed)
- base URL
- admin credentials: email, password
- customer credentials: email, password
- product name filter (e.g. tool)

## How to run
(exact commands — someone should succeed from this section alone)

## Test approach
Why these tests, what risks they cover, how the layers divide.

## Findings
POST / products 
- The spec says a successful product create returns 200. My test asserts 201, and it passes. So the API returns 201 and the documentation says 200. That’s a contract mismatch.
- The responses list 200, 404, 405 and 422. There’s no 401 Unauthorized or 403 Forbidden, so POST /products accepts writes without a login.

## Findings

**POST /products**

1. **Success code mismatch (documentation, low).** The spec documents 200 for a successful create. The API returns 201 (asserted in "Create prod - valid"). 201 is the correct code for a create, so the spec should be updated.
2. **Creates allowed without authentication (security, high).** POST /products creates a product with no token. The spec also documents no 401 or 403 response, so it does not state any auth requirement for this endpoint.
3. **422 error format not documented (documentation, low).** `UnprocessableEntityResponse` has a description but no schema. Actual format: `{ "price": ["The price field must be a number."] }`. Asserted in "Create prod - price is text".

## Assertions
- response is JSON
- product has required fields
- short response time
- field types are correct
- product list if not empty
- every product has a positive price