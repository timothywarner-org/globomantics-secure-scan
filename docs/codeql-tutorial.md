# CodeQL Tutorial: Analyzing JavaScript Security Vulnerabilities

This tutorial provides step-by-step instructions for using the Globomantics JavaScript Security Query Pack to find security vulnerabilities in JavaScript applications. This guide focuses on using the CodeQL CLI for analysis.

## Prerequisites

Before you begin, make sure you have:

1. [CodeQL CLI](https://github.com/github/codeql-cli-binaries/releases) installed and accessible in your PATH
2. The Globomantics JavaScript Security Query Pack (this repository)
3. A JavaScript project to analyze (you can use the included `express-testapp`)

## Quick Reference

Here's a quick reference of the most common commands you'll use:

```bash
# Create a database
codeql database create my-database --language=javascript --source-root=/path/to/source

# Run the entire suite
codeql database analyze my-database globomantics/javascript-security-queries:security-suite.qls --format=sarif-latest --output=results.sarif

# Run a specific query
codeql database analyze my-database globomantics/javascript-security-queries:queries/javascript/detect-eval-use.ql --format=sarif-latest --output=eval-results.sarif

# View results in CSV format
codeql bqrs decode --format=csv results.bqrs.* > results.csv
```

## Step 1: Creating a CodeQL Database

The first step in any CodeQL analysis is to create a database from your source code. The database contains a queryable representation of your code.

```bash
# Basic syntax
codeql database create <database-name> --language=javascript --source-root=<source-directory>

# Example for the included Express test app
codeql database create express-app-db --language=javascript --source-root=./express-testapp
```

This command:
- Creates a new directory named `<database-name>` that contains the CodeQL database
- Analyzes JavaScript code in the specified source root directory
- Resolves dependencies and extracts a queryable model of your code

**Expected output:**
```
[2023-xx-xx xx:xx:xx] [build-stdout] Initializing database at /path/to/express-app-db.
[2023-xx-xx xx:xx:xx] [build-stdout] Creating directory structures.
...
[2023-xx-xx xx:xx:xx] [build-stdout] Running quickeval.
[2023-xx-xx xx:xx:xx] [build-stdout] Evaluating default predicates.
[2023-xx-xx xx:xx:xx] [build-stdout] Finalizing database at /path/to/express-app-db.
Successfully created database at /path/to/express-app-db.
```

## Step 2: Running the Complete Security Suite

The Globomantics JavaScript Security Query Pack includes a suite file (`security-suite.qls`) that groups all our security queries together.

```bash
# Basic syntax
codeql database analyze <database-name> globomantics/javascript-security-queries:security-suite.qls --format=<format> --output=<output-file>

# Example using the previously created database 
codeql database analyze express-app-db globomantics/javascript-security-queries:security-suite.qls --format=sarif-latest --output=suite-results.sarif
```

This command:
- Analyzes the database using all queries in the security suite
- Outputs the results in SARIF format, which can be viewed in compatible tools
- Saves the results to `suite-results.sarif`

**Expected output:**
```
Running queries.
Compiling query plan for /path/to/detect-eval-use.ql.
...
[1/3] Compiled /path/to/detect-eval-use.ql.
...
Evaluation done; writing results to /path/to/suite-results.sarif
```

## Step 3: Running Individual Queries

You can also run individual queries from the pack to focus on specific security issues.

### 3.1: Detecting Eval Use

```bash
codeql database analyze express-app-db globomantics/javascript-security-queries:queries/javascript/detect-eval-use.ql --format=sarif-latest --output=eval-results.sarif
```

This query detects potentially dangerous uses of `eval()`, the `Function` constructor, and similar functions that can lead to code injection vulnerabilities.

### 3.2: Finding HTTP Header Injection Vulnerabilities

```bash
codeql database analyze express-app-db globomantics/javascript-security-queries:queries/javascript/http-header-injection.ql --format=sarif-latest --output=header-injection-results.sarif
```

This query finds instances where user-controlled data flows into HTTP headers without proper sanitization.

### 3.3: Identifying Insecure Randomness

```bash
codeql database analyze express-app-db globomantics/javascript-security-queries:queries/javascript/insecure-randomness.ql --format=sarif-latest --output=randomness-results.sarif
```

This query finds places where `Math.random()` is used in security-sensitive contexts instead of cryptographically secure alternatives.

## Step 4: Viewing Results

CodeQL results can be viewed in several ways:

### 4.1: Converting SARIF to CSV

SARIF files can be difficult to read directly. Convert them to CSV for easier analysis:

```bash
# For the suite results
codeql bqrs decode --format=csv suite-results.bqrs.* > suite-results.csv

# For individual query results
codeql bqrs decode --format=csv eval-results.bqrs.* > eval-results.csv
```

**Note:** The `bqrs` files have numbered extensions, so the wildcard `*` ensures we capture the correct file.

### 4.2: Using VS Code with SARIF Viewer

1. Install the [SARIF Viewer Extension](https://marketplace.visualstudio.com/items?itemName=MS-SarifVSCode.sarif-viewer) in VS Code
2. Open your project in VS Code
3. Open the SARIF file or drag it into VS Code
4. The "Problems" panel will display the security issues

### 4.3: Using GitHub Security Tab

If you're using GitHub Advanced Security and have pushed your code to GitHub, you can view the results in the Security tab of your repository.

## Step 5: Customizing Queries

The pack includes a template query and an exercise to help you write your own security queries:

```bash
# Running the exposed secrets exercise
codeql database analyze express-app-db ./exercises/exposed-secrets-exercise.ql --format=sarif-latest --output=secrets-results.sarif
```

## Advanced Usage

### Running with Multiple Threads

For larger codebases, you can speed up analysis with multiple threads:

```bash
codeql database analyze express-app-db globomantics/javascript-security-queries:security-suite.qls -j8 --format=sarif-latest --output=suite-results.sarif
```

The `-j8` flag uses 8 threads for analysis.

### Including Standard Library Queries

You can include the standard JavaScript security queries with your custom ones:

```bash
codeql database analyze express-app-db globomantics/javascript-security-queries:security-suite.qls,codeql-javascript/security --format=sarif-latest --output=combined-results.sarif
```

### Filtering Results

You can filter results by severity:

```bash
codeql database analyze express-app-db globomantics/javascript-security-queries:security-suite.qls --format=sarif-latest --output=high-severity-results.sarif --filter="alert.severity='error'"
```

## Troubleshooting

### Query Pack Not Found

If you get a "query pack not found" error, make sure:
1. You're running the command from the repository root directory
2. The pack name in your command matches the one in `qlpack.yml`

### Database Creation Issues

If database creation fails:
1. Check that the source directory exists and contains JavaScript code
2. Ensure you have permissions to write to the output directory
3. Make sure you have Node.js installed for JavaScript extraction

### Path Issues on Windows

On Windows, you may need to use:
- Forward slashes in paths (`/`) even in Windows
- Absolute paths rather than relative ones

## Next Steps

1. Try creating your own security queries based on the template
2. Modify the existing queries to detect additional vulnerabilities
3. Run the query pack against your own JavaScript projects
4. Set up GitHub Actions to automate CodeQL scanning

## Resources

- [CodeQL JavaScript Documentation](https://codeql.github.com/docs/codeql-language-guides/codeql-for-javascript/)
- [CodeQL Query Help](https://codeql.github.com/codeql-query-help/)
- [SARIF Specification](https://docs.oasis-open.org/sarif/sarif/v2.1.0/sarif-v2.1.0.html) 