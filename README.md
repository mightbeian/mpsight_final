# MPSight - Computer Vision-Based Mpox Lesion Detection and Severity Assessment System

![MPSight Logo](https://img.shields.io/badge/MPSight-v2.0.0-6C63FF?style=for-the-badge)
![Flutter](https://img.shields.io/badge/Flutter-3.0+-02569B?style=for-the-badge&logo=flutter)
![TensorFlow](https://img.shields.io/badge/TensorFlow-Lite-FF6F00?style=for-the-badge&logo=tensorflow)
![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)

A comprehensive, multi-modal AI system for mpox detection, lesion characterization, and severity assessment. MPSight integrates multiple advanced AI components to provide clinicians with detailed diagnostic support.

## 🏗️ System Architecture

### Core AI Components

```
┌─────────────────────────────────────────────────────────────────┐
│                         MPSight v2.0                          │
├─────────────────────────────────────────────────────────────────┤
│                    IMAGE ANALYSIS PIPELINE                     │
├───────────────┬────────────────┬───────────────┬────────────────┤
│ LESION TYPE   │ DISEASE CLASS  │ SEGMENTATION  │ FITZPATRICK    │
│ CLASSIFIER   │ (Multi-Label)  │ (U-Net+Attn)  │ CLASSIFIER     │
├───────────────┼────────────────┼───────────────┼────────────────┤
│ • Macular     │ • Mpox         │ • Pixel-level │ • Type I-VI     │
│ • Papular     │ • Chickenpox   │ • Boundary    │ • Bias eval    │
│ • Vesicular   │ • Measles      │ • Confluence  │ • Equity       │
│ • Pustular    │ • Cowpox       │ • Distribution│   metrics      │
│ • Crusted     │ • HFMD         │ • Counting    │                │
│               │ • Healthy      │               │                │
└───────────────┴────────────────┴───────────────┴────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                   MPOX-SSS SEVERITY SCORING                    │
├────────────────┬───────────────┬───────────────┬───────────────┤
│ Lesion Count  │ Distribution  │ Confluence    │ Mucosal       │
│ (0-25 pts)    │ (0-25 pts)    │ (0-25 pts)    │ (0-25 pts)    │
└────────────────┴───────────────┴───────────────┴───────────────┘
                    ↓ Dermatologist Ratings as Ground Truth
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                 MULTIMODAL INTEGRATION                         │
├────────────────┬───────────────┬───────────────┬───────────────┤
│ Patient       │ Clinical      │ Metadata      │ Exposure      │
│ Symptoms      │ Text Notes    │ (Location,    │ History       │
│ Assessment    │               │ Timing, etc.) │               │
└────────────────┴───────────────┴───────────────┴───────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│              PRIVACY-PRESERVING ARCHITECTURE                   │
├────────────────┬───────────────┬───────────────┬───────────────┤
│ On-Device     │ Encrypted     │ HIPAA/GDPR    │ De-ID &       │
│ Processing    │ Storage       │ Compliant     │ Anonymization │
└────────────────┴───────────────┴───────────────┴───────────────┘
```

## 🎯 Key Features

### 1. Multi-Class Lesion Type Classification
- **5 lesion stages**: Macular, Papular, Vesicular, Pustular, Crusted
- Enables disease progression tracking
- YOLOv8 architecture for real-time detection

### 2. Multi-Label Disease Classification
- **6 conditions**: Mpox, Chickenpox, Measles, Cowpox, HFMD, Healthy
- Supports co-infection scenario detection
- Comparative confidence scores with uncertainty quantification

### 3. Lesion Segmentation (U-Net with Attention)
- Pixel-level lesion identification
- Precise boundary detection for counting
- Automated confluence assessment
- Distribution mapping across body regions

### 4. MPOX-SSS Severity Scoring
- **Automated severity calculation** based on:
  - Lesion count (0-25 points)
  - Regional distribution (0-25 points)
  - Confluence (0-25 points)
  - Mucosal involvement (0-25 points)
- **Dermatologist ratings** as ground truth for training
- Standardized classification: Mild, Moderate, Severe

### 5. Fitzpatrick Skin Type Assessment
- Evaluates model performance across types I-VI
- Addresses algorithmic bias in skin representation
- Per-type performance metrics for equity evaluation
- Demographic parity scoring

### 6. Multimodal Integration
- Patient symptom assessment (fever, malaise, lymphadenopathy)
- Clinical text notes and medical history
- Metadata integration (location, onset timing, progression)
- Contact tracing and exposure history

### 7. Privacy-Preserving Architecture
- On-device processing with edge AI deployment
- Encrypted data transmission and storage
- HIPAA/GDPR-compliant data handling
- De-identification and anonymization protocols
- Audit logging for compliance

## 📂 Project Structure

```
mpsight-app/
├── lib/
│   ├── main.dart                              # App entry point
│   ├── models/
│   │   ├── models.dart                        # Export all models
│   │   ├── lesion_type.dart                   # Lesion stage classification
│   │   ├── disease_classification.dart        # Multi-label disease detection
│   │   ├── segmentation_result.dart           # U-Net segmentation results
│   │   ├── severity_score.dart                # MPOX-SSS scoring system
│   │   ├── fitzpatrick_type.dart              # Skin type classification
│   │   └── patient_data.dart                  # Multimodal patient data
│   ├── providers/
│   │   ├── detection_provider.dart            # Legacy single-model provider
│   │   └── comprehensive_detection_provider.dart  # Multi-model provider
│   ├── services/
│   │   └── privacy_service.dart               # HIPAA/GDPR privacy handling
│   ├── screens/
│   │   ├── home_screen.dart                   # Main dashboard
│   │   ├── camera_screen.dart                 # Real-time scanning
│   │   ├── gallery_screen.dart                # Image upload
│   │   ├── patient_assessment_screen.dart     # Multimodal data collection
│   │   ├── severity_dashboard_screen.dart     # MPOX-SSS results display
│   │   └── segmentation_viewer_screen.dart    # Interactive segmentation
│   └── widgets/
│       ├── feature_card.dart                  # Reusable card component
│       └── confidence_chart.dart              # Visualization charts
├── assets/
│   ├── models/                                # TFLite models
│   │   ├── lesion_type_classifier.tflite      # Lesion stage model
│   │   ├── disease_multilabel_classifier.tflite  # Disease detection
│   │   ├── unet_attention_segmentation.tflite # Segmentation model
│   │   ├── mpox_sss_severity.tflite           # Severity scoring
│   │   └── fitzpatrick_classifier.tflite      # Skin type model
│   ├── fonts/                                 # Poppins font family
│   └── icons/                                 # App icons
├── android/                                   # Android configuration
└── pubspec.yaml                               # Dependencies
```

## 🚀 Quick Start

### Prerequisites
- Flutter SDK (3.0 or higher)
- Android Studio with Android SDK (API 21+)
- Physical Android device or emulator
- Trained TFLite models (see Model Requirements)

### Installation

```bash
# Clone the repository
git clone https://github.com/mightbeian/mpsight-app.git
cd mpsight-app

# Install dependencies
flutter pub get

# Add your TFLite models to assets/models/
# (see Model Requirements section)

# Run the app
flutter run
```

### Model Requirements

Place the following TFLite models in `assets/models/`:

| Model | Input Shape | Output Shape | Description |
|-------|-------------|--------------|-------------|
| `lesion_type_classifier.tflite` | [1, 640, 640, 3] | [1, 5] | 5-class lesion stage |
| `disease_multilabel_classifier.tflite` | [1, 640, 640, 3] | [1, 6] | 6-class disease (sigmoid) |
| `unet_attention_segmentation.tflite` | [1, 256, 256, 3] | [1, 256, 256] | Pixel-wise mask |
| `mpox_sss_severity.tflite` | Variable | [1, 4] | Component scores |
| `fitzpatrick_classifier.tflite` | [1, 640, 640, 3] | [1, 6] | 6-type skin classification |

## 🔧 Configuration

### Privacy Configuration

```dart
// HIPAA-compliant configuration
final privacyService = PrivacyService(
  config: PrivacyConfig.hipaaCompliant,
);

// GDPR-compliant configuration
final privacyService = PrivacyService(
  config: PrivacyConfig.gdprCompliant,
);
```

### Comprehensive Analysis

```dart
final provider = ComprehensiveDetectionProvider();
await provider.loadAllModels();

// Run full analysis with patient data
final result = await provider.analyzeImage(
  imageBytes,
  patientData: patientAssessment,
  runAllModels: true,
);

// Access results
print('Disease: ${result?.diseaseClassification?.primaryCondition}');
print('Severity: ${result?.severityScore?.severityLevel}');
print('Lesion Count: ${result?.segmentation?.lesionCount}');
print('Fitzpatrick Type: ${result?.fitzpatrickType?.predictedType}');
```

## 📊 Severity Scoring (MPOX-SSS)

| Score Range | Severity Level | Clinical Guidance |
|-------------|----------------|-------------------|
| 0-33 | Mild | Outpatient management, supportive care |
| 34-66 | Moderate | Consider antiviral therapy, close monitoring |
| 67-100 | Severe | Immediate attention, hospitalization consideration |

### Component Scoring

| Component | Score Range | Criteria |
|-----------|-------------|----------|
| Lesion Count | 0-25 | ≤10: 5pts, ≤25: 10pts, ≤50: 15pts, ≤100: 20pts, >100: 25pts |
| Distribution | 0-25 | Based on number of body regions affected |
| Confluence | 0-25 | Based on percentage of confluent lesions |
| Mucosal | 0-25 | 0 if absent, 25 if present |

## 🛡️ Privacy & Security

### Data Handling Principles
- **Data Minimization**: Collect only necessary information
- **Anonymization**: SHA-256 hashing of identifiers
- **Encryption**: AES-256-GCM for data at rest
- **Audit Logging**: Full compliance trail
- **Consent Management**: Explicit user consent tracking

### Compliance Features
- HIPAA-compliant data retention (6 years)
- GDPR-compliant processing (30-day retention option)
- De-identification of PHI
- Secure deletion protocols

## 🧪 Testing

```bash
# Run all tests
flutter test

# Run with coverage
flutter test --coverage

# Analyze code quality
flutter analyze
```

## 📱 Building for Production

```bash
# Build APK (multiple ABIs)
flutter build apk --release --split-per-abi

# Build App Bundle (for Play Store)
flutter build appbundle --release
```

## ⚠️ Important Disclaimer

This is a **research prototype** for preliminary screening assistance. It is:
- **NOT** a replacement for professional medical diagnosis
- **NOT** FDA/CE cleared for clinical use
- For **research and educational purposes** only

Always consult qualified healthcare professionals for proper diagnosis and treatment.

## 📝 Research Paper

This application is part of the thesis:

**"MPSight: Computer Vision-Based Mpox Lesion Detection and Severity Assessment System"**

**Researchers:**
- Christian Paul Cabrera
- Vanjo Luis Geraldez
- Yuri Luis Gler

**Adviser:** Tita R. Herradura

## 📄 License

MIT License - see [LICENSE](LICENSE) file for details.

## 🤝 Contributing

Contributions are welcome! Please read our contributing guidelines and submit pull requests.

## 📧 Contact

- **Email**: cabrera.cpaul@gmail.com
- **LinkedIn**: [Christian Paul Cabrera](https://www.linkedin.com/in/mightbeian/)
- **GitHub**: [@mightbeian](https://github.com/mightbeian)

---

**Made with ❤️ for better healthcare accessibility**
