# GoRest API Automation

A Postman-based API automation project for testing the GoRest API, with a focus on CRUD lifecycle testing, data-driven execution, negative testing, test-data cleanup, and CI execution through GitHub Actions.

## Overview

This project demonstrates a practical API testing workflow using **Postman**, **Newman**, and **GitHub Actions**.

The main test flow covers a complete user lifecycle:

```text
Create User
    ↓
Get User
    ↓
Update User
    ↓
Delete User
    ↓
Verify User Deletion
```

The collection also includes negative/edge-case tests to verify that the API correctly rejects invalid requests.

## Tech Stack

- **Postman** — API request development and test scripting
- **Newman** — Command-line execution of the Postman collection
- **JavaScript** — Postman test scripts
- **CSV** — Data-driven test input
- **Git / GitHub** — Version control
- **GitHub Actions** — CI automation
- **GitHub Secrets** — Secure API token management

## Project Structure

```text
gorest-api-automation/
├── .github/
│   └── workflows/
│       └── api-tests.yml
├── .gitignore
├── collection.json
├── test-users.csv
└── README.md
```

`environment.json` is intentionally excluded from Git through `.gitignore` so environment-specific configuration and credentials are not committed.

## Test Coverage

### CRUD Lifecycle

#### 1. Create User — `POST /users`

Creates a new GoRest user using CSV test data and dynamic values.

Validations include:

- Response status is `201 Created`
- Response contains a generated user ID
- Created user's name matches the test data

The generated user ID is captured and stored as an environment variable so subsequent requests operate on the same user.

#### 2. Get User — `GET /users/{id}`

Retrieves the user created during the current test iteration.

Validations include:

- Response status is `200 OK`
- Returned user's name is correct
- Returned user's status is correct

#### 3. Update User — `PUT /users/{id}`

Updates the user created during the current iteration.

Validations include:

- Response status is `200 OK`
- Updated name is returned
- User status is correct

#### 4. Delete User — `DELETE /users/{id}`

Deletes the user created by the test.

Validations include:

- Response status is `204 No Content`
- Response body is empty

#### 5. Verify User Deletion — `GET /users/{id}`

Confirms that the deleted user can no longer be retrieved.

Validations include:

- Response status is `404 Not Found`
- API returns the expected error message

## Data-Driven Testing

The CRUD lifecycle uses the CSV data file:

```text
test-users.csv
```

This allows the same collection to run against multiple sets of test data without duplicating requests.

Example Newman execution:

```powershell
newman run collection.json `
  -d test-users.csv `
  --env-var "access_token=$env:GOREST_TOKEN" `
  --folder "CRUD Lifecycle"
```

The collection was successfully executed across **5 iterations**:

```text
25 requests
50 assertions
0 failures
```

## Negative Testing

The project contains a dedicated `Negative Tests` folder.

### Missing Name

Attempts to create a user without the required `name` field.

Expected result:

```text
HTTP 422 Unprocessable Entity
```

The test verifies the validation message:

```text
can't be blank
```

### Invalid Email

Attempts to create a user with an invalid email address.

Expected result:

```text
HTTP 422 Unprocessable Entity
```

The test verifies the validation message:

```text
is invalid
```

These scenarios demonstrate testing of **failure paths**, not only successful API responses.

## Test Data Cleanup

Because GoRest persists created users, the project includes a dedicated `Cleanup` folder.

The cleanup request deletes the test-created user using its captured ID and verifies:

- `204 No Content`
- Empty response body

This prevents automated tests from intentionally leaving unnecessary test data in the API environment.

## Authentication & Secret Management

The GoRest API requires an access token for the operations used by this collection.

The token is **not committed to the repository**.

For local execution, the token can be supplied through an environment variable:

```powershell
$env:GOREST_TOKEN="your_token_here"
```

Newman then receives the token at runtime:

```powershell
newman run collection.json `
  -d test-users.csv `
  --env-var "access_token=$env:GOREST_TOKEN" `
  --folder "CRUD Lifecycle"
```

For CI execution, the token is stored as a **GitHub Actions repository secret** named:

```text
GOREST_TOKEN
```

The workflow passes the secret to Newman at runtime.

## Running the Tests Locally

### Prerequisites

Install:

- Node.js
- Newman

Install Newman globally:

```powershell
npm install -g newman
```

### Set the API Token

PowerShell:

```powershell
$env:GOREST_TOKEN="your_token_here"
```

### Run CRUD Tests

```powershell
newman run collection.json `
  -d test-users.csv `
  --env-var "access_token=$env:GOREST_TOKEN" `
  --folder "CRUD Lifecycle"
```

### Run Negative Tests

```powershell
newman run collection.json `
  --env-var "access_token=$env:GOREST_TOKEN" `
  --folder "Negative Tests"
```

## Continuous Integration

The project uses **GitHub Actions** to automatically execute the API tests.

Workflow:

```text
.github/workflows/api-tests.yml
```

The CI pipeline follows this flow:

```text
GitHub Push / Manual Run
        ↓
Checkout Repository
        ↓
Set Up Node.js
        ↓
Install Newman
        ↓
Run CRUD Lifecycle Tests
        ↓
Run Negative Tests
        ↓
Complete Job
```

The API token is provided through the GitHub repository secret:

```text
GOREST_TOKEN
```

No API credential is committed to the repository.

The workflow has been successfully executed through GitHub Actions with all configured test stages passing.

## Example CI Result

```text
API Tests
└── api-tests
    ├── Set up job              ✓
    ├── Checkout repository     ✓
    ├── Set up Node.js          ✓
    ├── Install Newman         ✓
    ├── Run CRUD lifecycle     ✓
    ├── Run negative tests     ✓
    └── Complete job           ✓
```

## What This Project Demonstrates

- REST API testing
- CRUD lifecycle testing
- Request chaining and dependent requests
- Dynamic test data
- Data-driven testing
- Postman test scripting
- Positive and negative testing
- HTTP status-code validation
- Response-body validation
- Test-data cleanup
- Command-line execution with Newman
- Git version control
- CI automation with GitHub Actions
- Secure secret management in CI

## Future Improvements

Potential improvements include:

- Add more boundary and edge-case scenarios
- Expand response schema and field validation
- Generate and publish Newman HTML reports
- Add additional API resources beyond users
- Add richer CI reporting and artifacts
- Expand authentication and authorization scenarios

## Author

**MK-WLK**

This project is part of a QA automation portfolio focused on practical API testing, automation, and CI workflows.
