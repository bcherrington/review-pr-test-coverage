# Augment Review PR Test Coverage GitHub Action

AI-powered test coverage analysis for pull requests using Augment. This action automatically analyzes your PR changes and generates comprehensive test case suggestions to improve verification quality.

## Quick Start

### 1. Get Your Augment API Credentials

First, you'll need to obtain your Augment Authentication information from your local Auggie session:

Example session JSON:

```json
{
  "accessToken": "your-api-token-here",
  "tenantURL": "https://your-tenant.api.augmentcode.com"
}
```

There are 2 ways to get the credentials:

- Run `auggie tokens print`
  - Copy the JSON after `TOKEN=`
- Copy the credentials stored in your Augment cache directory, defaulting to `~/.augment/session.json`

> **⚠️ Security Warning**: These tokens are OAuth tokens tied to your personal Augment account and provide access to your Augment services. They are not tied to a team or enterprise. Treat them as sensitive credentials:
>
> - Never commit them to version control
> - Only store them in secure locations (like GitHub secrets)
> - Don't share them in plain text or expose them in logs
> - If a token is compromised, immediately revoke it using `auggie tokens revoke`

### 2. Set Up the GitHub Repository Secret

You need to add your Augment credentials to your GitHub repository:

#### Adding Secret

1. **Navigate to your repository** on GitHub
2. **Go to Settings** → **Secrets and variables** → **Actions**
3. **Add the following**:
   - **Secret**: Click "New repository secret"
     - Name: `AUGMENT_SESSION_AUTH`
     - Value: The JSON value from step 1

> **Need more help?** For detailed instructions on managing GitHub secrets, see GitHub's official documentation:
>
> - [Using secrets in GitHub Actions](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions)

### 3. Create Your Workflow File

Add a new workflow file to your repository's `.github/workflows/` directory and merge it.

### Example Workflows

For complete workflow examples, see the [`example-workflows/`](./example-workflows/) directory which contains:

- **[Basic Test Coverage Review](./example-workflows/basic-test-coverage-review.yml)** - Simple setup for test coverage analysis on new PRs
- **[Comprehensive Test Review](./example-workflows/comprehensive-test-review.yml)** - Detailed test coverage analysis with custom guidelines

Each example includes a complete workflow file that you can copy to your `.github/workflows/` directory and customize for your needs.

## Features

- **Automatic Test Gap Analysis**: Identifies missing test coverage across multiple test levels
- **Intelligent Test Suggestions**: Generates specific, actionable test case recommendations using AI
- **Multi-Level Coverage**: Analyzes unit, functional, integration, and system/E2E test needs
- **Comprehensive Testing Areas**: Covers security, performance, error handling, edge cases, and more
- **Context-Aware**: Understands your codebase structure and existing testing patterns
- **GitHub Integration**: Seamlessly posts test suggestions via GitHub review comments

## What Gets Analyzed

This action analyzes your PR and suggests tests across multiple dimensions:

### Test Levels
- **Unit Tests**: Individual functions, methods, classes, edge cases, error handling
- **Functional Tests**: Feature behavior, business logic, user workflows
- **Integration Tests**: Component interactions, API contracts, database operations
- **System/E2E Tests**: Complete user journeys, cross-component workflows

### Additional Testing Areas
- **Security Testing**: Authentication, authorization, input validation, injection attacks
- **Performance Testing**: Load handling, response times, scalability
- **Error Handling**: Exception handling, graceful degradation, recovery
- **Edge Cases**: Boundary values, null/empty inputs, concurrent access
- **Regression Testing**: Ensuring existing functionality still works
- **Contract Testing**: API contracts, data schemas, interface agreements
- **Accessibility Testing**: WCAG compliance, keyboard navigation (for UI changes)
- **Compatibility Testing**: Browser/platform compatibility, version compatibility

## Inputs

