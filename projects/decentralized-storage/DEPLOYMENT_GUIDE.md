# decentralized-storage - DEPLOYMENT_GUIDE

*This documentation is from the private repository decentralized-storage.*

---

# Distributed Storage System Deployment Guide

This guide provides instructions for deploying the decentralized storage system across multiple cloud instances to create a self-hosted distributed storage network.

## System Architecture

Our distributed storage system consists of the following components:

1. **Coordinator Nodes**: Manage the cluster and handle client requests
2. **Storage Nodes**: Store data and respond to coordinator requests
3. **Gateway Nodes**: Provide client access and request routing

Each node communicates with other nodes using a secure protocol, with built-in discovery, consensus, and sharding capabilities.

## Requirements

- Linux-based servers (Ubuntu 20.04 LTS or newer recommended)
- At least 4GB RAM per node (8GB+ recommended for coordinator nodes)
- Storage space as needed (SSDs recommended for performance)
- Open network ports for node communication (default: 9000)
- Rust toolchain (1.56.0+) for building the application

## Deployment Topology Recommendations

For a production-ready setup, we recommend:

- 3+ Coordinator nodes (for high availability)
- 5+ Storage nodes (for data redundancy)
- 2+ Gateway nodes (for load balancing)

The nodes should be distributed across:
- Multiple availability zones within a region for standard deployments
- Multiple regions for global deployments

## Installation Steps

### 1. Prepare the Environment

On each server that will host a node:

```bash
# Update system packages
sudo apt update && sudo apt upgrade -y

# Install build dependencies
sudo apt install -y build-essential pkg-config libssl-dev git

# Install Rust
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source $HOME/.cargo/env

# Clone the repository
git clone https://github.com/your-org/decentralized-storage.git
cd decentralized-storage

# Build the project
cargo build --release
```

### 2. Configure the Nodes

On each node, create a configuration file. Below are example configurations for each node type:

#### Coordinator Node Configuration

```bash
mkdir -p /opt/dstorage/data
cat > /opt/dstorage/coordinator.env <<EOF
NODE_TYPE=coordinator
NODE_ID=coordinator-$(hostname)
LISTEN_ADDRESS=0.0.0.0:9000
DATA_PATH=/opt/dstorage/data
STORAGE_BACKEND=sled
REPLICATION_FACTOR=3
DISCOVERY_METHOD=static
SEED_NODES=coordinator1-ip:9000,coordinator2-ip:9000,coordinator3-ip:9000
EOF
```

#### Storage Node Configuration

```bash
mkdir -p /opt/dstorage/data
cat > /opt/dstorage/storage.env <<EOF
NODE_TYPE=storage
NODE_ID=storage-$(hostname)
LISTEN_ADDRESS=0.0.0.0:9000
DATA_PATH=/opt/dstorage/data
STORAGE_BACKEND=sled
REPLICATION_FACTOR=3
DISCOVERY_METHOD=static
SEED_NODES=coordinator1-ip:9000,coordinator2-ip:9000,coordinator3-ip:9000
CAPACITY_GB=100
EOF
```

#### Gateway Node Configuration

```bash
mkdir -p /opt/dstorage/data
cat > /opt/dstorage/gateway.env <<EOF
NODE_TYPE=gateway
NODE_ID=gateway-$(hostname)
LISTEN_ADDRESS=0.0.0.0:9000
DATA_PATH=/opt/dstorage/data
STORAGE_BACKEND=sled
DISCOVERY_METHOD=static
SEED_NODES=coordinator1-ip:9000,coordinator2-ip:9000,coordinator3-ip:9000
EOF
```

### 3. Create Systemd Service

Create a systemd service file to manage the node process:

```bash
cat > /etc/systemd/system/dstorage.service <<EOF
[Unit]
Description=Distributed Storage Node
After=network.target

[Service]
User=dstorage
Group=dstorage
Type=simple
WorkingDirectory=/opt/dstorage
EnvironmentFile=/opt/dstorage/node.env
ExecStart=/path/to/decentralized-storage/target/release/dstnode start \
  --node-type \${NODE_TYPE} \
  --node-id \${NODE_ID} \
  --address \${LISTEN_ADDRESS} \
  --data-path \${DATA_PATH} \
  --seeds \${SEED_NODES} \
  --capacity \${CAPACITY_GB:-0} \
  --replication \${REPLICATION_FACTOR:-3} \
  --discovery \${DISCOVERY_METHOD:-static}
Restart=on-failure
RestartSec=5s

[Install]
WantedBy=multi-user.target
EOF
```

### 4. Create User and Set Permissions

