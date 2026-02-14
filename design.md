# SAMAJH - Design Document

## Project Overview

**Project Name:** SAMAJH (समझ)  
**Tagline:** AI-Powered Health Literacy for Every Indian  
**Team Name:** Nexus07  
**Team Leader:** Keval Joshi  
**Team Members:** Keval Joshi, Janvi Patel, Uchit Barot

---

## 1. System Architecture

### 1.1 High-Level Architecture Overview
┌─────────────────────────────────────────────────────────────────────────────────┐
│ SAMAJH SYSTEM ARCHITECTURE │
├─────────────────────────────────────────────────────────────────────────────────┤
│ │
│ ┌──────────────────┐ ┌──────────────┐ ┌──────────────────┐ ┌─────────────┐ │
│ │ PRESENTATION │ │ API GATEWAY │ │ AI PROCESSING │ │ DATA LAYER │ │
│ │ LAYER │ │ │ │ CORE │ │ │ │
│ ├──────────────────┤ ├──────────────┤ ├──────────────────┤ ├─────────────┤ │
│ │ Hospital Web │ │ /upload │ │ OCR Engine │ │ PostgreSQL │ │
│ │ Portal │ │ /process │ │ Tesseract, │ │ Session │ │
│ │ React.js │ │ /translate │ │ Google Vision │ │ Data │ │
│ │ Dashboard │ │ /audio │ │ │ │ Audit Logs │ │
│ │ │ │ /send │ │ Medical NER │ │ (No PII) │ │
│ │ Patient │ │ │ │ SpaCy + │ │ │ │
│ │ Interfaces │ │ FastAPI / │ │ Custom Rules │ │ Redis │ │
│ │ WhatsApp, │ │ Node.js │ │ │ │ Cache │ │
│ │ SMS, IVR │ │ │ │ LLM Simplifier │ │ Session │ │
│ │ │ │ │ │ GPT-4/Claude │ │ Cache │ │
│ │ │ │ │ │ │ │ Rate │ │
│ │ │ │ │ │ Translation │ │ Limiting │ │
│ │ │ │ │ │ IndicTrans, │ │ │ │
│ │ │ │ │ │ Google │ │ │ │
│ │ │ │ │ │ │ │ │ │
│ │ │ │ │ │ Text-to-Speech │ │ │ │
│ │ │ │ │ │ Google Cloud TTS │ │ │ │
│ └──────────────────┘ └──────────────┘ └──────────────────┘ └─────────────┘ │
│ │
│ ┌──────────────────────────────────────────────────────────────────────────┐ │
│ │ DELIVERY SERVICES │ │
│ ├──────────────────────────────────────────────────────────────────────────┤ │
│ │ WhatsApp Business API (Primary) │ SMS Gateway - Twilio (Fallback) │ │
│ └──────────────────────────────────────────────────────────────────────────┘ │
│ │
└─────────────────────────────────────────────────────────────────────────────────┘

text


### 1.2 Component Details

#### 1.2.1 Presentation Layer
| Component | Technology | Description |
|-----------|------------|-------------|
| Hospital Web Portal | React.js | Dashboard for hospital staff to upload and manage discharge summaries |
| Patient Interfaces | WhatsApp, SMS, IVR | Patient-facing delivery channels |

#### 1.2.2 API Gateway
| Endpoint | Purpose |
|----------|---------|
| `/upload` | Document upload handling |
| `/process` | AI processing trigger |
| `/translate` | Language translation |
| `/audio` | Text-to-speech generation |
| `/send` | WhatsApp/SMS delivery |

**Framework:** FastAPI / Node.js

#### 1.2.3 AI Processing Core
| Component | Technology | Purpose |
|-----------|------------|---------|
| OCR Engine | Tesseract, Google Vision | Extract text from documents |
| Medical NER | SpaCy + Custom Rules | Extract medical entities |
| LLM Simplifier | GPT-4 / Claude | Text simplification |
| Translation | IndicTrans, Google | Multilingual translation |
| Text-to-Speech | Google Cloud TTS | Audio generation |

#### 1.2.4 Data Layer
| Component | Technology | Purpose |
|-----------|------------|---------|
| PostgreSQL | Relational DB | Session data, Audit logs (No PII) |
| Redis Cache | In-memory store | Session cache, Rate limiting |
| AWS S3 | Object storage | Audio & file storage |