| Input                  | Description                                                                                | Required | Example                                             |
| ---------------------- | ------------------------------------------------------------------------------------------ | -------- | --------------------------------------------------- |
| `augment_session_auth` | Augment session authentication JSON containing accessToken and tenantURL (store as secret) | Yes      | `${{ secrets.AUGMENT_SESSION_AUTH }}`               |
| `github_token`         | GitHub token with `repo` scopes                                                            | Yes      | `${{ secrets.GITHUB_TOKEN }}`                       |
| `pull_number`          | The number of the pull request being reviewed                                              | Yes      | `${{ github.event.pull_request.number }}`           |
| `repo_name`            | The full name (owner/repo) of the repository                                               | Yes      | `${{ github.repository }}`                          |
| `custom_guidelines`    | Custom testing guidelines to include in test coverage analysis                             | No       | See [Custom Guidelines](#custom-guidelines) section |
| `model`                | Optional model name to use; passed directly to augment agent                               | No       | e.g. `sonnet4`, from `auggie --list-models`         |
| `rules`                | JSON array of rule file paths forwarded to augment agent as repeated `--rules` flags       | No       | `'[".augment/rules.md"]'`                           |
| `mcp_configs`          | JSON array of MCP config file paths forwarded as repeated `--mcp-config` flags             | No       | `'[".augment/mcp.json"]'`                           |

## How It Works

1. **Checkout**: Checks out the PR head to access the complete codebase
2. **Data Gathering**: Fetches PR metadata, changed files, and diff content from GitHub API
3. **Template Rendering**: Uses Nunjucks templates to create a structured test analysis instruction for the AI
4. **AI Processing**: Calls the Augment Agent to analyze the changes and identify test coverage gaps
5. **Test Suggestion Generation**: AI generates specific test case recommendations across multiple test levels
6. **PR Review Submission**: The Augment Agent submits a GitHub review containing test coverage analysis and inline test suggestions anchored to specific code locations

## Custom Guidelines

You can provide custom testing guidelines to tailor the test coverage analysis to your team's specific needs. These guidelines will be added to the default testing criteria.

### Basic Usage

```yaml
- name: Generate Test Coverage Review
  uses: augmentcode/review-pr-test-coverage@v0
  with:
    augment_session_auth: ${{ secrets.AUGMENT_SESSION_AUTH }}
    github_token: ${{ secrets.GITHUB_TOKEN }}
    pull_number: ${{ github.event.pull_request.number }}
    repo_name: ${{ github.repository }}
    custom_guidelines: |
      - Focus on testing React component behavior and hooks
      - Ensure all API endpoints have integration tests
      - Prioritize security tests for authentication flows
      - Include performance tests for data-heavy operations
      - Follow our testing conventions in /docs/testing-guide.md
```

## Test Suggestion Format

The AI provides test suggestions in a structured format:

- **Test Type**: Unit/Functional/Integration/System/Security/Performance/etc.
- **Test Description**: Clear description of what should be tested
- **Rationale**: Why this test is important (risk, impact, coverage gap)
- **Example Scenario**: Concrete example of what to verify
- **Priority**: High/Medium/Low based on risk and impact

## Permissions

The action requires the following GitHub token permissions:

```yaml
permissions:
  contents: read # To checkout the repository and read files
  pull-requests: write # To post test coverage analysis comments
```

## Example Output

The action will post a GitHub review with:

1. **Summary**: Overview of test coverage gaps and priorities
2. **Inline Comments**: Specific test suggestions anchored to relevant code lines
3. **Organized by Test Type**: Suggestions grouped by unit, integration, security, etc.
4. **Prioritized**: High-priority tests highlighted for critical paths

## Best Practices

1. **Run on PR Creation**: Trigger the action when PRs are opened to get early test feedback
2. **Custom Guidelines**: Provide project-specific testing requirements for better suggestions
3. **Review Suggestions**: Treat AI suggestions as recommendations, not requirements
4. **Iterate**: Use feedback reactions (👍/👎) to help improve future suggestions
5. **Combine with Coverage Tools**: Use alongside code coverage tools for comprehensive analysis

## Limitations

- Cannot execute tests or measure actual coverage percentages
- Cannot access CI/CD test reports or coverage data
- Suggestions are based on code analysis, not runtime behavior
- May not be aware of all project-specific testing requirements
- Cannot automatically implement suggested tests

## Contributing

Contributions are welcome! Please feel free to submit issues or pull requests.

## License

MIT License - see [LICENSE](LICENSE) file for details.

## Support

For issues or questions:
- GitHub Issues: [https://github.com/augmentcode/review-pr-test-coverage/issues](https://github.com/augmentcode/review-pr-test-coverage/issues)
- Documentation: [https://docs.augmentcode.com](https://docs.augmentcode.com)
