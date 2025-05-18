# hospital-management-system-blockchain - blockchain-deployment-guide

*This documentation is from the private repository hospital-management-system-blockchain.*

---

# MediChain Blockchain Deployment Guide

This guide explains how to deploy the MediChain blockchain with mainnet-level security optimized for minimal hardware requirements.

## Deployment Options

MediChain supports two deployment modes:

1. **Dual Mode (2x 2vCPU)**: Deploys a validator node and a processor node on separate 2vCPU instances
2. **Single Mode (1x 4vCPU)**: Deploys all components on a single 4vCPU instance for simplicity

## Prerequisites

- Docker and Docker Compose
- 4GB RAM minimum per node
- Linux-based OS (Ubuntu 20.04+ recommended)
- Port 30303 (TCP/UDP) open for P2P communication
- Ports 8545/8546 for RPC/WebSocket access (behind load balancer)

## Security Features

The MediChain blockchain implements mainnet-level security:

1. **JWT Authentication**: All RPC communications are secured using JWT tokens
2. **Enhanced Password Security**: High-entropy passwords for node accounts
3. **Secure RPC Configuration**: 
   - Unprotected transactions disabled
   - CORS protection
   - Transaction fee caps
   - Request limits
4. **Network Isolation**: Nodes communicate within secure network segments

## Quick Deployment

### 1. Using the Deployment Script

```bash
# Clone the repository (if you haven't already)
git clone <repository-url>
cd hospital-management-system-master/scripts

# Make the script executable
chmod +x deploy-optimized-blockchain.sh

# Deploy in dual mode (default)
./deploy-optimized-blockchain.sh

# Or deploy in single mode
./deploy-optimized-blockchain.sh -m single
```

### 2. Using Kubernetes (for production environments)

```bash
# Apply the Kubernetes configuration
kubectl apply -f k8s/blockchain/optimized-blockchain-nodes.yaml
```

## Monitoring

The deployment includes Prometheus and Grafana for monitoring:

- **Prometheus**: http://localhost:9090
- **Grafana**: http://localhost:3000 (default credentials: admin/admin)

## Connecting to the Blockchain

### JSON-RPC Endpoints

- **Load Balancer**: http://localhost:9545 (recommended)
- **Validator Node**: http://localhost:8545
- **Processor Node**: http://localhost:8645 (only in dual mode)

### WebSocket Endpoints

- **Validator Node**: ws://localhost:8546

## Smart Contract Deployment

After the blockchain is running, deploy your smart contracts:

```bash
cd hospital-management-system-master
npx hardhat run src/blockchain/scripts/deploy-optimized.js --network localhost
```

## Performance Optimization

The blockchain is preconfigured with the following optimizations:

1. **Block Time**: 2 seconds (optimal for low CPU resources)
2. **Gas Price**: Set to 1 to balance between throughput and cost
3. **Memory Management**: 
   - GOGC=20 for optimal garbage collection
   - GOMEMLIMIT for preventing OOM conditions
4. **Cache Settings**: Optimized for available RAM

## Resource Requirements

- **Validator Node**: 2vCPU, 2GB RAM minimum
- **Processor Node**: 2vCPU, 2GB RAM minimum
- **Single Node**: 4vCPU, 4GB RAM minimum

## Troubleshooting

### Common Issues

1. **Node not synchronizing**:
   - Check network connectivity (port 30303)
   - Verify JWT authentication is configured correctly

2. **RPC not accessible**:
   - Check if the node is running (`docker-compose ps`)
   - Verify the node has completed initialization

3. **Performance issues**:
   - Increase cache sizes if more memory is available
   - Adjust GOMEMLIMIT based on available resources

## Maintenance

### Backing Up Blockchain Data

```bash
# Stop the blockchain
cd scripts
docker-compose down

# Backup data
tar -czf medichain-backup-$(date +%Y%m%d).tar.gz data/

# Restart the blockchain
docker-compose up -d
```

### Upgrading

1. Stop the current deployment
2. Pull the latest changes
3. Run the deployment script again

## Security Recommendations for Production

1. **Firewall Configuration**:
   - Allow TCP/UDP port 30303 only from trusted nodes
   - Restrict RPC access (8545/8546) to internal networks

2. **Regular Updates**:
   - Keep the client software updated
   - Apply security patches promptly

3. **Monitoring**:
   - Set up alerts for unusual activity
   - Monitor resource usage and performance metrics

---

For additional support or questions, please refer to the project documentation or open an issue in the repository.
