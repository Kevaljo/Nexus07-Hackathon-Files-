# SAMAJH (समझ) - Requirements Document

## Project Information

| Field | Details |
|-------|---------|
| **Project Name** | SAMAJH (समझ) - AI-Powered Patient Discharge Understanding System |
| **Team Name** | Nexus07 |
| **Team Leader** | Keval Joshi |
| **Problem Statement** | AI for Healthcare & Life Sciences |

---

## 1. Problem Statement

### 1.1 Problem Definition

**80% of Indian patients do not understand their hospital discharge instructions**, leading to medication errors, missed warning signs, and preventable hospital readmissions costing **₹1.4 Lakh Crore annually**.

### 1.2 Key Statistics

| Metric | Value |
|--------|-------|
| Patient discharge comprehension | Only 20% understand fully |
| Health literacy rate in India | 25-30% adequate |
| Medication adherence post-discharge | 50-60% follow correctly |
| Preventable readmissions | 15-25% of total |
| Annual economic cost | ₹1.4 Lakh Crore |

### 1.3 Root Causes

- **Language Barrier**: 95% medical documents in English, 70% patients prefer regional languages
- **Complexity Barrier**: Medical jargon and abbreviations (BD, TDS, OD) incomprehensible to patients
- **Literacy Barrier**: 23% of India is illiterate, functional literacy even lower
- **Time Barrier**: Average discharge counseling only 3-5 minutes

---

## 2. Proposed Solution

### 2.1 Solution Overview

**SAMAJH** (meaning "Understanding" in Hindi) is an AI-powered platform that transforms complex medical discharge summaries into simple, voice-enabled, multilingual patient instructions delivered via WhatsApp.

### 2.2 Key Features

| Feature | Description |
|---------|-------------|
| Smart Document Processing | Upload PDF/image, extract text using OCR |
| Medical NER | Extract medications, instructions, warnings |
| AI Simplification | Convert jargon to Grade 5-6 reading level |
| Multilingual Translation | Support for 10+ Indian languages |
| Voice Output | Audio instructions for low-literacy patients |
| Visual Schedule | Medication timing chart with icons |
| WhatsApp Delivery | Accessible to 500M+ Indian users |
| Medication Reminders | Automated reminders at medicine times |

---

## 3. Functional Requirements

### 3.1 Core Requirements

| ID | Requirement | Priority |
|----|-------------|----------|
| FR-01 | Upload discharge summary (PDF/Image) | HIGH |
| FR-02 | Extract medical entities (medications, warnings, follow-up) | HIGH |
| FR-03 | Simplify medical text to simple language | HIGH |
| FR-04 | Translate to Hindi and regional languages | HIGH |
| FR-05 | Generate voice audio in regional languages | HIGH |
| FR-06 | Create visual medication schedule | MEDIUM |
| FR-07 | Deliver via WhatsApp | HIGH |
| FR-08 | Send medication reminders | MEDIUM |
| FR-09 | Hospital admin dashboard | HIGH |

### 3.2 Extraction Requirements

| Entity Type | Fields to Extract |
|-------------|-------------------|
| Medication | Name, Dose, Frequency, Timing, Duration |
| Diagnosis | Condition name, Type/Stage |
| Warning Signs | Symptoms, Required action |
| Follow-up | Date, Tests required |
| Emergency | Contact numbers |

### 3.3 Safety Requirements

| ID | Requirement |
|----|-------------|
| SR-01 | Never change medication names |
| SR-02 | Never modify dosages |
| SR-03 | Never remove medications from list |
| SR-04 | Never add medical advice not in original |
| SR-05 | Always include disclaimer |
| SR-06 | Always preserve warning signs |

---

## 4. Non-Functional Requirements

### 4.1 Performance

| Requirement | Target |
|-------------|--------|
| End-to-end processing time | < 60 seconds |
| API response time | < 2 seconds |
| System uptime | 99.5% |

### 4.2 Security & Privacy

| Requirement | Implementation |
|-------------|----------------|
| Data encryption | AES-256 at rest, TLS 1.3 in transit |
| No PII storage | Delete within 24 hours |
| DPDPA Compliance | Consent, minimization, deletion rights |
| Data localization | All processing in India |

### 4.3 Scalability

| Requirement | Target |
|-------------|--------|
| Concurrent processing | 100 documents |
| Daily capacity | 10,000 discharges |
| Language support | 10+ Indian languages |

---

## 5. Technical Requirements

### 5.1 Technology Stack

| Component | Technology |
|-----------|------------|
| Frontend | React.js / Next.js |
| Backend | Python FastAPI |
| Database | PostgreSQL |
| Cache | Redis |
| OCR | Tesseract / Google Cloud Vision |
| NER | SpaCy + Custom Rules |
| LLM | GPT-4 / Claude |
| Translation | IndicTrans / Google Translate |
| TTS | Google Cloud Text-to-Speech |
| Delivery | WhatsApp Business API |

### 5.2 External Integrations

| Integration | Purpose |
|-------------|---------|
| WhatsApp Business API | Primary message delivery |
| SMS Gateway (Twilio) | Fallback delivery |
| OpenAI / Anthropic | LLM processing |
| Google Cloud | TTS and Translation |

---

## 6. Data Requirements

### 6.1 Synthetic Data (Hackathon)

| Data Type | Quantity |
|-----------|----------|
| Synthetic discharge summaries | 20-30 samples |
| Medical terminology database | 500+ terms |
| Drug database | 200+ Indian drugs |
| Translation pairs | 500+ term pairs |

### 6.2 Data Privacy

- **Development**: Only synthetic data used
- **Production**: Data deleted within 24 hours
- **Logs**: Anonymized, no PII stored

---


## 7. AI Algorithms & Pipeline Requirements

