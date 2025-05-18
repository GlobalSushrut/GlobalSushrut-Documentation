# decentralized-storage - DOCUMENTATION

*This documentation is from the private repository decentralized-storage.*

---

# Decentralized Storage System Documentation

## Overview

The Decentralized Storage System is a robust, secure, and distributed data storage solution that leverages blockchain technology, encryption, and distributed systems concepts to provide reliable and tamper-proof data storage. This system is designed for applications requiring high security, data integrity verification, and distributed redundancy.

## Key Features

- **Distributed Architecture**: Multi-node architecture with peer discovery and consensus
- **End-to-End Encryption**: Secure data storage with multiple encryption algorithms
- **Content-Addressable Storage**: IPLD-based content addressing for efficient retrieval
- **Blockchain Integration**: Proof-of-storage and verification on blockchain
- **Advanced Compression**: Cross-zipping compression for efficient storage
- **Retention Policies**: Configurable data retention policies
- **Challenge-Response System**: Verifiable storage proofs through challenge-response mechanisms

## System Architecture

### Core Components

1. **Storage Layer**
   - `StorageBackend` trait with multiple implementations (Memory, RocksDB, Sled)
   - Compression utilities for efficient storage
   - Content-addressable storage system

2. **Security Layer**
   - Encryption services (AES, ChaCha20Poly1305, XChaCha20Poly1305)
   - Key management system
   - Password-based key derivation

3. **Distributed Layer**
   - Node discovery protocols
   - Consensus mechanisms (leader election)
   - Content replication and sharding
   - Challenge-response verification

4. **Blockchain Layer**
   - Smart contract integration
   - Proof-of-storage verification
   - Transaction management

5. **Monitoring**
   - Metrics collection
   - Performance monitoring

### Component Interactions

The system operates on a modular architecture where:

1. **Client** interfaces with the distributed network to store/retrieve data
2. **Nodes** maintain the distributed network, handle replication, and verify storage
3. **Storage Backends** provide the actual data persistence mechanisms
4. **Blockchain** serves as a verification and audit mechanism

## Recent Fixes and Improvements

We've made significant improvements to the codebase, focusing on:

1. **IPLD Implementation**
   - Fixed reference issues with CID parameters
   - Added proper Send bounds to trait methods for async support
   - Corrected method implementations related to CID handling

2. **MemoryBackend Implementation**
   - Created a complete in-memory storage backend for testing
   - Implemented error handling for StorageBackend trait methods
   - Added required flush and close methods
   - Added Clone derivation to make it compatible with RetentionManager

3. **Encryption System**
   - Fixed salt handling in password-based encryption methods
   - Updated SaltString usage to use from_b64 instead of deprecated new method
   - Improved password encryption test to focus on core functionality

4. **Code Quality**
   - Reduced unused imports across the codebase
   - Fixed warnings to improve code maintainability
   - Ensured all library tests pass successfully

## How to Use the System

### Prerequisites

- Rust (latest stable version)
- [Optional] RocksDB or Sled for persistent storage backends
- [Optional] Ethereum-compatible blockchain for verification features

### Building the Project

```bash
# Clone the repository
git clone <repository-url>
cd decentralized-storage

# Build the project
cargo build

# Run tests to ensure everything is working
cargo test --lib
```

### Core Usage Examples

#### 1. Creating a Storage Node

```rust
use cloud_blockchain_storage::distributed::node::{Node, NodeConfig, NodeType};
use cloud_blockchain_storage::storage::memory::MemoryBackend;

async fn create_node() {
    // Create a node configuration
    let config = NodeConfig {
        node_type: NodeType::Storage,
        storage_path: "/tmp/node1".into(),
        listen_addr: "127.0.0.1:9000".parse().unwrap(),
        peers: vec!["127.0.0.1:9001".parse().unwrap()],
        capacity_gb: 10,
        replication_factor: 3,
    };
    
    // Create a memory backend for storage
    let backend = MemoryBackend::new();
    
    // Create and start the node
    let node = Node::new(config, backend).await.unwrap();
    node.start().await.unwrap();
}
```

#### 2. Encrypting and Storing Data

```rust
use cloud_blockchain_storage::security::encryption::{EncryptionManager, EncryptionAlgorithm, KeyDerivationFunction};
use cloud_blockchain_storage::storage::StorageBackend;
use std::sync::Arc;

async fn encrypt_and_store<B: StorageBackend>(backend: Arc<B>) {
    // Create encryption manager
    let encryption_manager = EncryptionManager::new(
        EncryptionAlgorithm::XChaCha20Poly1305,
        KeyDerivationFunction::Argon2id,
    );
    
    // Generate a key
    let key_id = "content-123";
    let key = encryption_manager.generate_key(key_id).unwrap();
    
    // Encrypt data
    let data = b"Sensitive information";
    let (encrypted_data, metadata) = encryption_manager.encrypt(key_id, data).unwrap();
    
    // Store encrypted data
    backend.put("encrypted", key_id.as_bytes(), &encrypted_data).await.unwrap();
}
```