```bash
# Create user for the service
sudo useradd -r -s /bin/false dstorage

# Set ownership for the data directory
sudo chown -R dstorage:dstorage /opt/dstorage
```

### 5. Start the Services

Start the services in this order:
1. Coordinator nodes first
2. Storage nodes next
3. Gateway nodes last

```bash
# First, reload systemd config
sudo systemctl daemon-reload

# Enable and start the service
sudo systemctl enable dstorage
sudo systemctl start dstorage

# Check status
sudo systemctl status dstorage
```

### 6. Verify the Deployment

Check the logs to ensure nodes are communicating:

```bash
sudo journalctl -u dstorage -f
```

You should see messages indicating that nodes are discovering each other and the cluster is forming.

## Network Configuration

### Firewall Rules

Ensure the following ports are open between nodes:

- **TCP/UDP 9000**: Node-to-node communication (or your custom port)

Example using `ufw`:

```bash
sudo ufw allow 9000/tcp
sudo ufw allow 9000/udp
```

### Load Balancer Configuration (for Gateway Nodes)

If you're using a load balancer in front of gateway nodes:

1. Configure health checks to `/health` endpoint
2. Set up SSL termination if needed
3. Configure session persistence for uploads

## Scaling the Cluster

### Adding New Nodes

To add a new node to the existing cluster:

1. Follow steps 1-4 above to prepare the new server
2. Configure the node with existing coordinator nodes as seed nodes
3. Start the service on the new node
4. The node will automatically join the cluster via discovery

### Removing Nodes

To gracefully remove a node:

1. Stop the node service: `sudo systemctl stop dstorage`
2. The system will automatically rebalance data

## Monitoring

The distributed storage system exposes metrics via a `/metrics` endpoint that can be scraped by Prometheus. Set up monitoring using:

1. Prometheus for metrics collection
2. Grafana for visualization
3. Alertmanager for alerts

A sample Prometheus configuration:

```yaml
scrape_configs:
  - job_name: 'dstorage'
    scrape_interval: 15s
    static_configs:
      - targets: ['node1:9000', 'node2:9000', 'node3:9000']
```

## Backup and Recovery

For disaster recovery:

1. Regularly back up the `/opt/dstorage/data` directory on coordinator nodes
2. Store the backups in a separate location
3. Test the recovery process regularly

To recover from a backup:

1. Stop the service: `sudo systemctl stop dstorage`
2. Replace the data directory with the backup
3. Start the service: `sudo systemctl start dstorage`

## Cloud-Specific Deployment Notes

### AWS

- Use EC2 instances with instance storage or EBS volumes
- Place nodes in different availability zones
- Use security groups to restrict access
- Consider using Auto Scaling Groups for automatic node replacement

### Google Cloud

- Use Compute Engine instances with Persistent Disks
- Place nodes in different zones
- Use firewall rules to secure the network
- Consider using Managed Instance Groups

### Azure

- Use Virtual Machines with Premium Storage
- Place nodes in different availability zones
- Use Network Security Groups to control access
- Consider using Virtual Machine Scale Sets

## Performance Tuning

For optimal performance:

1. Use SSDs for storage nodes
2. Allocate at least 4GB RAM for each node
3. Tune Linux parameters:
   ```bash
   # Add to /etc/sysctl.conf
   fs.file-max = 100000
   net.core.somaxconn = 65535
   net.ipv4.tcp_max_syn_backlog = 65535
   ```
4. Use `ulimit` to increase file descriptor limits for the dstorage user

## Troubleshooting

Common issues and solutions:

1. **Nodes can't discover each other**:
   - Check network connectivity
   - Verify firewall rules
   - Ensure seed node addresses are correct

2. **High latency or slow performance**:
   - Check disk I/O performance
   - Monitor CPU and memory usage
   - Verify network bandwidth between nodes

3. **Node fails to start**:
   - Check logs with `journalctl -u dstorage`
   - Verify configuration file syntax
   - Ensure data directory permissions are correct

## Security Considerations

1. **Network Security**:
   - Use private networks for node communication when possible
   - Set up firewalls to restrict access
   - Consider using VPN for cross-region deployments

2. **Authentication**:
   - Configure proper authentication for API access
   - Rotate access keys regularly

3. **Encryption**:
   - Enable at-rest encryption for all stored data
   - Configure TLS for all node-to-node communication

4. **Audit Logging**:
   - Enable comprehensive logging
   - Ship logs to a centralized log management system

## Conclusion

Following this guide, you should have a fully operational self-hosted distributed storage system. The system provides high availability, scalability, and data redundancy across your cloud infrastructure.

For additional support or questions, please refer to the documentation or open an issue in the repository.