### 7.1 Six-Stage AI Pipeline

The SAMAJH system processes discharge summaries through a 6-stage AI pipeline:

| Stage | Name | Algorithm/Model | Input | Output |
|-------|------|-----------------|-------|--------|
| 1 | OCR | Tesseract + LSTM Neural Network | PDF/Image | Raw Text |
| 2 | NLP | SpaCy + Punkt + Regex | Raw Text | Structured Sections |
| 3 | NER | BiLSTM-CRF + Drug Database | Sections | Medical Entities |
| 4 | LLM | GPT-4/Claude + Prompt Engineering | Entities | Simplified Text |
| 5 | Translation | IndicTrans (Transformer NMT) | English Text | Regional Language |
| 6 | Output | WaveNet TTS + Pillow | All Data | Audio + Visual + Message |

### 7.2 Stage-wise Algorithm Requirements

#### Stage 1: OCR (Optical Character Recognition)
| Requirement | Specification |
|-------------|---------------|
| Algorithm | Tesseract 4.0+ with LSTM Neural Network |
| Preprocessing | Otsu's Thresholding, Hough Transform (Deskew) |
| Supported Formats | PDF, JPEG, PNG, Scanned Images |
| Accuracy Target | 95%+ on printed documents |
| Processing Time | < 10 seconds |

#### Stage 2: NLP (Natural Language Processing)
| Requirement | Specification |
|-------------|---------------|
| Tokenization | SpaCy Tokenizer (en_core_web_sm) |
| Sentence Boundary | Punkt Algorithm (NLTK) |
| Section Detection | Regex Pattern Matching |
| Sections Identified | Diagnosis, Medications, Advice, Warnings, Follow-up |

#### Stage 3: NER (Named Entity Recognition)
| Requirement | Specification |
|-------------|---------------|
| ML Model | BiLSTM-CRF (SpaCy en_core_sci_md) |
| Rule-based | Regex patterns for medication extraction |
| Database | Indian Drug Database (50,000+ drugs) |
| Fuzzy Matching | Levenshtein Distance (similarity > 85%) |
| Entities Extracted | Medicine Name, Dose, Frequency, Timing, Warnings |
| Accuracy Target | 95%+ for medications |

#### Stage 4: LLM (Large Language Model Simplification)
| Requirement | Specification |
|-------------|---------------|
| Model | GPT-4 / Claude API |
| Technique | Prompt Engineering + Few-shot Learning |
| Temperature | 0.3 (low for consistency) |
| Output Level | Grade 5-6 reading level |
| Safety Validation | 4-layer checks (med count, dosage, no new advice, disclaimer) |

#### Stage 5: Translation
| Requirement | Specification |
|-------------|---------------|
| Model | IndicTrans (AI4Bharat, IIT Madras) |
| Architecture | Transformer-based Neural Machine Translation |
| Languages | 11 Indian languages (Hindi, Tamil, Telugu, Kannada, Malayalam, Bengali, Marathi, Gujarati, Punjabi, Odia, Assamese) |
| Medical Terms | Custom dictionary override for accuracy |
| Quality Target | 98%+ translation accuracy |

#### Stage 6: Output Generation
| Requirement | Specification |
|-------------|---------------|
| TTS Engine | Google Cloud Text-to-Speech (WaveNet) |
| Voice | Indian female voices, 0.85x speed |
| Visual | Pillow/HTML2Image for schedule generation |
| Delivery | WhatsApp Business API (primary), SMS (fallback) |
| Outputs | MP3 audio, PNG schedule image, Text message |

### 7.3 Safety & Validation Requirements

| Check | Description | Implementation |
|-------|-------------|----------------|
| Medication Count | Input count must match output count | Automated comparison |
| Dosage Preservation | All dosages must remain unchanged | String matching |
| No New Advice | System cannot add medical advice | Content filtering |
| Disclaimer | Every output must include disclaimer | Hardcoded append |
| Low Confidence Flag | Flag extractions below 80% confidence | Human review queue |

### 7.4 Performance Requirements

| Metric | Target |
|--------|--------|
| End-to-end Processing | < 60 seconds |
| OCR Stage | < 10 seconds |
| NER Stage | < 5 seconds |
| LLM Stage | < 20 seconds |
| Translation Stage | < 10 seconds |
| Output Generation | < 15 seconds |


## 8. Acceptance Criteria

| ID | Criteria | Verification |
|----|----------|--------------|
| AC-01 | Upload PDF/Image successfully | Demo |
| AC-02 | Extract medications with >95% accuracy | Test data comparison |
| AC-03 | Simplify to Grade 5-6 level | Readability score |
| AC-04 | Translate to Hindi correctly | Manual verification |
| AC-05 | Generate audio in Hindi | Playback test |
| AC-06 | Display visual schedule | Visual inspection |
| AC-07 | Process in <60 seconds | Timing measurement |

---

## 9. Constraints & Assumptions

### Constraints
- Hackathon timeline limits feature scope
- Only synthetic data available
- Limited API budget
- Cannot provide medical advice

### Assumptions
- Discharge documents are legible
- Patients have WhatsApp access
- Standard medical document formats

---

## 10. Glossary

| Term | Definition |
|------|------------|
| NER | Named Entity Recognition |
| LLM | Large Language Model |
| TTS | Text-to-Speech |
| OCR | Optical Character Recognition |
| BD | Twice daily (Bis Die) |
| TDS | Three times daily |
| OD | Once daily |
| DPDPA | Digital Personal Data Protection Act 2023 |

---

## Important Notices

> **Synthetic Data Declaration**: This project uses ONLY synthetic/generated data. No real patient data is used.

> **Medical Disclaimer**: SAMAJH is an educational tool, NOT a replacement for professional medical advice.

---

*Document Version: 1.0 | Team Nexus07*