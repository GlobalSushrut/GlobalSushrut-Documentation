# entroos - README

*This documentation is from the private repository entroos.*

---

# Mock-K8 Orchestrator

The Mock-K8 Orchestrator is a core component of the EntroOS decentralized execution platform. It provides a mathematically governed container orchestration system with cryptographic proofs for all state transitions.

## Features

- **Mathematical Governance**: All operations are governed by precise mathematical formulations.
- **REST API**: Full-featured API for managing pods, services, and nodes.
- **Integration with MockDocker**: Seamless container lifecycle management through the MockDocker runtime.
- **Cryptographic Verification**: Secure state transitions with cryptographic proofs via the Node Kernel Operator (NKO).
- **Distributed Consensus**: Raft-based consensus for distributed state management.
- **Scheduling Algorithms**: Mathematically optimal pod placement with verifiable proofs.

## Building from Source

### Prerequisites

- Rust 1.70 or newer
- Dependency crates:
  - `nko` (Node Kernel Operator)
  - `mockdocker` (Container Runtime)
  - `morph_db` (Storage Backend)

### Build Instructions

```
cd /path/to/entroos/src/mock_k8
cargo build --release
```

The binary will be available at `target/release/mock_k8`.

## Docker Deployment

A Dockerfile is provided to simplify deployment:

```
cd /path/to/entroos/src/mock_k8
docker build -t mock-k8:latest .
docker run -p 8080:8080 -v /path/to/data:/app/data mock-k8:latest
```

## Configuration

Mock-K8 can be configured through command-line arguments or environment variables:

### Environment Variables

- `MOCK_K8_DATA_DIR`: Data directory for state storage
- `MOCK_K8_NODE_NAME`: Node name for this instance
- `MOCK_K8_API_ADDR`: API server address (host:port)
- `MOCK_K8_VERIFICATION_ENABLED`: Enable cryptographic verification (true/false)

### Command-Line Arguments

```
USAGE:
    mock_k8 [SUBCOMMAND]

SUBCOMMANDS:
    run                    Run the Mock-K8 Orchestrator
    init                   Initialize the data directory
    version                Display version information
    help                   Print help information
```

Example:

```
mock_k8 run --data-dir /path/to/data --api-addr 0.0.0.0:8080 --node-name mock-k8-node
```

## API Reference

The Mock-K8 API follows a REST architecture. All endpoints are available at:

```
http://<api-addr>/api/v1/...
```

### Pod Management

- `GET /api/v1/pods` - List all pods
- `POST /api/v1/pods` - Create a pod
- `GET /api/v1/pods/{id}` - Get pod by ID
- `DELETE /api/v1/pods/{id}` - Delete pod by ID

### Service Management

- `GET /api/v1/services` - List all services
- `POST /api/v1/services` - Create a service
- `GET /api/v1/services/{id}` - Get service by ID
- `DELETE /api/v1/services/{id}` - Delete service by ID

### Node Management

- `GET /api/v1/nodes` - List all nodes
- `GET /api/v1/nodes/{id}` - Get node by ID
- `PUT /api/v1/nodes/{id}/status` - Update node status

### Scheduler Operations

- `GET /api/v1/scheduler/policies` - List scheduling policies
- `POST /api/v1/scheduler/bind` - Manually bind a pod to a node

### Health Checks

- `GET /healthz` - Health check endpoint
- `GET /readyz` - Readiness check endpoint

## Mathematical Formulations

Mock-K8 implements the EntroOS mathematical governance system with the following key equations:

1. **Pod Scheduling**: 
   - Resource allocation follows optimization function *f(P,N)* where *P* is the pod and *N* is the node set
   - Scheduling decisions include cryptographic proof generation *π = G(P,N,d)*

2. **State Transitions**:
   - All state transitions *S_t → S_{t+1}* require valid proofs
   - Proof verification: *V(π, S_t, S_{t+1}) → {0,1}*

3. **Service Networking**:
   - Service endpoint mapping follows distribution function *D(S,E)* where *S* is the service and *E* is the endpoint set

## Integration with EntroOS

Mock-K8 integrates with other EntroOS components:

- **NKO (Node Kernel Operator)**: For cryptographic verification
- **MockDocker**: For container runtime operations
- **Morph DB**: For state storage
- **DAO Postman**: For cluster communication

## License

[License information]
