# flag-mesh-system - infra

*This documentation is from the private repository flag-mesh-system.*

---

# Flag Mesh System - Infrastructure Guide

This document provides infrastructure guidance for deploying and managing the Flag Mesh System in production environments.

## System Requirements

### Minimum Requirements (Development)

- **CPU**: 2 cores
- **Memory**: 4GB RAM
- **Storage**: 10GB
- **Network**: Reliable network connection

### Recommended Requirements (Production)

- **CPU**: 4+ cores
- **Memory**: 8GB+ RAM
- **Storage**: 20GB+ SSD
- **Network**: High-bandwidth, low-latency connection

## Infrastructure Components

### Core Infrastructure

1. **Redis Instance**
   - Purpose: Communication between components
   - Recommended: Redis 5.0+ with persistence enabled
   - High availability setup for production

2. **Container Registry**
   - Purpose: Store feature containers
   - Options: Docker Hub, AWS ECR, Google Container Registry, etc.

3. **Container Orchestration**
   - Purpose: Manage feature containers
   - Recommended: Kubernetes or Docker Swarm

4. **Monitoring System**
   - Purpose: Monitor system health
   - Recommended: Prometheus and Grafana

### Deployment Architecture

```
                   ┌─────────────────┐
                   │   Load Balancer  │
                   └────────┬────────┘
                            │
            ┌───────────────┼───────────────┐
            │               │               │
     ┌──────▼─────┐  ┌──────▼─────┐  ┌──────▼─────┐
     │  Router(s)  │  │  Router(s)  │  │  Router(s)  │
     └──────┬─────┘  └──────┬─────┘  └──────┬─────┘
            │               │               │
            └───────────────┼───────────────┘
                            │
                     ┌──────▼─────┐
                     │    Redis    │
                     └──────┬─────┘
                            │
   ┌────────────────────────┼────────────────────────┐
   │                        │                        │
┌──▼──────────┐    ┌────────▼────────┐    ┌──────────▼──┐
│ Flag Engine │    │ Merkle Validator │    │ YAML Locker │
└─────────────┘    └─────────────────┘    └─────────────┘
        │                   │                    │
        └───────────────────┼────────────────────┘
                            │
                  ┌─────────▼──────────┐
                  │  Feature Containers │
                  └────────────────────┘
```

## Kubernetes Deployment

### Prerequisites

- Kubernetes cluster (v1.16+)
- kubectl configured
- Helm (optional)

### Deployment Files

The deployment configurations are located in `/deployment/kubernetes/`:

- `kustomization.yaml`: Main Kustomize configuration
- Individual component manifests:
  - `flag-engine-deployment.yaml`
  - `router-deployment.yaml`
  - `merkle-validator-deployment.yaml`
  - `yaml-locker-job.yaml`
  - `redis-deployment.yaml`

### Deployment Steps

1. **Deploy Redis**

```bash
kubectl apply -f deployment/kubernetes/redis-deployment.yaml
```

2. **Deploy Flag Engine**

```bash
kubectl apply -f deployment/kubernetes/flag-engine-deployment.yaml
```

3. **Deploy Merkle Validator**

```bash
kubectl apply -f deployment/kubernetes/merkle-validator-deployment.yaml
```

4. **Deploy Router**

```bash
kubectl apply -f deployment/kubernetes/router-deployment.yaml
```

5. **Run YAML Locker as a Job**

```bash
kubectl apply -f deployment/kubernetes/yaml-locker-job.yaml
```

6. **Verify Deployments**

```bash
kubectl get pods
kubectl get services
```

### Using Kustomize

For environments with Kustomize:

```bash
kubectl apply -k deployment/kubernetes/
```

## Scaling Considerations

### Horizontal Scaling

- **Router**: Scale horizontally based on request volume
- **Flag Engine**: Scale as needed for configuration updates
- **Feature Containers**: Scale individually based on demand

### Vertical Scaling

- Increase CPU/memory for components with higher resource needs
- Tune Redis for performance based on workload

