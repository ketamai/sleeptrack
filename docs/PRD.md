# SPEC-01: Sleep Tracking and Analysis Application

## Background

Sleep disorders affect a significant portion of the population, and early intervention is critical. While many consumer-grade apps exist, they often lack medical-grade accuracy and clinician involvement. This product aims to bridge the gap between patient-reported sleep data and clinical insight by allowing users to log their sleep patterns via mobile devices and process sleep activity using microphone inputs. Healthcare professionals can access this data through a web dashboard to analyze trends and advise accordingly. The app targets digital clinics and sleep therapy centers as its primary customers.

## Requirements

The product requirements are structured using MoSCoW prioritization:

**Must Have**
- Users can register sleep start and wake-up times via a mobile interface.
- Users can optionally enable microphone tracking during sleep.
- Audio data is processed into sleep activity indicators (e.g., noise levels, snoring, movement proxies).
- Secure user registration and login functionality.
- Storage of user sleep logs and indicators in a MySQL database.
- A web dashboard for healthcare professionals to:
  - View aggregated sleep data across patients.
  - Drill down into individual patient sleep patterns.
- Role-based access control (patients vs healthcare professionals).
- Dockerized deployment for backend and frontend services.

**Should Have**
- Time-series visualization of sleep patterns and anomalies.
- Notification system for missed sleep logs or anomalies.
- Support for basic sleep hygiene advice based on activity.

**Could Have**
- Integration with wearable devices or mobile sensors.
- Sleep quality scoring (e.g., based on consistency and disturbances).
- In-app chat between patient and provider.

**Won't Have (for MVP)**
- Real-time monitoring or alerts during sleep.
- AI-based diagnosis or prescription recommendation.

## Method

The system is composed of three main components:

1. Mobile/Web Frontend (React) for both patients and healthcare providers.
2. Backend API service (Python FastAPI) to handle all data logic and user authentication.
3. MySQL database to persist user data and sleep logs.
4. Microservice for audio processing and sleep activity extraction.

### System Architecture

```
┌─────────┐     ┌─────────┐
│ Patient │     │ Doctor  │
└────┬────┘     └────┬────┘
     │               │
     ▼               ▼
┌─────────────────────────┐
│        Frontend         │
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│         Backend         │
└────┬───────────┬────────┘
     │           │
     ▼           ▼
┌─────────┐ ┌──────────────┐
│  MySQL  │ │AudioProcessor│
└─────────┘ └──────────────┘
```

### Component Overview

**Frontend (React)**
- Patient interface: Sleep log input, microphone recording toggle, history view.
- Doctor interface: Patient list, analytics dashboard, patient detail views.

**Backend (FastAPI)**
- Auth endpoints: Login, register, role assignment.
- Sleep log endpoints: Create, retrieve, update.
- Audio endpoint: Accept audio upload and trigger processing.
- Admin endpoints: Assign roles, list users.

**Audio Processor**
- Accepts uploaded audio.
- Uses signal processing or lightweight ML models to detect:
  - Snoring events
  - High noise levels
  - Duration of silence
- Returns JSON with nightly indicators.

**Database (MySQL)**

```sql
-- Users table
CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  password_hash VARCHAR(255) NOT NULL,
  role ENUM('patient', 'doctor') NOT NULL,
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);

-- Sleep sessions
CREATE TABLE sleep_logs (
  id INT AUTO_INCREMENT PRIMARY KEY,
  user_id INT,
  start_time DATETIME NOT NULL,
  end_time DATETIME NOT NULL,
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (user_id) REFERENCES users(id)
);

-- Processed audio indicators
CREATE TABLE sleep_indicators (
  id INT AUTO_INCREMENT PRIMARY KEY,
  sleep_log_id INT,
  snore_events INT DEFAULT 0,
  noise_duration_seconds INT DEFAULT 0,
  silence_periods INT DEFAULT 0,
  FOREIGN KEY (sleep_log_id) REFERENCES sleep_logs(id)
);
```

## Implementation