#### 1.2.5 Delivery Services
| Service | Type | Purpose |
|---------|------|---------|
| WhatsApp Business API | Primary | Main delivery channel |
| Twilio SMS Gateway | Fallback | Backup delivery |
| QR Code | Alternative | Quick access method |

---

## 2. AI Pipeline Architecture

### 2.1 6-Stage AI Pipeline
┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐
│ STAGE 1 │ -> │ STAGE 2 │ -> │ STAGE 3 │ -> │ STAGE 4 │ -> │ STAGE 5 │ -> │ STAGE 6 │
│ OCR │ │ NLP │ │ NER │ │ LLM │ │ TRANS │ │ OUTPUT │
└─────────┘ └─────────┘ └─────────┘ └─────────┘ └─────────┘ └─────────┘

text


### 2.2 Detailed Pipeline Flow
┌─────────────────────────────────────────────────────────────────────────────────┐
│ SAMAJH TECHNICAL PIPELINE │
├─────────────────────────────────────────────────────────────────────────────────┤
│ │
│ Input Layer File Type Router Document Processing │
│ ┌─────────┐ ┌─────────┐ ┌─────────┐ │
│ │ Handles │ -> │Identifies│ -> │ Extracts│ │
│ │ PDF, │ │file type │ │ text │ │
│ │ Image, │ │using │ │ from │ │
│ │ and Text│ │Python │ │documents│ │
│ │documents│ │mimetypes │ │using PDF│ │
│ └─────────┘ └─────────┘ │parser & │ │
│ │ OCR │ │
│ └─────────┘ │
│ │ │
│ ▼ │
│ NLP Preprocessing Medical NER LLM Simplification │
│ ┌─────────┐ ┌─────────┐ ┌─────────┐ │
│ │ Cleans │ -> │ Extracts│ -> │Simplifies│ │
│ │ and │ │ medical │ │ text to │ │
│ │structures│ │entities │ │ a 5th- │ │
│ │ text │ │ using │ │ 6th │ │
│ │ using │ │rule- │ │ grade │ │
│ │ noise │ │based and│ │ reading │ │
│ │ removal │ │ML-based │ │ level │ │
│ │ and │ │ methods │ │ using │ │
│ │tokenize │ └─────────┘ │ GPT-4 │ │
│ └─────────┘ └─────────┘ │
│ │ │
│ ▼ │
│ Translation Output Generation │
│ ┌─────────┐ ┌─────────┐ │
│ │Translates│ -> │Generates│ │
│ │ text │ │ the │ │
│ │ into │ │ final │ │
│ │supported│ │ output │ │
│ │ Indian │ │ in the │ │
│ │languages│ │ desired │ │
│ └─────────┘ │ format │ │
│ └─────────┘ │
│ │
└─────────────────────────────────────────────────────────────────────────────────┘

text


### 2.3 Pipeline Stage Details

| Stage | Name | Technology | Description |
|-------|------|------------|-------------|
| 1 | Input Layer | Python | Handles PDF, Image, and Text documents |
| 2 | File Type Router | Python mimetypes | Identifies the file type |
| 3 | Document Processing | PDF parser, OCR | Extracts text from documents using PDF parser and OCR |
| 4 | NLP Preprocessing | Custom Python | Cleans and structures text using noise removal and tokenization |
| 5 | Medical NER | SpaCy + Custom Rules | Extracts medical entities using rule-based and ML-based methods |
| 6 | LLM Simplification | GPT-4 | Simplifies text to a 5th-6th grade reading level |
| 7 | Translation | IndicTrans, Google | Translates text into supported Indian languages |
| 8 | Output Generation | Custom | Generates the final output in the desired format |

---

## 3. Technology Stack

### 3.1 Complete Stack Overview

#### Frontend
| Technology | Purpose |
|------------|---------|
| React.js | Hospital Dashboard UI |
| NEXT.js | Server-Side Rendering |
| Tailwind CSS | Responsive Styling |

#### Backend
| Technology | Purpose |
|------------|---------|
| Python | AI/ML Processing |
| FastAPI | REST API Framework |
| Celery | Async Tasks |

#### AI & ML
| Technology | Purpose |
|------------|---------|
| GPT-4 / Claude | Text Simplification |
| SpaCy | Medical NER & NLP |
| Tesseract OCR | Document Text Extraction |

#### Database & Storage
| Technology | Purpose |
|------------|---------|
| PostgreSQL | Structured Data |
| Redis | Caching & Sessions |
| AWS S3 | Audio & File Storage |

