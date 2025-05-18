# drug-ACC - how_to_use

*This documentation is from the private repository drug-ACC.*

---

# How to Use Drug ACC

This guide provides comprehensive instructions for installing, configuring, and using the Drug ACC system for pharmaceutical data analysis.

## Installation

### Prerequisites

- Python 3.8+
- Pip package manager
- 8GB+ RAM recommended for large datasets
- NumPy, Pandas, NetworkX, Matplotlib, SciPy

### Install from Source

```bash
# Clone repository
git clone https://github.com/your-org/drug_ACC.git
cd drug_ACC

# Install dependencies
pip install -r requirements.txt

# Install in development mode
pip install -e .
```

## Getting Started

### Basic Usage Pattern

```python
from drug_acc.core.data_loader import DataLoader
from drug_acc.utils.visualizer import Visualizer

# Initialize with configuration
config = {
    'patients_path': 'path/to/patients.csv',
    'drugs_path': 'path/to/drugs.csv',
    'outcomes_path': 'path/to/outcomes.csv',
    'genetics_path': 'path/to/genetics.csv',
    'trial_results_path': 'path/to/trial_results.csv',
    'auto_clean': True,  # Enable automatic data cleaning
    'cleaning_config': {
        'missing_threshold': 0.5,
        'outlier_threshold': 3.0
    }
}

# Load and clean data
loader = DataLoader(config)
data = loader.load_all_data()

# Analyze the data
# (See full example in the next section)
```

## Complete Analysis Pipeline

Here's a step-by-step guide to running a complete analysis:

```python
import os
import logging
from drug_acc.core.data_loader import DataLoader
from drug_acc.core.node_mesh_builder import NodeMeshBuilder
from drug_acc.core.mock_mesh_transformer import MockMeshTransformer
from drug_acc.core.sparsity_brut import SparsityBrutLearning
from drug_acc.core.prime_tree import PrimeTreeBuilder
from drug_acc.core.shadow_analyzer import ShadowPointAnalyzer
from drug_acc.utils.visualizer import Visualizer

# Configure logging
logging.basicConfig(level=logging.INFO)

# 1. Load and clean data
config = {
    'patients_path': 'data/patients.csv',
    'drugs_path': 'data/drugs.csv',
    'outcomes_path': 'data/outcomes.csv',
    'genetics_path': 'data/genetics.csv',
    'trial_results_path': 'data/trial_results.csv',
    'auto_clean': True
}
loader = DataLoader(config)
data = loader.load_all_data()

# 2. Build node mesh
mesh_builder = NodeMeshBuilder()
mesh_builder.build_nodes(data)
graph = mesh_builder.build_edges(data)

# 3. Transform mesh
transformer = MockMeshTransformer()
adj_matrix, node_mapping = mesh_builder.get_adjacency_matrix()
transformed_values = transformer.transform_mesh(adj_matrix.values)
transformed_graph = graph.copy()
# (Apply transformations to graph edges)

# 4. Analyze sparsity
sparsity_analyzer = SparsityBrutLearning()
sparsity_level = sparsity_analyzer.compute_sparsity(adj_matrix)
paths = sparsity_analyzer.find_all_paths(transformed_graph, 'drug', 'outcome')
critical_paths = sparsity_analyzer.find_critical_paths(transformed_graph, paths)

# 5. Build prime tree
prime_builder = PrimeTreeBuilder(config={'max_prime': 3000})
prime_tree = prime_builder.create_prime_subgraph(transformed_graph)

# 6. Analyze with shadow points
shadow_analyzer = ShadowPointAnalyzer()
node_ids = list(transformed_graph.nodes())
adj_array = adj_matrix.values
shadow_analyzer.decompose(adj_array)
importance = shadow_analyzer.calculate_node_importance(node_ids)

# 7. Visualize results
visualizer = Visualizer(output_dir="outputs")
visualizer.visualize_mesh(graph, title="Drug Network")
visualizer.visualize_sparsity({"sparsity_level": sparsity_level}, title="Network Sparsity")
```

## Configuration Options

### DataLoader Configuration

| Option | Description | Default |
|--------|-------------|---------|
| `auto_clean` | Enable automatic data cleaning | `True` |
| `missing_threshold` | Maximum ratio of missing values | `0.5` |
| `outlier_threshold` | Z-score threshold for outliers | `3.0` |
| `apply_mock_to_age` | Apply mock integer to age values | `True` |
| `apply_mock_to_potency` | Apply mock integer to drug potency | `True` |

