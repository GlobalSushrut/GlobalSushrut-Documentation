# hospital-management-system-blockchain - DEPLOYMENT

*This documentation is from the private repository hospital-management-system-blockchain.*

---

# Hospital Management System - Comprehensive Deployment Guide

This document provides a comprehensive guide for deploying the Hospital Management System, including all components: Frontend, Backend, Blockchain, and Rust Secure Database.

## System Architecture Overview

The Hospital Management System consists of the following components:

1. **Frontend**: React-based web application
2. **Backend**: Flask-based REST API
3. **Blockchain**: Ethereum-based MediChain for verification of medical records, pharmacy products, and lab tests
4. **Rust Secure Database**: For secure storage of sensitive healthcare data, replacing IPFS

## Prerequisites

- Kubernetes cluster (v1.20+)
- Docker (20.10+)
- kubectl CLI
- Helm (v3.6+)
- Database server (PostgreSQL 13+) for Rust Secure DB
- Node.js (v14+) for development

## Environment Setup

### 1. Clone the Repository

```bash
git clone https://github.com/your-org/hospital-management-system.git
cd hospital-management-system
```

### 2. Configure Environment Variables

Create or update the following .env files for each component:

#### Frontend (.env)
```
REACT_APP_BACKEND_URL=http://backend-service:5000
REACT_APP_BLOCKCHAIN_URL=http://blockchain-service:8545
NODE_ENV=production
```

#### Backend (.env)
```
FLASK_ENV=production
JWT_SECRET=your-secure-jwt-secret
BLOCKCHAIN_URL=http://blockchain-service:8545
RUST_DB_URL=http://rust-db-service:8000
AUTH0_ENABLED=true
AUTH0_DOMAIN=your-tenant.auth0.com
AUTH0_CLIENT_ID=your-client-id
AUTH0_CLIENT_SECRET=your-client-secret
AUTH0_API_AUDIENCE=your-api-identifier
AUTH0_CALLBACK_URL=https://your-domain.com/api/v1/auth/callback
AUTH0_ALGORITHMS=RS256
```

#### Blockchain (.env)
```
NETWORK_ID=1337
GETH_ARGS="--http --http.addr=0.0.0.0 --http.port=8545 --http.corsdomain=* --http.api=eth,net,web3,personal,miner"
ACCOUNT_PASSWORD=your-secure-password
```

#### Rust Secure DB (.env)
```
DATABASE_URL=postgres://user:password@postgres-host:5432/hospital_db
JWT_SECRET=your-secure-jwt-secret
BCRYPT_COST=12
API_PORT=8000
LOG_LEVEL=info
ENCRYPTION_KEY=your-32-byte-encryption-key
```

## Component-by-Component Deployment

### 1. Rust Secure Database Deployment

#### Build and Push Docker Image

```bash
cd rust-secure-db
docker build -t your-registry/rust-secure-db:latest -f rust-db/Dockerfile .
docker push your-registry/rust-secure-db:latest
```

#### Create Kubernetes ConfigMap and Secret

```bash
kubectl create namespace hospital-system
kubectl create secret generic rust-db-secrets \
  --namespace=hospital-system \
  --from-literal=database-url='postgres://user:password@postgres-host:5432/hospital_db' \
  --from-literal=jwt-secret='your-secure-jwt-secret' \
  --from-literal=encryption-key='your-32-byte-encryption-key'

kubectl create configmap rust-db-config \
  --namespace=hospital-system \
  --from-literal=bcrypt-cost='12' \
  --from-literal=api-port='8000' \
  --from-literal=log-level='info'
```

#### Deploy Rust DB to Kubernetes

Create a file named `k8s/rust-db/deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: rust-db
  namespace: hospital-system
  labels:
    app: rust-db
spec:
  replicas: 2
  selector:
    matchLabels:
      app: rust-db
  template:
    metadata:
      labels:
        app: rust-db
    spec:
      containers:
      - name: rust-db
        image: your-registry/rust-secure-db:latest
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 8000
          name: http
        resources:
          requests:
            memory: "256Mi"
            cpu: "100m"
          limits:
            memory: "512Mi"
            cpu: "300m"
        env:
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: rust-db-secrets
              key: database-url
        - name: JWT_SECRET
          valueFrom:
            secretKeyRef:
              name: rust-db-secrets
              key: jwt-secret
        - name: ENCRYPTION_KEY
          valueFrom:
            secretKeyRef:
              name: rust-db-secrets
              key: encryption-key
        - name: BCRYPT_COST
          valueFrom:
            configMapKeyRef:
              name: rust-db-config
              key: bcrypt-cost
        - name: API_PORT
          valueFrom:
            configMapKeyRef:
              name: rust-db-config
              key: api-port
        - name: LOG_LEVEL
          valueFrom:
            configMapKeyRef:
              name: rust-db-config
              key: log-level
        volumeMounts:
        - name: rust-db-data
          mountPath: /app/data
        readinessProbe:
          httpGet:
            path: /health
            port: 8000
          initialDelaySeconds: 10
          periodSeconds: 5
        livenessProbe:
          httpGet:
            path: /health
            port: 8000
          initialDelaySeconds: 15
          periodSeconds: 15
      volumes:
      - name: rust-db-data
        persistentVolumeClaim:
          claimName: rust-db-pvc
```

