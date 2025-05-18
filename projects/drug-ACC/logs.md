# drug-ACC - logs

*This documentation is from the private repository drug-ACC.*

---

# Drug ACC System Logging

Drug ACC implements a comprehensive logging system that provides detailed insights into all operations and processes, helping users track, debug, and audit the system's behavior.

## Logging Architecture

The Drug ACC system uses Python's built-in `logging` module with a hierarchical logger structure:

- Root logger: `drug_acc`
- Component loggers: 
  - `drug_acc.data_loader`
  - `drug_acc.data_cleaner`
  - `drug_acc.mock_integer`
  - `drug_acc.node_mesh_builder`
  - `drug_acc.mock_mesh_transformer`
  - `drug_acc.sparsity_brut`
  - `drug_acc.prime_tree`
  - `drug_acc.shadow_analyzer`
  - `drug_acc.visualizer`
  - `drug_acc.error`

## Log Levels

The system uses standard log levels to categorize messages:

- **DEBUG**: Detailed information for debugging
- **INFO**: General information about system operation
- **WARNING**: Potential issues that don't affect operation
- **ERROR**: Issues that impede functionality
- **CRITICAL**: Critical failures that prevent system operation

## What Gets Logged

### Data Processing & Cleaning
- Data loading operations with row counts
- Applied cleaning operations (e.g., missing value imputation, outlier detection)
- Data transformation details with before/after statistics
- Mock integer fluctuations and their statistical effects

### Graph Building & Analysis
- Node and edge creation with counts
- Sparsity and complexity metrics
- Path discovery statistics
- Matrix operations and decompositions
- Prime number assignments and path calculations

### Visualization
- Generation of visualization artifacts
- Rendering statistics and file paths

### Performance Metrics
- Operation durations
- Memory usage for large operations
- Computation statistics

## Sample Log Output

```
2025-05-10 03:39:15,097 - drug_acc.data_loader - INFO - Successfully loaded 208 rows from full_system_demo_output/messy_patients.csv
2025-05-10 03:39:15,097 - drug_acc.data_loader - INFO - Cleaning patients dataframe with 208 rows
2025-05-10 03:39:15,100 - drug_acc.data_cleaner - INFO - Removed 8 duplicate rows
2025-05-10 03:39:15,108 - drug_acc.data_cleaner - INFO - Imputing 'age' with mean: 53.66699195570115
2025-05-10 03:39:15,110 - drug_acc.data_cleaner - INFO - Imputed values in 22 rows (11.0%)
2025-05-10 03:39:15,116 - drug_acc.data_cleaner - INFO - Found 17 IQR outliers in 'age'
```

## Configuring Logging

You can configure the logging behavior in your application:

```python
import logging

# Set global log level
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    handlers=[
        logging.FileHandler("drug_acc.log"),
        logging.StreamHandler()
    ]
)

# Set component-specific log levels
logging.getLogger('drug_acc.data_cleaner').setLevel(logging.DEBUG)
```

## Error Handling Integration

The logging system is tightly integrated with the error handling framework. All exceptions wrapped by the `handle_exceptions` decorator are automatically logged with contextual information.

## Best Practices

1. **Read logs during development**: Use the logs to understand the system's behavior and flow.
2. **Configure appropriate levels**: Set INFO for general use, DEBUG for development.
3. **Analyze performance**: Examine timestamps to identify bottlenecks.
4. **Error diagnosis**: Check ERROR and WARNING logs when issues arise.
5. **Audit trail**: Use logs to trace data transformations and verify system behavior.

## Log File Management

For production deployments, consider:
- Log rotation policies using `RotatingFileHandler`
- Centralized log collection (e.g., ELK stack, CloudWatch)
- Log retention policies to manage storage usage
