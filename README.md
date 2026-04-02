# Terraform Provider for CongaLine

Terraform provider for declarative lifecycle management of [CongaLine](https://github.com/cruxdigital-llc/CongaLine) AI agent environments.

## Overview

This provider wraps the CongaLine Go `Provider` interface, enabling Terraform to manage agent environments across all three deployment targets:

- **local** — Docker containers on your machine
- **remote** — Docker containers on any SSH-accessible host
- **aws** — EC2 host with Docker containers in a zero-ingress VPC

## Resources

| Resource | Description |
|---|---|
| `conga_environment` | Shared infrastructure for agents (Docker, config dirs, image) |
| `conga_agent` | AI assistant container (user or team type) |
| `conga_secret` | Per-agent secret injected as an environment variable |
| `conga_channel` | Messaging platform (e.g. Slack) with shared credentials |
| `conga_channel_binding` | Maps a Slack user/channel to an agent |
| `conga_policy` | Egress rules, routing defaults, security posture |

## Data Sources

| Data Source | Description |
|---|---|
| `conga_agent_status` | Container state, uptime, and resource usage |
| `conga_channels` | Status of all configured messaging channels |
| `conga_policy` | Current policy configuration |

## Quick Start

```hcl
terraform {
  required_providers {
    conga = {
      source = "cruxdigital-llc/conga"
    }
  }
}

provider "conga" {
  provider_type = "local"
}

resource "conga_environment" "this" {
  image = "ghcr.io/openclaw/openclaw:2026.3.11"
}

resource "conga_agent" "aaron" {
  name       = "aaron"
  type       = "user"
  depends_on = [conga_environment.this]
}

resource "conga_secret" "api_key" {
  agent = "aaron"
  name  = "anthropic-api-key"
  value = var.anthropic_api_key
}
```

## Documentation

Full documentation for all resources, data sources, and import support is available on the [Terraform Registry](https://registry.terraform.io/providers/cruxdigital-llc/conga/latest/docs).

## Development

```bash
# Build
go build ./...

# Run tests
go test ./... -v

# Run acceptance tests (requires Docker)
TF_ACC=1 go test ./... -v -timeout 300s
```

This provider depends on shared packages from [CongaLine](https://github.com/cruxdigital-llc/CongaLine) (`cli/pkg/`).

## License

See [CongaLine](https://github.com/cruxdigital-llc/CongaLine) for license details.
