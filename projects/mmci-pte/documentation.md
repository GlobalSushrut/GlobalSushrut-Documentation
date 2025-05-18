# mmci-pte - documentation

*This documentation is from the private repository mmci-pte.*

---

# MMCI-PTE: Technical Documentation

This documentation provides technical details on the MMCI-PTE (Mock Mechanics Cancer Intelligence - Probability Tensor Engine) system, including implementation details, component interactions, and API references.

## Table of Contents

1. [System Architecture](#system-architecture)
2. [Core Components](#core-components)
3. [Data Flow](#data-flow)
4. [API Reference](#api-reference)
5. [Implementation Details](#implementation-details)
6. [Testing Framework](#testing-framework)
7. [Appendix: Clinical References](#appendix-clinical-references)

## System Architecture

MMCI-PTE implements a modular architecture focused on integrating multiple diagnostic components through a unified adaptive consensus layer. This architecture allows the system to process real-world clinical data and adapt to new information dynamically.

### Key Architectural Principles:

1. **Component Isolation**: Each component can function independently with minimal dependencies
2. **Data Flow Transparency**: Merkle traces provide auditability for all diagnostic paths
3. **Adaptability**: System dynamically adjusts to new patient data or guideline changes
4. **Ethical Validation**: Built-in validation against clinical standards and ethical norms

## Core Components

### Mock Tensor Vector Matrix Engine

**Purpose**: Create symbolic tensor representations of patient biomarkers and diagnostic test results.

**Key Classes**:
- `SymbolicVector`: Represents biomarker values with confidence and risk attributes
- `MockTensorVectorEngine`: Creates and manages the symbolic tensor representation

**File**: `mmci_pte/adaptive_consensus_engine/tensor_engine.py`

**Features**:
- Converts raw medical values to symbolic representations
- Handles different dimensions of patient data (biomarkers, imaging, histology)
- Computes risk and confidence matrices for probabilistic evaluation
- Supports integration with other data sources through knowledge fusion

Example usage:
```python
# Initialize tensor engine
tensor_engine = MockTensorVectorEngine(
    dimension_names=["biomarker", "clinical", "imaging", "histology", "genetic"]
)

# Create and add a biomarker vector
vector = SymbolicVector(
    range=MockNodeRange(min_val=0.0, max_val=10.0),
    confidence=0.9,
    risk=0.7,
    name="PSA",
    source="biomarker"
)
node_id = tensor_engine.add_vector(0, 0, vector)
```

### Confidence Tree Parameter Analyzer

**Purpose**: Build diagnostic path graphs and validate confidence levels against safety thresholds.

**Key Classes**:
- `DiagnosisNode`: Represents a single node in the diagnostic path
- `DiagnosisPath`: Represents a complete diagnostic path with multiple nodes
- `ConfidenceTreeAnalyzer`: Main class for creating and analyzing diagnostic paths

**File**: `mmci_pte/adaptive_consensus_engine/confidence_tree.py`

**Features**:
- Creates branching diagnosis paths with confidence scores
- Verifies confidence against safety thresholds
- Suggests additional tests when confidence is insufficient
- Tracks diagnostic evolution over time

Example usage:
```python
# Initialize confidence tree
confidence_tree = ConfidenceTreeAnalyzer(
    safety_threshold=0.75,
    suggest_tests_threshold=0.6
)

# Create a diagnostic path
path = confidence_tree.create_path()
path_id = path.path_id

# Add diagnostic nodes
initial_node = confidence_tree.add_node(
    path_id,
    name="initial_evaluation",
    confidence=0.85,
    attributes={"findings": {"PSA": 6.8}}
)

# Verify path confidence
verification = confidence_tree.verify_confidence(path_id)
```

### Redis Cache Tree Learning

**Purpose**: Cache similar past cases and adapt thresholds based on feedback.

**Key Classes**:
- `CachedCase`: Represents a cached diagnostic case with outcomes
- `RedisCacheTree`: Manages the cache and similarity matching

**File**: `mmci_pte/adaptive_consensus_engine/redis_cache_tree.py`

**Features**:
- Stores and retrieves similar patient cases
- Performs similarity matching based on biomarker profiles
- Adapts to system feedback over time
- Supports both Redis database and in-memory fallback

Example usage:
```python
# Initialize Redis cache (with fallback)
redis_cache = RedisCacheTree(
    use_memory_fallback=True,
    similarity_threshold=0.7
)

# Create and cache a case
cached_case = create_cached_case_from_tensor(
    tensor_engine,
    patient_id="PST001",
    diagnosis={"cancer_type": "prostate", "stage": "II"}
)
case_id = redis_cache.cache_case(cached_case)

# Find similar cases
similar_cases = redis_cache.get_similar_cases(
    biomarkers={"PSA": 7.2, "PSA_free": 0.14},
    max_cases=3
)
```

### Mock RAG Knowledge Connector

**Purpose**: Retrieve relevant medical knowledge and fuse with patient data.

**Key Classes**:
- `KnowledgeItem`: Represents a piece of medical knowledge
- `SymbolicQuery`: Query for retrieving relevant knowledge
- `MockRAGConnector`: Manages knowledge retrieval and fusion

**File**: `mmci_pte/adaptive_consensus_engine/mock_rag_connector.py`

**Features**:
- Simulates retrieval-augmented generation for medical knowledge
- Connects symbolic patient data with relevant medical guidelines
- Generates query embeddings based on patient context
- Fuses knowledge with tensor representation

Example usage:
```python
# Initialize knowledge connector
rag_connector = MockRAGConnector()

# Create a query from patient data
query = SymbolicQuery(
    cancer_type="prostate",
    biomarkers=["PSA", "PSA_free"],
    stage="II"
)

# Retrieve relevant knowledge
knowledge_items = rag_connector.retrieve_knowledge(query, max_items=3)

# Fuse knowledge with tensor
trace_id = tensor_engine.fuse_with_knowledge(knowledge_items)
```

### Consensus of Actuality Engine

**Purpose**: Validate diagnostic predictions against medical rules and ethical norms.

**Key Classes**:
- `MedicalRule`: Represents a medical guideline or rule
- `ConsensusValidation`: Result of validation against rules
- `ConsensusValidator`: Performs validation against all rules

**File**: `mmci_pte/adaptive_consensus_engine/consensus_validator.py`

**Features**:
- Validates diagnoses against clinical guidelines
- Ensures ethical compliance (e.g., uncertainty disclosure)
- Verifies clinical and demographic-specific rules
- Generates human-readable validation reports

Example usage:
```python
# Initialize consensus validator
consensus_validator = ConsensusValidator(
    rules_config_path=None,  # No config file, add rules directly
    min_consensus_confidence=0.65,
    ethical_validation=True
)

# Add a rule
rule = MedicalRule(
    rule_id="prostate_psa_threshold",
    description="PSA levels above age-specific threshold require further evaluation",
    parameters={"thresholds": {"60-69": 4.5}},
    rule_type="biomarker_threshold",
    priority=2
)
consensus_validator.add_rule(rule)

# Validate a diagnosis
validation = consensus_validator.validate_diagnosis(
    diagnosis={"cancer_type": "prostate", "t_stage": "T2a"},
    patient_demographics={"age": 62, "sex": "M"},
    biomarkers={"PSA": 6.8},
    confidence=0.85
)

# Generate a report
report = create_validation_report(validation)
```

### Conformity Router & Injector

**Purpose**: Detect changes in patient data and inject updates into the diagnostic model.

**Key Classes**:
- `ChangeDetection`: Represents detected changes in patient data
- `UpdateResult`: Result of updating the diagnostic model
- `ConformityInjector`: Manages change detection and update injection

**File**: `mmci_pte/adaptive_consensus_engine/conformity_injector.py`

**Features**:
- Compares new patient data with previous assessments
- Detects significant changes in biomarkers and test results
- Injects updates into all components of the system
- Routes to alternative diagnostic paths when needed

Example usage:
```python
# Initialize conformity injector
conformity_injector = ConformityInjector(
    tensor_engine=tensor_engine,
    confidence_tree=confidence_tree,
    redis_cache=redis_cache,
    change_threshold=0.1,
    significant_change_threshold=0.3
)

# Store current state
conformity_injector.current_state["PST001"] = {
    "biomarkers": {"PSA": 6.8},
    "diagnosis": current_diagnosis
}

# Detect changes in follow-up data
changes = conformity_injector.detect_changes(
    patient_id="PST001",
    new_data={"biomarkers": {"PSA": 9.2}},
    current_state=conformity_injector.get_current_state("PST001")
)

# Inject updates
update_result = conformity_injector.inject_updates(
    patient_id="PST001",
    changes=changes,
    current_diagnosis=current_diagnosis
)
```

## Data Flow

The MMCI-PTE system processes data through several stages:

1. **Data Ingestion**: Patient biomarkers and clinical data are loaded into the system
2. **Tensor Creation**: Data is converted to symbolic tensor representations
3. **Knowledge Fusion**: Relevant medical knowledge is retrieved and integrated
4. **Confidence Analysis**: Diagnostic paths are created with confidence scores
5. **Consensus Validation**: Diagnoses are validated against medical rules
6. **Output Generation**: Final diagnostic assessments with confidence metrics
7. **Adaptation**: New patient data triggers change detection and updates

![Data Flow Diagram](https://via.placeholder.com/800x400?text=MMCI-PTE+Data+Flow+Diagram)

## API Reference

### Core API Methods

#### `MockTensorVectorEngine`

```python
def __init__(self, dimension_names: List[str] = None)
def add_vector(self, dim_i: int, dim_j: int, vector: SymbolicVector) -> str
def compute_risk_matrix(self) -> Tuple[np.ndarray, str]
def compute_confidence_matrix(self) -> Tuple[np.ndarray, str]
def fuse_with_knowledge(self, knowledge_vectors: Dict[str, SymbolicVector]) -> str
```

#### `ConfidenceTreeAnalyzer`

```python
def __init__(self, safety_threshold: float = 0.75, suggest_tests_threshold: float = 0.6)
def create_path(self) -> DiagnosisPath
def add_node(self, path_id: str, name: str, confidence: float, attributes: Dict = None) -> str
def add_multiple_paths(self, base_path_id: str, base_node_id: str, options: List[Tuple]) -> List[str]
def verify_confidence(self, path_id: str) -> Dict[str, Any]
def export_path_visualization(self, path_id: str) -> Dict[str, Any]
```

#### `ConsensusValidator`

```python
def __init__(self, rules_config_path: str = None, min_consensus_confidence: float = 0.65, ethical_validation: bool = True)
def add_rule(self, rule: MedicalRule) -> None
def validate_diagnosis(self, diagnosis: Dict, patient_demographics: Dict, biomarkers: Dict, confidence: float) -> ConsensusValidation
```

#### `ConformityInjector`

```python
def __init__(self, tensor_engine=None, confidence_tree=None, redis_cache=None, change_threshold: float = 0.1, significant_change_threshold: float = 0.3)
def detect_changes(self, patient_id: str, new_data: Dict, current_state: Dict) -> ChangeDetection
def inject_updates(self, patient_id: str, changes: ChangeDetection, current_diagnosis: Dict) -> UpdateResult
```

## Implementation Details

### Tensor Engine Implementation

The tensor engine uses symbolic vectors to represent patient data points with the following attributes:
- **Range**: Actual medical values with min/max ranges
- **Confidence**: Confidence score for this measurement (0-1)
- **Risk**: Associated risk factor for this measurement (0-1)
- **Name**: Identifier for the measurement
- **Source**: Type of data (biomarker, imaging, etc.)

These vectors are positioned in a multi-dimensional tensor space where dimensions represent different aspects of the patient's clinical profile.

### Confidence Tree Implementation

The confidence tree creates a directed acyclic graph (DAG) where:
- Nodes represent diagnostic decisions or test results
- Edges represent progression between diagnostic states
- Node attributes include confidence scores and metadata
- Paths represent complete diagnostic pathways

### Consensus Validation Implementation

The consensus validator uses a rule-based system where:
- Rules are defined with parameters, types, and priorities
- Rules can be specific to cancer types, demographics, or biomarkers
- Validation results include pass/fail status, confidence, and suggestions
- Ethical requirements are enforced with dedicated rule types

### Conformity Injector Implementation

The conformity injector implements:
- Field-by-field comparison between data snapshots
- Weighted change magnitude calculation
- Significance thresholds for triggering updates
- Component-specific update strategies

## Testing Framework

MMCI-PTE includes a comprehensive testing framework:

- **Unit Tests**: For individual components
- **Integration Tests**: For component interactions
- **System Tests**: For end-to-end validation
- **Demonstration Scripts**: For showcasing system capabilities

### Running Tests

```bash
# Run unit tests
python -m unittest discover -s tests/unit

# Run integration tests
python -m unittest discover -s tests/integration

# Run system tests
python -m unittest discover -s tests/system

# Run demonstration
python mmci_pte/adaptive_consensus_demo.py
```

## Appendix: Clinical References

The MMCI-PTE system is designed to work with standard clinical reference ranges and guidelines, including:

### Biomarker Reference Ranges

| Biomarker | Normal Range | Units | Cancer Association |
|-----------|--------------|-------|-------------------|
| PSA | 0-4.0 | ng/mL | Prostate |
| PSA Free | >0.25 | ratio | Prostate |
| PSA Density | <0.15 | ng/mL/cc | Prostate |
| CA-125 | 0-35 | U/mL | Ovarian |
| CEA | 0-2.5 | ng/mL | Colorectal |
| AFP | 0-10 | ng/mL | Liver, Testicular |
| CA 19-9 | 0-37 | U/mL | Pancreatic |

### TNM Staging

The system implements the AJCC 8th Edition TNM staging system for various cancer types, including detailed staging criteria for:

- Prostate Cancer
- Breast Cancer
- Lung Cancer
- Colorectal Cancer
- Melanoma

### Gleason Scoring (Prostate)

| Gleason Score | Grade Group | Risk Category |
|---------------|-------------|---------------|
| ≤6 | 1 | Low |
| 7 (3+4) | 2 | Intermediate Favorable |
| 7 (4+3) | 3 | Intermediate Unfavorable |
| 8 | 4 | High |
| 9-10 | 5 | Very High |
