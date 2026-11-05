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

**POST /products**

1. **Success code mismatch (documentation, low).** The spec documents 200 for a successful create. The API returns 201 (asserted in "Create prod - valid"). 201 is the correct code for a create, so the spec should be updated.
2. **Product create has no authentication or authorization check (security, high).** POST /products creates a product with no token, and also with a customer token. Only admins should be able to create products. The spec documents no 401 or 403 response for this endpoint. Asserted by "[KNOWN BUG] Create prod - no token" (expects 401) and "[KNOWN BUG] Create prod - customer token" (expects 403), which fail until this is fixed.
3. **422 error format not documented (documentation, low).** `UnprocessableEntityResponse` has a description but no schema. Actual format: `{ "price": ["The price field must be a number."] }`. Asserted in "Create prod - price is text".
4. **Negative prices are accepted (data validation, high).** POST /products creates a product with price -5. Asserted by the negativePrice row in data/products.csv.
5. **Product name limit is undocumented (documentation, low).** The API accepts 120 characters and rejects 121 with 422. The spec states no maximum length. Asserted by the maxName and nameTooLong rows.
6. **Open question: is a price of 0 allowed?** The API accepts 0. The spec does not say whether free products are valid. The zeroPrice row expects 422 until the product owner decides.

## Assertions
- response is JSON
- product has required fields
- short response time
- field types are correct
- product list if not empty
- every product has a positive price