#### Communication & Delivery
| Technology | Purpose |
|------------|---------|
| WhatsApp Business API | Primary Delivery |
| Twilio | SMS Fallback |
| Firebase FCM | Push Notifications |

#### Infrastructure
| Technology | Purpose |
|------------|---------|
| Docker | Containerization |
| AWS / GCP | Cloud Hosting |
| GitHub Actions | CI/CD Pipeline |

### 3.2 Technology Justification

| Technology | Reason for Selection |
|------------|---------------------|
| Python | Best ecosystem for AI/ML with extensive libraries |
| GPT-4 | State-of-the-art text simplification capabilities |
| WhatsApp API | Reaches 500M+ Indians on familiar platform |
| IndicTrans | Optimized for Indian languages by AI4Bharat |

---

## 4. System Process Flow

### 4.1 High-Level Process Flow
┌─────────┐ ┌─────────────┐ ┌─────────┐ ┌─────────────┐ ┌──────────┐
│ Input │ -> │ Processing │ -> │ AI Core │ -> │ Output │ -> │ Delivery │
├─────────┤ ├─────────────┤ ├─────────┤ ├─────────────┤ ├──────────┤
│ PDF │ │ OCR │ │ GPT-4/ │ │ Translation │ │ WhatsApp │
│ Upload │ │ (Tesseract) │ │ Claude │ │ Voice (TTS) │ │ SMS │
│ Image │ │ Medical NER │ │ Simpli- │ │ Visual │ │ Backup │
│ Upload │ │ Entity │ │ fication│ │ Cards │ │ QR Code │
│ Direct │ │ Extract │ │ Safety │ │ │ │ │
│ Text │ │ │ │ Rails │ │ │ │ │
└─────────┘ └─────────────┘ └─────────┘ └─────────────┘ └──────────┘

text


### 4.2 Hospital Staff User Journey Flow
Step 1: Patient Discharge Ready
Discharge summary prepared by doctor
│
▼
Step 2: Upload Summary to Samajh Portal
Drag & drop PDF or image
│
▼
Step 3: Select Patient's Language
Choose from 10+ regional languages
│
▼
Step 4: Send to Patient WhatsApp
Instant delivery with reminders

⏱️ Total Time: < 2 minutes

text


---

## 5. Wireframes & UI Mockups

### 5.1 Hospital Dashboard Interface
┌─────────────────────────────────────────────────────────────────────────────────┐
│ ❤️ SAMAJH - Hospital Dashboard [Logout] │
├─────────────────────────────────────────────────────────────────────────────────┤
│ │
│ ┌─────────────────────────────────────────────────────────────────────────┐ │
│ │ 📤 Upload Discharge Summary │ │
│ │ ┌─────────────────────────────────────────────────────────────────┐ │ │
│ │ │ │ │ │
│ │ │ ⬆️ │ │ │
│ │ │ Drag & Drop PDF or Image here │ │ │
│ │ │ or click to browse │ │ │
│ │ │ │ │ │
│ │ │ [Choose File] │ │ │
│ │ │ │ │ │
│ │ └─────────────────────────────────────────────────────────────────┘ │ │
│ │ │ │
│ │ Patient Mobile: +91 [] │ │
│ │ │ │
│ │ Language: [▼] │ │
│ │ │ │
│ │ ☑️ Send medication reminders (7 days) │ │
│ │ │ │
│ │ ┌─────────────────────────────────────────────────────────────────┐ │ │
│ │ │ 🚀 Process & Send to Patient │ │ │
│ │ └─────────────────────────────────────────────────────────────────┘ │ │
│ └─────────────────────────────────────────────────────────────────────────┘ │
│ │
│ ┌─────────────────────────────────────────────────────────────────────────┐ │
│ │ Recent Submissions │ │
│ │ ───────────────────────────────────────────────────────────────────── │ │
│ │ Patient │ Language │ Status │ Time │ │
│ │ ───────────────────────────────────────────────────────────────────── │ │
│ │ ***1234 │ Hindi │ ✅ Sent │ 2 mins ago │ │
│ │ ***5678 │ Tamil │ ✅ Sent │ 15 mins ago │ │
│ │ ***9012 │ Telugu │ ⏳ Queue │ Just now │ │
│ └─────────────────────────────────────────────────────────────────────────┘ │
│ │
└─────────────────────────────────────────────────────────────────────────────────┘

