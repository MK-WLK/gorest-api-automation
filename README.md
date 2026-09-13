# GoRest API Automation

Postman/Newman API automation project demonstrating **CRUD testing, data-driven execution, negative testing, test-data cleanup, and CI with GitHub Actions**.

## Highlights

- End-to-end CRUD lifecycle with chained requests
- CSV-based data-driven testing
- Positive and negative API validation
- Runtime test-data cleanup
- Newman CLI execution
- GitHub Actions CI
- Secure API token handling with GitHub Secrets

## Test Coverage

### CRUD Lifecycle

```text
POST Create User
      ↓
GET User
      ↓
PUT Update User
      ↓
DELETE User
      ↓
GET Verify Deletion
```

The created user's ID is captured at runtime and passed between requests so each test iteration operates on the same user.

**Latest local run:**

```text
5 iterations
25 requests
50 assertions
0 failures
```

### Negative Tests

| Scenario | Expected Result |
|---|---|
| Missing `name` | `422` + `can't be blank` |
| Invalid email | `422` + `is invalid` |

### Test-Data Cleanup

The `Cleanup` folder deletes test-created users and verifies the expected `204 No Content` response.

## Data-Driven Testing

`test-users.csv` provides test data for repeated CRUD execution, allowing multiple user scenarios to run without duplicating requests.

Example fields:

```text
name,emailPrefix,gender,status,updatedName
```

## Tech Stack

- **Postman** — API requests and test scripting
- **JavaScript** — Postman assertions
- **Newman** — CLI test execution
- **CSV** — Data-driven testing
- **Git/GitHub** — Version control
- **GitHub Actions** — CI
- **GitHub Secrets** — API token management

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

`environment.json` contains environment-specific configuration and is excluded through `.gitignore`.

## Run Locally

### Prerequisites

- Node.js
- Newman
- GoRest API token

Install Newman:

```powershell
npm install -g newman
```

Set the API token:

```powershell
$env:GOREST_TOKEN="your_token_here"
```

Run the CRUD tests:

```powershell
newman run collection.json `
  -d test-users.csv `
  --env-var "access_token=$env:GOREST_TOKEN" `
  --folder "CRUD Lifecycle"
```

Run the negative tests:

```powershell
newman run collection.json `
  --env-var "access_token=$env:GOREST_TOKEN" `
  --folder "Negative Tests"
```

## CI with GitHub Actions

The workflow in `.github/workflows/api-tests.yml` automatically:

1. Checks out the repository
2. Sets up Node.js
3. Installs Newman
4. Runs the CRUD lifecycle tests
5. Runs the negative tests

The API token is stored as the repository secret `GOREST_TOKEN` and injected at runtime rather than committed to the repository.

The workflow has been successfully executed with all configured stages passing.

## Skills Demonstrated

- REST API testing
- CRUD and end-to-end lifecycle testing
- Request chaining and dependent requests
- Data-driven testing
- Postman scripting and assertions
- Positive and negative testing
- HTTP status and response validation
- Test-data cleanup
- Newman CLI execution
- Git/GitHub
- GitHub Actions CI
- Secure secret management

## Future Improvements

- Add more boundary and edge-case tests
- Expand response/schema validation
- Add Newman HTML reports as CI artifacts
- Test additional GoRest resources
- Expand authentication/authorization scenarios

## AI Assistance

This project was developed with assistance from AI tools, primarily OpenAI's GPT-5.6 Luna and Anthropic Claude Sonnet 5.0. AI was used to explain unfamiliar programming concepts, assist with code development, troubleshoot issues, and guide implementation. All test logic, configurations, and project decisions were reviewed and validated by the author.

---

**Author:** MK-WLK

QA automation portfolio project focused on practical API testing and CI.
