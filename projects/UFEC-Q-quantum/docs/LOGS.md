# UFEC-Q-quantum - LOGS

*This documentation is from the private repository UFEC-Q-quantum.*

---

# UFEC-Q Logging System

This document provides an overview of the UFEC-Q structured logging system, its capabilities, and how to use it effectively in your quantum error correction workflows.

## Overview

The UFEC-Q logging system is designed for production-grade observability of quantum operations, with features specifically tailored to the needs of quantum error correction:

- **Structured JSON Logging**: All logs are structured for machine parsing and analytics
- **Context-Aware Logging**: Quantum state information is preserved throughout the pipeline
- **Severity Levels**: Granular control over log verbosity
- **Quantum-Specific Metrics**: Built-in logging of quantum fidelity, phase errors, and correction metrics
- **File and Console Output**: Supports logging to files and console simultaneously
- **Log Rotation**: Automatic rotation of log files to prevent disk space issues

## Log Structure

Each log entry follows this structure:

```
TIMESTAMP - COMPONENT - LEVEL - MESSAGE | Context: {CONTEXT_JSON}
```

Example:
```
2025-05-09 23:36:57 - ufecq.demo - INFO - Quantum system initialized | Context: {"num_modes": 3, "dim": 20, "alpha": 1.0, "simulation_id": "sim-1746848217"}
```

### Context Preservation

The logs maintain context throughout the quantum pipeline. As operations progress, the context accumulates:

1. **Initial context**: Basic system parameters
   ```
   {"num_modes": 3, "dim": 20, "alpha": 1.0, "simulation_id": "sim-1746848217"}
   ```

2. **After noise injection**: Added noise parameters
   ```
   {"num_modes": 3, "dim": 20, "alpha": 1.0, "simulation_id": "sim-1746848217", "phase_errors": [0.119, 0.016, -0.099], "loss_rates": [0.004, 0.088, 0.011]}
   ```

3. **After graph creation**: Added graph parameters
   ```
   {"num_modes": 3, ..., "loss_rates": [0.004, 0.088, 0.011], "graph_nodes": 3, "connection_density": 0.7, "error_hits": [1, 0, 1]}
   ```

4. **Final results**: Complete pipeline metrics
   ```
   {..., "metrics": {"initial_fidelity": 0.509, "final_fidelity": 0.806, "improvement_percent": 58.149}}
   ```

## Log Files

The system generates two main log files:

1. **Main log** (`logs/ufecq_demo_YYYYMMDD.log`): Contains all operational logs
2. **Error log** (`logs/ufecq_errors_YYYYMMDD.log`): Contains only error messages

This separation enables:
- Efficient error monitoring without parsing through routine messages
- Targeted alerting based on error patterns
- Different retention policies for different log types

## Log Levels

The system supports standard logging levels:

| Level | Use Case |
|-------|----------|
| **DEBUG** | Detailed technical information (e.g., `Calculating drift for mode 0`) |
| **INFO** | Normal operational events (e.g., `Quantum system initialized`) |
| **WARNING** | Potential issues that don't stop execution (e.g., `Potential phase instability detected`) |
| **ERROR** | Operational failures (e.g., `Error in FGAC process`) |
| **CRITICAL** | Critical failures requiring immediate attention |

## Using the Logging System

### Basic Setup

```python
from ufec_q.utils.logging import setup_logging, logger

# Set up logging with desired parameters
setup_logging(
    log_level="INFO",  # Verbosity level
    log_dir="./logs",  # Directory for log files
    app_name="my_quantum_app",  # Application name prefix
    enable_console=True,  # Output logs to console
    enable_json=False  # Output logs in human-readable format
)

# Use the logger
logger.info("Starting quantum operation")
```

### Logging with Context

```python
# Create context dictionary
context = {
    "num_modes": 3,
    "alpha": 1.0,
    "simulation_id": "sim-123456"
}

# Log with context
logger.info(f"Quantum system initialized | Context: {json.dumps(context)}")

# Update context as operations progress
context["phase_errors"] = [0.1, 0.05, -0.02]
logger.info(f"Noise injected | Context: {json.dumps(context)}")
```

### For Production Use

In production environments, enable JSON formatting for machine parsing:

```python
setup_logging(
    log_level="INFO",
    log_dir="/var/log/ufecq",
    app_name="ufecq_production",
    enable_console=False,  # Disable console logging in production
    enable_json=True  # Enable structured JSON logging
)
```

## Analyzing Logs

### Basic Analysis

```bash
# View all errors
grep ERROR logs/ufecq_errors_20250509.log

# View all logs for a specific simulation
grep "sim-1746848217" logs/ufecq_demo_20250509.log

# View fidelity improvements
grep "improvement_percent" logs/ufecq_demo_20250509.log | jq
```

### Advanced Analysis

For advanced log analysis, we recommend:

1. **ELK Stack**: Forward logs to Elasticsearch for indexing, visualize with Kibana
2. **Prometheus Integration**: Export metrics from logs to Prometheus for alerting
3. **Jupyter Notebooks**: Parse logs with pandas for scientific analysis

## Prometheus Integration

The UFEC-Q metrics exporter automatically extracts metrics from logs and exposes them to Prometheus:

```python
from ufec_q.monitoring.prometheus_exporter import init_metrics, get_exporter

# Initialize metrics exporter
exporter = init_metrics(port=9090)

# Record metrics from log context
metrics = {
    "id": "job-123",
    "initial_fidelity": 0.65,
    "final_fidelity": 0.85,
    "improvement_percent": 30.7,
    "phase_errors": [0.1, 0.05, -0.02],
    "loss_rates": [0.01, 0.02, 0.005]
}
exporter.record_correction(metrics)
```

## Best Practices

1. **Be Consistent with Context**: Maintain the same context structure throughout your application
2. **Include Unique IDs**: Always include simulation_id or job_id for correlation
3. **Log State Transitions**: Log at the beginning and end of each quantum operation
4. **Include Metrics**: Always include quantitative metrics for analysis
5. **Set Appropriate Log Levels**: Use DEBUG sparingly in production
6. **Forward to Aggregation**: Configure log forwarding to centralized log aggregation system

## Example Log Analysis

```python
import json
import pandas as pd

# Parse logs into a DataFrame
logs = []
with open('logs/ufecq_demo_20250509.log', 'r') as f:
    for line in f:
        if 'Context:' in line:
            timestamp, component, level, message = line.split(' - ', 3)
            message, context_json = message.split(' | Context: ', 1)
            context = json.loads(context_json)
            logs.append({
                'timestamp': timestamp,
                'component': component,
                'level': level,
                'message': message.strip(),
                **context
            })

# Create DataFrame
df = pd.DataFrame(logs)

# Analyze improvement by simulation
improvements = df[df['message'] == 'Simulation completed'].groupby('simulation_id')['improvement_percent'].mean()
print(f"Average improvement: {improvements.mean():.2f}%")

# Plot fidelity improvements
import matplotlib.pyplot as plt
df_results = df[df['message'].str.contains('Simulation .* results')]
plt.figure(figsize=(10, 6))
plt.bar(range(len(df_results)), df_results['improvement'].values)
plt.xlabel('Simulation Run')
plt.ylabel('Fidelity Improvement (%)')
plt.title('UFEC-Q Fidelity Improvements')
plt.tight_layout()
plt.savefig('fidelity_improvements.png')
```

This analysis would produce a bar chart showing the fidelity improvement for each simulation run.
