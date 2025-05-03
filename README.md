# Globomantics JavaScript Security Query Pack

A comprehensive CodeQL query pack for identifying security vulnerabilities in JavaScript applications. This custom query pack can be used with GitHub Advanced Security (GHAS) or with the CodeQL CLI.

## Contents

This query pack includes three custom security queries for JavaScript:

- **Detect Eval Use** (`queries/javascript/detect-eval-use.ql`): Identifies potentially dangerous uses of `eval()`, `Function()` constructor, and similar functions that can lead to code injection.

- **HTTP Header Injection** (`queries/javascript/http-header-injection.ql`): Detects when user-controlled data flows into HTTP headers without proper sanitization.

- **Insecure Randomness** (`queries/javascript/insecure-randomness.ql`): Flags instances where `Math.random()` is used in security-sensitive contexts instead of cryptographically secure alternatives.

These queries are organized in a suite (`queries/javascript/security-suite.qls`) for easy execution.

## Usage

### Prerequisites

- [CodeQL CLI](https://github.com/github/codeql-cli-binaries/releases)
- A JavaScript project to analyze

### Running the Queries

To run the entire query suite:

```bash
# Create a database
codeql database create js-db --language=javascript --source-root=/path/to/source

# Run the security suite
codeql database analyze js-db globomantics/javascript-security-queries:security-suite.qls --format=sarif-latest --output=suite-results.sarif
```

To run an individual query:

```bash
# Run a specific query
codeql database analyze js-db globomantics/javascript-security-queries:queries/javascript/detect-eval-use.ql --format=sarif-latest --output=eval-results.sarif
```

## Integration with GitHub Advanced Security

This query pack can be used with GitHub Advanced Security by:

1. Adding the query pack to your repository
2. Configuring CodeQL to use the custom queries

Example workflow configuration:

```yaml
- uses: github/codeql-action/analyze@v2
  with:
    queries: ./path/to/globomantics-secure-scan
```

## License

MIT 