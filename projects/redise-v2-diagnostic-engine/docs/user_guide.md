# redise-v2-diagnostic-engine - user_guide

*This documentation is from the private repository redise-v2-diagnostic-engine.*

---

# RediSe v2 Diagnostic Engine User Guide

## Introduction

The RediSe v2 Diagnostic Engine is an advanced medical diagnostic system that combines mathematical modeling, symptom analysis, and evidence-based reasoning to provide accurate and explainable diagnoses. This guide will help you understand how to use the RediSe v2 system effectively.

## Key Features

- **Mathematical Foundations**: Advanced algorithms based on entropy vectors, mock tensors, and sparsity graphs that enable accurate diagnosis
- **Evidence-Based Reasoning**: Integration with medical knowledge bases to support diagnostic decisions
- **Risk Assessment**: Timeline-based risk projection for informed treatment planning
- **Treatment Tracking**: Monitor and analyze treatment effectiveness over time
- **Interactive Visualizations**: Rich visual representations of diagnostic data for better decision-making
- **Comprehensive Reporting**: Detailed reports that combine all diagnostic information in a single document

## Getting Started

### Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/your-organization/redise-v2.git
   cd redise-v2
   ```

2. Install the required dependencies:
   ```bash
   pip install -r requirements.txt
   ```

3. Additional visualization dependencies:
   ```bash
   pip install plotly kaleido
   ```

4. For PDF report generation (optional):
   ```bash
   pip install weasyprint
   ```

### Basic Usage

The RediSe v2 Diagnostic Engine can be used in several ways:

1. **Command-line examples**: Run the included example scripts
2. **Streamlit UI**: Use the interactive web interface
3. **API**: Integrate with other healthcare systems via the REST API

#### Command-line Examples

Run the basic diagnosis example:
```bash
python examples/basic_diagnosis.py
```

Run the simulation and treatment tracking example:
```bash
python examples/simulation.py
```

Run the advanced visualization demo:
```bash
python examples/visualization_demo.py
```

#### Streamlit UI

Launch the interactive web interface:
```bash
streamlit run frontend/app.py
```

This will open a browser window with the RediSe v2 UI, where you can:
- Initialize the engine
- Enter patient information and symptoms
- View diagnostic results
- Simulate risk progression
- Track treatments

#### API

Start the API server:
```bash
cd api
python main.py
```

The API will be available at http://localhost:8000, with interactive documentation at http://localhost:8000/docs.

## Core Components

### SYM-Inject

The SYM-Inject module converts patient symptoms into vector representations suitable for processing by the diagnostic engine. It tokenizes symptoms and assigns intensity values to create a symptom profile.

Example:
```python
from redise.engine import RediSeEngine

engine = RediSeEngine()

# Define symptoms as (symptom_text, intensity) tuples
symptoms = [
    ("fever", 0.8),
    ("cough", 0.7),
    ("fatigue", 0.9)
]

# Create symptom vector
symptom_vector = engine.sym_inject.create_patient_symptom_profile(symptoms)
```

### MTMFA (Mock Tree Multi-Factor Analysis)

The MTMFA module matches symptom vectors to potential diagnoses using a combination of pattern matching and similarity calculations.

Example:
```python
# After creating symptom vector
diagnoses = engine.mtmfa.match_symptoms_to_diagnoses(symptom_vector, patient_data)

# Top diagnosis
if diagnoses:
    top_diagnosis = diagnoses[0]
    print(f"Top diagnosis: {top_diagnosis['name']} (Confidence: {top_diagnosis['confidence']:.2f})")
```

### YAML-Graftor

The YAML-Graftor module provides test suggestions based on diagnoses. It uses predefined protocols to recommend appropriate diagnostic tests.

Example:
```python
# After getting diagnoses
if diagnoses:
    top_diagnosis = diagnoses[0]["name"]
    test_suggestions = engine.yaml_graftor.get_test_suggestions(top_diagnosis)
    
    for test in test_suggestions:
        print(f"Recommended test: {test['name']} (Priority: {test['priority']})")
```

### SimuRisk-E

The SimuRisk-E module simulates disease progression over time, allowing healthcare providers to assess potential risks and plan interventions accordingly.

Example:
```python
# Simulate risk progression for 30 days
risk_simulation = engine.simulate_risks(
    diagnosis_name,
    confidence,
    patient_age,
    days=30
)

# Critical points
for point in risk_simulation["critical_points"]:
    print(f"Critical point at day {point['day']} with risk level {point['risk']:.2f}")
```

### Treatment Tracker

The Treatment Tracker module monitors treatment effectiveness over time, helping healthcare providers adjust treatment plans as needed.

Example:
```python
# Register a treatment
treatment_id = str(uuid.uuid4())
patient_id = str(uuid.uuid4())

treatment_data = {
    "name": "Standard Therapy",
    "dosage": "standard",
    "frequency": "daily",
    "duration": 7,
    "start_date": datetime.now()
}

engine.track_treatment(treatment_id, patient_id, diagnosis_name, treatment_data)

