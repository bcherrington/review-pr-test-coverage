# Setup Guide for Review PR Test Coverage

This guide will help you set up and deploy the test coverage review action for your repository.

## Repository Structure

```
review-pr-test-coverage/
├── action.yml                          # GitHub Action definition
├── package.json                        # Package metadata
├── LICENSE                             # MIT License
├── README.md                           # Main documentation
├── RELEASE.md                          # Release notes
├── SETUP_GUIDE.md                      # This file
├── templates/                          # Nunjucks templates for AI prompts
│   ├── prompt.njk                      # Main template orchestrator
│   ├── capabilities/base.njk           # AI capabilities description
│   ├── context/                        # PR context templates
│   │   ├── pr_or_issue.njk            # PR metadata
│   │   └── pr_details.njk             # PR details
│   ├── formatting/base.njk             # Output formatting guidelines
│   ├── guidelines/base.njk             # Test coverage analysis guidelines
│   ├── instructions/base.njk           # AI workflow instructions
│   ├── limitations/base.njk            # AI limitations
│   └── request/base.njk                # Test analysis request
└── example-workflows/                  # Example GitHub Actions workflows
    ├── README.md                       # Workflow documentation
    ├── basic-test-coverage-review.yml  # Basic example
    └── comprehensive-test-review.yml   # Advanced example with custom guidelines

```

## Quick Setup (5 minutes)

### 1. Get Augment Credentials
```bash
# Option 1: Use auggie CLI
auggie tokens print

# Option 2: Read from cache
cat ~/.augment/session.json
```

Copy the JSON output (should contain `accessToken` and `tenantURL`).

### 2. Add GitHub Secret
1. Go to your repository on GitHub
2. Navigate to: **Settings** → **Secrets and variables** → **Actions**
3. Click **New repository secret**
4. Name: `AUGMENT_SESSION_AUTH`
5. Value: Paste the JSON from step 1
6. Click **Add secret**

### 3. Add Workflow File
```bash
# Create workflows directory if it doesn't exist
mkdir -p .github/workflows

# Copy the basic example
cp example-workflows/basic-test-coverage-review.yml .github/workflows/test-coverage.yml

# Commit and push
git add .github/workflows/test-coverage.yml
git commit -m "feat: add test coverage review workflow"
git push
```

### 4. Test It
Create a new pull request and watch the action run. The AI will analyze your changes and suggest test cases!

## Advanced Setup

### Custom Testing Guidelines

Create a workflow with project-specific testing requirements:

```yaml
- name: Test Coverage Analysis
  uses: augmentcode/review-pr-test-coverage@v0
  with:
    augment_session_auth: ${{ secrets.AUGMENT_SESSION_AUTH }}
    github_token: ${{ secrets.GITHUB_TOKEN }}
    pull_number: ${{ github.event.pull_request.number }}
    repo_name: ${{ github.repository }}
    custom_guidelines: |
      ## Our Testing Standards
      
      ### Frameworks
      - Backend: pytest with pytest-cov
      - Frontend: Vitest + React Testing Library
      - E2E: Playwright
      
      ### Requirements
      - All new API endpoints need integration tests
      - UI components need unit tests + visual regression tests
      - Critical flows need E2E tests
      
      ### Priority Areas
      - Payment processing (CRITICAL)
      - Authentication (HIGH)
      - Data validation (HIGH)
```

### Using Custom Models

Specify a different AI model:

```yaml
with:
  model: "sonnet4"  # or other available models from `auggie --list-models`
```

### Using Custom Rules

Add project-specific rules:

```yaml
with:
  rules: '[".augment/testing-rules.md", ".augment/security-rules.md"]'
```

### Using MCP Configs

Include MCP (Model Context Protocol) configurations:

```yaml
with:
  mcp_configs: '[".augment/mcp-config.json"]'
```

## What Gets Analyzed

The action analyzes your PR and suggests tests across:

### Test Levels
- ✅ **Unit Tests**: Functions, methods, classes, edge cases
- ✅ **Functional Tests**: Features, business logic, workflows
- ✅ **Integration Tests**: Component interactions, APIs, databases
- ✅ **System/E2E Tests**: Complete user journeys, cross-component flows

