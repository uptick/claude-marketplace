# Uptick CI Workflow Reference

Complete configuration reference for `uptick/actions/.github/workflows/ci.yaml`.

## Inputs

### Checkout Settings

| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `checkout-ref` | string | - | Branch, tag, or SHA to checkout |
| `checkout-ssh-key` | string | - | SSH key for repository authentication |

### Python & uv

| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `python` | boolean | false | Enable Python installation |
| `python-version` | string | "3.10" | Python version to install |
| `uv` | boolean | false | Install uv package manager |
| `uv-version` | string | "0.5.0" | uv version |
| `uv-sync` | boolean | true | Run dependency sync |
| `uv-sync-command` | string | "uv sync" | Custom sync command |
| `uv-directory` | string | "." | Working directory for uv |
| `uv-cache` | boolean | true | Cache uv dependencies |

### Node.js & pnpm

| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `node` | boolean | false | Install Node.js |
| `node-version` | string | "16" | Node.js version |
| `pnpm` | boolean | false | Install pnpm |
| `pnpm-install` | boolean | true | Run pnpm install |
| `pnpm-build` | boolean | true | Run pnpm build |

### Mise

| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `mise` | boolean | false | Install mise |
| `mise-install` | boolean | false | Run mise install |
| `mise-cache` | boolean | true | Cache mise tools |

### Docker

| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `docker-enabled` | boolean | false | Build and push images |
| `docker-buildx-enabled` | boolean | false | Enable Buildx |
| `docker-repository` | string | - | ECR repository URL (required if docker enabled) |
| `docker-context` | string | "." | Dockerfile location |
| `docker-prefix` | string | - | Image tag prefix |
| `docker-tag-latest` | boolean | false | Tag as latest |
| `docker-tag` | string | - | Manual tag |
| `docker-image-platforms` | string | "linux/amd64,linux/arm64" | Target platforms |
| `docker-build-args` | string | - | Build arguments (comma-separated) |
| `docker-push` | boolean | true | Push images to registry |
| `ecr-type` | string | "private" | Repository type: "private" or "public" |

### AWS

| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `aws` | boolean | false | Enable AWS credentials setup |
| `aws-region` | string | "ap-southeast-2" | AWS region |
| `aws-iam-role-arn` | string | org default | IAM role ARN for OIDC assumption |

### Deployment

| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `bump-app` | string | - | Application identifier for Tickforge deployment |

### Execution

| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `command` | string | "make ci" | Primary CI command to run |
| `debug` | boolean | false | Output GitHub context for debugging |

## Secrets

| Secret | Description |
|--------|-------------|
| `checkout-ssh-key` | SSH key for private repository access |
| `SECRET_ENV` | Generic secret environment variable |

## Common Configurations

### Python API with uv

```yaml
with:
  python: true
  python-version: "3.12"
  uv: true
  mise: true
  command: "mise run ci"
```

### Fullstack (Python + Node)

```yaml
with:
  python: true
  uv: true
  node: true
  node-version: "20"
  pnpm: true
  mise: true
  command: "mise run ci"
```

### Docker + Tickforge Deploy

```yaml
with:
  python: true
  uv: true
  aws: true
  docker-enabled: true
  docker-repository: 610829907584.dkr.ecr.ap-southeast-2.amazonaws.com/my-app
  bump-app: my-app
  command: "mise run ci"
```
