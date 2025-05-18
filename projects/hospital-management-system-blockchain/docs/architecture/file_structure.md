# hospital-management-system-blockchain - file_structure

*This documentation is from the private repository hospital-management-system-blockchain.*

---

# Hospital Management System - File Structure Documentation

## Project Overview

This documentation outlines the file structure of the restructured Hospital Management System, which has been organized into a clean, industry-standard architecture with proper separation of concerns between components.

## Main Components

The system consists of the following primary components:

1. **Backend** - RESTful API services implemented with Flask
2. **Frontend** - React-based user interface
3. **Medical Chain** - Blockchain for secure medical data storage
4. **Rust Secure DB** - High-performance secure database implemented in Rust
5. **Orchestration** - Deployment and infrastructure configuration
6. **Documentation** - Comprehensive system documentation

## Directory Structure

### 1. Backend (`/restructured/backend`)

```
backend/
├── app/                        # Main application directory
│   ├── auth/                   # Authentication service
│   │   ├── controllers/        # Business logic controllers
│   │   ├── models/             # Data models
│   │   └── api/                # API endpoints
│   │       └── routes.py       # User authentication endpoints
│   ├── kyc/                    # KYC verification service
│   │   ├── controllers/        # KYC verification controllers
│   │   ├── models/             # KYC data models
│   │   └── api/                # API endpoints
│   │       └── routes.py       # KYC verification endpoints
│   ├── treatment/              # Treatment management service
│   │   ├── controllers/        # Treatment controllers
│   │   ├── models/             # Treatment data models
│   │   └── api/                # API endpoints
│   │       └── routes.py       # Treatment record endpoints
│   ├── appointment/            # Appointment management service
│   │   ├── controllers/        # Appointment controllers
│   │   ├── models/             # Appointment data models
│   │   └── api/                # API endpoints
│   │       └── routes.py       # Appointment scheduling endpoints
│   ├── prescription/           # Prescription management service
│   │   ├── controllers/        # Prescription controllers
│   │   ├── models/             # Prescription data models
│   │   └── api/                # API endpoints
│   │       └── routes.py       # Prescription endpoints
│   ├── payment/                # Payment processing service
│   │   ├── controllers/        # Payment controllers
│   │   ├── models/             # Payment data models
│   │   └── api/                # API endpoints
│   │       └── routes.py       # Payment endpoints
│   ├── department/             # Department management service
│   │   ├── controllers/        # Department controllers
│   │   ├── models/             # Department data models
│   │   └── api/                # API endpoints
│   │       └── routes.py       # Department endpoints
│   ├── utils/                  # Utility functions and helpers
│   │   ├── blockchain.py       # Blockchain utilities
│   │   ├── security.py         # Security utilities
│   │   ├── validators.py       # Data validation utilities
│   │   └── helpers.py          # General helper functions
│   ├── middlewares/            # Request/response middlewares
│   │   ├── auth.py             # Authentication middleware
│   │   ├── logging.py          # Logging middleware
│   │   └── error_handler.py    # Error handling middleware
│   ├── schemas/                # Serialization schemas
│   │   ├── auth.py             # Authentication schemas
│   │   ├── user.py             # User schemas
│   │   └── medical.py          # Medical data schemas
│   └── config/                 # Configuration
│       ├── __init__.py         # Configuration initialization
│       ├── development.py      # Development configuration
│       └── production.py       # Production configuration
├── tests/                      # Automated tests
│   ├── unit/                   # Unit tests
│   ├── integration/            # Integration tests
│   └── fixtures/               # Test fixtures
├── migrations/                 # Database migrations
├── run.py                      # Application entry point
├── wsgi.py                     # WSGI entry point
└── requirements.txt            # Python dependencies
```

### 2. Frontend (`/restructured/frontend`)

