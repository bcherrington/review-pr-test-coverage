# Migration Guide: From review-pr to review-pr-test-coverage

This document explains the differences between the original `review-pr` action and the new `review-pr-test-coverage` action.

## Overview

| Aspect | review-pr | review-pr-test-coverage |
|--------|-----------|-------------------------|
| **Purpose** | General code review | Test coverage analysis |
| **Focus** | Code quality, bugs, best practices | Test gaps, test case suggestions |
| **Output** | Code improvement suggestions | Test case recommendations |
| **Analysis** | Code patterns, security, performance | Test coverage across multiple levels |
| **Icon** | ⚡ (zap) | ✅ (check-circle) |
| **Color** | Purple | Green |

## Key Differences

### 1. Action Metadata

**review-pr:**
```yaml
name: "Augment Review PR"
description: "AI-powered PR Reviews"
branding:
  icon: "zap"
  color: "purple"
```

**review-pr-test-coverage:**
```yaml
name: "Augment Review PR Test Coverage"
description: "AI-powered test coverage analysis and test case suggestions for PRs"
branding:
  icon: "check-circle"
  color: "green"
```

### 2. Template Focus

#### instructions/base.njk

**review-pr**: Focuses on code review workflow
- Understand the request
- Analyze code changes
- Provide code improvement feedback

**review-pr-test-coverage**: Focuses on test analysis workflow
- Understand code changes
- Analyze test coverage gaps
- Suggest specific test cases across multiple levels

#### request/base.njk

**review-pr**: Requests code quality review
- Code quality issues
- Potential bugs
- Best practices
- Security concerns

**review-pr-test-coverage**: Requests test coverage analysis
- Test gap identification
- Test case suggestions (unit, functional, integration, system)
- Security, performance, error handling tests
- Prioritized test recommendations

#### guidelines/base.njk

**review-pr**: Code review guidelines
- Code quality focus areas
- Bug identification
- Best practices
- Security concerns
- Performance issues

**review-pr-test-coverage**: Testing guidelines
- 10 comprehensive test coverage areas
- Test quality principles
- Prioritization framework
- Specific test suggestion formats
- Examples for each test type

### 3. Coverage Areas

**review-pr** analyzes:
- Code Quality
- Potential Bugs
- Best Practices
- Security Concerns
- Performance
- Testing (as one of many areas)
- Documentation

**review-pr-test-coverage** analyzes:
1. Unit Testing
2. Functional Testing
3. Integration Testing
4. System/E2E Testing
5. Security Testing
6. Performance Testing
7. Error Handling & Resilience
8. Edge Cases & Boundary Conditions
9. Regression Testing
10. Contract/API Testing
11. Accessibility Testing (for UI)
12. Compatibility Testing

### 4. Output Format

**review-pr:**
- General code review comments
- Inline suggestions for code improvements
- Focus on "what's wrong" with the code

**review-pr-test-coverage:**
- Structured test suggestions with:
  - Test Type
  - Test Description
  - Rationale
  - Example Scenario
  - Priority (High/Medium/Low)
- Focus on "what's missing" in test coverage

### 5. Custom Guidelines

**review-pr:**
```yaml
custom_guidelines: |
  - Focus on TypeScript type safety
  - Ensure JSDoc comments
  - Check error handling
```

**review-pr-test-coverage:**
```yaml
custom_guidelines: |
  - All new features must have unit tests
  - API endpoints require integration tests
  - Critical flows need E2E tests
  - Use Jest for unit tests
  - Follow testing conventions in /docs/testing-guide.md
```

## When to Use Each

### Use `review-pr` when:
- ✅ You want general code quality feedback
- ✅ You need bug detection and security analysis
- ✅ You want best practices suggestions
- ✅ You need code improvement recommendations

### Use `review-pr-test-coverage` when:
- ✅ You want to improve test coverage
- ✅ You need specific test case suggestions
- ✅ You want to identify test gaps
- ✅ You need guidance on what to test
- ✅ You want prioritized test recommendations

## Can You Use Both?

**Yes!** They complement each other:

```yaml
name: Comprehensive PR Review
on:
  pull_request:
    types: [opened, synchronize]

jobs:
  code-review:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write
    steps:
      - name: Code Quality Review
        uses: augmentcode/review-pr@v0
        with:
          augment_session_auth: ${{ secrets.AUGMENT_SESSION_AUTH }}
          github_token: ${{ secrets.GITHUB_TOKEN }}
          pull_number: ${{ github.event.pull_request.number }}
          repo_name: ${{ github.repository }}

  test-coverage-review:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write
    steps:
      - name: Test Coverage Analysis
        uses: augmentcode/review-pr-test-coverage@v0
        with:
          augment_session_auth: ${{ secrets.AUGMENT_SESSION_AUTH }}
          github_token: ${{ secrets.GITHUB_TOKEN }}
          pull_number: ${{ github.event.pull_request.number }}
          repo_name: ${{ github.repository }}
```

This gives you:
1. Code quality feedback from `review-pr`
2. Test coverage suggestions from `review-pr-test-coverage`

## Migration Checklist

If you're switching from `review-pr` to `review-pr-test-coverage`:

- [ ] Update workflow file to use `augmentcode/review-pr-test-coverage@v0`
- [ ] Update custom guidelines to focus on testing requirements
- [ ] Review and adjust trigger conditions if needed
- [ ] Update workflow name and description
- [ ] Test with a sample PR
- [ ] Adjust custom guidelines based on initial results
- [ ] Consider running both actions for comprehensive coverage

## File-by-File Comparison

### Modified Files

| File | Changes |
|------|---------|
| `action.yml` | Updated name, description, branding (icon/color) |
| `package.json` | Updated name, description, keywords, repository URL |
| `README.md` | Complete rewrite focusing on test coverage |
| `RELEASE.md` | New release notes for test coverage version |
| `templates/instructions/base.njk` | Rewritten for test analysis workflow |
| `templates/request/base.njk` | Rewritten to request test suggestions |
| `templates/guidelines/base.njk` | Comprehensive testing guidelines (10 areas) |
| `templates/capabilities/base.njk` | Updated to include test analysis capabilities |
| `templates/formatting/base.njk` | Updated for test suggestion format |
| `templates/limitations/base.njk` | Updated with test-specific limitations |

### Unchanged Files

| File | Status |
|------|--------|
| `LICENSE` | Copied as-is (MIT) |
| `templates/prompt.njk` | Same structure (includes other templates) |
| `templates/context/pr_or_issue.njk` | Unchanged (PR context) |
| `templates/context/pr_details.njk` | Unchanged (PR details) |

### New Files

| File | Purpose |
|------|---------|
| `SETUP_GUIDE.md` | Comprehensive setup instructions |
| `MIGRATION_FROM_REVIEW_PR.md` | This file - migration guide |
| `example-workflows/basic-test-coverage-review.yml` | Basic test coverage workflow |
| `example-workflows/comprehensive-test-review.yml` | Advanced workflow with custom guidelines |
| `example-workflows/README.md` | Workflow documentation |

## Summary

The `review-pr-test-coverage` action is a specialized version of `review-pr` that focuses exclusively on test coverage analysis. While `review-pr` provides broad code review feedback, `review-pr-test-coverage` provides deep, structured test case suggestions across multiple test levels and testing areas.

Both actions use the same underlying infrastructure (Augment Agent, Nunjucks templates, GitHub integration) but with completely different prompts and guidelines to achieve their specific purposes.

Choose based on your needs, or use both for comprehensive PR analysis!