Create a file named `k8s/rust-db/service.yaml`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: rust-db-service
  namespace: hospital-system
spec:
  selector:
    app: rust-db
  ports:
  - port: 8000
    targetPort: 8000
  type: ClusterIP
```

Create a file named `k8s/rust-db/pvc.yaml`:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: rust-db-pvc
  namespace: hospital-system
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
```

Apply the Kubernetes manifests:

```bash
kubectl apply -f k8s/rust-db/pvc.yaml
kubectl apply -f k8s/rust-db/deployment.yaml
kubectl apply -f k8s/rust-db/service.yaml
```

### 2. Blockchain Deployment

#### Build and Push Docker Image

```bash
cd medical-chain
docker build -t your-registry/medical-chain:latest .
docker push your-registry/medical-chain:latest
```

#### Deploy Blockchain to Kubernetes

The blockchain deployment is already defined in the k8s/blockchain directory. Apply the manifests:

```bash
kubectl apply -f k8s/blockchain/configmap.yaml
kubectl apply -f k8s/blockchain/contracts-configmap.yaml
kubectl apply -f k8s/blockchain/secrets.yaml
kubectl apply -f k8s/blockchain/geth-statefulset.yaml
kubectl apply -f k8s/blockchain/service.yaml
kubectl apply -f k8s/blockchain/contract-deployer-job.yaml
```

### 3. Backend Deployment

#### Build and Push Docker Image

```bash
cd flask-backend
docker build -t your-registry/hospital-backend:latest .
docker push your-registry/hospital-backend:latest
```

#### Deploy Backend to Kubernetes

The backend deployment is already defined in the k8s/backend directory. Create a secret for the backend:

```bash
kubectl create secret generic backend-secrets \
  --namespace=hospital-system \
  --from-literal=jwt-secret='your-secure-jwt-secret'
```

Apply the manifests:

```bash
kubectl apply -f k8s/backend/deployment.yaml
kubectl apply -f k8s/backend/service.yaml
```

### 4. Frontend Deployment

#### Build and Push Docker Image

First, update the frontend code to support the webpack configuration for the crypto module:

```bash
cd hospital-management-system-master
npm install
npm run build
docker build -t your-registry/hospital-frontend:latest .
docker push your-registry/hospital-frontend:latest
```

#### Deploy Frontend to Kubernetes

The frontend deployment is already defined in the k8s/frontend directory. Create a configmap for the frontend:

```bash
kubectl create configmap frontend-config \
  --namespace=hospital-system \
  --from-literal=backend_url='http://backend-service:5000' \
  --from-literal=blockchain_url='http://blockchain-service:8545'
```

Apply the manifests:

```bash
kubectl apply -f k8s/frontend/nginx-configmap.yaml
kubectl apply -f k8s/frontend/deployment.yaml
kubectl apply -f k8s/frontend/service.yaml
```

### 5. Ingress Configuration

Create a file named `k8s/ingress.yaml`:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: hospital-system-ingress
  namespace: hospital-system
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
    cert-manager.io/cluster-issuer: letsencrypt-prod
spec:
  tls:
  - hosts:
    - hospital.example.com
    secretName: hospital-tls
  rules:
  - host: hospital.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: frontend
            port:
              number: 80
      - path: /api
        pathType: Prefix
        backend:
          service:
            name: backend-service
            port:
              number: 5000
