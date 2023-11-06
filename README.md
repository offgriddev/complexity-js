# CyclomaticJS - A Complexity Measure for JavaScript

Cyclomatic Complexity was developed by a Computer Scientist at IBM named Thomas McCabe in an article titled [A Complexity Measure](https://ieeexplore.ieee.org/document/1702388). A metric we often use to identify "code quality" concerns, cyclomatic complexity is a useful analytical tool for many reasons. And now it's available for both CommonJS and ESModules JavaScript repositories!

First, let's cover the technical How-To for the contained NPM package and it's GitHub Action. Second, we'll dive deep into theory and explain why you would even want to use this anyway.

## Using

You can levarage CyclomaticJS as both a standalone import for your JavaScript project or as a GitHub Action in your CI workflows.

## CyclomaticJS as an Import

CyclomaticJS provides a library for importing into JS:

```javascript
import {calculateComplexity} from 'cyclomatic-js'

const complexity = calculateComplexity('somefile.js')
console.log(complexity)
```

This will produce a file called `complexity-report.json` in the main directory of your repository. The file will loo like this:

```json
[
  {
    "file": "./src/some-file.js",
    "report": {
      "function1": 2,
      "function2": 1,
      "function3": 3
    }
  }
]
```

The report object is simple, it contains the filename and a `report` with the function name and the cyclomatic complexity for that function.

## CyclomaticJS as a GitHub Action

CyclomaticJS also provides a GitHub Action to generate a report for pushing to any BI tool or storage for aggregation and analysis. This action can be found at [offgriddev/cyclomatic-js-action](https://github.com/offgriddev/cyclomatic-js-action). To use it, the following is an example step for a GitHub Action workflow:

```yml
- name: Generate Code Metrics for Commit
  id: metrics
  uses: offgriddev/cyclomatic-js-action@main
  with:
    github_token: ${{ github.token }}
    event: ${{ toJson(github.event) }}
    includedFileTypes: .js$
    excludedFileTypes: __mocks__|.test.js|Test.js|dist
```

This action has one export: `export_filename`. This stores the filename for the report generated by the complexity reporter in the CyclomaticJS package.

The action will produce a report that looks like this:

```json
{
  "actor": "offgriddev",
  "sha": "c7654a17c6c9ff33c7a93b2baadcb8d7b42f2490",
  "ref": "refs/heads/main",
  "repository": {
    "owner": "offgriddev",
    "repo": "cyclomatic-js-action"
  },
  "workingDirectory": "./",
  "files": [
    {
      "file": ".//src/index.js",
      "report": {
        "getPushDetails": 3,
        "getSourceFile": 2,
        "generateComplexityReport": 2,
        "run": 2
      }
    },
    {
      "file": ".//src/require-polyfill.js",
      "report": {}
    }
  ],
  "totalComplexity": 9,
  "dateUtc": 1699302398892
}
```
## Why Use CyclomaticJS?

To answer why you would want to use CyclomaticJS, you need to know a little about the code metric it uses. You may be new to software engineering, or you might be a senior or director and have not thoughtfully understood this concept. So let us briefly cover its nature.

### What is Cyclomatic Complexity

Cyclomatic Complexity, the code metric this repository analyses, measures the number of linear paths through a given function. Your function may be simple:

```javascript
function getNumberPolarity(a) {
  if (a > 0) {
    return 'is positive'
  } else if (a < 0) {
    return 'is negative'
  } else {
    return 'is neither positive nor negative'
  }
}
```

But as you can see, given a specific range of the variable `a`, the output may be different. There are three possible outputs.

1. When `a > 0`, the output is `is positive`.
2. When `a < 0`, the output is `is negative`.
3. When `a == 0`, the output is `is neither positive nor negative`.

Since there are 3 possible paths through this function, the Cyclomatic Complexity is 3. This is acquired by creating an Abstract Syntax Tree and measuring the _logical structures_ of a function. An `IfStatement`, for example, increases the Cyclomatic Complexity of the function because it creates a _logical branch_ in it. The lowest possible number of logical branches in a given function is 1 as an empty `BlockStatement`, or `function empty() { }` has one logical branch.

### Logical Structures that Increase Complexity

These logical structures are Universal. Any Turing Complete language can be measured by its logical structures. The following statements and expressions unconditionally increase the complexity of a function:

`IfStatement`
`TryStatement`
`CatchClause`
`DoWhileStatement`
`ForInStatement`
`ForOfStatement`
`WhileStatement`
`ConditionalExpression`

The following conditionally increase the complexity of a function:

`SwitchCase` as in the `case` statement of a `SwitchStatement` increases the complexity when the `case` has statements.
`LogicalExpression` increases the complexity when it is a `||`, `&&`, or `??`.

### Why Use Cyclomatic Complexity

Now for the question you may have asked yourself at some point if you've made it this far and have been looking for a JavaScript library to do this analysis for you. There are two main personas this library and action are targetting: Developers and anyone concerned with _estimations_. That last persona is vague. I promise, I will get there.

From a developer perspective, you may be concerned with Code Quality and Maintainability. You may have linting rules setup to limit the "size" if your functions, e.g. [eslint-complexity](https://eslint.org/docs/latest/rules/complexity). Some developers have a genuine interest in this as an engineering concern, and rightfully so. This may be around modularization and the role of writing more simple and elegant functions.

If you don't believe Cyclomatic Complexity or nerd out on analysing the logical complexity of your code, then you may be concerned about Estimations in some sense. You might be a Project Manager, a Lead Engineer, or perhaps you work in Professional Services and are putting a bid on a job and want to be competitive _and_ realistic.

That's right, you might want to give a _realistic estimation for a given software development effort_. You might, for example, be the CTO and need to report to ELT a high-level estimate for getting funding for a project. The second category is broad, but you want to provide as realistic of an estimation as you can.

## Estimation Methodology

Code is complex. Complexity in our functions is a result of the logical structure we write. When we develop, we produce functions, algorithms, that codify _business requirements_. If we take snapshots of this over time, we can understand the pace at which we develop software. Therefore, we can provide realistic estimations based on the concrete code we deliver over time.

This repository allows your teams to analyze the comlexity you're deliver as part of codifying the business requirements in your repositories. Complexity grows over time. Each developer also delivers functions of a given complexity at some rate. Cyclomatic Complexity is the objective measurement you can use the give estimations. By measuring the complexity a developer commits to production, you can do the following:

1. The actual complexity delivered by getting the delta from the previous commit
2. Validate the accuracy of an estimate
3. Understand the rate a developer produces business requirements based on the developer's historic delivery of functional complexity

# Supported Source Code

CyclomaticJS supports both CommonJS and ESModules. Please raise an issue if there are problems with either module loading systems.