### Additional Areas
- 🔒 **Security**: Auth, authorization, input validation, injection attacks
- ⚡ **Performance**: Load handling, response times, scalability
- 🛡️ **Error Handling**: Exceptions, graceful degradation, recovery
- 🎯 **Edge Cases**: Boundary values, null inputs, concurrent access
- 🔄 **Regression**: Ensuring existing functionality still works
- 📋 **Contract Testing**: API contracts, schemas, interfaces
- ♿ **Accessibility**: WCAG compliance (for UI changes)
- 🌐 **Compatibility**: Browser/platform compatibility

## Understanding the Output

The action posts a GitHub review with:

1. **Summary**: Overview of test coverage gaps (5-7 sentences)
2. **Inline Comments**: Specific test suggestions on relevant code lines
3. **Structured Format**: Each suggestion includes:
   - Test Type (Unit/Integration/Security/etc.)
   - Description (what to test)
   - Rationale (why it's important)
   - Example Scenario (concrete test case)
   - Priority (High/Medium/Low)

### Example Output

```markdown
**Test Type**: Unit Test
**Priority**: High

Consider adding unit tests for the `processPayment` function:
- Test with valid payment data (should succeed)
- Test with invalid card number (should throw ValidationError)
- Test with expired card (should throw PaymentError)
- Test with amount = 0 (should handle gracefully)
- Test with negative amount (should throw error)

**Rationale**: This function handles financial transactions where edge cases 
could lead to incorrect charges or security issues.

**Example**:
```javascript
test('should reject negative payment amounts', () => {
  expect(() => processPayment({ amount: -100 }))
    .toThrow('Payment amount must be positive');
});
```
```

## Troubleshooting

### Action Not Running
- ✅ Verify workflow file is in `.github/workflows/`
- ✅ Check trigger conditions match your PR events
- ✅ Ensure workflow is committed to default branch

### Authentication Errors
- ✅ Verify `AUGMENT_SESSION_AUTH` secret is set correctly
- ✅ Check token hasn't expired (run `auggie tokens print` to refresh)
- ✅ Ensure JSON format is correct (must include `accessToken` and `tenantURL`)

### No Test Suggestions
- ✅ AI may determine existing tests are sufficient
- ✅ Check that code changes are substantial
- ✅ Review custom guidelines - they may be too restrictive

### Irrelevant Suggestions
- ✅ Add custom guidelines with project-specific context
- ✅ Specify testing frameworks and conventions
- ✅ Define priority areas for your project

### Permission Errors
Ensure your workflow has correct permissions:
```yaml
permissions:
  contents: read
  pull-requests: write
```

## Best Practices

1. **Start Simple**: Use basic workflow first, add customization later
2. **Iterate**: Refine custom guidelines based on suggestion quality
3. **Provide Feedback**: Use 👍/👎 reactions on suggestions
4. **Combine Tools**: Use with code coverage tools (Codecov, Coveralls)
5. **Team Alignment**: Ensure guidelines match team standards
6. **Regular Updates**: Review and update guidelines periodically

## Integration with CI/CD

### Combine with Coverage Reports

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run Tests
        run: npm test -- --coverage
      - name: Upload Coverage
        uses: codecov/codecov-action@v3
  
  test-coverage-review:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write
    steps:
      - name: AI Test Coverage Analysis
        uses: augmentcode/review-pr-test-coverage@v0
        with:
          augment_session_auth: ${{ secrets.AUGMENT_SESSION_AUTH }}
          github_token: ${{ secrets.GITHUB_TOKEN }}
          pull_number: ${{ github.event.pull_request.number }}
          repo_name: ${{ github.repository }}
```

### Conditional Execution

Only run on specific paths:

```yaml
on:
  pull_request:
    paths:
      - 'src/**'
      - 'lib/**'
      - '!**/*.md'
```

## Next Steps

1. ✅ Set up the basic workflow
2. ✅ Create a test PR to see it in action
3. ✅ Review the suggestions and provide feedback
4. ✅ Customize guidelines based on your needs
5. ✅ Share with your team and iterate

## Support

- 📖 [Full Documentation](README.md)
- 🐛 [Report Issues](https://github.com/augmentcode/review-pr-test-coverage/issues)
- 💬 [Augment Docs](https://docs.augmentcode.com)

## Contributing

Contributions welcome! Please submit issues or pull requests.

## License

MIT License - see [LICENSE](LICENSE) file.