```

Apply the ingress configuration:

```bash
kubectl apply -f k8s/ingress.yaml
```

## Integration Between Components

### 1. Frontend-Backend Integration

The frontend connects to the backend through the REACT_APP_BACKEND_URL environment variable. The backend API endpoints are used for:
- User authentication and authorization
- Patient, doctor, and staff management
- Medical record management
- Appointment scheduling
- Pharmacy product and lab test ordering

### 2. Backend-Blockchain Integration

The backend connects to the blockchain through the BLOCKCHAIN_URL environment variable. The blockchain is used for:
- Verification of pharmacy products
- Verification of lab tests
- Medical record integrity
- Supply chain validation

### 3. Backend-Rust Secure DB Integration

The backend connects to the Rust Secure DB through the RUST_DB_URL environment variable. The Rust Secure DB is used for:
- Secure storage of sensitive patient data
- Encrypted medical records
- Secure pharmacy prescription data
- Encrypted payment information

## Security Considerations

### 1. Data Encryption

- Sensitive data is encrypted at rest in the Rust Secure DB
- Data is encrypted in transit using TLS
- The Rust Secure DB uses industry-standard encryption algorithms

### 2. Authentication and Authorization

- JWT-based authentication for API access
- Role-based access control (RBAC)
- Auth0 integration for external authentication
- Multi-factor authentication (MFA) support

### 3. Network Security

- Internal services are not exposed directly to the internet
- Kubernetes network policies restrict pod-to-pod communication
- API rate limiting to prevent abuse
- Web Application Firewall (WAF) for the ingress

### 4. Audit Logging

- Comprehensive audit logs for all sensitive operations
- Secure log storage with tamper detection
- Regular log analysis for security events

## Monitoring and Maintenance

### 1. Health Checks

Each component has health check endpoints:
- Frontend: `/` or `/health`
- Backend: `/health`
- Blockchain: TCP check on port 8545
- Rust Secure DB: `/health`

### 2. Prometheus Metrics

Set up Prometheus monitoring:

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install prometheus prometheus-community/kube-prometheus-stack \
  --namespace monitoring \
  --create-namespace
```

### 3. Log Management

Set up centralized logging with Elasticsearch, Fluentd, and Kibana (EFK):

```bash
helm repo add elastic https://helm.elastic.co
helm repo update
helm install elasticsearch elastic/elasticsearch --namespace logging --create-namespace
helm install kibana elastic/kibana --namespace logging
helm install fluentd-elasticsearch stable/fluentd-elasticsearch --namespace logging
```

## Backup and Restore Procedures

### 1. Database Backup

Set up automated PostgreSQL backups for the Rust Secure DB:

```bash
kubectl create cronjob postgres-backup --image=postgres:13 --schedule="0 2 * * *" \
  --namespace=hospital-system -- /bin/bash -c "pg_dump -h postgres-host -U user hospital_db > /backup/hospital_db_$(date +%Y%m%d).sql"
```

### 2. Blockchain Data Backup

Set up automated backups for the blockchain data:

```bash
kubectl create cronjob blockchain-backup --image=alpine:latest --schedule="0 3 * * *" \
  --namespace=hospital-system -- /bin/bash -c "tar -czf /backup/blockchain_$(date +%Y%m%d).tar.gz /data"
```

## Disaster Recovery

### 1. Multi-Region Deployment

For production environments, deploy the system across multiple Kubernetes clusters in different regions:

1. Set up a global database with read replicas
2. Use a global load balancer to direct traffic to the nearest healthy region
3. Implement database replication for the Rust Secure DB
4. Implement backup verification procedures

### 2. Recovery Time Objectives (RTO)

- Database: 1 hour
- Blockchain: 2 hours
- Application: 30 minutes

## Testing Plan

### 1. Component Testing

Test each component individually:

- Frontend: Run unit and integration tests with Jest
- Backend: Run unit tests with pytest
- Blockchain: Test smart contracts with Truffle
- Rust Secure DB: Run integration tests

### 2. End-to-End Testing

Test the full system flow:

- User registration and authentication
- Patient data management
- Doctor appointment scheduling
- Pharmacy product verification
- Lab test ordering and verification
- Telemedicine capabilities
- Medical record access and management

### 3. Security Testing

- Perform penetration testing
- Run vulnerability scanning
- Conduct code security reviews

## Scaling Considerations

### 1. Horizontal Scaling

- Frontend and Backend: Increase replica count based on load
- Rust Secure DB: Set up read replicas for read-heavy workloads
- Blockchain: Use sidecar containers for API load distribution

### 2. Vertical Scaling

- Adjust resource requests and limits in Kubernetes manifests
- Use node selectors to deploy components on more powerful nodes

### 3. Database Partitioning

- Implement database sharding for the Rust Secure DB
- Use time-based partitioning for historical data

## Additional Considerations

### 1. Compliance

- HIPAA compliance for medical data
- GDPR compliance for user data
- FDA compliance for pharmacy products

### 2. Audit Trail

- Maintain a cryptographically verifiable audit trail
- Track all data access and modifications
- Store audit logs securely and immutably

### 3. User Training

- Provide documentation for system administrators
- Develop training materials for healthcare staff
- Create user guides for patients

---

This comprehensive deployment guide covers all aspects of deploying and maintaining the Hospital Management System. For additional assistance or specific component configurations, please refer to the individual component documentation.
