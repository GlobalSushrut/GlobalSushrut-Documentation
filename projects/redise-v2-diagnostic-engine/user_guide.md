# redise-v2-diagnostic-engine - user_guide

*This documentation is from the private repository redise-v2-diagnostic-engine.*

---

# RediSe v2 Diagnostic Engine - User Guide

## Getting Started

Welcome to the RediSe v2 Diagnostic Engine with Redis RAG Training capabilities. This guide will help you navigate the system's features and make the most of its diagnostic and learning capabilities.

## System Requirements

- Python 3.8+
- Redis (optional, for production environments)
- Streamlit 1.10+
- NumPy, Pandas, Matplotlib
- 4GB RAM minimum (8GB recommended)

## Installation

1. Clone the repository:
   ```
   git clone https://github.com/yourusername/redise-v2.git
   cd redise-v2
   ```

2. Install dependencies:
   ```
   pip install -r requirements.txt
   ```

3. Launch the application:
   ```
   cd frontend
   streamlit run app.py
   ```

## Navigation

The Streamlit interface provides several key sections accessible from the sidebar:

- **Initialize Engine**: Set up the diagnostic and training engines
- **Patient Input**: Enter patient symptoms and information
- **Diagnostic Analysis**: View diagnostic results and confidence scores
- **Risk Simulation**: Project condition progression without treatment
- **Treatment Tracker**: Monitor treatment effectiveness
- **Emergency Diagnosis**: Get rapid diagnosis for emergency cases
- **Clinical Feedback**: Submit expert feedback to train the system
- **Training Analytics**: View system learning metrics and performance

## Quick Start Guide

### 1. Initialize the Engine

- Navigate to "Initialize Engine" in the sidebar
- Click "Create Mock Data" if this is your first time (creates sample disease patterns)
- Click "Initialize Diagnostic Engine" to start the main engine
- Click "Initialize RAG Trainer" to enable the learning capabilities
- Verify both engines show "initialized" in the status section

### 2. Patient Diagnosis Workflow

1. **Enter Patient Information**:
   - Go to "Patient Input" in the sidebar
   - Enter demographics and select symptoms
   - Adjust symptom intensities using the sliders
   - Click "Submit" to process the case

2. **Review Diagnosis**:
   - Navigate to "Diagnostic Analysis"
   - Review the ranked diagnoses and confidence scores
   - Examine the entropy analysis for diagnostic certainty
   - View recommended tests based on diagnostic uncertainty

3. **Assess Risks**:
   - Go to "Risk Simulation"
   - See the projected progression of conditions without treatment
   - Understand time-sensitive treatment windows

4. **Track Treatment**:
   - Navigate to "Treatment Tracker"
   - Record treatments applied
   - Monitor effectiveness over time
   - View outcome analysis

### 3. Emergency Diagnosis Workflow

For time-critical cases:

1. Go to "Emergency Diagnosis" in the sidebar
2. Select presenting symptoms and adjust severity
3. Enter vital signs (temperature, blood pressure, heart rate, etc.)
4. Click "Generate Emergency Diagnosis"
5. Review the diagnosis, confidence score, and protocols
6. Note any critical vital sign alerts

### 4. Training the System

After confirming actual diagnoses:

1. Navigate to "Clinical Feedback"
2. Enter the confirmed diagnosis and relevant symptoms
3. Adjust confidence based on diagnostic certainty
4. Add any clinical notes for context
5. Submit the case to improve the system
6. Track learning progress in "Training Analytics"

## Feature Details

### Symptom Input System

- Select from common symptoms or add custom ones
- Set intensity from 0.1 (mild) to 1.0 (severe)
- Add contextual information like onset and triggers
- System automatically correlates abnormal vital signs with symptoms

### Diagnostic Engine

- Uses entropy vector analysis for accurate diagnosis
- Provides confidence scores based on symptom match certainty
- Recommends tests to reduce diagnostic uncertainty
- Explains reasoning with symptom-condition correlation analysis

### RAG Training System

