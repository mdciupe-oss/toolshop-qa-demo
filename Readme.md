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

## Assertions
- response is JSON
- product has required fields
- short response time
- field types are correct
- product list if not empty
- every product has a positive price