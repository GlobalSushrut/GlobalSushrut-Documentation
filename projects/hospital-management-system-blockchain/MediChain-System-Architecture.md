# hospital-management-system-blockchain - MediChain-System-Architecture

*This documentation is from the private repository hospital-management-system-blockchain.*

---

# MediChain System Architecture & Component Integration

## Overview

MediChain is a comprehensive healthcare management platform built on a stack of interconnected technologies:

1. **Blockchain Layer** - Smart contracts for secure medical records and transactions
2. **Secure Database Layer** - Rust-based secure database for protected health information
3. **Backend Services Layer** - Dual backend architecture (Flask & Express) for advanced functionality
4. **Frontend Layer** - React-based user interface with specialized healthcare components

This document outlines how these components interact, data flows between systems, and the integration points that ensure seamless operation.

## System Architecture Diagram

```
┌───────────────────────────────────────────────────────────────────────┐
│                          Frontend Layer (React)                        │
│                                                                        │
│  ┌────────────┐  ┌────────────┐  ┌────────────┐  ┌────────────────┐   │
│  │   Patient  │  │   Doctor   │  │ Pharmacy   │  │ Admin          │   │
│  │  Dashboard │  │  Interface │  │ Interface  │  │ Dashboard      │   │
│  └─────┬──────┘  └──────┬─────┘  └─────┬──────┘  └───────┬────────┘   │
└────────┼───────────────┼──────────────┼───────────────┬─┼────────────┘
         │               │              │               │ │
         │               │              │               │ │ Authentication/
         │               │              │               │ │ Authorization
┌────────▼───────────────▼──────────────▼───────────────▼─▼────────────┐
│                      Express Backend (Node.js)                        │
│                                                                       │
│  ┌────────────┐  ┌────────────┐  ┌────────────┐  ┌────────────────┐  │
│  │ User Mgmt  │  │ Marketplace│  │ Insurance  │  │ Telemedicine   │  │
│  │  Services  │  │  Services  │  │  Services  │  │  Services      │  │
│  └─────┬──────┘  └──────┬─────┘  └─────┬──────┘  └───────┬────────┘  │
└────────┼───────────────┼──────────────┼───────────────┬─┼────────────┘
         │               │              │               │ │
         │               │              │               │ │ Cross-Backend
         │               │              │               │ │ Communication
┌────────▼───────────────▼──────────────▼───────────────▼─▼────────────┐
│                         Flask Backend (Python)                        │
│                                                                       │
│  ┌────────────┐  ┌────────────┐  ┌────────────┐  ┌────────────────┐  │
│  │ Blockchain │  │ Rust DB    │  │ Appointment│  │ Prescription   │  │
│  │ Interface  │  │ Interface  │  │ Services   │  │ Services       │  │
│  └─────┬──────┘  └──────┬─────┘  └─────┬──────┘  └───────┬────────┘  │
└────────┼───────────────┼──────────────┼───────────────┬─┼────────────┘
         │               │              │               │ │
         ▼               ▼              │               │ │
┌─────────────┐  ┌────────────────┐     │               │ │
│  Blockchain │  │ Rust Secure    │     │               │ │
│  Contracts  │  │    Database    │     │               │ │
└─────────────┘  └────────────────┘     │               │ │
                                        │               │ │
┌──────────────────────────────────────┐│               │ │
│      External Healthcare Services     ││               │ │
│                                       ││               │ │
│  ┌─────────────┐  ┌────────────────┐ ││               │ │
│  │ Lab Testing │  │ Insurance      │◄┘│               │ │
│  │  Services   │  │  Providers     │  │               │ │
│  └─────────────┘  └────────────────┘  │               │ │
│                                       │               │ │
│  ┌─────────────┐  ┌────────────────┐  │               │ │
│  │ Pharmacy    │  │ Emergency      │◄─┘               │ │
│  │  Networks   │  │  Services      │                  │ │
│  └─────────────┘  └────────────────┘                  │ │
│                                                        │ │
│  ┌─────────────────────────────────────────────────┐  │ │
│  │ Remote Monitoring and Telemedicine Providers    │◄─┘ │
│  └─────────────────────────────────────────────────┘    │
│                                                          │
│  ┌─────────────────────────────────────────────────┐    │
│  │ Regulatory and Health Authority Systems         │◄───┘
│  └─────────────────────────────────────────────────┘
└──────────────────────────────────────────────────────────┘
```

