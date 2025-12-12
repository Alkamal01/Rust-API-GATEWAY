# Rusta 🚀

A high-performance API Gateway built with Rust, featuring multi-protocol support, advanced security, and a modern web-based admin interface.

[![Rust](https://img.shields.io/badge/rust-2021-orange.svg)](https://www.rust-lang.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## What It Does

Rusta is a production-ready API gateway that intelligently routes traffic to your backend services with:

- **Multi-Protocol Support**: HTTP/HTTPS, WebSocket, FTP, and DNS
- **Smart Load Balancing**: 5 strategies including round-robin, least connections, weighted, IP hash, and random
- **Security First**: JWT authentication, rate limiting, CORS policies, and security headers
- **High Availability**: Circuit breaker pattern, automatic retries with exponential backoff
- **Real-time Monitoring**: Web-based admin UI with live metrics and performance insights
- **Request Transformation**: Header manipulation, path rewriting, query parameter transformation

## Tech Stack

- **Language**: Rust 2021 Edition
- **Web Framework**: Actix Web (async/await)
- **UI Framework**: Leptos (WebAssembly)
- **Protocols**: HTTP/HTTPS, WebSocket, FTP, DNS
- **Security**: JWT, rate limiting, circuit breakers
- **Observability**: Prometheus metrics, structured logging

## Quick Start

### Using Docker (Recommended)

```bash
# Pull and run the latest image
docker pull ghcr.io/danielsarmiento04/rusta:latest

docker run -d \
  -p 5900:5900 \
  -v $(pwd)/config.json:/app/config.json:ro \
  ghcr.io/danielsarmiento04/rusta:latest
```

### Build from Source

```bash
# Clone the repository
git clone https://github.com/DanielSarmiento04/Rust-API-GATEWAY.git
cd Rust-API-GATEWAY

# Run the gateway
cargo run --bin rusta-gateway

# Gateway starts on http://localhost:5900
```

### Start the Web Admin UI (Optional)

```bash
# Install cargo-leptos
cargo install cargo-leptos

# Start the UI
cd crates/rusta-ui
cargo leptos serve

# Admin UI available at http://localhost:3000
```

## Configuration Example

Create a `config.json` file:

```json
{
  "version": 1,
  "jwt_secret": "your-secret-key",
  "rate_limit": {
    "algorithm": "token_bucket",
    "requests_per_second": 100,
    "burst_size": 50
  },
  "routers": [
    {
      "external_path": "/api/users/{id}",
      "internal_path": "/users/{id}",
      "methods": ["GET", "POST"],
      "auth_required": true,
      "backends": [
        {"host": "http://api1.example.com", "port": 8080, "weight": 2},
        {"host": "http://api2.example.com", "port": 8080, "weight": 1}
      ],
      "load_balancing_strategy": "weighted",
      "retry_config": {
        "max_retries": 3,
        "initial_backoff_ms": 100,
        "max_backoff_ms": 5000
      }
    }
  ]
}
```

## Key Features

### Load Balancing Strategies
- **Round Robin**: Even distribution across backends
- **Least Connections**: Routes to least busy backend
- **Weighted**: Distribute based on backend capacity
- **IP Hash**: Sticky sessions based on client IP
- **Random**: Simple random selection

### Security Features
- **JWT Authentication**: Validate bearer tokens with configurable claims
- **Rate Limiting**: Token bucket, sliding window, or fixed window algorithms
- **Circuit Breaker**: Automatic failure detection and recovery
- **CORS Policies**: Configurable cross-origin resource sharing
- **Security Headers**: Automatic security header injection

### Request/Response Transformation
- **Header Manipulation**: Add, remove, or replace headers
- **Path Rewriting**: Transform request paths with regex
- **Query Parameters**: Modify query strings on the fly
- **Status Code Mapping**: Normalize backend responses

### Observability
- **Prometheus Metrics**: Built-in metrics endpoint
- **Structured Logging**: JSON-formatted logs
- **Health Checks**: Endpoint health monitoring
- **Web Dashboard**: Real-time metrics visualization

## Testing

```bash
# Run all tests
cargo test --workspace

# Test a specific feature
cargo test rate_limit
cargo test circuit_breaker
cargo test jwt
```

## Performance

Benchmarks on M1 MacBook Pro:
- **Static routes**: ~450k ops/sec
- **Dynamic routes**: ~200k ops/sec
- **JWT validation**: ~50k tokens/sec
- **Request latency**: P99 < 2ms
- **Memory usage**: ~25MB under load

## Project Structure

```
rusta/
├── crates/
│   ├── rusta-rs/        # Core library
│   ├── rusta-gateway/   # Gateway binary
│   ├── rusta-ui/        # Web admin interface
│   ├── rusta-cli/       # Command-line tool
│   └── rusta-client/    # Client library
├── docs/                # Documentation
├── examples/            # Usage examples
└── config.json          # Configuration file
```

## Environment Variables

```bash
RUSTA_HOST=0.0.0.0           # Server bind address
RUSTA_PORT=5900              # Server port
RUSTA_CONFIG_PATH=./config.json  # Config file path
RUST_LOG=info                # Log level
```

## Why Rusta?

- **Performance**: Built with Rust for maximum speed and minimal resource usage
- **Reliability**: Circuit breakers and retries ensure high availability
- **Flexibility**: Support for multiple protocols and load balancing strategies
- **Modern**: WebAssembly-based admin UI with real-time updates
- **Production-Ready**: Comprehensive testing and battle-tested patterns

## License

MIT License - see [LICENSE](LICENSE) file.

## Author

**Daniel Sarmiento** - [GitHub](https://github.com/DanielSarmiento04)

---

Built with ❤️ using Rust, Actix Web, and Leptos
