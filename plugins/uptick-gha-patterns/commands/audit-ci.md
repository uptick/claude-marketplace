---
name: audit-ci
description: Audit GitHub Actions workflows for Uptick CI best practices compliance
argument-hint: "[path to workflows]"
allowed-tools:
  - Read
  - Glob
  - Grep
  - Bash
  - Edit
  - Write
  - AskUserQuestion
---

# Audit CI Workflows

Audit the repository's GitHub Actions workflows for compliance with Uptick CI best practices.

## Workflow Path

If an argument is provided, use it as the path to audit. Otherwise, default to `.github/workflows/`.

## Audit Checks

Perform the following checks on all `.yaml` and `.yml` files in the workflows directory:

### 1. Unpinned Actions

Search for `uses:` statements that are NOT pinned to a SHA. Look for patterns like:
- `uses: actions/checkout@v4` (unpinned - missing SHA)
- `uses: some-action@main` (unpinned - using branch)

**Compliant patterns:**
- `uses: actions/checkout@abc123... # ratchet:actions/checkout@v4` (pinned with ratchet)
- `uses: uptick/actions/.github/workflows/ci.yaml@main  # ratchet:exclude` (excluded correctly)

### 2. Missing Ratchet Exclusions

Check that all `uptick/actions` references have `# ratchet:exclude` comment:
- `uses: uptick/actions/.github/workflows/ci.yaml@main` should have `# ratchet:exclude`

### 3. External Action Dependencies

Identify third-party actions that are NOT from:
- `actions/*` (GitHub official)
- `uptick/*` (Uptick organization)
- `./.github/actions/*` (local actions)

Flag these as potential security risks that should be replaced with scripts.

### 4. Authentication Patterns

Check for:
- Long-lived AWS credentials (should use OIDC instead)
- `ANTHROPIC_API_KEY` usage (should prefer `CLAUDE_CODE_OAUTH_TOKEN`)

### 5. Missing Uptick Patterns

Check if the repository could benefit from:
- Claude PR review (`claude_review.yaml`) - if not present on PRs
- Claude mention (`claude_mention.yaml`) - if not present

## Output Format

Present findings in a structured report:

```
## CI Audit Results

### Critical Issues
- [ ] Issue description with file:line reference

### Warnings
- [ ] Warning description

### Recommendations
- [ ] Suggestion for improvement

### Compliant
- [x] What's already correct
```

## Offering Fixes

After presenting the audit results, ask the user if they want to apply fixes:

1. **Run ratchet pin** - Pin unpinned actions
2. **Add ratchet:exclude** - Add exclusion comments to uptick/actions references
3. **Switch to OAuth** - Replace ANTHROPIC_API_KEY with CLAUDE_CODE_OAUTH_TOKEN
4. **Add Claude workflows** - Add missing claude_review.yaml or claude_mention.yaml

Use `mise use ratchet && ratchet pin .github/workflows/*.yaml` for pinning.

For adding new workflows, create them based on the examples in the uptick-ci-patterns skill.
