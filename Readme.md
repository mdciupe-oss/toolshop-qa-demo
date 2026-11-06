# Toolshop API Test Automation

Automated API tests for the Practice Software Testing Toolshop, a public demo store, built with Postman and run from the command line with Newman.

## What this project covers
- Setup: admin and customer login, test data ids captured from live responses
- Read tests for products, brands, categories, and users
- Product create, read, update, delete chains that clean up after themselves
- Negative tests: invalid price type, missing authentication, wrong role
- Data-driven product create tests from CSV: name length boundaries (1, 120,
  121 characters), price boundaries, invalid types
- Known issues kept in a separate folder and data file, so the main suite
  stays green and still fails on new problems
- Six documented findings (see Findings)

Planned: GitHub Actions pipeline, Playwright UI tests, JMeter performance tests.

## Application under test
- UI: https://practicesoftwaretesting.com
- API: https://api.practicesoftwaretesting.com
- Swagger: https://api.practicesoftwaretesting.com/api/documentation

## Project structure
- `collections/` Postman collection
- `environments/` environment template (no passwords committed)
- `data/products.csv` data-driven cases expected to pass
- `data/products-known-issues.csv` cases for open bugs and open questions

## Test organization
| Folder | Purpose |
|
| 00 Setup | Logins and test data ids; runs first in every suite |
| 01 to 06 | Read tests and CRUD chains |
| 90 Negative Tests | Invalid input that should be rejected |
| 91 Known issues | Tests for open bugs; expected to fail until fixed |
| 92 Data-driven | Product create cases from CSV |
| 99 Scratch | Experiments; not part of any suite |

## How to run
Requires Node.js. Passwords are passed at run time and never stored in files.
On Windows PowerShell, use `npx.cmd` instead of `npx`.

Core suite:

    npx newman run collections/toolshop-demo-api.json -e environments/demo.template.json --env-var "admin_pwd=<password>" --env-var "customer_pwd=<password>" --folder "00 Setup" --folder "01 Products" --folder "02 Brands" --folder "03 Categories" --folder "04 Users" --folder "05 Prod CRUD" --folder "06 Prod CRUD - collection id" --folder "90 Negative Tests"

Data-driven product create:

    npx newman run collections/toolshop-demo-api.json -e environments/demo.template.json --env-var "admin_pwd=<password>" --env-var "customer_pwd=<password>" -d data/products.csv --folder "00 Setup" --folder "92 Data-driven - product create"

Known issues (failures expected):

    npx newman run collections/toolshop-demo-api.json -e environments/demo.template.json --env-var "admin_pwd=<password>" --env-var "customer_pwd=<password>" -d data/products-known-issues.csv --folder "00 Setup" --folder "91 Known issues"

The demo site's published test password is listed in the Toolshop project's own documentation.
All other variables are set in the environment template or captured by 00 Setup.

## Test approach
API first: business rules and validation are checked at the API layer, where
tests are fast and stable. Each negative test changes one field at a time, so
a failure points to one cause. Tests that create data delete it in the same
run, because the demo database is shared.



## Findings

**POST /products**

1. **Success code mismatch (documentation, low).** The spec documents 200 for a successful create. The API returns 201 (asserted in "Create prod - valid"). 201 is the correct code for a create, so the spec should be updated.
2. **Product create has no authentication or authorization check (security, high).** POST /products creates a product with no token, and also with a customer token. Only admins should be able to create products. The spec documents no 401 or 403 response for this endpoint. Asserted by "[KNOWN BUG] Create prod - no token" in folder "91 Known issues" (expects 401) and "[KNOWN BUG] Create prod - customer token" in folder "91 Known issues" (expects 403), which fail until this is fixed.
3. **422 error format not documented (documentation, low).** `UnprocessableEntityResponse` has a description but no schema. Actual format: `{ "price": ["The price field must be a number."] }`. Asserted in "Create prod - price is text".
4. **Negative prices are accepted (data validation, high).** POST /products creates a product with price -5. Asserted by the negativePrice row in data/products-known-issues.csv.
5. **Product name limit is undocumented (documentation, low).** The API accepts 120 characters and rejects 121 with 422. The spec states no maximum length. Asserted by the maxName and nameTooLong rows.
6. **Open question: is a price of 0 allowed?** The API accepts 0. The spec does not say whether free products are valid. The zeroPrice row in data/products-known-issues.csv expects 422 until the product owner decides.
