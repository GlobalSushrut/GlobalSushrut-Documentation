# drug-ACC - README

*This documentation is from the private repository drug-ACC.*

---

# Data Directory

This directory contains data files used by the Drug ACC system.

## Expected File Format

The system expects the following CSV files:

### patients.csv
Patient information with the following structure:
- `patient_id`: Unique identifier for each patient
- Additional columns like age, sex, weight, etc.

### drugs.csv
Drug information with the following structure:
- `drug_id`: Unique identifier for each drug
- Additional columns like name, category, dosage, etc.

### outcomes.csv
Outcome metrics with the following structure:
- `outcome_id`: Unique identifier for each outcome
- Additional columns like name, severity, is_positive, etc.

### genetics.csv
Genetic markers with the following structure:
- `genetic_id`: Unique identifier for each genetic marker
- Additional columns like marker name, chromosome, position, etc.

### trial_results.csv
Relationships between entities (edges in the graph) with the following structure:
- `source_id`: ID of the source node
- `source_type`: Type of the source node (patient, drug, outcome, genetic)
- `target_id`: ID of the target node
- `target_type`: Type of the target node (patient, drug, outcome, genetic)
- `weight`: Numeric weight/strength of the relationship
- `relation`: Optional description of the relationship type

## Sample Data

The system includes functionality to generate sample data for testing purposes. 
Run the following to generate sample data:

```python
from drug_acc.utils.sample_data import generate_sample_data
generate_sample_data()
```

Or use the CLI tool:

```bash
python -m drug_acc.cli generate-data
```
