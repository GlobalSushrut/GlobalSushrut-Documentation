# FactorialGraphDB - DEVELOPER_GUIDE

*This documentation is from the private repository FactorialGraphDB.*

---

# FactorialGraphDB (FGraphDB) Developer Guide

This comprehensive developer guide provides detailed information on how to use FactorialGraphDB, when to use it, its core mechanisms, and typical use cases where it excels.

## Table of Contents

1. [Introduction](#introduction)
2. [When to Use FGraphDB](#when-to-use-fgraphdb)
3. [Core Concepts](#core-concepts)
4. [Implementation Details](#implementation-details)
5. [Integration Guide](#integration-guide)
6. [Performance Optimization](#performance-optimization)
7. [Advanced Usage](#advanced-usage)
8. [Use Cases & Examples](#use-cases--examples)
9. [Troubleshooting](#troubleshooting)

## Introduction

FactorialGraphDB (FGraphDB) is a production-ready, enterprise-grade graph-amplified, verifiable database layer built on top of MongoDB. It combines the flexibility and scalability of document databases with the traversal capabilities of graph databases, while adding cryptographic verification capabilities through Merkle proofs. FGraphDB is now enhanced with comprehensive security, monitoring, and enterprise features for mission-critical applications.

### Key Value Propositions

- **Graph Capabilities on MongoDB**: Achieve graph database functionality without having to migrate to a specialized graph database.
- **Verifiable Data**: Cryptographically verify data integrity and lineage without exposing sensitive information.
- **Enterprise Security**: Role-based access control, JWT authentication, and secure API key management.
- **Production Monitoring**: Comprehensive observability with structured logging, metrics collection, and health checks.
- **High Reliability**: Connection pooling with automatic retries, schema validation, and error handling.
- **Disaster Recovery**: Automated backup and restore capabilities with retention policies.
- **Containerized Deployment**: Secure Docker implementation with multi-stage builds and least-privilege principles.
- **Scalable Performance**: Optimized connection management and indexing strategies for high throughput.

## When to Use FGraphDB

FGraphDB is particularly valuable in the following scenarios:

### Ideal Use Cases

1. **When you need graph traversal in an existing MongoDB system**: 
   - You're already using MongoDB but need graph-like relationships and queries
   - You want to avoid migrating to a dedicated graph database
   - You need to maintain strict data integrity across relationships

2. **When data verifiability is crucial**:
   - Supply chain tracking where proof of origin is required
   - Medical data where verification of source without exposing PHI is needed
   - Financial systems requiring audit trails with cryptographic proof
   - Compliance environments with strict data lineage requirements

3. **When enterprise security is required**:
   - Applications requiring role-based access control
   - Systems with multiple user types and permission levels
   - Environments with strict authentication requirements
   - Applications dealing with sensitive data under regulatory oversight

4. **When production monitoring and reliability are essential**:
   - Mission-critical systems requiring high uptime
   - Applications needing detailed operational insights
   - Environments with comprehensive SLAs
   - Systems requiring disaster recovery capabilities

5. **When both document flexibility and graph relationships matter**:
   - IoT systems with complex device relationships but semi-structured data
   - Knowledge graphs with document-style attributes
   - Social networks with varied user profiles and connection types
   - Research and intelligence applications with heterogeneous data

### When to Consider Alternatives

- If you need pure graph traversal algorithms like PageRank or shortest path, consider a dedicated graph database
- If you don't need verifiability and your graph relationships are simple, MongoDB's reference fields might be sufficient
- For extremely high write-throughput systems where the overhead of computing EFS, RCH and proofs might be problematic

## Core Concepts

### Edge Factorial Signature (EFS)

The EFS is a weighted factorial signature that encodes the relationship pattern of a node. It enables efficient querying of nodes with identical edge patterns without requiring complex JOIN operations.

**Mathematical definition**:
```
EFS(Node) = ∑(hash(edge_i) * factorial(i)) for i from 1 to k
```

**Practical benefits**:
- Fast lookup of nodes with identical connection patterns
- Efficient subgraph matching
- Locality-sensitive hashing of graph structures

### Retrograde Chain Hash (RCH)

The RCH encodes the ancestry chain of a node, allowing efficient tracing back to origin or root causes.

**Mathematical definition**:
```
RCH(Node) = hash(Parent) ⊕ (hash(GrandParent) >> 1) ⊕ (hash(GreatGrandParent) >> 2) ⊕ ... ⊕ (hash(Root) >> n)
```

**Practical benefits**:
- Trace back ancestry with a single query
- Identify nodes with shared lineage
- Root cause analysis in complex systems

### Merkle Proofs

Merkle trees provide cryptographic verification of node properties and relationships without exposing the entire dataset.

**Structure**:
```
Proof(Node) = MerkleProof(EFS || RCH || DataHash)
```

**Practical benefits**:
- Verify data without exposing sensitive information
- Provide zero-knowledge proofs of membership
- Enable distributed verification without centralized trust

## Implementation Details

FGraphDB consists of several key enterprise components:

### Core Engine (`fgraphdb/core.py`)

The core engine handles the computation of EFS, RCH, and Merkle proofs. It uses:

- Deterministic hashing mechanisms to ensure consistency
- Dictionary-based storage for MongoDB compatibility
- Montgomery hashing for integer-range optimization
- Ordered leaf handling for reliable verification

```python
# Example: Computing EFS
efs = FGraphCore.compute_efs(edges)

# Example: Computing RCH
rch = FGraphCore.compute_rch(parents)

# Example: Generating a Merkle proof
proof = FGraphCore.generate_merkle_proof(efs, rch, {"data_hash": data_hash})
```

### MongoDB Integration (`fgraphdb/db.py`)

The enterprise database integration layer implements:

- Connection pooling with automatic retries
- Document insertion with automatic EFS/RCH/Proof computation
- Efficient query methods using indexed signatures
- Error handling with graceful degradation
- Performance monitoring and metrics collection

```python
# Example: Creating a production-ready database connection
db = FGraphDB(
    mongo_uri="mongodb://username:password@localhost:27017/",
    database_name="fgraphdb",
    max_pool_size=50,
    min_pool_size=5,
    max_idle_time_ms=30000,
    retry_writes=True,
    schema_validation=True
)

# Example: Inserting a document
doc = db.insert_document(
    data={"sensor_id": "A", "reading": 42.5},
    edges=["node1", "node2"], 
    parents=["parent1", "root"]
)
```

### Authentication System (`fgraphdb/auth.py`)

The security module provides enterprise-grade authentication and authorization:

- JWT-based token generation and validation
- Role-based permission management
- API key handling for machine-to-machine communication
- Token refresh mechanisms

```python
# Example: Creating authentication tokens
auth = FGraphAuth()
user_id = "user123"
roles = ["admin", "writer"]

access_token = auth.create_access_token(user_id, roles)
refresh_token = auth.create_refresh_token(user_id)

# Example: Verifying permissions
if auth.has_permission(token, "write"):
    # Proceed with write operation
```

### Monitoring and Observability (`fgraphdb/monitoring.py`)

Enterprise-grade monitoring capabilities include:

- Performance metrics collection (operations, latency, database stats)
- Health check endpoints for service status
- Resource usage tracking (CPU, memory, connections)
- Integration with Prometheus and OpenTelemetry

```python
# Example: Recording operation metrics
from fgraphdb.monitoring import metrics_instance

# Record a successful operation with latency
metrics_instance.record_operation("insert_document", "success", 0.035)

# Update document count metric
metrics_instance.update_document_count("documents", 1250)
```

### Backup and Recovery (`fgraphdb/backup.py`)

Automated disaster recovery capabilities:

- Scheduled backups with retention policies
- Point-in-time recovery options
- Compressed backup storage
- Incremental backup support

```python
# Example: Creating and managing backups
backup_manager = BackupManager(mongo_uri, database_name)

# Create a full backup
backup_result = backup_manager.create_backup("weekly_backup")

# Restore from a backup
restore_result = backup_manager.restore_backup("weekly_backup")

# Start scheduled backups
backup_manager.start_scheduled_backups()
```

### REST API (`api/main.py` and `api/health.py`)

The API exposes the FGraphDB functionality through a secure RESTful interface:

- Document operations with authentication
- Query endpoints with permission controls
- Verification endpoints for data integrity
- Health check and monitoring endpoints
- Kubernetes-ready liveness and readiness probes

## Integration Guide

### Direct Library Usage for Enterprise Applications

For Python applications requiring enterprise-grade features:

```python
from fgraphdb.db import FGraphDB
from fgraphdb.auth import FGraphAuth
from fgraphdb.monitoring import metrics_instance
from fgraphdb.backup import BackupManager

# Initialize enterprise database connection with production settings
db = FGraphDB(
    mongo_uri="mongodb://username:password@hostname:27017/",
    database_name="my_fgraphdb",
    max_pool_size=100,  # Concurrent connection limit
    min_pool_size=10,   # Maintain minimum connections
    max_idle_time_ms=30000,  # Idle connection timeout
    connect_timeout_ms=3000,  # Connection timeout
    retry_writes=True,  # Auto-retry writes
    w=1,  # Write concern level
    schema_validation=True  # Enable JSON Schema validation
)

# Setup authentication
auth = FGraphAuth()

# Generate secure tokens for users
user_token = auth.create_access_token("user_123", ["writer", "reader"])

# Create automated backup manager
backup_mgr = BackupManager(mongo_uri, database_name)
backup_mgr.start_scheduled_backups()

# Use the database with transaction-like context management
with db as secure_db:
    # Insert documents with automatic proof generation
    doc = secure_db.insert_document(
        data={"name": "Product XYZ", "batch": "B12345"},
        edges=["supplier_A", "warehouse_B"],
        parents=["manufacturer_X", "company_Y"]
    )
    
    # Query by EFS signature
    results = secure_db.query_by_efs(doc["EFS"]["signature"])
    
    # Verify a document's integrity with cryptographic proof
    is_valid = secure_db.verify_document(doc["_id"])
    
    # Record operation metrics
    metrics_instance.record_operation("query_document", "success", 0.0025)
```

### Secure REST API Integration

For non-Python applications or microservice architectures, use the REST API with authentication:

```bash
# First, authenticate to get a token
curl -X POST "http://localhost:8000/auth/login" \
  -H "Content-Type: application/json" \
  -d '{"username":"admin","password":"secure_password"}'

# Response will contain access and refresh tokens
# {"access_token":"eyJ0eXAi...", "refresh_token":"eyJ0eXAi...", "token_type":"bearer"}

# Insert a document with authentication
curl -X POST "http://localhost:8000/insert" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer eyJ0eXAi..." \
  -d '{"data":{"name":"Sensor Reading","value":23.5},"edges":["node1","node2"],"parents":["parent1","root"]}'

# Query by EFS with authentication
curl -X GET "http://localhost:8000/query/efs/12345678901234567890" \
  -H "Authorization: Bearer eyJ0eXAi..."

# Get health status (no authentication required)
curl -X GET "http://localhost:8000/health"

# Get detailed health metrics (requires admin role)
curl -X GET "http://localhost:8000/health?full=true" \
  -H "Authorization: Bearer eyJ0eXAi..."
```

### Enterprise Docker Deployment

For production containerized environments:

1. Create a secure `.env` file with sensitive configuration:
   ```
   MONGO_ROOT_USER=admin
   MONGO_ROOT_PASSWORD=your_secure_password
   MONGO_APP_USERNAME=fgraphdb_app
   MONGO_APP_PASSWORD=another_secure_password
   JWT_SECRET_KEY=generate_a_long_random_string
   LOG_LEVEL=INFO
   ENABLE_PROMETHEUS=true
   ```

2. Launch the services with proper resource limits:
   ```bash
   docker-compose up -d
   ```

3. Verify deployment with health checks:
   ```bash
   curl http://localhost:8000/health
   ```

4. For high-availability deployments, use Docker Swarm or Kubernetes:
   ```bash
   # Example Docker Swarm deployment
   docker stack deploy -c docker-compose.yml fgraphdb
   ```

## Performance Optimization

### Enterprise Database Indexing

FGraphDB automatically creates production-optimized indexes with background creation to avoid blocking operations:

```python
# Indexes for graph queries
db.documents.create_index([("EFS.signature", ASCENDING)], background=True)
db.documents.create_index([("RCH.signature", ASCENDING)], background=True)
db.documents.create_index([("edges", ASCENDING)], background=True)
db.documents.create_index([("parents", ASCENDING)], background=True)

# Indexes for verification and auditing
db.documents.create_index([("DataHash", ASCENDING)], background=True)
db.documents.create_index([("created_at", ASCENDING)], background=True)

# Indexes for user management
db.users.create_index("username", unique=True, background=True)
db.users.create_index("email", sparse=True, background=True)

# Indexes for API key management
db.api_keys.create_index("key_id", unique=True, background=True)
db.api_keys.create_index("user_id", background=True)

# Indexes for audit logging
db.audit_log.create_index([("timestamp", ASCENDING)], background=True)
db.audit_log.create_index([("user_id", ASCENDING)], background=True)
db.audit_log.create_index([("action", ASCENDING)], background=True)
```

For larger datasets in production, consider additional strategies:

1. **Compound Indexes**: Create targeted compound indexes for common query patterns
2. **Partial Indexes**: For frequently queried subsets of your data
3. **TTL Indexes**: For automatic data expiration (e.g., on audit logs)

### Connection Pool Management

Optimize connection pooling for your workload characteristics:

```python
# High throughput configuration for write-heavy workloads
db = FGraphDB(
    mongo_uri="mongodb://username:password@hostname:27017/",
    database_name="my_fgraphdb",
    max_pool_size=150,  # Increased for write concurrency
    min_pool_size=20,   # Higher minimum to reduce connection creation overhead
    max_idle_time_ms=60000,  # Longer idle timeout to reuse connections
    server_selection_timeout_ms=15000,  # More time for replica set elections
    retry_writes=True  # Automatic retry of write operations
)

# Read-optimized configuration
read_db = FGraphDB(
    mongo_uri="mongodb://readonly_user:password@replica.hostname:27017/",
    database_name="my_fgraphdb",
    max_pool_size=200,  # Higher for concurrent read operations
    min_pool_size=10,
    maxIdleTimeMS=30000,
    read_preference="secondaryPreferred"  # Use secondary replicas for reads
)
```

### Batch Operations and Bulk Writes

For high-volume insertions, use optimized batch operations:

```python
# Prepare large batch of documents
documents = [
    {
        "data": {"id": f"item{i}", "value": i * 10},
        "edges": [f"category_{i % 5}", f"supplier_{i % 10}"],
        "parents": [f"region_{i % 3}", "global"]
    } for i in range(10000)  # Generating 10,000 documents
]

# Process in optimal batch sizes
batch_size = 1000  # Optimized based on document size
batches = [documents[i:i+batch_size] for i in range(0, len(documents), batch_size)]

for batch in batches:
    results = db.batch_insert(batch)
    
    # Optional: Sleep briefly between large batches to reduce server load
    if len(batch) > 500:
        time.sleep(0.5)  # 500ms pause between large batches
```

### Distributed Caching

For enterprise deployments with high read loads, implement distributed caching:

```python
# Example with Redis caching for high-scale deployments
import redis
import json

# Initialize Redis connection
redis_client = redis.Redis(
    host='redis.hostname',
    port=6379,
    password='secure_password',
    socket_timeout=5,
    socket_connect_timeout=5
)

def get_cached_document(doc_id):
    """Get document with Redis caching"""
    # Try cache first
    cache_key = f"fgraphdb:doc:{doc_id}"
    cached = redis_client.get(cache_key)
    
    if cached:
        return json.loads(cached)
    
    # Cache miss - get from database
    doc = db.db.documents.find_one({"_id": doc_id})
    
    if doc:
        # Serialize and cache with expiration
        redis_client.setex(
            cache_key,
            3600,  # 1 hour expiration
            json.dumps(doc, default=str)  # Handle datetime serialization
        )
    
    return doc
```

### Query Optimization

For complex production queries, leverage MongoDB's aggregation framework:

```python
# Example: Efficient graph-based aggregation query
def find_connected_nodes(edge_id, max_depth=3):
    """Find all documents connected through a specific edge up to max_depth"""
    pipeline = [
        # Find documents connected to this edge
        {"$match": {"edges": edge_id}},
        
        # Get their IDs to use in subsequent queries
        {"$project": {"_id": 1}},
        
        # Lookup recursively (simplified - in production use $graphLookup)
        {"$lookup": {
            "from": "documents",
            "localField": "edges", 
            "foreignField": "_id",
            "as": "connected_docs"
        }},
        
        # Limit fields returned
        {"$project": {
            "_id": 1,
            "data": 1,
            "EFS.signature": 1,
            "connected_count": {"$size": "$connected_docs"}
        }}
    ]
    
    return list(db.db.documents.aggregate(pipeline))
```

## Advanced Usage

### Role-Based Access Control

Implement fine-grained access control for enterprise applications:

```python
from fgraphdb.auth import FGraphAuth, Role, Permission

# Define custom roles with specific permissions
ROLES = {
    "admin": Role(
        name="admin",
        permissions=[Permission.READ, Permission.WRITE, Permission.DELETE, Permission.ADMIN]
    ),
    "data_scientist": Role(
        name="data_scientist",
        permissions=[Permission.READ, Permission.COMPUTE]
    ),
    "auditor": Role(
        name="auditor",
        permissions=[Permission.READ, Permission.VERIFY, Permission.AUDIT_LOG]
    ),
    "monitoring": Role(
        name="monitoring",
        permissions=[Permission.HEALTH_CHECK, Permission.METRICS]
    )
}

# Create authentication instance with custom roles
auth = FGraphAuth(roles=ROLES)

# Generate a token with specific roles
token = auth.create_access_token(
    user_id="user123",
    roles=["data_scientist", "monitoring"],
    additional_claims={"department": "research"}
)

# Verify token has required permissions
def requires_permission(token, permission):
    """Decorator to check if token has required permission"""
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            if not auth.has_permission(token, permission):
                raise PermissionError(f"Token lacks {permission} permission")
            return func(*args, **kwargs)
        return wrapper
    return decorator

# Use the decorator to protect a function
@requires_permission(token, Permission.COMPUTE)
def run_complex_analysis(data):
    # This function will only run if the token has COMPUTE permission
    pass
```

### Custom Hash Functions for Enterprise Settings

Customize the hashing function while ensuring MongoDB compatibility:

```python
def enterprise_hash_function(data):
    """Hash function that generates MongoDB-compatible integers with
    enhanced security properties suitable for regulated environments"""
    # Use a strong hashing algorithm with salt
    import hashlib
    import struct
    import os
    
    # Get salt from environment or generate one (stored securely)
    salt = os.environ.get('HASH_SALT', 'default_salt_change_in_production')
    
    # Create salted hash
    hasher = hashlib.sha256((str(data) + salt).encode('utf-8'))
    digest = hasher.digest()
    
    # Convert to 64-bit integer (MongoDB limit)
    # Use consistent slicing for deterministic results
    int_val = struct.unpack("<Q", digest[:8])[0]
    
    # Ensure we're within MongoDB's signed 64-bit limit
    return int_val & 0x7FFFFFFFFFFFFFFF

# Use the enterprise hash function
fg_core = FGraphCore(hash_fn=enterprise_hash_function)
```

### Advanced Merkle Tree Implementations

For enterprise verification needs with higher security requirements or specific regulatory compliance:

```python
from pymerkle import InmemoryTree
import json
import hashlib

class EnterpriseVerificationTree:
    """Enterprise Merkle tree implementation with enhanced security
    and audit logging capabilities"""
    
    def __init__(self, audit_logger=None):
        # Initialize underlying Merkle tree implementation
        self.tree = InmemoryTree()
        self.audit_logger = audit_logger
        
    def append_entry(self, data):
        """Add data to the tree with audit logging"""
        # Standardize data format
        if isinstance(data, dict):
            data = json.dumps(data, sort_keys=True)
        
        # Record hash operation in audit log if available
        if self.audit_logger:
            entry_hash = hashlib.sha256(str(data).encode()).hexdigest()
            self.audit_logger.log_operation(
                "append_merkle_entry",
                {"data_hash": entry_hash[:10] + "..."}
            )
        
        # Add to the tree
        self.tree.append_entry(str(data).encode())
        return len(self.tree) - 1  # Return index
        
    def get_proof(self, index):
        """Generate a proof with additional metadata"""
        # Get the standard proof
        proof = self.tree.prove_inclusion(index)
        
        # Add enterprise metadata
        proof_data = {
            "raw_proof": proof.serialize(),
            "tree_size": len(self.tree),
            "generation_time": datetime.datetime.utcnow().isoformat(),
            "validation_url": "/api/v1/verify/proof"
        }
        
        # Log proof generation
        if self.audit_logger:
            self.audit_logger.log_operation(
                "generate_merkle_proof",
                {"index": index, "proof_id": proof.key.hex()[:10]}
            )
            
        return proof_data

# Use the enterprise Merkle tree
fg_core = FGraphCore(merkle_tree_impl=EnterpriseVerificationTree)
```

### Multi-Region Data Distribution

For globally distributed enterprise deployments:

```python
from fgraphdb.db import FGraphDB
import threading

class GlobalFGraphDB:
    """Manages multiple FGraphDB instances across regions with replication"""
    
    def __init__(self, config):
        self.regions = {}
        self.primary_region = config.get('primary_region')
        self.lock = threading.RLock()
        
        # Initialize DB connections for each region
        for region_name, region_config in config.get('regions', {}).items():
            self.regions[region_name] = FGraphDB(
                mongo_uri=region_config['mongo_uri'],
                database_name=region_config['database_name'],
                max_pool_size=region_config.get('max_pool_size', 50),
                min_pool_size=region_config.get('min_pool_size', 5),
                retry_writes=True,
                schema_validation=True
            )
            
    def insert_document(self, data, edges, parents, **kwargs):
        """Insert document to primary region first, then replicate"""
        with self.lock:
            # Insert to primary region
            primary_db = self.regions[self.primary_region]
            result = primary_db.insert_document(data, edges, parents, **kwargs)
            
            # Replicate to other regions asynchronously
            for region_name, db in self.regions.items():
                if region_name != self.primary_region:
                    threading.Thread(
                        target=self._replicate_document,
                        args=(region_name, result)
                    ).start()
                    
            return result
                
    def _replicate_document(self, region_name, document):
        """Replicate a document to a specific region"""
        try:
            db = self.regions[region_name]
            # Use the same _id and all properties from original document
            db.db.documents.insert_one(document)
        except Exception as e:
            # Log replication failure - would use proper logger in production
            print(f"Replication to {region_name} failed: {e}")
```

### Asynchronous Operations

For high-throughput applications, use the async API:

```python
import asyncio
from fgraphdb.db import FGraphDB

async def process_data():
    db = FGraphDB("mongodb://localhost:27017/", "fgraphdb")
    results = await db.query_by_efs_async("12345678901234567890")
    return results

results = asyncio.run(process_data())
```

## Use Cases & Examples

### IoT Sensor Network

In an IoT scenario, FGraphDB can manage complex sensor relationships while providing verification:

```python
# Sensor data insertion
sensor_data = {
    "device_id": "sensor123",
    "temperature": 22.5,
    "humidity": 45,
    "timestamp": "2023-06-01T12:34:56Z"
}

# Edges represent physical connections to other devices
edges = ["gateway_A", "router_B", "switch_C"]

# Parents represent the deployment hierarchy
parents = ["floor_1", "building_alpha", "campus_main", "company_hq"]

# Insert the sensor reading with its relationships
doc = db.insert_document(sensor_data, edges, parents)
```

### Supply Chain Traceability

FGraphDB can track product movement while providing cryptographic proof of origin:

```python
# Product batch record
product_data = {
    "product_id": "SKU12345",
    "batch_number": "LOT2023-06-A",
    "production_date": "2023-06-01",
    "expiry_date": "2025-06-01"
}

# Edges represent lateral relationships (e.g., other products produced together)
edges = ["SKU12346", "SKU12347", "QA_Station5"]

# Parents represent the supply chain hierarchy
parents = ["Factory_Shanghai", "Supplier_XYZ", "Manufacturer_ABC"]

# Record the product with its relationships and get verifiable proof
doc = db.insert_document(product_data, edges, parents)

# Later, verify the product's authenticity
is_authentic = db.verify_document(doc["_id"])
```

### Healthcare Data Network

FGraphDB can manage patient relationships to doctors, treatments, and facilities with privacy-preserving verification:

```python
# Patient record (anonymized)
patient_data = {
    "patient_hash": "e8c7a1234567890abcdef...",  # Anonymized ID
    "treatment_code": "PROC123",
    "outcome_code": "OUT45"
}

# Edges represent connections to providers
edges = ["doctor_A", "nurse_B", "lab_C"]

# Parents represent the treatment hierarchy
parents = ["department_cardiology", "hospital_general", "network_healthcare"]

# Store with verifiable proof
doc = db.insert_document(patient_data, edges, parents)

# For research, verify data integrity without exposing PHI
is_valid = db.verify_document(doc["_id"])
```

## Troubleshooting

### Enterprise Issues and Solutions

#### Authentication and Authorization

If you encounter permission errors in production:

```python
# DEBUG: Inspect JWT token claims
from fgraphdb.auth import FGraphAuth

auth = FGraphAuth()

try:
    # Decode and validate token
    token_claims = auth.decode_token(token)
    print(f"Token valid: {token_claims is not None}")
    if token_claims:
        print(f"User ID: {token_claims.get('sub')}")
        print(f"Roles: {token_claims.get('roles', [])}")
        print(f"Expires: {token_claims.get('exp')}")
        
        # Check specific permission
        has_write = auth.has_permission(token, "write")
        print(f"Has write permission: {has_write}")
        
 except Exception as e:
    print(f"Token validation failed: {e}")
```

#### Connection Pool Management

For connection pool issues in high-load environments:

```python
# Get detailed connection pool statistics
pool_info = db.get_connection_pool_info()
print(f"Pool size: {pool_info['max']}")
print(f"Active connections: {pool_info['active']}")
print(f"Available connections: {pool_info['available']}")

# Check if pool is exhausted
if pool_info['active'] >= pool_info['max']:
    print("WARNING: Connection pool exhausted!")
    
    # Reset connection pool in emergency situations
    db.reset_connection_pool()
    print("Connection pool has been reset")
    
# Monitor connection timeouts
timeout_metrics = db.get_connection_timeout_metrics()
print(f"Timeout rate: {timeout_metrics['rate']:.2f}%")
```

#### Schema Validation Failures

If documents fail schema validation:

```python
from fgraphdb.schema import SchemaManager

# Initialize schema manager
schema_mgr = SchemaManager(db.db)

# Get active schema for collection
current_schema = schema_mgr.get_collection_schema("documents")
print(json.dumps(current_schema, indent=2))

# Validate a document against schema manually
doc_to_validate = {"data": {"sensor_id": "A", "reading": 42.5}, "edges": ["e1"]}
validation_result = schema_mgr.validate_document("documents", doc_to_validate)

if not validation_result['valid']:
    print(f"Validation errors: {validation_result['errors']}")
```

#### Production Performance Issues

For diagnosing slow operations in production:

```python
from fgraphdb.monitoring import metrics_instance

# Get operation timing statistics
op_stats = metrics_instance.get_operation_stats()
for op_name, stats in op_stats.items():
    print(f"{op_name}: avg={stats['avg_time']:.4f}s, p95={stats['p95_time']:.4f}s, count={stats['count']}")
    
# Enable MongoDB profiling temporarily
db.db.command({"profile": 2, "slowms": 100})  # Log operations taking > 100ms

# After running problem operations, check profile
slow_ops = list(db.db.system.profile.find().sort("millis", -1).limit(10))
for op in slow_ops:
    print(f"Slow operation: {op['op']} - {op['millis']}ms")
    print(f"Query: {op.get('query', op.get('command', {}))}") 
    
# Disable profiling when done
db.db.command({"profile": 0})
```

#### Backup and Recovery Issues

For diagnosing backup problems:

```python
from fgraphdb.backup import BackupManager

backup_mgr = BackupManager(mongo_uri, database_name)

# Check backup status
backups = backup_mgr.list_backups()
for backup in backups:
    print(f"Backup: {backup['name']}")
    print(f"  - Created: {backup['timestamp']}")
    print(f"  - Size: {backup['size_mb']:.2f} MB")
    print(f"  - Status: {backup['status']}")
    
# Verify backup integrity
verification = backup_mgr.verify_backup(backups[0]['name'])
print(f"Backup verification: {verification['status']}")
if verification['status'] != 'valid':
    print(f"Errors: {verification['errors']}")
    
# Test restore to temporary database
restore_test = backup_mgr.test_restore(backups[0]['name'])
print(f"Restore test: {restore_test['success']}")
```

#### Merkle Proof Verification Failures

If cryptographic verification fails:

```python
# Detailed debug of proof verification
from fgraphdb.core import FGraphCore

# Get document with proof
doc = db.db.documents.find_one({"_id": doc_id})

# Check if proof structure is valid
if "Proof" not in doc or "EFS" not in doc or "RCH" not in doc:
    print("Document is missing required verification fields")

# Extract components
efs = doc.get("EFS", {})
rch = doc.get("RCH", {})
proof = doc.get("Proof", {})
data_hash = doc.get("DataHash")

print(f"EFS signature: {efs.get('signature')}")
print(f"RCH signature: {rch.get('signature')}")
print(f"Data hash: {data_hash}")

# Debug proof structure
print("Proof details:")
print(json.dumps(proof, indent=2))

# Manual verification attempt
core = FGraphCore()
try:
    verification = core.verify_merkle_proof(
        proof, 
        {"efs_signature": efs.get('signature'), 
         "rch_signature": rch.get('signature'),
         "data_hash": data_hash}
    )
    print(f"Manual verification result: {verification}")
except Exception as e:
    print(f"Verification error: {e}")
```

---

## Conclusion

FactorialGraphDB bridges the gap between document databases and graph databases, adding powerful verification capabilities. By using the patterns and recommendations in this developer guide, you can leverage FGraphDB to build applications with complex data relationships, efficient traversal capabilities, and cryptographic verifiability.

For further assistance, check the unit tests in the `tests/` directory for more usage examples, or open an issue in the project repository.