### NodeMeshBuilder Configuration

| Option | Description | Default |
|--------|-------------|---------|
| `use_mock_integer` | Use mock integer for edge weights | `True` |
| `edge_threshold` | Minimum weight for edges | `0.1` |

### SparsityBrutLearning Configuration

| Option | Description | Default |
|--------|-------------|---------|
| `threshold` | Sparsity threshold | `0.8` |
| `max_path_length` | Maximum path length to consider | `3` |

### PrimeTreeBuilder Configuration

| Option | Description | Default |
|--------|-------------|---------|
| `max_prime` | Maximum prime number to use | `1000` |

### ShadowPointAnalyzer Configuration

| Option | Description | Default |
|--------|-------------|---------|
| `n_components` | Number of components to use | `5` |
| `threshold_percentile` | Percentile for shadow points | `90` |

## Working with Different Data Types

### Patient Data Format

Expected CSV columns:
- `patient_id`: Unique patient identifier
- `age`: Patient age
- `gender`: Patient gender
- (Other demographic fields)

### Drug Data Format

Expected CSV columns:
- `drug_id`: Unique drug identifier
- `name`: Drug name
- `potency`: Drug potency value
- (Other drug properties)

### Trial Results Format

Expected CSV columns:
- `source`: Source entity ID (e.g., patient, drug)
- `target`: Target entity ID (e.g., outcome)
- `source_type`: Type of source entity
- `target_type`: Type of target entity
- `weight`: Relationship strength
- `relationship`: Type of relationship

## Common Use Cases

### 1. Data Cleaning and Preparation

```python
from drug_acc.utils.data_cleaner import DataCleaner

# Initialize cleaner
cleaner = DataCleaner()

# Clean patient data
clean_patients = cleaner.clean_dataset(messy_patients_df, 'patients')

# Visualize cleaning results
visualizer = Visualizer()
visualizer.visualize_cleaning_comparison(
    messy_patients_df, clean_patients,
    columns=['age'], title="Patient Age Cleaning"
)
```

### 2. Network Analysis

```python
# Find critical paths between drugs and outcomes
paths = sparsity_analyzer.find_all_paths(graph, 'drug', 'outcome')
critical = sparsity_analyzer.find_critical_paths(graph, paths, weight_threshold=0.7)

# Calculate path statistics
stats = sparsity_analyzer.calculate_path_statistics(graph, critical)
print(f"Most common transitions: {stats['type_transitions']}")
```

### 3. Matrix Decomposition

```python
# Perform SVD on adjacency matrix
U, S, Vt = shadow_analyzer.decompose(adj_matrix.values)

# Get explained variance
variance = shadow_analyzer.get_explained_variance_ratio()
print(f"Top 5 components explain {sum(variance[:5])*100:.2f}% of variance")

# Find shadow points (significant factors)
shadow_points = shadow_analyzer.get_shadow_points(threshold_percentile=85)
```

## Advanced Topics

### Custom Data Cleaning Rules

You can extend the `DataCleaner` class to implement custom cleaning rules:

```python
from drug_acc.utils.data_cleaner import DataCleaner

class CustomCleaner(DataCleaner):
    def clean_my_special_dataset(self, df):
        # Apply standard cleaning
        df = self.handle_missing_values(df)
        
        # Apply custom rules
        df['special_column'] = df['special_column'].apply(my_transformation)
        
        return df
```

### Performance Optimization

For large datasets:

1. Enable batch processing:
   ```python
   loader = DataLoader(config={'batch_size': 10000})
   ```

2. Use sparse matrices for large networks:
   ```python
   import scipy.sparse as sp
   sparse_adj = sp.csr_matrix(adj_matrix.values)
   ```

3. Limit path search depth:
   ```python
   paths = sparsity_analyzer.find_all_paths(
       graph, 'drug', 'outcome', max_length=2
   )
   ```

## Troubleshooting

### Common Issues

1. **Memory Errors**: For large datasets, increase batch size and use sparse matrices.
2. **Long Computation Times**: Reduce path search depth, limit network size.
3. **Missing Values in Results**: Check cleaning thresholds and imputation methods.
4. **Visualization Errors**: Ensure output directory exists and is writable.

### Logging

Enable detailed logging to diagnose issues:

```python
import logging
logging.basicConfig(
    level=logging.DEBUG,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    filename='drug_acc_debug.log'
)
```

## Further Resources

- Example notebooks: `examples/` directory
- API Documentation: `docs/api/`
- Test datasets: `data/test/`