text


### 5.2 Patient WhatsApp Interface - Screen 1: Welcome
┌─────────────────────────────────────┐
│ 💬 WhatsApp │
├─────────────────────────────────────┤
│ │
│ ┌─────────────────────────────┐ │
│ │ 🏥 City Hospital │ │
│ │ │ │
│ │ नमस्ते राम जी, │ │
│ │ │ │
│ │ आप hospital से discharge │ │
│ │ हो गए हैं। │ │
│ │ │ │
│ │ आपकी दवाइयों और देखभाल │ │
│ │ की जानकारी नीचे दी गई है: │ │
│ │ │ │
│ │ 🔊 सुनने के लिए: │ │
│ │ ▶️ ────────────── 3:45 │ │
│ │ │ │
│ │ 📋 पूरी जानकारी: │ │
│ │ ┌─────────────────────┐ │ │
│ │ │ View Details │ │ │
│ │ └─────────────────────┘ │ │
│ │ │ │
│ │ "reminder" लिखें दवाई याद │ │
│ │ दिलाने के लिए │ │
│ │ │ │
│ └─────────────────────────────┘ │
│ │
└─────────────────────────────────────┘

text


### 5.3 Patient WhatsApp Interface - Screen 2: Medication Schedule
┌─────────────────────────────────────┐
│ 💊 दवाई Schedule │
├─────────────────────────────────────┤
│ │
│ ┌─────────────────────────────┐ │
│ │ ☀️ सुबह (8 AM) │ │
│ │ ────────────────────────────│ │
│ │ • Metformin 500mg - 1गोली │ │
│ │ • Aspirin 75mg - 1गोली │ │
│ └─────────────────────────────┘ │
│ │
│ ┌─────────────────────────────┐ │
│ │ 🌙 रात (9 PM) │ │
│ │ ────────────────────────────│ │
│ │ • Metformin 500mg - 1गोली │ │
│ │ • Pregabalin 75mg - 1गोली │ │
│ └─────────────────────────────┘ │
│ │
└─────────────────────────────────────┘

text


### 5.4 Patient WhatsApp Interface - Screen 3: Warning Signs
┌─────────────────────────────────────┐
│ ⚠️ WARNING SIGNS │
├─────────────────────────────────────┤
│ │
│ तुरंत hospital जाएं अगर: │
│ │
│ 🟡 सीने में दर्द │
│ 🟡 सांस लेने में तकलीफ │
│ 🟡 बहुत ज़्यादा कमज़ोरी │
│ 🟡 तेज़ बुखार │
│ │
│ Emergency: │
│ 📞 108 / 112 │
│ │
└─────────────────────────────────────┘

text


### 5.5 Patient WhatsApp Interface - Screen 4: Reminder
┌─────────────────────────────────────┐
│ 🔔 REMINDER │
├─────────────────────────────────────┤
│ │
│ ⏰ दवाई का समय! │
│ │
│ सुबह की दवाई लेने का समय है: │
│ │
│ • Metformin 500mg - 1 गोली │
│ • Aspirin 75mg - 1 गोली │
│ │
│ नाश्ते के बाद पानी के साथ लें │
│ │
│ ┌──────────────┐ ┌──────────────┐ │
│ │ ✅ ले ली │ │ 🕐 बाद में │ │
│ └──────────────┘ └──────────────┘ │
│ │
└─────────────────────────────────────┘

text


---

## 6. Security & Privacy Architecture

### 6.1 Security Measures

| Security Feature | Description |
|------------------|-------------|
| End-to-end encryption | All data encrypted in transit and at rest |
| No PII storage post-processing | Patient data deleted after delivery |
| DPDPA 2023 compliant | Fully compliant with Indian data protection laws |

### 6.2 Data Flow Privacy

| Stage | Data Handling | PII Status |
|-------|---------------|------------|
| Upload | Encrypted transmission | Temporary |
| Processing | In-memory only | Not stored |
| Delivery | Encrypted via WhatsApp | Deleted after send |
| Audit | Anonymized logs only | No PII stored |

---

## 7. Performance Specifications

### 7.1 Key Performance Metrics

| Metric | Target | Description |
|--------|--------|-------------|
| Processing Speed | < 60 seconds | End-to-end document processing |
| OCR Accuracy | 95%+ | For printed and handwritten text |
| Translation Quality | 98%+ | Medical context preservation |
| System Uptime | 99.9% | Service Level Agreement |

