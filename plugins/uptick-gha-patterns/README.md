# uptick-gha-patterns

A Claude Code plugin for GitHub Actions best practices following Uptick's security-first patterns.

## Features

- **Skill: Uptick CI Patterns** - Domain knowledge for GitHub Actions following Uptick's security-first approach
- **Command: /audit-ci** - Audit workflows for compliance and offer fixes

## Best Practices Covered

### Uptick Reusable Workflows

From [uptick/actions](https://github.com/uptick/actions):

- `ci.yaml` - Comprehensive CI/CD pipeline with Python, uv, mise, AWS OIDC, Docker/ECR
- `claude_review.yaml` - Claude Code PR review pipeline
- `claude_mention.yaml` - Claude Code @mention pipeline

### Security Principles

- Avoid external action dependencies (security risks)
- Implement via Python/bash scripts with built-in libraries only
- Pin all third-party actions using Ratchet
- Exclude `uptick/actions` references from pinning with `# ratchet:exclude`

### Ratchet Pinning

Pin actions to SHA for immutability:

```yaml
uses: 'actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683' # ratchet:actions/checkout@v4
```

Install ratchet:
```bash
mise use -g ratchet
```

## Installation

```bash
# Test locally
claude --plugin-dir /path/to/uptick-gha-patterns
```

## Usage

### Skill (automatic)

Ask questions that trigger the skill:

- "How should I set up CI for this repo?"
- "What's the best way to pin GitHub Actions?"
- "Help me configure the Uptick CI workflow"
- "Set up Claude code review for this repo"
- "Deploy with tickforge"

### Command

```
/uptick-gha-patterns:audit-ci
```

Audits `.github/workflows/` for:
- Unpinned actions (should use ratchet)
- Missing `# ratchet:exclude` on uptick/actions references
- External dependencies that could be replaced with scripts
- Missing Claude review/mention workflows
- Authentication patterns (OIDC vs long-lived credentials)

## Plugin Structure

```
uptick-gha-patterns/
├── .claude-plugin/
│   └── plugin.json
├── commands/
│   └── audit-ci.md
├── skills/
│   └── uptick-ci-patterns/
│       ├── SKILL.md
│       ├── references/
│       │   ├── uptick-workflows.md
│       │   └── ratchet-guide.md
│       └── examples/
│           ├── ci-python-uv.yaml
│           └── ci-docker-tickforge.yaml
└── README.md
```

## Requirements

- [Ratchet](https://github.com/sethvargo/ratchet) for action pinning (`mise use -g ratchet`)
- [mise](https://mise.jdx.dev/) for task running
