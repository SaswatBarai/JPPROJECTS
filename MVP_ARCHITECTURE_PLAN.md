# AI-Powered Anti-Fraudulent Message Detection and Advisory System
## Real-Time MVP - Complete Architecture & Implementation Plan

---

## 📋 Table of Contents

1. [System Architecture](#1-system-architecture)
2. [Workflow Diagram](#2-workflow-diagram)
3. [Backend (Go) Specification](#3-backend-go-specification)
4. [Frontend (TypeScript) Specification](#4-frontend-typescript-specification)
5. [AI/ML Logic Specification](#5-aiml-logic-specification)
6. [Database Schema](#6-database-schema)
7. [Implementation Plan (4 Days)](#7-implementation-plan-4-days)
8. [Demo Script](#8-demo-script)
9. [Code Structure](#9-code-structure)

---

## 1. System Architecture

### 1.1 High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         CLIENT LAYER                             │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  React/Next.js Frontend (TypeScript)                      │  │
│  │  - Upload Screen                                          │  │
│  │  - Dashboard                                              │  │
│  │  - Notification Center                                    │  │
│  └──────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
                              │
                              │ HTTP/WebSocket
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                      API GATEWAY LAYER                           │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  Go Backend Server                                        │  │
│  │  - REST API Endpoints                                     │  │
│  │  - WebSocket Server                                       │  │
│  │  - Request Router                                         │  │
│  └──────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
                              │
                ┌─────────────┼─────────────┐
                ▼             ▼             ▼
┌──────────────────┐  ┌──────────────┐  ┌──────────────┐
│  OCR Service     │  │  AI/ML       │  │  Rule Engine  │
│  (Python)        │  │  Engine      │  │  (Go)        │
│  - Tesseract     │  │  (Python)    │  │  - RBI Rules │
│  - Image Proc    │  │  - NLP       │  │  - Patterns  │
└──────────────────┘  │  - Scoring   │  └──────────────┘
                      └──────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                      DATA LAYER                                   │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  PostgreSQL Database                                      │  │
│  │  - Messages                                               │  │
│  │  - Risk Scores                                            │  │
│  │  - Advisory Log                                           │  │
│  │  - User Sessions                                          │  │
│  └──────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
```

### 1.2 Component Interaction Flow

```
User Upload → Frontend → Go API → OCR Service (Python)
                                    ↓
                              Text Extraction
                                    ↓
                              AI/ML Engine (Python)
                                    ↓
                              Risk Score Calculation
                                    ↓
                              Rule Engine (Go)
                                    ↓
                              Classification + Advisory
                                    ↓
                              WebSocket Notification
                                    ↓
                              Frontend Update
```

### 1.3 Deployment View

```
┌─────────────────────────────────────────────────────────────┐
│                    Docker Compose Stack                      │
│                                                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     │
│  │  Frontend    │  │  Go Backend  │  │  Python      │     │
│  │  Container   │  │  Container   │  │  Services    │     │
│  │  (Next.js)   │  │  (API+WS)    │  │  (OCR+ML)    │     │
│  └──────────────┘  └──────────────┘  └──────────────┘     │
│                                                              │
│  ┌──────────────────────────────────────────────────────┐  │
│  │         PostgreSQL Container                         │  │
│  └──────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

---

## 2. Workflow Diagram

### 2.1 End-to-End Flow

```
┌─────────────┐
│   User      │
│  Uploads    │
│ Text/Image   │
└──────┬──────┘
       │
       ▼
┌─────────────────────────────────────┐
│  Frontend: Upload Screen            │
│  - Validates file type              │
│  - Shows preview                    │
└──────┬──────────────────────────────┘
       │ POST /api/v1/analyze
       ▼
┌─────────────────────────────────────┐
│  Go Backend: Receives Request       │
│  - Creates analysis job             │
│  - Returns job_id                   │
│  - Opens WebSocket connection       │
└──────┬──────────────────────────────┘
       │
       ├───► Image? ──► OCR Service (Python)
       │                - Tesseract extraction
       │                - Text normalization
       │
       └───► Text? ───► Direct to AI/ML
       
       ▼
┌─────────────────────────────────────┐
│  AI/ML Engine (Python)              │
│  - NLP feature extraction           │
│  - Risk score calculation           │
│  - Pattern matching                 │
└──────┬──────────────────────────────┘
       │
       ▼
┌─────────────────────────────────────┐
│  Rule Engine (Go)                   │
│  - RBI compliance checks            │
│  - Bank name validation             │
│  - URL/domain analysis              │
│  - Final classification             │
└──────┬──────────────────────────────┘
       │
       ▼
┌─────────────────────────────────────┐
│  Advisory Generator                 │
│  - Maps risk to advisory            │
│  - Bank-specific guidance           │
│  - Official contact info            │
└──────┬──────────────────────────────┘
       │
       ▼
┌─────────────────────────────────────┐
│  Database: Save Results             │
│  - Message record                   │
│  - Risk score                       │
│  - Classification                   │
│  - Advisory                         │
└──────┬──────────────────────────────┘
       │
       ▼
┌─────────────────────────────────────┐
│  WebSocket: Real-time Notification │
│  - Status updates                   │
│  - Final result                     │
└──────┬──────────────────────────────┘
       │
       ▼
┌─────────────────────────────────────┐
│  Frontend: Dashboard Update         │
│  - Shows classification             │
│  - Displays advisory                │
│  - Updates notification center      │
└─────────────────────────────────────┘
```

---

## 3. Backend (Go) Specification

### 3.1 API Endpoints

#### REST API Structure

```
Base URL: /api/v1

POST   /analyze              - Upload text/image for analysis
GET    /analysis/{job_id}    - Get analysis status
GET    /history              - Get user's analysis history
GET    /advisory/{message_id} - Get detailed advisory
DELETE /analysis/{job_id}    - Delete analysis record
```

#### Request/Response Models

**POST /api/v1/analyze**
```json
Request:
{
  "type": "text" | "image",
  "content": "base64_encoded_string" | "plain_text",
  "user_id": "optional_session_id"
}

Response:
{
  "job_id": "uuid",
  "status": "processing",
  "websocket_url": "ws://host/ws/{job_id}"
}
```

**GET /api/v1/analysis/{job_id}**
```json
Response:
{
  "job_id": "uuid",
  "status": "completed" | "processing" | "failed",
  "result": {
    "message": "extracted_text",
    "risk_score": 0.92,
    "classification": "fraud" | "suspicious" | "legitimate",
    "bank_detected": "SBI",
    "advisory": {
      "title": "Fraud Alert",
      "message": "Visit official SBI app or bank branch",
      "official_contact": "+91-1800-123-456"
    },
    "features": {
      "has_suspicious_url": true,
      "has_urgency_keywords": true,
      "bank_name_match": true
    }
  }
}
```

### 3.2 WebSocket Notification Service

**Connection:** `ws://host/ws/{job_id}`

**Message Types:**

1. **Status Update**
```json
{
  "type": "status",
  "job_id": "uuid",
  "status": "processing",
  "stage": "ocr" | "nlp" | "classification",
  "progress": 0.5
}
```

2. **Result Notification**
```json
{
  "type": "result",
  "job_id": "uuid",
  "result": { /* full analysis result */ }
}
```

3. **Error Notification**
```json
{
  "type": "error",
  "job_id": "uuid",
  "error": "error_message"
}
```

### 3.3 OCR Integration

**Service Interface:**
- Go backend calls Python OCR service via HTTP/gRPC
- Python service exposes: `POST /ocr/extract`
- Returns extracted text and confidence scores

### 3.4 NLP Scoring Pipeline

**Service Interface:**
- Go backend calls Python ML service: `POST /ml/analyze`
- Input: extracted text
- Output: risk score, features, confidence

---

## 4. Frontend (TypeScript) Specification

### 4.1 Screen Specifications

#### 4.1.1 Upload Screen

**Components:**
- File upload area (drag & drop)
- Text input area (alternative)
- Preview component
- Submit button
- Loading state

**State Management:**
- Uploaded file/image
- Text input value
- Upload status
- Job ID

**API Integration:**
- `POST /api/v1/analyze`
- WebSocket connection management

#### 4.1.2 Dashboard

**Components:**
- Analysis results card
- Risk score visualization (gauge/chart)
- Classification badge (color-coded)
- Advisory panel
- Bank information card

**Features:**
- Real-time updates via WebSocket
- Historical results list
- Export/share functionality

#### 4.1.3 Notification Center

**Components:**
- Notification list
- Real-time WebSocket updates
- Filter by status/type
- Mark as read/unread

### 4.2 API Calls

**Service Layer Structure:**

```typescript
// services/api.ts
- analyzeMessage(type, content)
- getAnalysisStatus(jobId)
- getAnalysisHistory()
- getAdvisory(messageId)
```

### 4.3 WebSocket Subscriptions

**WebSocket Client:**
- Connect on job creation
- Subscribe to job_id channel
- Handle status updates
- Handle result notifications
- Auto-reconnect logic
- Error handling

---

## 5. AI/ML Logic Specification

### 5.1 Risk Score Calculation Formula

```
Risk Score = (
    URL_Risk_Weight * URL_Score +
    Urgency_Risk_Weight * Urgency_Score +
    Bank_Mismatch_Weight * Bank_Mismatch_Score +
    Pattern_Risk_Weight * Pattern_Score +
    NLP_Model_Weight * NLP_Model_Score
) / Total_Weight

Where:
- URL_Score: 0.0-1.0 (based on suspicious domains, short links)
- Urgency_Score: 0.0-1.0 (urgency keywords: "urgent", "immediate", "blocked")
- Bank_Mismatch_Score: 0.0-1.0 (bank name vs official domain mismatch)
- Pattern_Score: 0.0-1.0 (known fraud patterns)
- NLP_Model_Score: 0.0-1.0 (ML model prediction)

Weights (example):
- URL_Risk_Weight: 0.25
- Urgency_Risk_Weight: 0.15
- Bank_Mismatch_Weight: 0.20
- Pattern_Risk_Weight: 0.20
- NLP_Model_Weight: 0.20
```

### 5.2 Classification Rules

```
IF risk_score >= 0.75:
    classification = "fraud"
ELSE IF risk_score >= 0.50:
    classification = "suspicious"
ELSE:
    classification = "legitimate"
```

### 5.3 RBI Rule-Based Validation

**Rules:**
1. **Bank Name Validation**
   - Extract bank name from message
   - Check against official bank list
   - Verify domain/contact matches official records

2. **URL Analysis**
   - Check for suspicious TLDs
   - Verify short link expansion
   - Compare against known phishing domains

3. **Contact Number Validation**
   - Check against official bank contact databases
   - Flag non-official numbers

4. **Language Patterns**
   - Detect grammatical errors
   - Check for urgency manipulation
   - Identify impersonation attempts

### 5.4 Knowledge Base Structure

**Bank Database:**
```json
{
  "bank_code": "SBI",
  "official_name": "State Bank of India",
  "official_domains": ["sbi.co.in", "onlinesbi.com"],
  "official_contacts": ["1800-123-456", "1800-425-3800"],
  "official_apps": ["SBI Anywhere", "SBI YONO"],
  "branches_api": "https://api.sbi.co.in/branches"
}
```

**Fraud Pattern Database:**
- Known phishing URLs
- Suspicious keyword combinations
- Common fraud templates
- Reported fraud cases

---

## 6. Database Schema

### 6.1 PostgreSQL Schema

#### Messages Table
```sql
CREATE TABLE messages (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    job_id UUID UNIQUE NOT NULL,
    user_id VARCHAR(255),
    content_type VARCHAR(10) NOT NULL, -- 'text' | 'image'
    original_content TEXT,
    extracted_text TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

#### Analysis Results Table
```sql
CREATE TABLE analysis_results (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    message_id UUID REFERENCES messages(id),
    risk_score DECIMAL(3,2) NOT NULL,
    classification VARCHAR(20) NOT NULL, -- 'fraud' | 'suspicious' | 'legitimate'
    bank_detected VARCHAR(50),
    confidence DECIMAL(3,2),
    features JSONB,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

#### Advisory Log Table
```sql
CREATE TABLE advisory_log (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    analysis_result_id UUID REFERENCES analysis_results(id),
    advisory_title VARCHAR(255),
    advisory_message TEXT,
    official_contact VARCHAR(50),
    official_domain VARCHAR(255),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

#### User Sessions Table
```sql
CREATE TABLE user_sessions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    session_id VARCHAR(255) UNIQUE NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    last_activity TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### 6.2 Indexes

```sql
CREATE INDEX idx_messages_job_id ON messages(job_id);
CREATE INDEX idx_messages_user_id ON messages(user_id);
CREATE INDEX idx_analysis_results_message_id ON analysis_results(message_id);
CREATE INDEX idx_analysis_results_classification ON analysis_results(classification);
CREATE INDEX idx_advisory_log_result_id ON advisory_log(analysis_result_id);
```

---

## 7. Implementation Plan (4 Days)

### Day 1: Foundation & Backend Core

**Morning (4 hours):**
- [ ] Project structure setup (Go, Python, TypeScript)
- [ ] PostgreSQL database setup and schema creation
- [ ] Go backend: Basic HTTP server, routing setup
- [ ] Go backend: Database connection and models

**Afternoon (4 hours):**
- [ ] Go backend: REST API endpoints (analyze, status, history)
- [ ] Go backend: Request/response models
- [ ] Python OCR service: Tesseract integration
- [ ] Python OCR service: HTTP API endpoint

**Deliverables:**
- Working Go API server
- Basic OCR service
- Database schema deployed

### Day 2: AI/ML Engine & Rule Engine

**Morning (4 hours):**
- [ ] Python ML service: NLP feature extraction
- [ ] Python ML service: Risk score calculation
- [ ] Python ML service: HTTP API endpoint
- [ ] Go backend: Integration with Python services

**Afternoon (4 hours):**
- [ ] Go rule engine: RBI compliance rules
- [ ] Go rule engine: Bank name validation
- [ ] Go rule engine: URL/domain analysis
- [ ] Go rule engine: Classification logic
- [ ] Knowledge base: Bank database setup

**Deliverables:**
- Complete AI/ML pipeline
- Rule-based classification
- End-to-end analysis flow

### Day 3: WebSocket & Frontend

**Morning (4 hours):**
- [ ] Go backend: WebSocket server implementation
- [ ] Go backend: Real-time notification system
- [ ] Frontend: Project setup (Next.js/React)
- [ ] Frontend: Upload screen component

**Afternoon (4 hours):**
- [ ] Frontend: Dashboard component
- [ ] Frontend: WebSocket client integration
- [ ] Frontend: API service layer
- [ ] Frontend: Notification center

**Deliverables:**
- Real-time WebSocket notifications
- Complete frontend UI
- Full user flow working

### Day 4: Integration, Testing & Demo Prep

**Morning (4 hours):**
- [ ] End-to-end integration testing
- [ ] Bug fixes and refinements
- [ ] Advisory generation logic
- [ ] Error handling improvements

**Afternoon (4 hours):**
- [ ] Docker setup (optional)
- [ ] Demo script preparation
- [ ] Documentation finalization
- [ ] Presentation materials

**Deliverables:**
- Fully functional MVP
- Demo-ready application
- Documentation complete

---

## 8. Demo Script

### 8.1 2-Minute Presentation Flow

**Problem (20 seconds):**
- "Financial fraud through SMS/phishing is increasing"
- "Users need real-time detection and official guidance"
- "Current solutions are reactive, not preventive"

**Solution (30 seconds):**
- "AI-powered system that analyzes messages in real-time"
- "Combines NLP, rule-based checks, and RBI compliance"
- "Provides instant advisory with official bank contacts"

**Demo (60 seconds):**
1. **Upload suspicious SMS** (15s)
   - Show upload screen
   - Paste: "Your SBI account is blocked. Update KYC here: bit.ly/xyz"

2. **Real-time Analysis** (20s)
   - Show WebSocket status updates
   - Display OCR extraction (if image)
   - Show processing stages

3. **Results Display** (15s)
   - Risk score: 0.92 (high)
   - Classification: FRAUD
   - Bank detected: SBI
   - Advisory: "Visit official SBI app or bank branch"

4. **Dashboard Features** (10s)
   - Show historical analysis
   - Notification center
   - Export/share options

**Impact (10 seconds):**
- "Prevents financial loss through early detection"
- "Empowers users with official guidance"
- "Scalable to all major Indian banks"

### 8.2 Example Inputs & Outputs

#### Example 1: Fraudulent SMS
**Input:**
```
"Your SBI account is blocked. Update KYC here: bit.ly/xyz"
```

**Output:**
```json
{
  "bank_detected": "SBI",
  "risk_score": 0.92,
  "classification": "fraud",
  "advisory": {
    "title": "Fraud Alert",
    "message": "This message contains suspicious elements. Do not click any links. Visit official SBI app (SBI Anywhere) or contact your bank branch directly.",
    "official_contact": "1800-123-456",
    "official_domain": "sbi.co.in"
  },
  "features": {
    "has_suspicious_url": true,
    "has_urgency_keywords": true,
    "bank_name_match": true,
    "url_domain_mismatch": true
  }
}
```

#### Example 2: Legitimate Message
**Input:**
```
"Your SBI account statement for Jan 2024 is ready. View in SBI Anywhere app."
```

**Output:**
```json
{
  "bank_detected": "SBI",
  "risk_score": 0.15,
  "classification": "legitimate",
  "advisory": {
    "title": "Verified Message",
    "message": "This message appears legitimate. Always verify through official SBI channels.",
    "official_contact": "1800-123-456"
  }
}
```

#### Example 3: Suspicious Message
**Input:**
```
"URGENT: Your HDFC card needs verification. Call 9876543210 immediately."
```

**Output:**
```json
{
  "bank_detected": "HDFC",
  "risk_score": 0.65,
  "classification": "suspicious",
  "advisory": {
    "title": "Caution Advised",
    "message": "This message shows suspicious patterns. Verify through official HDFC channels before taking any action.",
    "official_contact": "1800-202-6161",
    "official_domain": "hdfcbank.com"
  }
}
```

---

## 9. Code Structure

### 9.1 Backend (Go) Structure

```
backend/
├── cmd/
│   └── server/
│       └── main.go
├── internal/
│   ├── api/
│   │   ├── handlers.go
│   │   ├── routes.go
│   │   └── middleware.go
│   ├── models/
│   │   ├── message.go
│   │   ├── analysis.go
│   │   └── advisory.go
│   ├── services/
│   │   ├── ocr_client.go
│   │   ├── ml_client.go
│   │   ├── rule_engine.go
│   │   └── advisory_service.go
│   ├── websocket/
│   │   ├── server.go
│   │   └── hub.go
│   └── database/
│       ├── connection.go
│       └── migrations.go
├── pkg/
│   └── utils/
│       ├── validators.go
│       └── helpers.go
├── config/
│   └── config.go
├── go.mod
└── go.sum
```

### 9.2 Python Services Structure

```
python-services/
├── ocr_service/
│   ├── app.py
│   ├── ocr_engine.py
│   ├── image_processor.py
│   └── requirements.txt
├── ml_service/
│   ├── app.py
│   ├── nlp_analyzer.py
│   ├── risk_calculator.py
│   ├── models/
│   │   └── fraud_detector.py
│   └── requirements.txt
└── knowledge_base/
    ├── banks.json
    ├── fraud_patterns.json
    └── rules.json
```

### 9.3 Frontend (TypeScript) Structure

```
frontend/
├── src/
│   ├── app/
│   │   ├── page.tsx
│   │   ├── dashboard/
│   │   │   └── page.tsx
│   │   └── layout.tsx
│   ├── components/
│   │   ├── UploadScreen.tsx
│   │   ├── Dashboard.tsx
│   │   ├── NotificationCenter.tsx
│   │   ├── RiskScoreGauge.tsx
│   │   └── AdvisoryPanel.tsx
│   ├── services/
│   │   ├── api.ts
│   │   └── websocket.ts
│   ├── hooks/
│   │   ├── useWebSocket.ts
│   │   └── useAnalysis.ts
│   ├── types/
│   │   └── index.ts
│   └── utils/
│       └── helpers.ts
├── package.json
└── tsconfig.json
```

### 9.4 Key Code Snippets (Structure Only)

#### Go: Main Server Entry
```go
// cmd/server/main.go structure
- Initialize config
- Setup database connection
- Initialize HTTP router
- Setup WebSocket hub
- Start server
```

#### Go: Analysis Handler
```go
// internal/api/handlers.go structure
- POST /analyze handler
- Create job_id
- Start async processing
- Return job_id and WebSocket URL
- Send status updates via WebSocket
```

#### Go: WebSocket Hub
```go
// internal/websocket/hub.go structure
- Manage client connections
- Broadcast to specific job channels
- Handle connection lifecycle
```

#### Python: OCR Service
```python
# ocr_service/app.py structure
- Flask/FastAPI app
- POST /ocr/extract endpoint
- Call Tesseract OCR
- Return extracted text
```

#### Python: ML Service
```python
# ml_service/app.py structure
- Flask/FastAPI app
- POST /ml/analyze endpoint
- Extract NLP features
- Calculate risk score
- Return analysis result
```

#### TypeScript: WebSocket Client
```typescript
// services/websocket.ts structure
- WebSocket connection manager
- Subscribe to job_id
- Handle message types
- Auto-reconnect logic
```

#### TypeScript: Upload Component
```typescript
// components/UploadScreen.tsx structure
- File upload handler
- Text input handler
- API call to /analyze
- WebSocket connection setup
- Loading states
```

---

## 10. Additional Considerations

### 10.1 Security
- Input validation and sanitization
- Rate limiting on API endpoints
- Secure WebSocket connections (WSS)
- SQL injection prevention
- File upload size limits

### 10.2 Performance
- Async processing for long-running tasks
- Connection pooling for database
- Caching for bank database lookups
- Image optimization before OCR

### 10.3 Scalability
- Horizontal scaling for Python services
- WebSocket load balancing considerations
- Database connection pooling
- Queue system for high load (optional)

### 10.4 Monitoring & Logging
- Structured logging
- Error tracking
- Performance metrics
- Analysis success/failure rates

---

## 11. Technology Stack Summary

| Component | Technology |
|-----------|-----------|
| Backend API | Go (Golang) |
| Frontend | TypeScript (React/Next.js) |
| OCR Service | Python + Tesseract |
| ML/AI Service | Python (NLP libraries) |
| Database | PostgreSQL |
| Real-time | WebSockets (Go) |
| Deployment | Docker (Optional) |

---

## 12. Success Metrics

- **Accuracy**: >85% fraud detection rate
- **Latency**: <5 seconds end-to-end analysis
- **Availability**: 99% uptime during demo
- **User Experience**: Intuitive 3-click flow

---

**Document Version:** 1.0  
**Last Updated:** 2024  
**Prepared for:** JPMC Hackathon MVP

