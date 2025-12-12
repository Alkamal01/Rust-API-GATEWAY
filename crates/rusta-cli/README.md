# Rusta CLI

[![Crates.io](https://img.shields.io/crates/v/Rusta-cli.svg)](https://crates.io/crates/Rusta-cli)
[![Documentation](https://docs.rs/Rusta-cli/badge.svg)](https://docs.rs/Rusta-cli)
[![License](https://img.shields.io/crates/l/Rusta-cli.svg)](https://github.com/Alkamal01/rusta/blob/main/License)

Command-line interface for managing and interacting with Rusta API Gateway.

## Overview

`Rusta-cli` provides a powerful command-line tool for managing Rusta Gateway instances, testing configurations, and monitoring gateway health.

## Installation

### From crates.io

```bash
cargo install Rusta-cli
```

The binary is named `Rusta`:

```bash
Rusta --help
```

### From Source

```bash
git clone https://github.com/Alkamal01/rusta.git
cd rusta
cargo build --release --bin Rusta
```

The binary will be located at `target/release/Rusta`.

## Features

- 🔧 **Configuration Management**: Validate and test gateway configurations
- 🏥 **Health Checks**: Monitor gateway and backend health
- 📊 **Metrics**: Query gateway metrics and statistics
- 🔍 **Route Testing**: Test route configurations before deployment
- 📝 **Config Generation**: Generate sample configurations
- 🚀 **Quick Setup**: Bootstrap new gateway configurations

## Usage

### Basic Commands

```bash
# Show help
Rusta --help

# Show version
Rusta --version

# Validate configuration
Rusta validate config.json

# Test a route
Rusta test-route --config config.json --path /api/users

# Check gateway health
Rusta health --url http://localhost:5900

# Generate sample config
Rusta init --output my-config.json
```

## Command Reference

### `Rusta validate`

Validate a configuration file:

```bash
Rusta validate [OPTIONS] <CONFIG_FILE>

Options:
  -v, --verbose    Show detailed validation output
  --strict         Enable strict validation mode
  -h, --help       Print help information
```

**Examples:**

```bash
# Basic validation
Rusta validate config.json

# Verbose validation with details
Rusta validate -v config.json

# Strict mode (error on warnings)
Rusta validate --strict config.json
```

### `Rusta test-route`

Test a specific route configuration:

```bash
Rusta test-route [OPTIONS] --config <CONFIG> --path <PATH>

Options:
  -c, --config <CONFIG>      Path to configuration file
  -p, --path <PATH>          Route path to test
  -m, --method <METHOD>      HTTP method (default: GET)
  --backend <BACKEND>        Specific backend to test
  -h, --help                 Print help information
```

**Examples:**

```bash
# Test a GET route
Rusta test-route --config config.json --path /api/users

# Test a POST route
Rusta test-route --config config.json --path /api/users --method POST

# Test specific backend
Rusta test-route --config config.json --path /api/users --backend http://localhost:8080
```

### `Rusta health`

Check gateway health status:

```bash
Rusta health [OPTIONS]

Options:
  -u, --url <URL>          Gateway URL (default: http://localhost:5900)
  --check-backends         Also check backend health
  --timeout <SECONDS>      Request timeout in seconds (default: 5)
  -h, --help              Print help information
```

**Examples:**

```bash
# Check gateway health
Rusta health

# Check with custom URL
Rusta health --url http://gateway.example.com:5900

# Check gateway and all backends
Rusta health --check-backends
```

### `Rusta init`

Initialize a new gateway configuration:

```bash
Rusta init [OPTIONS]

Options:
  -o, --output <FILE>         Output file path (default: config.json)
  -t, --template <TEMPLATE>   Configuration template to use
  --protocol <PROTOCOL>       Protocol type (http, websocket, ftp, dns)
  -h, --help                  Print help information
```

**Templates:**

- `basic` - Simple HTTP proxy configuration
- `advanced` - Full-featured configuration with all options
- `multi-protocol` - Example with multiple protocol types
- `kubernetes` - Configuration for Kubernetes deployment

**Examples:**

```bash
# Generate basic configuration
Rusta init

# Use advanced template
Rusta init --template advanced --output gateway-config.json

# Generate WebSocket configuration
Rusta init --protocol websocket --output ws-config.json
```

### `Rusta metrics`

Query gateway metrics:

```bash
Rusta metrics [OPTIONS]

Options:
  -u, --url <URL>          Gateway URL (default: http://localhost:5900)
  --format <FORMAT>        Output format (json, text, prometheus)
  --filter <FILTER>        Metric name filter
  -h, --help              Print help information
```

**Examples:**

```bash
# Get all metrics
Rusta metrics

# Get metrics in JSON format
Rusta metrics --format json

# Filter specific metrics
Rusta metrics --filter request_count
```

### `Rusta config`

Configuration management commands:

```bash
Rusta config <SUBCOMMAND>

Subcommands:
  show       Show current configuration
  diff       Compare two configurations
  merge      Merge multiple configurations
  format     Format and prettify configuration
  convert    Convert between configuration formats

Options:
  -h, --help    Print help information
```

**Examples:**

```bash
# Show configuration
Rusta config show config.json

# Compare configurations
Rusta config diff config-old.json config-new.json

# Merge configurations
Rusta config merge base.json override.json --output final.json

# Format configuration
Rusta config format config.json --indent 2
```

## Configuration File Example

```json
{
  "version": 1,
  "routers": [
    {
      "protocol": "http",
      "external_path": "/api/users",
      "internal_path": "/users",
      "methods": ["GET", "POST"],
      "backends": [
        {
          "host": "http://localhost",
          "port": 8080,
          "weight": 1
        }
      ],
      "load_balancing_strategy": "round_robin",
      "auth_required": false
    }
  ]
}
```

## Use Cases

### 1. Pre-deployment Validation

```bash
# Validate before deploying
Rusta validate production-config.json --strict

# Test critical routes
Rusta test-route --config production-config.json --path /api/critical

# Check backend connectivity
Rusta health --url http://staging-gateway:5900 --check-backends
```

### 2. Configuration Development

```bash
# Generate starting point
Rusta init --template advanced --output my-config.json

# Edit configuration...

# Validate changes
Rusta validate my-config.json -v

# Test locally
Rusta test-route --config my-config.json --path /api/test
```

### 3. Production Monitoring

```bash
# Monitor gateway health
Rusta health --url http://production-gateway:5900

# Check metrics
Rusta metrics --url http://production-gateway:5900 --format json

# Verify configuration
Rusta config show /etc/Rusta/config.json
```

### 4. CI/CD Integration

```bash
#!/bin/bash
# validate.sh

# Validate configuration
if ! Rusta validate config/gateway.json --strict; then
  echo "Configuration validation failed!"
  exit 1
fi

# Test routes
for route in "/api/users" "/api/products" "/api/orders"; do
  if ! Rusta test-route --config config/gateway.json --path "$route"; then
    echo "Route test failed: $route"
    exit 1
  fi
done

echo "All validations passed!"
```

## Environment Variables

- `Rusta_GATEWAY_URL`: Default gateway URL for commands
- `Rusta_CONFIG_PATH`: Default configuration file path
- `Rusta_TIMEOUT`: Default request timeout in seconds

**Example:**

```bash
export Rusta_GATEWAY_URL=http://localhost:5900
export Rusta_CONFIG_PATH=/etc/Rusta/config.json

# Now you can omit these options
Rusta health
Rusta validate
```

## Exit Codes

- `0` - Success
- `1` - Validation/test failure
- `2` - Connection error
- `3` - Configuration error
- `4` - Authentication error

## Integration Examples

### Shell Script

```bash
#!/bin/bash

# Check if gateway is healthy
if ! Rusta health --url http://localhost:5900; then
  echo "Gateway is unhealthy, restarting..."
  systemctl restart Rusta-gateway
  sleep 5
fi

# Validate config before reload
if Rusta validate /etc/Rusta/config.json; then
  echo "Reloading gateway configuration..."
  systemctl reload Rusta-gateway
else
  echo "Invalid configuration, aborting reload"
  exit 1
fi
```

### GitHub Actions

```yaml
name: Validate Configuration

on: [push, pull_request]

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Install Rusta CLI
        run: cargo install Rusta-cli
      
      - name: Validate Configuration
        run: Rusta validate config.json --strict
      
      - name: Test Routes
        run: |
          Rusta test-route --config config.json --path /api/users
          Rusta test-route --config config.json --path /api/products
```

### Kubernetes Deployment

```bash
# Validate config before creating ConfigMap
Rusta validate k8s-config.json --strict

# Create ConfigMap
kubectl create configmap Rusta-config --from-file=config.json=k8s-config.json

# Deploy gateway
kubectl apply -f Rusta-deployment.yaml

# Wait for gateway to be ready
kubectl wait --for=condition=ready pod -l app=Rusta-gateway

# Verify health
Rusta health --url http://$(kubectl get svc Rusta-gateway -o jsonpath='{.status.loadBalancer.ingress[0].ip}'):5900
```

## Troubleshooting

### Connection Issues

```bash
# Test with increased timeout
Rusta health --timeout 30

# Check network connectivity
curl http://localhost:5900/health
```

### Validation Errors

```bash
# Use verbose mode for details
Rusta validate config.json -v

# Check specific format issues
Rusta config format config.json
```

## Dependencies

This crate works with the [`Rusta-gateway`](https://crates.io/crates/Rusta-gateway) binary.

## Documentation

- [Main Documentation](../../Readme.md)
- [Configuration Guide](../../docs/)
- [API Documentation](https://docs.rs/Rusta-cli)
- [Examples](../../examples/)

## Contributing

Contributions are welcome! Please see the [main repository](https://github.com/Alkamal01/rusta) for contribution guidelines.

## License

Licensed under MIT License. See [LICENSE](../../License) for details.

## Links

- [GitHub Repository](https://github.com/Alkamal01/rusta)
- [Issue Tracker](https://github.com/Alkamal01/rusta/issues)
- [Crates.io](https://crates.io/crates/Rusta-cli)
- [Documentation](https://docs.rs/Rusta-cli)