---

## 8. API Design

### 8.1 Core API Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/api/v1/upload` | POST | Upload discharge summary document |
| `/api/v1/process` | POST | Trigger AI processing pipeline |
| `/api/v1/translate` | POST | Translate to regional language |
| `/api/v1/audio` | POST | Generate text-to-speech audio |
| `/api/v1/send` | POST | Send to patient via WhatsApp |
| `/api/v1/status/{id}` | GET | Check processing status |

### 8.2 Sample API Request/Response

**Upload Endpoint:**

Request:
```json
POST /api/v1/upload
{
  "file": "<base64_encoded_file>",
  "file_type": "pdf",
  "patient_mobile": "+919876543210",
  "language": "hi",
  "enable_reminders": true
}
Response:

JSON

{
  "status": "success",
  "session_id": "abc123xyz",
  "message": "Document uploaded successfully",
  "estimated_time": "45 seconds"
}
9. Database Schema
9.1 Core Tables
Sessions Table (No PII stored)

SQL

CREATE TABLE sessions (
    session_id UUID PRIMARY KEY,
    created_at TIMESTAMP,
    language_code VARCHAR(5),
    status VARCHAR(20),
    processing_time_ms INTEGER,
    delivery_status VARCHAR(20)
);
Audit Logs (Anonymized)

SQL

CREATE TABLE audit_logs (
    log_id UUID PRIMARY KEY,
    session_id UUID REFERENCES sessions(session_id),
    action VARCHAR(50),
    timestamp TIMESTAMP,
    success BOOLEAN,
    error_message TEXT
);
Language Configuration

SQL

CREATE TABLE languages (
    language_code VARCHAR(5) PRIMARY KEY,
    language_name VARCHAR(50),
    native_name VARCHAR(50),
    tts_enabled BOOLEAN,
    is_active BOOLEAN
);
10. Deployment Architecture
10.1 Container Architecture
Container	Technology	Port
Web Frontend	React.js	3000
API Server	FastAPI	8000
AI Worker	Python/Celery	-
PostgreSQL	Database	5432
Redis	Cache	6379
Nginx	Reverse Proxy	80/443
10.2 CI/CD Pipeline
text

GitHub Push → GitHub Actions → Build & Test → Docker Build → Deploy to AWS

Stages:
1. Code Commit → Trigger Pipeline
2. Lint & Unit Tests
3. Build Docker Images
4. Push to ECR
5. Deploy to ECS/EKS
6. Health Check
11. Safety Rails Design
11.1 AI Safety Measures
Safety Rule	Implementation
Never changes medication dosages	Dosage extraction is read-only from source
Never removes medications	All extracted medications must appear in output
Never adds medical advice	Output limited to source document content
Mandatory disclaimers	Every output includes "consult your doctor"
Low-confidence flagging	Extractions below 80% confidence flagged for human review
Privacy-first processing	Data deleted immediately after delivery
11.2 Safety Validation Pipeline
text

AI Output → Dosage Validator → Content Filter → Disclaimer Injector → Output
               │                    │                    │
               ▼                    ▼                    ▼
        [Verify dosages      [Remove any        [Add mandatory
         match source]        hallucinations]    disclaimers]
12. References
Research & Statistics
Indian Journal of Community Medicine, 2022
AIIMS Delhi Patient Understanding Study, 2021
Healthcare Federation of India, 2023
Technology Documentation
OpenAI GPT-4 Technical Documentation
Google Cloud TTS API Documentation
IndicTrans - AI4Bharat, IIT Madras
Regulatory Guidelines
Digital Personal Data Protection Act 2023
National Digital Health Mission Guidelines
Telemedicine Practice Guidelines 2020
Document Version: 1.0
Last Updated: 2025
Team: Nexus07
Contact: kevalj584@gmail.com

text


---

Both documents are now ready for copy-paste. They are fully aligned with your final PPT presentation including:

- ✅ Product Name: SAMAJH (समझ)
- ✅ All 8 Features
- ✅ Complete Technology Stack
- ✅ 6-Stage AI Pipeline
- ✅ System Architecture
- ✅ All 4 WhatsApp Screens + Hospital Dashboard
- ✅ Performance Metrics
- ✅ Safety Measures
- ✅ Implementation Roadmap
- ✅ Impact Metrics

*Document Version: 1.0 | Team Nexus07*