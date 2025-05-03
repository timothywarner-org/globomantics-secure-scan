# CodeQL Workflow Diagram

## How CodeQL Works

```
┌───────────────────┐     ┌───────────────────┐     ┌───────────────────┐
│                   │     │                   │     │                   │
│  Source Code      │────▶│  CodeQL Database  │────▶│  CodeQL Queries   │
│                   │     │  Creation         │     │                   │
└───────────────────┘     └───────────────────┘     └─────────┬─────────┘
                                                              │
                                                              ▼
┌───────────────────┐     ┌───────────────────┐     ┌───────────────────┐
│                   │     │                   │     │                   │
│  Results Viewing  │◀────│  Results in SARIF │◀────│  Query Execution  │
│  & Reporting      │     │  Format           │     │                   │
└───────────────────┘     └───────────────────┘     └───────────────────┘
```

## CodeQL Process Steps

1. **Database Creation**: CodeQL analyzes your source code and builds a database representation that captures the semantic structure of your code.

2. **Query Development**: Write or use existing CodeQL queries that define code patterns to search for, including security vulnerabilities.

3. **Query Execution**: Run the queries against the database to identify matching patterns.

4. **Results Analysis**: Review the results to identify true security issues and fix vulnerabilities.

## Example Workflow Commands

### Creating a Database

```bash
codeql database create my-database --language=javascript --source-root=/path/to/source
```

### Running Queries

```bash
codeql database analyze my-database globomantics/javascript-security-queries:security-suite.qls --format=sarif-latest --output=results.sarif
```

### Viewing Results

Use either:
- GitHub Security tab (when run in GitHub Actions)
- VS Code with CodeQL extension
- SARIF viewers
- Convert to CSV:
  ```bash
  codeql bqrs decode --format=csv results.bqrs > results.csv
  ```

## Integration with GitHub Actions

CodeQL can be automatically run in your CI/CD pipeline with GitHub Actions:

```yaml
name: "CodeQL Analysis"
on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
  schedule:
    - cron: '0 0 * * 0'

jobs:
  analyze:
    name: Analyze
    runs-on: ubuntu-latest
    permissions:
      security-events: write
    steps:
    - name: Checkout repository
      uses: actions/checkout@v3
    - name: Initialize CodeQL
      uses: github/codeql-action/init@v2
      with:
        languages: javascript
        queries: globomantics/javascript-security-queries@1.0.0
    - name: Perform CodeQL Analysis
      uses: github/codeql-action/analyze@v2
``` 