- Learns from expert feedback to improve accuracy
- Builds knowledge tree of symptom-diagnosis relationships
- Maintains confidence weights for different diagnoses
- Adapts to new conditions and presentations over time

### Risk Progression Models

- Simulates condition progression based on medical literature
- Shows severity escalation timelines
- Identifies critical intervention windows
- Factors patient demographics into risk calculations

### Treatment Tracking

- Records interventions and their effectiveness
- Tracks symptom resolution over time
- Correlates treatments with outcome improvements
- Builds evidence base for treatment efficacy

## Advanced Usage

### Batch Processing

For processing multiple cases:

```python
from redise.engine import RediSeEngine
import pandas as pd

# Initialize engine
engine = RediSeEngine()
engine.load_data("data/mock_diagnoses.yaml", "data/test_protocols.yaml")

# Load cases from CSV
cases_df = pd.read_csv("cases.csv")

# Process each case
results = []
for index, case in cases_df.iterrows():
    symptoms = case['symptoms'].split(',')
    intensities = [float(x) for x in case['intensities'].split(',')]
    
    # Run diagnosis
    diagnosis = engine.diagnose(symptoms, intensities)
    results.append(diagnosis)
    
# Save results
results_df = pd.DataFrame(results)
results_df.to_csv("diagnosis_results.csv", index=False)
```

### Custom Disease Pattern Integration

To add specialized disease patterns:

1. Create a YAML file with your disease patterns:
   ```yaml
   "Specialized Condition":
     pattern: [0.8, 0.7, 0.9, 0.5, 0.3, 0.0, ...]
     description: "Description of the specialized condition"
     common_symptoms: ["symptom1", "symptom2", "symptom3"]
   ```

2. Load it into the engine:
   ```python
   engine.load_additional_patterns("specialized_patterns.yaml")
   ```

### Redis Persistence Configuration

For production environments using real Redis:

1. Modify the Redis configuration in `config.yaml`:
   ```yaml
   redis:
     host: "localhost"
     port: 6379
     db: 0
     password: "your_password"  # Optional
   ```

2. Initialize the trainer with real Redis:
   ```python
   trainer = RedisRAGTrainer(use_mock_redis=False)
   ```

## Troubleshooting

### Common Issues

1. **Engine Initialization Failure**
   - Check that all required data files exist
   - Ensure YAML files are properly formatted
   - Verify Python dependencies are installed

2. **Low Confidence Diagnoses**
   - Add more symptom details
   - Adjust symptom intensities to be more precise
   - The system may need more training for rare conditions

3. **RAG Trainer Connection Issues**
   - Check Redis connection if using real Redis
   - Verify permission settings for data directories
   - Ensure model files are not corrupted

4. **Visualization Errors**
   - Update Matplotlib and Streamlit to latest versions
   - Reduce dataset size if memory errors occur
   - Check for valid data in visualization inputs

### Logging and Diagnostics

Enable detailed logging for troubleshooting:

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

Log files are stored in the `logs` directory and can help identify issues.

## Best Practices

1. **Training Frequency**
   - Submit expert feedback regularly to improve the system
   - Focus training on areas where the system shows lower confidence
   - Include diverse case presentations for each condition

2. **Data Quality**
   - Ensure symptom intensities accurately reflect clinical observations
   - Be consistent with symptom terminology
   - Include complete vital sign data for emergency cases

3. **Model Management**
   - Save model versions after significant training milestones
   - Test new models on validation cases before deployment
   - Keep a log of training inputs and performance changes

4. **Clinical Integration**
   - Use the system as a decision support tool, not a replacement for clinical judgment
   - Always review the confidence scores and entropy analysis
   - Document cases where the system's diagnosis differs from clinical assessment

## Support and Resources

- **Documentation**: Full API documentation available in the `docs` directory
- **Training Notebooks**: Jupyter notebooks in the `notebooks` directory
- **Sample Data**: Test cases and disease patterns in the `data` directory
- **Issues**: Report bugs and request features via GitHub issues