## Component Integration Points

### 1. Frontend to Express Backend Integration

The React frontend communicates with the Express backend through RESTful API calls:

```json
{
  "integration_point": "Frontend to Express",
  "primary_interfaces": [
    "/api/users/*",
    "/api/marketplace/*",
    "/api/insurance/*",
    "/api/telemedicine/*"
  ],
  "data_flow": "Bidirectional",
  "auth_mechanism": "JWT Tokens",
  "communication_protocol": "HTTPS/REST"
}
```

### 2. Express to Flask Backend Integration

The Express backend communicates with the Flask backend for specialized functionality:

```json
{
  "integration_point": "Express to Flask",
  "primary_interfaces": [
    "/api/v1/blockchain/*",
    "/api/v1/prescription/*",
    "/api/v1/appointment/*",
    "/api/v1/marketplace/*"
  ],
  "data_flow": "Bidirectional",
  "auth_mechanism": "API Keys",
  "communication_protocol": "HTTPS/REST"
}
```

### 3. Flask to Blockchain Integration

The Flask backend communicates with the blockchain through Web3 interfaces:

```json
{
  "integration_point": "Flask to Blockchain",
  "primary_interfaces": [
    "MedicalRecordContract",
    "PatientRegistryContract",
    "PrescriptionContract",
    "InsuranceVerificationContract"
  ],
  "data_flow": "Bidirectional",
  "auth_mechanism": "Private Keys",
  "communication_protocol": "Web3/JSON-RPC"
}
```

### 4. Flask to Rust Secure Database Integration

The Flask backend communicates with the Rust secure database for sensitive data:

```json
{
  "integration_point": "Flask to RustDB",
  "primary_interfaces": [
    "PatientRecords",
    "MedicalHistory",
    "PrescriptionData",
    "InsuranceInformation"
  ],
  "data_flow": "Bidirectional",
  "auth_mechanism": "Encrypted API Keys",
  "communication_protocol": "Custom Secure Protocol"
}
```

## Data Flow Diagrams

### 1. Telemedicine Consultation Flow

```
┌─────────┐    1. Book    ┌──────────┐   2. Process   ┌───────────┐
│ Patient │───────────────► Express  │────────────────► Flask     │
│ Frontend│               │ Backend  │                │ Backend   │
└────┬────┘               └────┬─────┘                └─────┬─────┘
     │                         │                            │
     │                         │                            │
     │                         │                            │ 3. Verify Doctor
     │                         │                            │    Credentials
     │                         │                            ▼
     │                         │                      ┌───────────┐
     │                         │                      │ Blockchain│
     │                         │                      └─────┬─────┘
     │                         │                            │
     │                         │                            │
     │                         │ 4. Return                  │
     │                         │    Status                  │
     │                         ▼                            │
     │                    ┌──────────┐                      │
     │                    │ Insurance │                      │
     │                    │ Service   │                      │
     │                    └────┬──────┘                      │
     │ 7. Confirmation         │                            │
     │    & Details            │                            │
     ◄────────────────────────┴────────────────────────────┘
                              5. Verify
                                Coverage
                                    │
                                    ▼
                              ┌───────────┐
                              │ Insurance │
                              │ Provider  │
                              └───────────┘
```

### 2. Digital Prescription and Marketplace Flow

```
┌─────────┐    1. Create   ┌──────────┐   2. Process   ┌───────────┐
│ Doctor  │───────────────► Flask    │────────────────► Blockchain │
│ Frontend│               │ Backend  │                │           │
└────┬────┘               └────┬─────┘                └─────┬─────┘
     │                         │                            │
     │                         │ 3. Store                   │
     │                         │    Details                 │
     │                         ▼                            │
     │                    ┌──────────┐                      │
     │                    │ Rust DB  │                      │
     │                    └────┬─────┘                      │
     │                         │                            │
     │                         │                            │
     │                         │                            │
     │                         │                            │
┌────▼────┐   5. Access   ┌────▼─────┐   4. Notify    ┌────▼─────┐
│ Patient │◄──────────────┤ Express  │◄───────────────┤ Flask    │
│ Frontend│               │ Backend  │                │ Backend  │
└────┬────┘               └────┬─────┘                └──────────┘
     │                         │
     │ 6. Order                │
     │    Medication           │
     │                         │
     ▼                         ▼
┌────────────┐            ┌──────────┐
│ Marketplace │            │ Pharmacy │
│ Interface  │            │ Service  │
└────────────┘            └──────────┘
```