```
frontend/
├── public/                     # Public assets
│   ├── index.html              # HTML entry point
│   ├── favicon.ico             # Favicon
│   └── assets/                 # Static assets
├── src/                        # Source code
│   ├── assets/                 # Frontend assets
│   │   ├── images/             # Image files
│   │   ├── styles/             # Global styles
│   │   └── fonts/              # Font files
│   ├── components/             # Reusable UI components
│   │   ├── common/             # Common components
│   │   │   ├── Button.jsx      # Button component
│   │   │   ├── Modal.jsx       # Modal component
│   │   │   └── ...             # Other common components
│   │   ├── layout/             # Layout components
│   │   │   ├── Header.jsx      # Header component
│   │   │   ├── Sidebar.jsx     # Sidebar component
│   │   │   └── Footer.jsx      # Footer component
│   │   └── dashboard/          # Dashboard components
│   │       ├── Chart.jsx       # Chart component
│   │       ├── Stats.jsx       # Statistics component
│   │       └── ...             # Other dashboard components
│   ├── pages/                  # Application pages
│   │   ├── auth/               # Authentication pages
│   │   │   ├── Login.jsx       # Login page
│   │   │   ├── Register.jsx    # Registration page
│   │   │   └── ForgotPassword.jsx  # Password recovery page
│   │   ├── dashboard/          # Dashboard pages
│   │   │   ├── Patient.jsx     # Patient dashboard
│   │   │   ├── Doctor.jsx      # Doctor dashboard
│   │   │   └── Admin.jsx       # Admin dashboard
│   │   ├── appointment/        # Appointment pages
│   │   ├── prescription/       # Prescription pages
│   │   ├── treatment/          # Treatment pages
│   │   ├── payment/            # Payment pages
│   │   └── settings/           # Settings pages
│   ├── services/               # Service integrations
│   │   ├── api/                # API services
│   │   │   ├── ApiService.jsx  # Base API service
│   │   │   └── index.js        # API service exports
│   │   ├── auth/               # Authentication services
│   │   │   └── index.js        # Auth service exports
│   │   ├── blockchain/         # Blockchain services
│   │   ├── verification/       # Verification services
│   │   │   ├── KYCVerificationController.jsx  # KYC verification
│   │   │   ├── DocumentVerificationService.jsx  # Document verification
│   │   │   ├── FaceRecognitionService.jsx     # Face recognition
│   │   │   └── BlockchainVerificationService.jsx  # Blockchain verification
│   │   └── KeycloakService.jsx # Keycloak authentication service
│   ├── hooks/                  # Custom React hooks
│   │   ├── useAuth.js          # Authentication hook
│   │   └── useApi.js           # API hook
│   ├── utils/                  # Utility functions
│   │   ├── formatters.js       # Data formatting utilities
│   │   ├── validators.js       # Form validation utilities
│   │   └── helpers.js          # Helper functions
│   ├── store/                  # State management
│   │   ├── actions/            # Redux actions
│   │   ├── reducers/           # Redux reducers
│   │   └── index.js            # Store configuration
│   ├── config/                 # Frontend configuration
│   │   └── index.js            # Configuration constants
│   ├── routes.js               # Application routes
│   ├── App.jsx                 # Main application component
│   └── index.jsx               # Application entry point
├── .eslintrc.js                # ESLint configuration
├── .prettierrc                 # Prettier configuration
└── package.json                # NPM dependencies
```

### 3. Medical Chain (`/restructured/medical-chain`)

```
medical-chain/
├── contracts/                  # Smart contracts
│   ├── KYCVerification.sol     # KYC verification contract
│   ├── MedicalRecords.sol      # Medical records contract
│   ├── PatientIdentity.sol     # Patient identity contract
│   └── AccessControl.sol       # Access control contract
├── migrations/                 # Migration scripts
│   └── deploy_contracts.js     # Contract deployment script
├── scripts/                    # Utility scripts
│   ├── deploy-local.js         # Local deployment script
│   └── deploy-aws.js           # AWS deployment script
├── test/                       # Contract tests
│   ├── kyc.test.js             # KYC contract tests
│   └── medical.test.js         # Medical records tests
├── genesis/                    # Genesis configuration
│   └── genesis.json            # Genesis block configuration
├── client/                     # Blockchain client
│   ├── index.js                # Client entry point
│   └── api.js                  # Client API
├── truffle-config.js           # Truffle configuration
├── hardhat.config.js           # Hardhat configuration
└── requirements.txt            # Python dependencies
```

### 4. Rust Secure DB (`/restructured/rust-db`)