### Auto-Scaling Configuration

Kubernetes HPA example for Router:

```yaml
apiVersion: autoscaling/v2beta2
kind: HorizontalPodAutoscaler
metadata:
  name: router-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: router
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
```

## Networking

### Required Ports

| Component | Port | Protocol | Purpose |
|-----------|------|----------|---------|
| Flag Engine | 8071 | HTTP | API Server |
| Router | 9071 | HTTP | Feature routing |
| Redis | 6379 | TCP | Component communication |
| Feature Containers | Various | HTTP | Feature services |

### Network Policies

Recommended Kubernetes network policies to secure communication:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: flag-mesh-network-policy
spec:
  podSelector:
    matchLabels:
      app: flag-mesh
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: flag-mesh
    ports:
    - protocol: TCP
```

## High Availability

### Redis High Availability

- Use Redis Sentinel or Redis Cluster
- Configure appropriate persistence settings
- Regular backups

### Component Redundancy

- Deploy multiple instances of each component
- Use anti-affinity rules to distribute across nodes
- Configure appropriate liveness and readiness probes

### Disaster Recovery

1. Regular Redis snapshots
2. Configuration backups
3. Documented recovery procedures

## Monitoring and Logging

### Prometheus Metrics

Key metrics to monitor:

- Request latency
- Error rates
- Redis operation latency
- Container health
- Feature flag evaluation counts

Example Prometheus scrape config:

```yaml
scrape_configs:
  - job_name: 'flag-mesh'
    kubernetes_sd_configs:
      - role: pod
    relabel_configs:
      - source_labels: [__meta_kubernetes_pod_label_app]
        regex: flag-mesh
        action: keep
```

### Grafana Dashboards

Recommended dashboards:
- System overview
- Per-component health
- Feature flag usage
- Error rates

### Log Aggregation

- Use Fluentd or other log aggregators
- Centralize logs in Elasticsearch or similar
- Structured logging for easier parsing

## Security Considerations

### Redis Security

- Enable authentication
- Use TLS for production
- Network isolation

### API Security

- Implement authentication for Flag Engine API
- Use HTTPS in production
- API rate limiting

### Container Security

- Regular container image scanning
- No privileged containers
- Resource limits enforced

### Network Security

- Use TLS for all communications
- Implement network policies in Kubernetes
- Restrict access to Redis

## Backup and Restore

### Redis Backup

```bash
# Create Redis backup
redis-cli SAVE

# Copy dump.rdb
kubectl cp redis-pod:/data/dump.rdb backup/dump.rdb
```

### Configuration Backup

- Regular backups of flag configuration files
- Version control for YAML files
- Backup of lock files

### Restore Procedure

1. Restore Redis data from backup
2. Restart components to reload configuration
3. Verify system health with health endpoints

## Performance Tuning

### Redis Tuning

- Appropriate maxmemory setting
- Persistence configuration tuning
- Connection pooling

### Router Tuning

- Adjust worker count based on load
- Connection timeouts
- Circuit breaker configuration

### Resource Allocation

- Adjust CPU/memory requests and limits based on observed usage
- Monitor for bottlenecks and adjust accordingly

## Maintenance Procedures

### Updating Components

1. Apply rolling updates to minimize downtime
2. Update one component at a time
3. Verify health after each update

### Adding New Feature Containers

1. Deploy the new feature container
2. Update the flag configuration
3. Validate with the YAML Locker
4. Gradually increase rollout percentage

### Troubleshooting

- Check component logs
- Verify Redis connectivity
- Confirm feature container health
- Validate configuration integrity with YAML Locker

## Compliance and Auditing

- All configuration changes are tracked in lock files
- Merkle validation provides cryptographic proof of integrity
- API access logs for auditing purposes

## Reference Architecture

For larger deployments, consider the following architecture:

- Multiple Redis instances with sharding
- Dedicated Merkle Validator clusters
- Geographic distribution of Router instances
- CDN integration for feature content delivery
