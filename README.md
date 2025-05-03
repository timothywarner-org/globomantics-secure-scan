# Globomantics JavaScript Security Query Pack

A comprehensive CodeQL query pack for identifying security vulnerabilities in JavaScript applications. This custom query pack can be used with GitHub Advanced Security (GHAS) or with the CodeQL CLI.

## 🔍 Overview

This repository contains custom CodeQL queries designed to help identify security vulnerabilities in JavaScript code. It's designed as a teaching tool for GitHub Enterprise Cloud and CodeQL education.

## 📦 Query Pack Contents

This query pack includes three custom security queries for JavaScript:

- **Detect Eval Use** (`queries/javascript/detect-eval-use.ql`): Identifies potentially dangerous uses of `eval()`, `Function()` constructor, and similar functions that can lead to code injection vulnerabilities (CWE-95).

- **HTTP Header Injection** (`queries/javascript/http-header-injection.ql`): Detects when user-controlled data flows into HTTP headers without proper sanitization, which can lead to header injection attacks (CWE-113).

- **Insecure Randomness** (`queries/javascript/insecure-randomness.ql`): Flags instances where `Math.random()` is used in security-sensitive contexts instead of cryptographically secure alternatives (CWE-338).

These queries are organized in a suite (`queries/javascript/security-suite.qls`) for easy execution.

## 🚀 Usage

### Prerequisites

- [CodeQL CLI](https://github.com/github/codeql-cli-binaries/releases)
- A JavaScript project to analyze

### Running the Queries

To run the entire query suite:

```bash
# Create a database
codeql database create js-db --language=javascript --source-root=/path/to/source

# Run the security suite from GitHub Container Registry
codeql database analyze js-db timothywarner-org/globomantics-security-queries:queries/javascript/security-suite.qls --format=sarif-latest --output=suite-results.sarif
```

To run an individual query:

```bash
# Run a specific query from GitHub Container Registry
codeql database analyze js-db timothywarner-org/globomantics-security-queries:queries/javascript/detect-eval-use.ql --format=sarif-latest --output=eval-results.sarif
```

## 🔒 Integration with GitHub Advanced Security

This query pack can be used with GitHub Advanced Security by adding it to your CodeQL workflow:

```yaml
- uses: github/codeql-action/init@v2
  with:
    languages: javascript
    queries: timothywarner-org/globomantics-security-queries@main
```

## 🧪 Testing

For testing these queries, check out our companion repository [globomantics-vulnerable-app](https://github.com/timothywarner-org/globomantics-vulnerable-app), which contains a deliberately vulnerable Express.js application designed to trigger these queries.

## 📚 Educational Purpose

This repository is part of the GitHub Enterprise Cloud training materials created by Tim Warner for Pluralsight. It demonstrates how organizations can create and govern custom CodeQL query packs that can be centrally published to GitHub Container Registry and consumed across multiple repositories.

## 📄 License

MIT 