```
rust-db/
├── src/                        # Source code
│   ├── lib.rs                  # Library entry point
│   ├── main.rs                 # Application entry point
│   ├── db/                     # Database modules
│   │   ├── mod.rs              # Module definition
│   │   ├── models.rs           # Data models
│   │   └── schema.rs           # Database schema
│   ├── api/                    # API modules
│   │   ├── mod.rs              # Module definition
│   │   ├── routes.rs           # API routes
│   │   └── handlers.rs         # Request handlers
│   ├── auth/                   # Authentication modules
│   │   ├── mod.rs              # Module definition
│   │   └── jwt.rs              # JWT authentication
│   └── utils/                  # Utility modules
│       ├── mod.rs              # Module definition
│       ├── crypto.rs           # Cryptography utilities
│       └── error.rs            # Error handling
├── tests/                      # Integration tests
├── migrations/                 # Database migrations
└── Cargo.toml                  # Cargo configuration
```

### 5. Orchestration (`/restructured/orchestration`)

```
orchestration/
├── docker/                     # Docker configurations
│   ├── backend/                # Backend Dockerfile
│   ├── frontend/               # Frontend Dockerfile
│   ├── medical-chain/          # Medical chain Dockerfile
│   ├── rust-db/                # Rust DB Dockerfile
│   └── docker-compose.yml      # Compose configuration
├── kubernetes/                 # Kubernetes configurations
│   ├── backend/                # Backend K8s configs
│   ├── frontend/               # Frontend K8s configs
│   ├── medical-chain/          # Medical chain K8s configs
│   ├── rust-db/                # Rust DB K8s configs
│   └── ingress.yaml            # Ingress configuration
├── terraform/                  # Infrastructure as code
│   ├── main.tf                 # Main Terraform config
│   ├── variables.tf            # Terraform variables
│   └── outputs.tf              # Terraform outputs
├── scripts/                    # Deployment scripts
│   ├── deploy-aws.sh           # AWS deployment script
│   ├── deploy-azure.sh         # Azure deployment script
│   └── deploy-gcp.sh           # GCP deployment script
└── monitoring/                 # Monitoring configurations
    ├── prometheus/             # Prometheus configuration
    ├── grafana/                # Grafana dashboards
    └── loki/                   # Loki configuration
```

### 6. Documentation (`/restructured/docs`)

```
docs/
├── architecture/               # Architecture documentation
│   ├── system_overview.md      # System overview
│   ├── file_structure.md       # File structure documentation
│   ├── data_flow.md            # Data flow diagrams
│   └── security.md             # Security architecture
├── api/                        # API documentation
│   ├── auth.md                 # Authentication API
│   ├── kyc.md                  # KYC API
│   ├── medical.md              # Medical records API
│   └── payment.md              # Payment API
├── user-guides/                # User guides
│   ├── patient.md              # Patient guide
│   ├── doctor.md               # Doctor guide
│   ├── admin.md                # Admin guide
│   └── staff.md                # Staff guide
├── development/                # Development guides
│   ├── setup.md                # Development setup
│   ├── coding_standards.md     # Coding standards
│   ├── testing.md              # Testing guide
│   └── contribution.md         # Contribution guide
├── deployment/                 # Deployment guides
│   ├── local.md                # Local deployment
│   ├── aws.md                  # AWS deployment
│   ├── azure.md                # Azure deployment
│   └── gcp.md                  # GCP deployment
└── blockchain/                 # Blockchain documentation
    ├── smart_contracts.md      # Smart contract documentation
    ├── node_setup.md           # Node setup guide
    └── integration.md          # Integration guide
```

## Configuration Files

The system includes several key configuration files:

1. **Environment Variables**
   - `.env` files for each component containing environment-specific variables

2. **API Configuration**
   - `backend/app/config/*.py` for API configuration
   - `frontend/src/config/*.js` for frontend API endpoints

3. **Blockchain Configuration**
   - `medical-chain/genesis/genesis.json` for blockchain genesis configuration
   - `medical-chain/truffle-config.js` and `hardhat.config.js` for contract deployment

4. **Database Configuration**
   - `rust-db/src/db/config.rs` for database connection and settings

5. **Deployment Configuration**
   - Docker and Kubernetes configuration files in the orchestration directory

## Dependencies

Each component has its own dependency management file:

- Backend: `requirements.txt`
- Frontend: `package.json`
- Medical Chain: `requirements.txt` and contract dependencies
- Rust DB: `Cargo.toml`

This architecture ensures clean separation of concerns while maintaining efficient communication between components through well-defined interfaces.