#### 3. Using IPLD Storage

```rust
use cloud_blockchain_storage::distributed::ipld::{IpldNode, IpldStore, MemoryIpldStore};
use serde::{Serialize, Deserialize};

#[derive(Serialize, Deserialize)]
struct Person {
    name: String,
    age: u32,
}

async fn store_with_ipld() {
    // Create IPLD store
    let store = MemoryIpldStore::new();
    
    // Create data to store
    let person = Person {
        name: "Alice".to_string(),
        age: 30,
    };
    
    // Create and store IPLD node
    let node = IpldNode::new(&person).unwrap();
    let cid = store.put_node(&node).await.unwrap();
    
    // Retrieve node
    let retrieved_node = store.get_node(&cid).await.unwrap();
    let retrieved_person: Person = retrieved_node.to_object().unwrap();
    
    assert_eq!(person.name, retrieved_person.name);
    assert_eq!(person.age, retrieved_person.age);
}
```

#### 4. Implementing Retention Policies

```rust
use cloud_blockchain_storage::distributed::retention::{RetentionPolicy, RetentionManager};
use cloud_blockchain_storage::storage::memory::MemoryBackend;
use std::time::Duration;
use std::sync::Arc;

async fn setup_retention() {
    // Create a storage backend
    let backend = Arc::new(MemoryBackend::new());
    
    // Create retention policy (30 days)
    let policy = RetentionPolicy::new(
        "default",
        Duration::from_secs(30 * 24 * 60 * 60), // 30 days
        true, // allow early deletion
    );
    
    // Create retention manager
    let retention_manager = RetentionManager::new(backend, vec![policy]);
    
    // Start retention management
    retention_manager.start_gc().await.unwrap();
}
```

## API Reference

### StorageBackend Trait

The core storage interface:

```rust
pub trait StorageBackend: Send + Sync {
    async fn get(&self, tree: &str, key: &[u8]) -> Result<Option<Vec<u8>>>;
    async fn put(&self, tree: &str, key: &[u8], value: &[u8]) -> Result<()>;
    async fn delete(&self, tree: &str, key: &[u8]) -> Result<()>;
    async fn contains_key(&self, tree: &str, key: &[u8]) -> Result<bool>;
    async fn list_keys(&self, tree: &str) -> Result<Vec<Vec<u8>>>;
    async fn create_tree(&self, tree: &str) -> Result<()>;
    async fn flush(&self) -> Result<()>;
    async fn close(&self) -> Result<()>;
}
```

### EncryptionManager

Manages encryption and key operations:

```rust
pub struct EncryptionManager {
    // ...
}

impl EncryptionManager {
    pub fn new(
        algorithm: EncryptionAlgorithm,
        kdf: KeyDerivationFunction,
    ) -> Self;
    
    pub fn generate_key(&self, key_id: &str) -> Result<SecureKey>;
    pub fn get_key(&self, key_id: &str) -> Option<SecureKey>;
    pub fn store_key(&self, key_id: &str, key: SecureKey) -> Result<()>;
    pub fn encrypt(&self, key_id: &str, data: &[u8]) -> Result<(Vec<u8>, EncryptionMetadata)>;
    pub fn decrypt(&self, key_id: &str, data: &[u8], metadata: &EncryptionMetadata) -> Result<Vec<u8>>;
}
```

### IpldStore Trait

Content-addressable storage interface:

```rust
#[async_trait]
pub trait IpldStore: Send + Sync {
    async fn put_node(&self, node: &IpldNode) -> Result<Cid>;
    async fn get_node(&self, cid: &Cid) -> Result<IpldNode>;
    async fn put_block(&self, cid: &Cid, data: &[u8]) -> Result<()>;
    async fn get_block(&self, cid: &Cid) -> Result<Vec<u8>>;
    async fn has(&self, cid: &Cid) -> Result<bool>;
}
```

## Future Improvements

While the core library functionality is now working correctly, there are several areas for future improvement:

1. **Binary Compilation**
   - Add the missing `tracing_subscriber` dependency to Cargo.toml
   - Fix CLI implementation issues (implement clap::Parser for Cli struct)
   - Resolve u64 and u8 dereferencing issues in the binary code

2. **Integration Tests**
   - Complete the integration tests that depend on binary functionality

3. **Code Cleanup**
   - Address remaining warnings about unused struct fields
   - Clean up remaining unused imports and variables

4. **Documentation**
   - Add more comprehensive API documentation
   - Provide additional usage examples

## Conclusion

The Decentralized Storage System provides a robust, secure framework for building distributed storage applications. With its combination of encryption, distributed architecture, and blockchain verification, it offers a unique solution for applications requiring high security and data integrity.

The system is now functional at the library level, with all core tests passing. Future work will focus on improving the binary components, completing integration testing, and enhancing documentation."
