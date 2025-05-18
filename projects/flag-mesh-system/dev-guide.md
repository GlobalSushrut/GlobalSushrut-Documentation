# flag-mesh-system - dev-guide

*This documentation is from the private repository flag-mesh-system.*

---

# Flag Mesh System - Developer Guide

This developer guide provides comprehensive information for developers working with the Flag Mesh System.

## Prerequisites

- Go 1.13.8 or compatible version
- Redis server (v5+)
- Docker for containerization (optional)
- Git for version control

## Project Structure

```
flag-mesh-system/
├── config/                 # Feature flag configurations
│   └── flag.yaml           # Main flag configuration file
├── flag-engine/            # Flag Engine component
│   ├── api/                # API server implementation
│   ├── container/          # Container management
│   ├── minimal/            # Simplified implementation
│   └── go.mod              # Go module file
├── router/                 # Router component
│   ├── routing/            # Request routing implementation
│   ├── merkle/             # Merkle tree integration
│   └── go.mod              # Go module file
├── merkle-validator/       # Merkle Validator component
│   └── go.mod              # Go module file
├── yaml-locker/            # YAML Locker component
│   ├── locker/             # Locker implementation
│   └── go.mod              # Go module file
├── locks/                  # Configuration lock files
├── deployment/             # Deployment configurations
│   └── kubernetes/         # Kubernetes manifests
└── scripts/                # Utility scripts
    └── health_check.sh     # Health checking script
```

## Setting Up Development Environment

1. **Clone the Repository**

```bash
git clone https://github.com/your-org/flag-mesh-system.git
cd flag-mesh-system
```

2. **Start Redis Server**

```bash
# Using Docker
docker run -d -p 6379:6379 --name redis redis:alpine

# Or use a local installation
redis-server
```

3. **Run Components (Development Mode)**

Run each component in a separate terminal:

```bash
# Flag Engine
cd flag-engine
go run main.go --redis=localhost:6379 --config=../config --api=:8071

# Router
cd router
go run main.go --redis=localhost:6379 --addr=:9071

# Merkle Validator
cd merkle-validator
go run main.go --redis=localhost:6379

# YAML Locker (to create a lock file)
cd yaml-locker
go run main.go --config=../config --locks=../locks --redis=localhost:6379
```

## Development Workflow

### 1. Modifying Feature Flags

1. Edit the YAML configuration in `config/flag.yaml`
2. Restart the Flag Engine to load the new configuration
3. Create a new lock file with the YAML Locker

### 2. Adding a New Component

1. Create a new directory for your component
2. Initialize a Go module with `go mod init component-name`
3. Implement the component functionality
4. Add Redis integration for communication with other components
5. Update documentation to reflect the new component

### 3. Implementing a Feature Container

1. Create a new Docker container for your feature
2. Expose an HTTP endpoint for the feature
3. Add the feature configuration to `config/flag.yaml`
4. Update the endpoint in the configuration to point to your container

## API Reference

### Flag Engine API

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/api/v1/flags` | GET | List all feature flags |
| `/api/v1/flags/{flag_name}` | GET | Get specific flag details |
| `/api/v1/health` | GET | Health check endpoint |

#### Example Response

```json
[
  {
    "name": "feature_x",
    "config": {
      "rollout": "10%",
      "container": "feature_x_container",
      "mesh_proof": true,
      "endpoint": "http://localhost:8081",
      "db": "feature_x_db"
    },
    "state": {
      "is_active": true,
      "proof_verified": false,
      "health_status": "running"
    }
  }
]
```

### Router API

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/feature/{feature_name}/{path}` | Any | Route to feature container |
| `/health` | GET | Health check endpoint |

## Testing

### Unit Testing

Run unit tests for each component:

```bash
cd component-name
go test ./...
```

### Integration Testing

1. Start all components as described in the "Setting Up Development Environment" section
2. Test the API endpoints using curl or a similar tool
3. Verify that the components are communicating correctly

### Load Testing

Use tools like `wrk` or `Apache Benchmark` to test the performance of the system:

```bash
# Example with wrk
wrk -t12 -c400 -d30s http://localhost:9071/feature/feature_x/test
```

## Common Issues and Solutions

### Redis Connection Issues

If components cannot connect to Redis:

1. Verify that Redis is running: `redis-cli ping`
2. Check the Redis connection string: `localhost:6379`
3. Ensure Redis is accessible from your network

### Feature Flag Not Found

If a feature flag is not found by the Router:

1. Verify that the flag is defined in `config/flag.yaml`
2. Restart the Flag Engine to reload the configuration
3. Check the Redis data with `redis-cli get feature-configs`

### Merkle Validation Failures

If Merkle validation fails:

1. Create a new lock file with the current configuration
2. Verify that the Merkle Validator is running
3. Check the Redis Merkle root with `redis-cli get merkle:root`

## Debugging

### Logging

All components use Go's built-in `log` package for logging. Log messages are written to stdout.

### Redis Inspection

Use Redis CLI to inspect the data:

```bash
redis-cli
> get feature-configs
> get merkle:root
> monitor  # To view all Redis commands
```

### API Inspection

Use curl with verbose output to inspect API responses:

```bash
curl -v http://localhost:8071/api/v1/health
```

## Best Practices

1. **Code Style**: Follow Go's standard code style and use `gofmt`
2. **Error Handling**: Use proper error handling and return descriptive error messages
3. **Configuration**: Keep all feature configurations in YAML files
4. **Testing**: Write unit tests for all new functionality
5. **Documentation**: Update documentation when making changes
6. **Versioning**: Use semantic versioning for releases
7. **Logging**: Log important events and errors for troubleshooting

## Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/my-feature`
3. Make your changes
4. Run tests: `go test ./...`
5. Commit your changes: `git commit -m 'Add my feature'`
6. Push to the branch: `git push origin feature/my-feature`
7. Create a pull request

## Resources

- [Go Documentation](https://golang.org/doc/)
- [Redis Documentation](https://redis.io/documentation)
- [Docker Documentation](https://docs.docker.com/)
- [Kubernetes Documentation](https://kubernetes.io/docs/)
