# SleepTrack: Sleep Tracking and Analysis Application

A comprehensive sleep tracking solution that bridges the gap between patient-reported sleep data and clinical insight. SleepTrack enables users to log sleep patterns via mobile devices and process sleep activity using microphone inputs, while healthcare professionals can access this data through a web dashboard to analyze trends and provide advice.

## Overview

SleepTrack is designed for digital clinics and sleep therapy centers, offering:

- Mobile-friendly interface for patients to log sleep data
- Optional audio recording during sleep for enhanced analysis
- Secure data storage and processing
- Professional dashboard for healthcare providers to monitor patient sleep patterns
- Role-based access control for data privacy

## Features

### Core Features (MVP)

- **Patient Features**
  - Register sleep start and wake-up times
  - Enable/disable microphone tracking during sleep
  - View personal sleep history and patterns

- **Healthcare Professional Features**
  - Access aggregated sleep data across patients
  - Drill down into individual patient sleep patterns
  - Analyze processed audio indicators (snoring, noise levels, etc.)

- **System Features**
  - Secure user authentication
  - Role-based access control
  - Audio processing for sleep activity indicators
  - Data persistence in MySQL database

### Future Enhancements

- Time-series visualization of sleep patterns
- Notification system for missed logs or anomalies
- Sleep hygiene advice based on activity
- Integration with wearable devices
- Sleep quality scoring
- In-app chat between patient and provider

## Tech Stack

- **Frontend**: React with TypeScript
- **Backend**: Python with FastAPI
- **Database**: MySQL
- **Deployment**: Docker
- **Audio Processing**: Python libraries (librosa, pydub, scipy)

## Installation

### Prerequisites

- Docker and Docker Compose
- Node.js (for local frontend development)
- Python 3.8+ (for local backend development)

### Using Docker (Recommended)

1. Clone the repository:
   ```
   git clone https://github.com/ketamai/sleeptrack.git
   cd sleeptrack
   ```

2. Create a `.env` file with necessary configuration:
   ```
   MYSQL_ROOT_PASSWORD=your_secure_password
   MYSQL_DATABASE=sleeptrack
   MYSQL_USER=sleeptrack_user
   MYSQL_PASSWORD=another_secure_password
   JWT_SECRET=your_jwt_secret
   ```

3. Start the application:
   ```
   docker-compose up -d
   ```

4. Access the application:
   - Frontend: http://localhost:3000
   - Backend API: http://localhost:8000
   - API Documentation: http://localhost:8000/docs

### Manual Setup

#### Backend

1. Navigate to the backend directory:
   ```
   cd backend
   ```

2. Create a virtual environment:
   ```
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. Install dependencies:
   ```
   pip install -r requirements.txt
   ```

4. Set up environment variables and run:
   ```
   uvicorn app.main:app --reload
   ```

#### Frontend

1. Navigate to the frontend directory:
   ```
   cd frontend
   ```

2. Install dependencies:
   ```
   npm install
   ```

3. Start the development server:
   ```
   npm start
   ```

## Project Structure

```
sleeptrack/
├── backend/
│   ├── app/
│   │   ├── api/
│   │   ├── core/
│   │   ├── db/
│   │   ├── models/
│   │   └── services/
│   ├── tests/
│   ├── Dockerfile
│   └── requirements.txt
├── frontend/
│   ├── public/
│   ├── src/
│   ├── Dockerfile
│   └── package.json
├── audio_processor/
│   ├── app/
│   ├── Dockerfile
│   └── requirements.txt
├── docker-compose.yml
└── README.md
```

## Development

### Backend Development

- API documentation available at `/docs` endpoint
- Run tests with `pytest`
- Use `alembic` for database migrations

### Frontend Development

- Components are organized by feature
- Use React hooks for state management
- Follow the established design system

## Security & Privacy

- All data is encrypted in transit (HTTPS)
- Audio data is processed and not stored in raw format
- Role-based access ensures patient data privacy
- Regular security audits are performed

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request