### 1. Backend Setup (Python + FastAPI)
- Set up FastAPI project structure with routers for:
  - `/auth`: registration, login, JWT issuance
  - `/sleep`: create, retrieve sleep logs
  - `/audio`: upload audio and trigger background processing
- Implement role-based middleware using JWT claims
- Connect to MySQL using SQLAlchemy ORM
- Secure all endpoints using OAuth2 with JWT tokens
- Implement worker service (e.g. Celery or background tasks) to handle audio processing asynchronously

### 2. Frontend Setup (React)
- Create two main role-based views:
  - **Patient Portal**: 
    - Sleep session input form (start/end time)
    - Audio recording toggle (WebRTC API or mobile-native hooks)
    - Session history list
  - **Doctor Dashboard**:
    - Patient list (searchable/filterable)
    - Visual summary (charts) of sleep indicators
    - Detailed patient view per session

### 3. Audio Processor
- Build a microservice using Python (Flask or lightweight FastAPI)
- Use libraries like `librosa`, `pydub`, or `scipy` for:
  - Snore detection (volume peaks and frequency analysis)
  - Silence detection (long gaps below threshold)
  - Overall noise/activity estimation
- Return a structured JSON result with metrics for storage

### 4. MySQL Database
- Use Docker Compose to bundle the backend and MySQL database
- Apply database migrations using Alembic

### 5. Dockerization
- Create Dockerfiles for:
  - Backend
  - Frontend
  - Audio processor
- Create a `docker-compose.yml` to orchestrate:
  - Backend API
  - Frontend
  - MySQL
  - Audio processing service
- Add volumes for persistent MySQL storage and environment variables for secrets

### 6. Security & Data Privacy
- Use HTTPS in production
- Encrypt audio files at rest if stored temporarily
- Apply RBAC to ensure only doctors can access multiple patients data

### 7. Testing
- Write unit tests for API endpoints using `pytest`
- Add frontend integration tests using `React Testing Library`

## Milestones

### Milestone 1: Project Setup & Core Infrastructure (Week 1–2)
- Set up monorepo with backend, frontend, and audio service
- Dockerize all services and configure Docker Compose
- Initialize MySQL schema and migrations with Alembic
- Implement user authentication and role-based access

### Milestone 2: Patient Sleep Logging (Week 3)
- Build patient interface (React) for logging sleep sessions
- Backend API to save and retrieve logs
- Frontend history view for patient logs
- Connect UI to backend using REST APIs

### Milestone 3: Audio Upload & Processing (Week 4–5)
- Implement audio upload (frontend + backend API)
- Process audio in background using simple detection algorithms
- Save and display sleep indicators in user's sleep history

### Milestone 4: Healthcare Professional Dashboard (Week 6–7)
- Build provider dashboard interface with patient list
- Add per-patient view with visualized sleep patterns
- Implement filtering and drill-down features
- Display aggregated sleep indicators and insights

### Milestone 5: QA, Security, and Deployment (Week 8)
- Add tests (unit + integration)
- Add HTTPS support and JWT-based access control
- Run security audit (basic checks for sensitive data, access control)
- Deploy Docker stack 

## Gathering Results

Post-deployment, the system should be evaluated both technically and from a clinical outcomes perspective.

### Technical Metrics
- **Sleep Log Completion Rate**: % of nights where users log sleep data
- **Audio Processing Accuracy**: Manual validation of snore/noise detection against known events
- **System Uptime**: Aim for >99% uptime with Dockerized services
- **Latency**: Audio upload and indicator return time should remain under 30 seconds
- **Security Audits**: Periodic verification of access logs, encryption, and data exposure risk

### Clinical Metrics
- **Adoption by Professionals**: Number of active sleep specialists using the dashboard weekly
- **Patient Engagement**: Frequency of log entries per patient
- **Insight-to-Advice Ratio**: How often logged data leads to behavioral recommendations
- **Patient Feedback**: Qualitative reports on usability and effectiveness

These metrics will help validate the platform's real-world usefulness in clinical workflows and shape future feature development such as AI-based scoring, deeper audio analysis, and wearable integrations.