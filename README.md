# TestLedger Action — Test Estate Balance Sheet

Price every automated test in your CI. TestLedger books each test's **carrying cost** (CI minutes, reruns, engineer triage on flaky failures, maintenance churn from git) against its **return** (regressions caught before production) and posts a one-page **Quality Investment Memo** with a retire / demote / fix / quarantine list.

Engine, docs and methodology: **[surkuar/testledger](https://github.com/surkuar/testledger)** · [sample memo](https://github.com/surkuar/testledger/blob/main/docs/sample-report/memo.md)

## Usage

```yaml
name: TestLedger
on:
  schedule: [{ cron: "0 6 * * 1" }]   # weekly memo
  pull_request:
permissions:
  contents: read
  actions: read
  pull-requests: write
jobs:
  ledger:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with: { fetch-depth: 0 }          # full git history for churn + fix detection
      # ... run your tests so JUnit/TestNG/Cucumber reports exist ...
      - uses: surkuar/testledger-action@v0.1.0
        with:
          reports: build/test-results
          config: testledger.yml          # optional: rates, thresholds, GitHub/Jenkins sources
```

The memo is appended to the job summary, all reports are uploaded as the `testledger-reports` artifact, and one comment per PR is kept up to date.

## Inputs

| Input | Default | Description |
|---|---|---|
| `reports` | `.` | Folder with JUnit XML, TestNG XML or Cucumber JSON |
| `config` | `testledger.yml` | Rates, thresholds and CI sources (`testledger init` writes one) |
| `out` | `testledger-out` | Output folder |
| `mutation-report` | | PIT XML / Stryker JSON — flags low-quality AI-generated tests |
| `fail-on-quarantine` | `false` | Fail the job if any test is quarantine-grade flaky |
| `comment-on-pr` | `true` | Upsert a summary comment on pull requests |
| `testledger-version` | `v0.1.0` | Engine version to install |
| `github-token` | `github.token` | For workflow history and PR comments |

## Outputs

| Output | Description |
|---|---|
| `net-usd` | Value delivered minus carrying cost in the window |
| `summary-path` | Path to `summary.json` |

## License
MIT © Suresh Babu