## API Contract Specifications

### Express Backend API Contracts

#### 1. Telemedicine API

```yaml
/api/telemedicine/consultation:
  post:
    summary: Book a telemedicine consultation
    parameters:
      - name: doctorId
        required: true
      - name: patientId
        required: true
      - name: symptoms
        required: true
      - name: preferredTime
        required: true
    responses:
      200:
        description: Consultation booked successfully
        schema:
          properties:
            consultationId:
              type: string
            doctorDetails:
              type: object
            appointmentTime:
              type: string
            joinUrl:
              type: string
      400:
        description: Invalid request
      401:
        description: Unauthorized
```

#### 2. Insurance API

```yaml
/api/insurance/verify-coverage:
  post:
    summary: Verify insurance coverage for a procedure
    parameters:
      - name: consultationId
        required: true
      - name: insuranceId
        required: true
      - name: procedureCode
        required: true
    responses:
      200:
        description: Coverage verification successful
        schema:
          properties:
            covered:
              type: boolean
            copayAmount:
              type: number
            deductibleApplied:
              type: number
            authorizationCode:
              type: string
      400:
        description: Invalid request
      404:
        description: Insurance or procedure not found
```

#### 3. Marketplace API

```yaml
/api/marketplace/orders:
  post:
    summary: Place an order for marketplace items
    parameters:
      - name: items
        required: true
        schema:
          type: array
          items:
            properties:
              productId:
                type: string
              quantity:
                type: integer
      - name: prescriptionId
        required: false
      - name: deliveryAddress
        required: true
    responses:
      200:
        description: Order placed successfully
        schema:
          properties:
            orderId:
              type: string
            totalAmount:
              type: number
            estimatedDelivery:
              type: string
      400:
        description: Invalid request
      401:
        description: Unauthorized
      403:
        description: Prescription required for this item
```

### Flask Backend API Contracts

#### 1. Prescription API

```yaml
/api/v1/prescription/add:
  post:
    summary: Create a new prescription
    parameters:
      - name: patient_id
        required: true
      - name: doctor_id
        required: true
      - name: medications
        required: true
        schema:
          type: array
          items:
            properties:
              name:
                type: string
              dosage:
                type: string
              frequency:
                type: string
              duration:
                type: string
      - name: diagnosis
        required: true
    responses:
      200:
        description: Prescription created successfully
        schema:
          properties:
            status:
              type: string
            prescription:
              type: object
      400:
        description: Invalid request
      401:
        description: Unauthorized
```

#### 2. Blockchain API

```yaml
/api/v1/blockchain/verify-patient:
  get:
    summary: Verify patient data on blockchain
    parameters:
      - name: Authorization
        in: header
        required: true
    responses:
      200:
        description: Verification successful
        schema:
          properties:
            verified:
              type: boolean
            blockNumber:
              type: integer
            transactionHash:
              type: string
      401:
        description: Unauthorized
      404:
        description: Patient not found on blockchain
```

## Smart Contract Integration Points

### 1. Medical Record Contract

```solidity
interface MedicalRecordContract {
    function addRecord(address patient, bytes32 recordHash, uint256 timestamp) external returns (uint256);
    function getRecord(uint256 recordId) external view returns (address, bytes32, uint256);
    function verifyRecord(uint256 recordId, bytes32 recordHash) external view returns (bool);
    function grantAccess(uint256 recordId, address provider) external;
    function revokeAccess(uint256 recordId, address provider) external;
}
```

### 2. Prescription Contract

```solidity
interface PrescriptionContract {
    function issuePrescription(address patient, address doctor, bytes32 medicationHash) external returns (uint256);
    function verifyPrescription(uint256 prescriptionId) external view returns (bool);
    function fillPrescription(uint256 prescriptionId, address pharmacy) external;
    function getPrescriptionStatus(uint256 prescriptionId) external view returns (uint8);
}
```

### 3. Insurance Verification Contract

```solidity
interface InsuranceVerificationContract {
    function verifyInsurance(address patient, address provider, bytes32 procedureCode) external view returns (bool);
    function submitClaim(address patient, address provider, bytes32 procedureCode, uint256 amount) external returns (uint256);
    function getClaimStatus(uint256 claimId) external view returns (uint8);
    function settlePayment(uint256 claimId) external;
}
```