# Record treatment response
response_data = {
    "date": datetime.now() + timedelta(days=3),
    "effectiveness": 0.7,
    "notes": "Patient showing improvement"
}

engine.record_treatment_response(treatment_id, response_data)

# Analyze effectiveness
analysis = engine.treatment_tracker.analyze_treatment_effectiveness(treatment_id)
print(f"Treatment effectiveness: {analysis['avg_effectiveness']:.2f}")
print(f"Status: {analysis['status']}")
```

## Advanced Visualization

The RediSe v2 Diagnostic Engine includes advanced visualization capabilities that make diagnostic information more accessible and interpretable.

### Diagnostic Confidence Visualization

```python
from redise.visualization.dashboard import DiagnosticVisualization

visualizer = DiagnosticVisualization()

# Create confidence chart
confidence_fig = visualizer.create_confidence_chart(
    diagnoses,
    title="Diagnostic Confidence Analysis",
    save_path="diagnosis_confidence.png"
)
```

### Risk Timeline Visualization

```python
# Create risk timeline
risk_fig = visualizer.create_risk_timeline(
    risk_simulation["progression"],
    risk_simulation["critical_points"],
    title="Risk Progression Timeline",
    save_path="risk_timeline.png"
)
```

### Symptom Radar Chart

```python
# Create symptom radar chart
symptom_fig = visualizer.create_symptom_radar(
    symptoms,
    title="Patient Symptom Profile",
    save_path="symptom_radar.png"
)
```

### Treatment Comparison

```python
# Create treatment comparison
treatment_fig = visualizer.create_treatment_comparison(
    treatments,
    title="Treatment Effectiveness Comparison",
    save_path="treatment_comparison.png"
)
```

## Generating Reports

The ReportGenerator allows you to create comprehensive HTML reports that combine all diagnostic information, risk assessments, and treatment tracking in a single document.

```python
from redise.visualization.dashboard import ReportGenerator

report_generator = ReportGenerator("reports")

# Generate report
report_path = report_generator.generate_html_report(
    diagnostic_results,
    patient_data,
    risk_simulation,
    treatments
)

print(f"Report generated at: {report_path}")
```

## API Integration

The RediSe v2 Diagnostic Engine can be integrated with other healthcare systems via its REST API.

### Available Endpoints

- `GET /status`: Get system status
- `POST /diagnose`: Process symptoms and generate diagnoses
- `POST /simulate_risk`: Simulate risk progression for a diagnosis
- `POST /track_treatment`: Track a treatment for a diagnosis
- `POST /record_treatment_response`: Record a response to treatment
- `GET /visualize/diagnosis/{patient_id}`: Generate visualization for diagnosis results
- `GET /generate_report/{patient_id}`: Generate a comprehensive diagnostic report
- `GET /download/visualization/{filename}`: Download a visualization file
- `GET /download/report/{filename}`: Download a report file

### Example API Usage

```python
import requests
import json

# Define API base URL
base_url = "http://localhost:8000"

# Diagnose patient
patient_data = {
    "first_name": "Jane",
    "last_name": "Smith",
    "age": 45,
    "gender": "female",
    "medical_history": ["hypertension"],
    "symptoms": [
        {"description": "fever", "intensity": 0.8},
        {"description": "cough", "intensity": 0.7},
        {"description": "fatigue", "intensity": 0.9}
    ]
}

response = requests.post(f"{base_url}/diagnose", json=patient_data)
result = response.json()

patient_id = result["patient_id"]
diagnoses = result["results"]["diagnoses"]

# Generate report
response = requests.get(f"{base_url}/generate_report/{patient_id}")
report_info = response.json()

# Download report
report_url = report_info["download_url"]
response = requests.get(f"{base_url}{report_url}")

# Save report locally
with open("patient_report.html", "wb") as f:
    f.write(response.content)
```

## Troubleshooting

### No Diagnoses Generated

If the system is not generating any diagnoses:

1. Check that the symptom intensities are sufficiently high (0.5 or higher for clear symptoms)
2. Ensure that disease patterns have been loaded correctly
3. Verify that the symptom descriptions match those in the disease patterns

### Visualization Issues

If visualizations are not generating:

1. Ensure that plotly and kaleido are installed: `pip install plotly kaleido`
2. Check that the output directory is writable
3. Try running with explicit file paths instead of relative paths

### API Connection Problems

If you can't connect to the API:

1. Verify that the API server is running with `ps aux | grep main.py`
2. Check that the port isn't blocked by another application or firewall
3. Ensure that the client is using the correct URL with port number

## Conclusion

The RediSe v2 Diagnostic Engine provides a comprehensive platform for medical diagnosis, risk assessment, and treatment tracking. By leveraging its mathematical foundations, visualization capabilities, and API integration, healthcare providers can make more informed decisions and provide better care for their patients.

For additional help or to report issues, please contact support at support@redise-diagnostics.com or visit our documentation website at https://docs.redise-diagnostics.com.
