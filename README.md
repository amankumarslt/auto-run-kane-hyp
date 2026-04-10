# Kane AI HyperExecute Orchestrator

Automates the creation and execution of test runs on LambdaTest using Kane AI via their Test Manager API.

## What it does

1. Fetches a test case by title from your LambdaTest project
2. Matches target environments (browser/OS configs) from your account
3. Creates a test run and populates it with instances for each environment
4. Triggers Kane AI execution on HyperExecute

## Prerequisites

- Node.js (v14+)
- A LambdaTest account with Test Manager and Kane AI access

## Setup

### Install dependencies

```bash
npm install
```

### Environment Variables

The following environment variables are required:

| Variable | Description |
|---|---|
| `LT_USERNAME` | Your LambdaTest username |
| `LT_ACCESS_KEY` | Your LambdaTest access key (found under Account Settings > Password & Security) |
| `LT_PROJECT_ID` | The project ID from LambdaTest Test Manager |
| `TARGET_TITLE` | Exact title of the test case to run |
| `TARGET_ENV_NAMES` | Comma-separated list of environment configuration names (no spaces around commas) |

### GitHub Actions CI/CD Setup

The secrets are stored in a **GitHub Environment** called `USERNAME`.

#### Adding secrets to the environment

1. Go to your repo > **Settings** > **Environments**
2. Select the `USERNAME` environment (or click **New environment** to create it)
3. Under **Environment secrets**, click **Add secret** and add each one:
   - `LT_USERNAME`
   - `LT_ACCESS_KEY`
   - `LT_PROJECT_ID`
   - `TARGET_TITLE`
   - `TARGET_ENV_NAMES`

#### Workflow file

Reference the environment in your workflow so the job can access its secrets:

```yaml
# .github/workflows/kane-ai.yml
name: Kane AI Test Run

on:
  workflow_dispatch:  # manual trigger
  # or schedule, push, etc.

jobs:
  run-tests:
    runs-on: ubuntu-latest
    environment: USERNAME  # pulls secrets from this GitHub Environment
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: '20'

      - run: npm install

      - name: Run Kane AI Orchestrator
        env:
          LT_USERNAME: ${{ secrets.LT_USERNAME }}
          LT_ACCESS_KEY: ${{ secrets.LT_ACCESS_KEY }}
          LT_PROJECT_ID: ${{ secrets.LT_PROJECT_ID }}
          TARGET_TITLE: ${{ secrets.TARGET_TITLE }}
          TARGET_ENV_NAMES: ${{ secrets.TARGET_ENV_NAMES }}
        run: node flow.js
```

> **Note:** The `environment: USERNAME` line is required. Without it, the job cannot access secrets stored in that environment.

### Local Development

For running locally, export the variables in your terminal:

```bash
export LT_USERNAME="your_lambdatest_username"
export LT_ACCESS_KEY="your_lambdatest_access_key"
export LT_PROJECT_ID="your_project_id"
export TARGET_TITLE="Your Test Case Title"
export TARGET_ENV_NAMES="EnvName1,EnvName2,EnvName3"
```

## Usage

```bash
node flow.js
```

## Output

- Console logs showing progress through each step
- `kane_job.env` file containing `KANE_JOB_ID=<job_id>` for downstream CI/CD usage
