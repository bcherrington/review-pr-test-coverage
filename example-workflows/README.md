# Example Workflows

This directory contains example GitHub Actions workflows for the Augment Review PR Test Coverage action.

## Available Examples

### [basic-test-coverage-review.yml](./basic-test-coverage-review.yml)
A simple, straightforward workflow that analyzes test coverage for all new pull requests.

**Use this when:**
- You want to get started quickly
- You don't need custom testing guidelines
- You want test coverage analysis on all PRs

**Triggers on:**
- Pull request opened
- Pull request synchronized (new commits)

---

### [comprehensive-test-review.yml](./comprehensive-test-review.yml)
A detailed workflow with custom testing guidelines tailored to specific project requirements.

**Use this when:**
- You have specific testing standards or frameworks
- You want to prioritize certain types of tests
- You have project-specific testing conventions
- You need to enforce coverage requirements

**Triggers on:**
- Pull request opened or synchronized
- Only for PRs targeting `main` or `develop` branches

**Includes custom guidelines for:**
- Testing frameworks (Jest, Cypress, React Testing Library)
- Coverage requirements
- Testing conventions and file organization
- Priority areas (authentication, payments, security)
- Performance testing requirements
- Security testing requirements

---

## How to Use These Examples

1. **Choose an example** that best fits your needs
2. **Copy the workflow file** to your repository's `.github/workflows/` directory
3. **Customize as needed**:
   - Adjust trigger conditions (branches, event types)
   - Modify custom guidelines to match your project
   - Add or remove testing requirements
4. **Ensure secrets are configured**:
   - `AUGMENT_SESSION_AUTH` must be set in repository secrets
   - `GITHUB_TOKEN` is automatically provided by GitHub Actions
5. **Commit and push** the workflow file to your repository

## Customization Tips

### Trigger Conditions
You can customize when the workflow runs:

```yaml
on:
  pull_request:
    types: [opened, synchronize, reopened]
    branches:
      - main
      - develop
      - 'feature/**'
```

### Custom Guidelines
Tailor the testing guidelines to your project:

```yaml
custom_guidelines: |
  ## Our Testing Standards
  
  - All new features require unit tests
  - API changes need integration tests
  - UI changes need visual regression tests
  - Security-sensitive code needs security tests
  
  ## Testing Frameworks
  - Backend: pytest
  - Frontend: Vitest + Testing Library
  - E2E: Playwright
  
  ## Priority Areas
  - Payment processing (CRITICAL)
  - User authentication (HIGH)
  - Data validation (HIGH)
```

### Model Selection
Use a specific AI model:

```yaml
with:
  model: "sonnet4"  # or other available models
```

### Rules and MCP Configs
Include custom rules or MCP configurations:

```yaml
with:
  rules: '[".augment/testing-rules.md"]'
  mcp_configs: '[".augment/mcp-config.json"]'
```

## Permissions

All workflows require these permissions:

```yaml
permissions:
  contents: read        # To checkout the repository
  pull-requests: write  # To post test coverage comments
```

## Best Practices

1. **Start Simple**: Begin with the basic workflow and add customization as needed
2. **Iterate on Guidelines**: Refine custom guidelines based on the quality of suggestions
3. **Use Feedback**: React with 👍/👎 on suggestions to help improve future analysis
4. **Combine with Coverage Tools**: Use alongside code coverage tools (e.g., Codecov, Coveralls)
5. **Review Regularly**: Periodically review and update testing guidelines
6. **Team Alignment**: Ensure custom guidelines align with team testing standards

## Troubleshooting

### Workflow Not Triggering
- Check that the workflow file is in `.github/workflows/`
- Verify trigger conditions match your PR events
- Ensure the workflow is on the default branch

### No Test Suggestions
- The AI may determine existing tests are sufficient
- Check that code changes are substantial enough to require new tests
- Review custom guidelines to ensure they're not too restrictive

### Irrelevant Suggestions
- Refine custom guidelines to be more specific
- Add project-specific context about testing frameworks
- Specify priority areas and testing conventions

## Additional Resources

- [Main README](../README.md) - Full documentation
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Augment Documentation](https://docs.augmentcode.com)
