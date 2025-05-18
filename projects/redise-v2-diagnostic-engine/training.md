# redise-v2-diagnostic-engine - training

*This documentation is from the private repository redise-v2-diagnostic-engine.*

---

# Redis RAG Training Engine - Training Guide

## Introduction

The Redis RAG (Retrieval Augmented Generation) Training Engine is a specialized component of the RediSe v2 Diagnostic Engine that enables continuous learning from clinical feedback. This guide explains how to train, validate, and deploy the RAG engine to improve diagnostic accuracy over time.

## Training Methods

The Redis RAG Training Engine supports multiple training approaches:

### 1. Jupyter Notebook Training

The most interactive training method is through Jupyter notebooks that allow real-time inspection of model performance:

- **Location**: `/redise-v2/notebooks/redis_rag_training.ipynb` - Comprehensive training notebook
- **Location**: `/redise-v2/notebooks/quick_rag_train_test.ipynb` - Streamlined training for quick testing

Running these notebooks provides:
- Visual performance metrics
- Sample case generation and testing
- Model evaluation at different training stages
- Visualization of learning progress

### 2. Clinical Feedback Interface

For ongoing training in clinical environments:

- Use the Streamlit frontend's "Clinical Feedback" page
- Submit cases with expert-confirmed diagnoses
- Review case history and training progress
- Save models after significant learning milestones

### 3. Programmatic API Training

For automated or batch training scenarios:

```python
from engines.rag_trainer.trainer_runner import RedisRAGTrainer

# Initialize trainer
trainer = RedisRAGTrainer(use_mock_redis=False)  # Set to False to use real Redis

# Train with single case
trainer.ingest_training_case(
    symptoms=["chest_pain", "shortness_of_breath", "cold_sweat"],
    intensities=[0.9, 0.8, 0.7],
    true_diagnosis="Acute Myocardial Infarction",
    confidence=0.95,
    metadata={"expert_id": "cardiologist_1234", "feedback_type": "expert"}
)

# Train with batch data
training_data = [
    {
        "symptoms": ["fever", "cough", "shortness_of_breath"],
        "intensities": [0.9, 0.8, 0.8],
        "diagnosis": "Pneumonia",
        "confidence": 0.92
    },
    # Add more cases...
]
trainer.ingest_batch_training_data(training_data)

# Save trained model
trainer.save_model("/path/to/save/model")
```

## Data Formats and Requirements

### Symptom Vector Format

The system represents symptoms as 100-dimensional vectors with the following characteristics:
- Vector dimensions represent different symptom categories and attributes
- Sparse encoding for efficient representation of varied symptoms
- Intensity values range from 0.0 (absent) to 1.0 (severe)

### Training Case Format

Each training case should include:

```json
{
  "symptoms": ["symptom1", "symptom2", "symptom3"],
  "intensities": [0.8, 0.7, 0.9],
  "diagnosis": "Condition Name",
  "confidence": 0.95,
  "metadata": {
    "patient_age": 65,
    "patient_gender": "female",
    "timestamp": "2025-05-13T03:33:31.642",
    "expert_id": "physician_1234",
    "feedback_type": "expert"
  }
}
```

## Model Persistence

### Saving Models

Models should be saved after significant training:

```python
# Create timestamp-based directory for the model
import os
from datetime import datetime

timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
save_dir = os.path.join("data", f"rag_model_{timestamp}")
os.makedirs(save_dir, exist_ok=True)

# Save the model
trainer.save_model(save_dir)
```

### Loading Pre-trained Models

To load a previously saved model:

```python
# Initialize trainer with existing model
trainer = RedisRAGTrainer(use_mock_redis=True)
trainer.load_model("/path/to/saved/model")
```

## Performance Evaluation

### Evaluation Metrics

The system provides several metrics to evaluate training effectiveness:

- **Accuracy**: Percentage of correct diagnoses in test set
- **Confidence**: Average confidence score for correct diagnoses
- **Tree Growth**: Number of nodes and paths in the knowledge tree
- **Learning Curve**: Accuracy improvement over training iterations

### Running Evaluations

```python
# Test the model with new cases
test_data = [
    {
        "symptoms": ["chest_pain", "arm_pain", "shortness_of_breath"],
        "intensities": [0.9, 0.7, 0.8],
        "diagnosis": "Acute Myocardial Infarction"
    },
    # Add more test cases...
]

results = []
for case in test_data:
    # Convert to symptom tuples
    symptom_tuples = list(zip(case["symptoms"], case["intensities"]))
    
    # Get prediction
    prediction = trainer.process_symptoms(symptom_tuples)
    
    # Check if correct
    predicted_diagnoses = [d["name"] for d in prediction.get("rag_diagnoses", [])]
    correct = case["diagnosis"] in predicted_diagnoses
    
    results.append({
        "true_diagnosis": case["diagnosis"],
        "predicted": predicted_diagnoses,
        "correct": correct
    })

# Calculate accuracy
accuracy = sum(1 for r in results if r["correct"]) / len(results)
print(f"Model accuracy: {accuracy:.2f}")
```

## Advanced Training Techniques

### Consensus Training

For conditions with diagnostic uncertainty, use consensus training:

```python
# Multiple expert opinions on the same case
trainer.ingest_training_case(
    symptoms=symptoms,
    intensities=intensities,
    true_diagnosis="Condition A",
    confidence=0.7,
    metadata={"expert_id": "expert1", "consensus_group": "case_12345"}
)

trainer.ingest_training_case(
    symptoms=symptoms,
    intensities=intensities,
    true_diagnosis="Condition B",
    confidence=0.8,
    metadata={"expert_id": "expert2", "consensus_group": "case_12345"}
)
```

### Noise-Resistant Training

The MockSparsityRegressor uses dropout and noise-injection for robust training:

```python
# Train with explicit noise levels
trainer.ingest_training_case_with_params(
    symptoms=symptoms,
    intensities=intensities,
    true_diagnosis=diagnosis,
    noise_level=0.2,  # Add 20% random noise to simulate real-world messiness
    dropout_rate=0.1  # Randomly drop 10% of symptoms to simulate incomplete data
)
```

## Common Training Scenarios

### Emergency Department Training

Focus on acute conditions with rapid onset:
- Myocardial infarction, stroke, pulmonary embolism, anaphylaxis
- High symptom intensities and clear progression patterns
- Vital sign correlation with symptoms

### General Practice Training

Focus on common conditions with varied presentations:
- Respiratory infections, chronic diseases, common ailments
- More subtle symptom intensities and higher variability
- Longer-term progression patterns

## Troubleshooting

### Common Issues

1. **Dimension Mismatch**: Ensure all symptom vectors are the correct length (100)
2. **Overfitting**: If accuracy on training data is high but test performance is poor, increase training diversity
3. **Low Confidence**: If diagnoses show low confidence, more training cases are needed for those conditions
4. **Redis Connection Issues**: Check Redis connection parameters when using real Redis instances

### Diagnostic Commands

```python
# Get detailed statistics on the training state
stats = trainer.get_statistics()
print(f"Tree nodes: {stats['tree_stats']['total_nodes']}")
print(f"Unique diagnoses: {stats['tree_stats']['unique_diagnoses']}")
print(f"Total paths: {stats['tree_stats']['total_paths']}")
print(f"Ingest count: {stats['ingestor_stats']['ingest_count']}")

# Examine a specific path
path_info = trainer.get_path_info(path_id)
print(f"Path symptoms: {path_info['symptoms']}")
print(f"Path diagnosis: {path_info['diagnosis']}")
print(f"Path confidence: {path_info['confidence']}")
```
