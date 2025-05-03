# CodeQL Query Development Guide

This guide provides a step-by-step approach to developing custom CodeQL queries for JavaScript applications.

## Query Structure

Every CodeQL query (.ql file) has the following structure:

```ql
/**
 * @name Name of the query
 * @description Description of what the query detects
 * @kind problem | path-problem | metric
 * @problem.severity error | warning | recommendation
 * @security-severity 0.0-10.0
 * @precision very-high | high | medium | low
 * @id js/my-query-id
 * @tags security | maintainability | correctness | etc.
 *       external/cwe/cwe-XXX
 */

// Import libraries
import javascript
import DataFlow::PathGraph  // For path-problem queries

// Optional predicate definitions
predicate isSource(DataFlow::Node node) {
  // Logic to identify sources
}

predicate isSink(DataFlow::Node node) {
  // Logic to identify sinks
}

// Query clause
from <variables>
where <conditions>
select <results>
```

## Step 1: Define the Query Metadata

The metadata in the comment header is essential for integration with CodeQL tools:

- `@name`: Short, descriptive name
- `@description`: Detailed explanation of the vulnerability
- `@kind`: The type of query (problem, path-problem, metric)
- `@problem.severity`: The severity level
- `@security-severity`: Numeric severity (0.0-10.0)
- `@precision`: How accurate the results are expected to be
- `@id`: Unique identifier
- `@tags`: Categories and CWE mappings

## Step 2: Import Required Libraries

Import the libraries you need for your query:

```ql
import javascript
import DataFlow::PathGraph  // For path queries
```

## Step 3: Define Predicates

Predicates are reusable logic blocks that help organize your query:

```ql
/**
 * Identifies potential sources of user input
 */
predicate isUserInput(DataFlow::Node node) {
  node instanceof DataFlow::ParameterNode or
  node.(DataFlow::PropRead).getPropertyName() = "body" or
  node.(DataFlow::PropRead).getPropertyName() = "query"
}
```

## Step 4: Write the Query Clause

The query clause follows this pattern:

```ql
from <variable declarations>
where <logical conditions>
select <result expressions>
```

Example:

```ql
from DataFlow::Node source
where isUserInput(source)
select source, "This user input might be used in a dangerous context."
```

## Step 5: Advanced Path Queries

For data flow vulnerabilities, use a configuration:

```ql
class MyVulnerabilityConfig extends DataFlow::Configuration {
  MyVulnerabilityConfig() { this = "MyVulnerabilityConfig" }
  
  override predicate isSource(DataFlow::Node source) {
    // Define sources
  }
  
  override predicate isSink(DataFlow::Node sink) {
    // Define sinks
  }
  
  override predicate isBarrier(DataFlow::Node node) {
    // Define sanitizers (optional)
  }
}

from MyVulnerabilityConfig config, DataFlow::PathNode source, DataFlow::PathNode sink
where config.hasFlowPath(source, sink)
select sink.getNode(), source, sink, "Vulnerability description with $@.", source.getNode(), "user-controlled data"
```

## Step 6: Testing Your Query

Test your query using these commands:

```bash
# Create a database if you haven't already
codeql database create my-db --language=javascript --source-root=/path/to/source

# Run your query
codeql database run-queries my-db path/to/my-query.ql

# View the results
codeql bqrs decode --format=csv path/to/results.bqrs > results.csv
```

## Common CodeQL Patterns for JavaScript

### Identifying Function Calls

```ql
exists(CallExpr call |
  call.getCalleeName() = "dangerousFunction"
)
```

### Tracking Data Flow

```ql
DataFlow::Configuration config = ...;
config.hasFlow(source, sink)
```

### Regular Expression Matching

```ql
string s = node.toString();
s.regexpMatch("pattern.*here")
```

## Resources

- [CodeQL for JavaScript](https://codeql.github.com/docs/codeql-language-guides/codeql-for-javascript/)
- [CodeQL Library for JavaScript](https://codeql.github.com/codeql-standard-libraries/javascript/)
- [Writing Custom CodeQL Queries](https://codeql.github.com/docs/writing-codeql-queries/) 