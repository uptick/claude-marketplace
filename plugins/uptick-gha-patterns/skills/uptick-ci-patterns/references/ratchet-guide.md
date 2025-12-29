# Ratchet Guide

[Ratchet](https://github.com/sethvargo/ratchet) is a tool for securing CI/CD workflows by pinning actions to immutable SHA references.

## Why Pin Actions?

GitHub action tags are mutable. A malicious actor could update an action to steal credentials or inject malicious code. Pinning to SHA ensures you always run the exact version you audited.

**Unpinned (risky):**
```yaml
uses: actions/checkout@v4
```

**Pinned (secure):**
```yaml
uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # ratchet:actions/checkout@v4
```

## Installation

```bash
# Using mise (recommended)
mise use -g ratchet

# Using Go
go install github.com/sethvargo/ratchet@latest

# Using Homebrew
brew install ratchet
```

## Commands

### pin

Resolve and pin all versions to their current SHA:

```bash
ratchet pin .github/workflows/*.yaml
```

### update

Update pinned versions to the latest matching the constraint:

```bash
ratchet update .github/workflows/*.yaml
```

### unpin

Revert to unpinned values (for editing):

```bash
ratchet unpin .github/workflows/*.yaml
```

### lint

Report unpinned versions without modifying:

```bash
ratchet lint .github/workflows/*.yaml
```

### upgrade

Upgrade constraints to latest major version:

```bash
ratchet upgrade .github/workflows/*.yaml
```

## Exclusions

### Uptick Actions - Always Exclude

**Always use `# ratchet:exclude` for anything from the `uptick/actions` repository.** These reusable workflows are maintained centrally and pinning them defeats the purpose of having shared, updatable workflows.

```yaml
# ALWAYS exclude uptick/actions - they are maintained centrally
uses: uptick/actions/.github/workflows/ci.yaml@main  # ratchet:exclude
uses: uptick/actions/.github/workflows/claude_review.yaml@main  # ratchet:exclude
uses: uptick/actions/.github/workflows/claude_mention.yaml@main  # ratchet:exclude
```

### Other Exclusions

```yaml
# Exclude local actions in the same repository
uses: ./.github/actions/my-action  # ratchet:exclude
```

### When to Exclude

- **`uptick/actions` workflows** - Always exclude, maintained centrally
- **Local actions** - Actions in the same repository (`./.github/actions/`)
- **Internal organization actions** - Actions your team maintains

### When NOT to Exclude

- **Third-party actions** - Always pin these (actions/checkout, aws-actions/*, etc.)
- **Public actions** - Even popular ones can be compromised

## CI Integration

Add ratchet lint to your CI pipeline:

```yaml
- name: Check pinned actions
  run: |
    mise use ratchet
    ratchet lint .github/workflows/*.yaml
```

## Workflow

1. **Initial setup:** Run `ratchet pin .github/workflows/*.yaml`
2. **Before editing:** Run `ratchet unpin` to see readable versions
3. **After editing:** Run `ratchet pin` to re-pin
4. **Regular updates:** Run `ratchet update` to get security patches
5. **CI check:** Run `ratchet lint` in CI to enforce pinning

## Dependabot Integration

Configure Dependabot to update pinned actions:

```yaml
# .github/dependabot.yml
version: 2
updates:
  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "weekly"
```

Dependabot will create PRs to update SHA pins when new versions are released.

## Troubleshooting

### "SHA not found"

The version tag may not exist. Check the action repository for valid tags.

### "Rate limited"

GitHub API rate limits apply. Authenticate with `GITHUB_TOKEN`:

```bash
GITHUB_TOKEN=ghp_xxx ratchet pin .github/workflows/*.yaml
```

### "Unable to resolve"

The action path may be incorrect. Verify the action exists at the specified path.
