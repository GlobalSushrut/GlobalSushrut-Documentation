# hospital-management-system-blockchain - README

*This documentation is from the private repository hospital-management-system-blockchain.*

---

# Medical Chain - Polygon Sidechain

This is a Polygon sidechain implementation for storing medical records securely.

## Prerequisites

- Docker
- Docker Compose

## Setup Instructions

1. Start the validators:
```bash
docker-compose up -d
```

2. Check the status:
```bash
docker-compose ps
```

3. View logs:
```bash
docker-compose logs -f
```

## Network Details

- Chain ID: 100
- RPC Endpoints:
  - Validator 1: http://localhost:8545
  - Validator 2: http://localhost:8546
  - Validator 3: http://localhost:8547

## Smart Contract Deployment

After the network is running, you can deploy smart contracts using:
- Web3.js
- Ethers.js
- Hardhat
- Truffle

Make sure to use the local RPC endpoint (http://localhost:8545) as your provider.

## Security Notes

This is a private sidechain for medical records. Ensure:
1. Proper access control in smart contracts
2. Encrypted data storage
3. Regular backups
4. Monitoring of validator nodes