## Database Schema (Rust Secure DB)

The Rust secure database uses the following core schema:

```
Table: Patients
  - Id: UUID (primary key)
  - BlockchainAddress: String
  - Name: EncryptedString
  - DateOfBirth: EncryptedDate
  - MedicalHistory: EncryptedJSON
  - InsuranceInfo: EncryptedJSON
  - ContactInfo: EncryptedJSON

Table: Doctors
  - Id: UUID (primary key)
  - BlockchainAddress: String
  - Name: EncryptedString
  - Specialization: String
  - License: EncryptedString
  - ContactInfo: EncryptedJSON
  - TelemedicineCapabilities: JSON

Table: Prescriptions
  - Id: UUID (primary key)
  - PatientId: UUID (foreign key)
  - DoctorId: UUID (foreign key)
  - Medications: EncryptedJSON
  - Diagnosis: EncryptedString
  - IssuedDate: Date
  - ExpiryDate: Date
  - Status: String
  - BlockchainReference: String

Table: Appointments
  - Id: UUID (primary key)
  - PatientId: UUID (foreign key)
  - DoctorId: UUID (foreign key)
  - DateTime: DateTime
  - Purpose: EncryptedString
  - Status: String
  - Type: String (In-person/Remote)
  - Notes: EncryptedString
```

## Security Integration

The MediChain system implements several security integration mechanisms:

1. **Blockchain Verification Layers**
   - All critical medical transactions are recorded on the blockchain
   - Digital signatures verify authenticity of records
   - Smart contracts enforce access control

2. **Encryption Layers**
   - End-to-end encryption for all patient data
   - Encrypted communication channels between all components
   - Hardware-backed key storage where available

3. **Authentication & Authorization**
   - JWT-based authentication for API access
   - Role-based access control
   - Multi-factor authentication for sensitive operations
   - Biometric verification options for high-security contexts

4. **Audit Trail**
   - Comprehensive logging across all components
   - Blockchain-based immutable audit records for critical operations
   - Real-time monitoring for suspicious activity

## Testing Strategy

The integration testing strategy covers:

1. **Component Testing**
   - Unit tests for each backend component
   - Smart contract testing with Truffle
   - Rust database testing with integration test suite

2. **Integration Testing**
   - Backend-to-backend API integration tests
   - Backend-to-blockchain integration tests
   - Database integration tests

3. **End-to-End Testing**
   - Complete patient journey scenarios
   - Telemedicine consultation flow testing
   - Prescription issuance to marketplace fulfillment flow

4. **Security Testing**
   - Penetration testing for each component
   - Cryptographic verification testing
   - Access control boundary testing

## Deployment Architecture

The MediChain system is designed for deployment in a containerized environment:

```
┌─────────────────────────────┐
│     Load Balancer Layer     │
└─────────────┬───────────────┘
              │
┌─────────────▼───────────────┐
│      Kubernetes Cluster     │
│                             │
│  ┌───────────┐ ┌──────────┐ │
│  │ Frontend  │ │ Express  │ │
│  │ Containers│ │Containers│ │
│  └───────────┘ └──────────┘ │
│                             │
│  ┌───────────┐ ┌──────────┐ │
│  │  Flask    │ │  RustDB  │ │
│  │ Containers│ │Containers│ │
│  └───────────┘ └──────────┘ │
└─────────────────────────────┘
              │
┌─────────────▼───────────────┐
│   Persistent Storage Layer   │
│                             │
│  ┌───────────┐ ┌──────────┐ │
│  │  Database │ │ Blockchain│ │
│  │  Volumes  │ │  Nodes   │ │
│  └───────────┘ └──────────┘ │
└─────────────────────────────┘
```

## Conclusion

This architecture document provides a comprehensive overview of the MediChain system components and their integration points. The multi-layered architecture ensures:

1. **Security** - Through blockchain verification, encryption, and secure databases
2. **Scalability** - Via containerized deployment and microservices architecture
3. **Interoperability** - Through standardized API contracts and data formats
4. **Regulatory Compliance** - By enforcing data protection at all levels

Implementation should follow this architecture to ensure all components work together seamlessly while maintaining the highest standards of security and performance.
