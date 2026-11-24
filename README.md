[mentormatch_repository_readme.md](https://github.com/user-attachments/files/35335712/mentormatch_repository_readme.md)
# MentorMatch: Intelligent Graduate Mentorship Platform

[![Live Demo](https://img.shields.io/badge/Vercel-Hosted_App-brightgreen)](https://mentor-match-one.vercel.app)
[![Backend Framework](https://img.shields.io/badge/FastAPI-Python_3.11-blue)](https://fastapi.tiangolo.com/)
[![Frontend Framework](https://img.shields.io/badge/React-18-61dafb)](https://react.dev/)
[![NLP Engine](https://img.shields.io/badge/spaCy-en__core__web__sm-orange)](https://spacy.io/)
[![Database](https://img.shields.io/badge/PostgreSQL-15-336791)](https://www.postgresql.org/)

**MentorMatch** is an end-to-end, full-stack web application designed to connect recent university graduates with compatible industry mentors. The platform features an explainable Natural Language Processing (NLP) matching engine that extracts domain-specific skills from uploaded PDF resumes and evaluates compatibility against mentor profiles using TF-IDF vectorization and Cosine Similarity. The system also supports role-based onboarding, transactional session booking, and mutual goal tracking.

---

## Key Features

* **Role-Based Authentication & Workspaces:** Secure JWT-based authentication for Mentees and Mentors with distinct dashboard workflows.
* **Automated PDF Resume Ingestion:** Server-side PDF validation ($\le 5\text{ MB}$, `%PDF-` binary check) and text extraction using `pdfplumber` / `PyPDF2`.
* **Explainable NLP Skill Matching:** Preprocessing pipeline combining `spaCy` (`en_core_web_sm`) lemmatization and a custom `EntityRuler` pattern library to identify technical competencies.
* **Structured Profile Fallback:** Guarantees mentor recommendations remain available even when a mentee has not uploaded a PDF resume by deriving query features from structured profile attributes.
* **Transparent Compatibility Ranking:** TF-IDF feature vectorization and pairwise Cosine Similarity compute percentage match scores (e.g., `96% MATCH`) along with explicit overlapping skill badges.
* **Atomic Session Scheduling:** Transactional calendar slot booking that locks available mentor time slots to prevent double-booking.
* **Mentorship Goal Tracker:** Shared goal management allowing mentees and mentors to monitor categorized milestones and track percentage completion (`0%` to `100%`).

---

## Tech Stack

| Layer | Technology / Library | Role |
| :--- | :--- | :--- |
| **Frontend** | React 18, JavaScript (ES6+), HTML5/CSS3 | Single-Page Web Interface |
| **Hosting (Client)** | Vercel Edge Network | Production Frontend Hosting |
| **Backend API** | Python 3.11, FastAPI, Uvicorn, Pydantic | RESTful API Services & Security |
| **NLP & Vectorization** | spaCy (`en_core_web_sm`), `scikit-learn` | Skill Extraction, TF-IDF, Cosine Similarity |
| **PDF Processing** | `PyPDF2`, `pdfplumber` | Document Ingestion & Text Extraction |
| **Database** | PostgreSQL, SQLAlchemy ORM | Relational Data Persistence |
| **Containerization** | Docker, Docker Compose | Development & Production Environment Setup |
| **Testing** | `pytest`, `httpx` | Unit, Integration, and Benchmark Testing |

---

## Repository Structure

```text
mentormatch/
├── backend/
│   ├── app/
│   │   ├── api/             # REST API endpoint routes (auth, profile, matching, slots, goals)
│   │   ├── core/            # Security (JWT, bcrypt), database session configuration
│   │   ├── db/              # SQLAlchemy models and schema migration scripts
│   │   ├── nlp/             # spaCy EntityRuler, TF-IDF vectorizer, matching logic
│   │   │   └── patterns/    # skills_patterns.jsonl for custom entity extraction
│   │   ├── schemas/         # Pydantic request/response data models
│   │   └── main.py          # FastAPI application entry point
│   ├── tests/               # pytest suite (auth, matching, scheduling, concurrency)
│   ├── Dockerfile
│   └── requirements.txt
├── frontend/
│   ├── public/              # Static web assets
│   ├── src/
│   │   ├── components/      # Reusable React UI components (Navbar, Cards, Modals)
│   │   ├── pages/           # Mentee/Mentor Dashboards, Profile, Login, Scheduling
│   │   ├── services/        # Axios/Fetch API client bindings
│   │   └── App.jsx          # Primary application component & router
│   ├── package.json
│   └── vite.config.js
├── docs/
│   └── AI_USE_LOG.md        # Log of AI-assisted coding and editorial usage
├── docker-compose.yml
└── README.md
```

---

## System Requirements

* **Node.js:** `v18.x` or higher
* **npm:** `v9.x` or higher
* **Python:** `v3.11`
* **PostgreSQL:** `v15` or higher (or Docker)
* **Docker & Docker Compose:** *(Optional, for containerized setup)*

---

## Environment Setup

### 1. Database Configuration
Create a local PostgreSQL database named `mentormatch_db`:

```sql
CREATE DATABASE mentormatch_db;
CREATE USER mentormatch_user WITH PASSWORD 'secure_password';
GRANT ALL PRIVILEGES ON DATABASE mentormatch_db TO mentormatch_user;
```

### 2. Backend Environment Variables (`backend/.env`)
Create a `.env` file inside the `backend/` directory:

```env
PROJECT_NAME="MentorMatch API"
ENVIRONMENT="development"
SECRET_KEY="your_jwt_secret_key_change_in_production"
ALGORITHM="HS256"
ACCESS_TOKEN_EXPIRE_MINUTES=1440

# Database
DATABASE_URL="postgresql://mentormatch_user:secure_password@localhost:5432/mentormatch_db"

# CORS Settings
ALLOWED_ORIGINS="http://localhost:5173,https://mentor-match-one.vercel.app"
```

### 3. Frontend Environment Variables (`frontend/.env`)
Create a `.env` file inside the `frontend/` directory:

```env
VITE_API_BASE_URL="http://localhost:8000"
```

---

## Local Development Installation

### Backend Setup

1. **Navigate to the backend directory:**
   ```bash
   cd backend
   ```

2. **Create and activate a virtual environment:**
   ```bash
   python -m venv venv
   # On Linux/macOS:
   source venv/bin/activate
   # On Windows:
   venv\Scripts\activate
   ```

3. **Install Python dependencies:**
   ```bash
   pip install --upgrade pip
   pip install -r requirements.txt
   ```

4. **Download the spaCy language model:**
   ```bash
   python -m spacy download en_core_web_sm
   ```

5. **Initialize Database Tables:**
   ```bash
   python -m app.db.init_db
   ```

6. **Start the FastAPI Development Server:**
   ```bash
   uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload
   ```
   The API interactive documentation will be available at [http://localhost:8000/docs](http://localhost:8000/docs).

---

### Frontend Setup

1. **Navigate to the frontend directory:**
   ```bash
   cd frontend
   ```

2. **Install Node.js packages:**
   ```bash
   npm install
   ```

3. **Launch Vite Development Server:**
   ```bash
   npm run dev
   ```
   The web application will open at [http://localhost:5173](http://localhost:5173).

---

## Running via Docker Compose

To launch the full system (PostgreSQL database, FastAPI backend, and React frontend) in a containerized setup:

```bash
# Build and start all services
docker-compose up --build -d

# View service logs
docker-compose logs -f

# Shut down containers
docker-compose down
```

---

## Running Tests and Verification

The repository includes a comprehensive `pytest` test suite covering authentication, database constraints, PDF ingestion, NLP matching accuracy, and booking concurrency.

```bash
cd backend

# Run all test suites
pytest -v

# Run with coverage report
pytest --cov=app tests/

# Run NLP benchmark evaluation specifically
pytest tests/test_nlp_matching.py -v
```

### Benchmark Metrics Summary
| Metric | Baseline (Raw Keywords) | Observed Result (spaCy + TF-IDF) | Target Threshold |
| :--- | :--- | :--- | :--- |
| **Precision@3** | `0.67` | **`0.89`** | $\ge 0.80$ |
| **Mean Reciprocal Rank (MRR)** | `0.74` | **`0.93`** | $\ge 0.85$ |
| **Vector Similarity Latency** | — | **`14.2 ms`** | $< 50\text{ ms}$ |
| **End-to-End API Latency** | — | **`1.18 s`** | $\le 3.0\text{ s}$ |

---

## Live Deployment

* **Hosted Web Application:** [https://mentor-match-one.vercel.app](https://mentor-match-one.vercel.app)
* **Production Deployment Branch:** `main`

The frontend automatically builds and deploys to Vercel whenever verified code is merged into the `main` branch.

---

## Author & Licensing

* **Author:** Tillen Otuoma
* **Project Type:** Undergraduate Capstone Project
* **License:** [MIT License](LICENSE)
