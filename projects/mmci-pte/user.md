# mmci-pte - user

*This documentation is from the private repository mmci-pte.*

---

# MMCI-PTE: User Guide for Clinical Applications

This guide is intended for clinical users of the MMCI-PTE (Mock Mechanics Cancer Intelligence - Probability Tensor Engine) system. It provides practical instructions for using the system in clinical scenarios for cancer diagnostics and monitoring.

## Table of Contents

1. [Introduction](#introduction)
2. [Getting Started](#getting-started)
3. [Entering Patient Data](#entering-patient-data)
4. [Understanding Diagnostic Results](#understanding-diagnostic-results)
5. [Follow-up Assessments](#follow-up-assessments)
6. [Interpreting Confidence Scores](#interpreting-confidence-scores)
7. [System Recommendations](#system-recommendations)
8. [Case Studies](#case-studies)

## Introduction

MMCI-PTE is a clinical decision support system designed to assist healthcare providers in cancer diagnostics, staging, and treatment planning. The system:

- Analyzes clinical biomarkers, imaging results, and histology
- Provides diagnostic assessments with quantified confidence levels
- Detects changes in patient condition over time
- Suggests treatment modifications based on disease progression
- Validates diagnoses against clinical guidelines

This system is meant to **augment** clinical decision-making, not replace it. All system outputs should be reviewed by qualified healthcare professionals.

## Getting Started

### System Requirements

- Modern web browser (Chrome, Firefox, Safari)
- Secure clinical network connection
- Authentication credentials

### Logging In

1. Navigate to the MMCI-PTE web interface
2. Enter your institutional credentials
3. Complete two-factor authentication if required
4. Select your clinical role and department

## Entering Patient Data

### Creating a New Patient Record

1. Select "New Patient Assessment" from the dashboard
2. Enter patient demographics (ID, age, sex)
3. Select the suspected or confirmed cancer type
4. The system will dynamically present relevant biomarker panels

### Entering Biomarker Data

The system supports clinically accurate biomarker values for multiple cancer types:

#### Prostate Cancer

- **PSA**: Enter value in ng/mL
- **Free PSA Ratio**: Enter as decimal (e.g., 0.15)
- **PSA Density**: Enter value (requires prostate volume from imaging)
- **Testosterone**: Enter value in ng/dL
- **ALP and LDH**: Enter values in U/L

#### Breast Cancer

- **CA 15-3**: Enter value in U/mL
- **CEA**: Enter value in ng/mL
- **Estrogen/Progesterone Receptor Status**: Select positive or negative
- **HER2 Status**: Select positive, negative, or equivocal

#### Lung Cancer

- **CEA**: Enter value in ng/mL
- **CYFRA 21-1**: Enter value in ng/mL
- **NSE**: Enter value for SCLC assessment
- **Mutation Status**: Select options for EGFR, ALK, ROS1, etc.

### Entering Imaging Results

1. Select the imaging modality (CT, MRI, PET, Ultrasound)
2. Enter key measurements:
   - Tumor size (in cm)
   - Number of lesions
   - Location(s)
   - For prostate: prostate volume

### Entering Histology Data

#### Prostate Cancer

- Gleason Score: Select primary and secondary patterns
- Perineural Invasion: Yes/No
- Extraprostatic Extension: Yes/No

#### Breast Cancer

- Histological Type: Select from options
- Grade: Select 1, 2, or 3
- Lymphovascular Invasion: Yes/No

### TNM Staging

The system implements AJCC 8th Edition TNM staging:

1. Select T stage (tumor characteristics)
2. Select N stage (nodal involvement)
3. Select M stage (metastasis status)
4. The system will calculate the overall stage automatically

## Understanding Diagnostic Results

### Diagnosis Summary

After entering patient data, the system will display:

- Calculated cancer risk with confidence score
- TNM stage with calculated overall stage
- Risk category classification
- Key contributing factors to the diagnosis
- Merkle trace ID for audit purposes

### Confidence Visualization

The system visualizes diagnostic confidence using:

- Color-coded confidence indicators
- Diagnostic path visualization showing decision points
- Contributing factors with weighted influence

### Treatment Options

Based on the diagnosis, the system may suggest:

- Treatment modalities appropriate for the stage and risk category
- Clinical trials that match the patient profile
- Follow-up testing recommendations

## Follow-up Assessments

### Entering Follow-up Data

1. Select the patient from your active cases
2. Choose "Add Follow-up Assessment"
3. Enter new biomarker values, imaging results, etc.
4. The system will automatically compare with previous values

### Understanding Change Detection

The system will highlight:

- Significant biomarker changes (with percent change)
- Disease progression indicators
- Changes in confidence levels
- Suggested modifications to treatment plans

Example: A patient shows PSA increase from 6.8 to 9.2 ng/mL (35% increase), triggering a reevaluation of the treatment approach.

## Interpreting Confidence Scores

### Confidence Scale

The system uses a 0-1 scale for confidence scores:

- **0.9 - 1.0**: Very High Confidence
- **0.8 - 0.9**: High Confidence
- **0.7 - 0.8**: Moderate Confidence
- **0.6 - 0.7**: Low Confidence
- **< 0.6**: Very Low Confidence - Additional testing recommended

### Factors Affecting Confidence

Confidence scores are affected by:

- Biomarker specificity and values
- Consistency of multiple indicators
- Quality and recency of imaging
- Histological confirmation
- Alignment with clinical guidelines

### Clinical Interpretation

- High confidence (>0.8): Diagnosis is well-supported by multiple factors
- Moderate confidence (0.7-0.8): Diagnosis is likely but could benefit from additional confirmation
- Low confidence (<0.7): Diagnosis is tentative and requires additional testing or expert consultation

## System Recommendations

### Additional Testing

When confidence is below threshold, the system may recommend:

- Additional biomarker tests
- Follow-up imaging
- Biopsy or additional histological assessment
- Specialist consultation

### Treatment Adjustments

Based on follow-up assessments, the system may recommend:

- Treatment intensification for disease progression
- Treatment de-escalation for good response
- Modality changes based on changing clinical picture
- Additional supportive care

### Validation Warnings

The system validates diagnoses against clinical guidelines and may warn about:

- Inconsistent TNM staging
- Biomarker values requiring urgent attention
- Treatment plans not aligned with current guidelines
- Missing critical assessments

## Case Studies

### Case 1: Prostate Cancer Progression

**Initial Assessment:**
- 62-year-old male
- PSA: 6.8 ng/mL
- Free PSA Ratio: 0.12
- Gleason Score: 3+4
- Clinical Stage: T2a N0 M0 (Stage II)
- Diagnostic Confidence: 0.89

**3-Month Follow-up:**
- PSA increased to 9.2 ng/mL
- Free PSA Ratio decreased to 0.10
- Repeat biopsy showed Gleason 4+3
- Perineural invasion now present
- New T stage: T2b
- Diagnostic Confidence: 0.73
- System recommendation: Add hormone therapy to treatment plan

This case demonstrates how the system detects significant progression (43.5% change) and adapts treatment recommendations accordingly.

### Case 2: Breast Cancer Monitoring

**Initial Assessment:**
- 54-year-old female
- Invasive ductal carcinoma
- ER+/PR+, HER2-
- T1c N0 M0 (Stage I)
- CA 15-3: 22 U/mL
- Diagnostic Confidence: 0.92

**6-Month Follow-up:**
- Post-surgery and radiation
- CA 15-3: 18 U/mL (improved)
- No new imaging findings
- Diagnostic Confidence: 0.95
- System recommendation: Continue current treatment plan

This case shows how the system tracks treatment response and maintains appropriate recommendations when progress is good.

---

## Support and Assistance

For technical support or clinical questions about the MMCI-PTE system, please contact:

- Technical Help Desk: support@mmci-pte.example.org
- Clinical Application Team: clinical@mmci-pte.example.org

For urgent clinical concerns, always consult with appropriate medical specialists rather than relying solely on system recommendations.
