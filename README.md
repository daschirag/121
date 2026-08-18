# VulnRadar — Vulnerability Aggregation System

> A production-grade cybersecurity intelligence platform aggregating CVEs from 5 sources, with AI-powered RAG chatbot, risk scoring, and a modern dark UI.

---

## Overview

VulnRadar is a full-stack vulnerability management platform built for security teams. It aggregates vulnerability data from NVD, OSV, GitHub Advisory, CISA KEV, and EPSS into a unified searchable database with intelligent risk scoring and an AI assistant powered by Retrieval-Augmented Generation (RAG).

**Built by:** Audix Interns — Chirag (Tech Lead)

---

## Features

- **31,569+ CVEs** aggregated from 5 sources — NVD, OSV, GitHub Advisory, CISA KEV, EPSS
- **Risk Scoring** — custom algorithm scoring each CVE 0–100 based on CVSS, KEV status, recency, affected products
- **RAG AI Chatbot** — semantic vector search across all CVEs + Llama 3.3 70B via Groq
- **5 Data Sources** — automated sync with deduplication
- **Admin Portal** — user management, source sync control, real-time stats
- **Full-text + Vector Search** — MongoDB Atlas Search + Vector Search (384-dim embeddings)
- **Dark Cyberpunk UI** — cyan/teal theme built with React + Tailwind

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | React 18, TypeScript, Vite, TailwindCSS, TanStack Query |
| Backend | FastAPI, Python 3.11, Motor (async MongoDB) |
| Database | MongoDB Atlas (M0), Vector Search Index |
| AI/ML | sentence-transformers (all-MiniLM-L6-v2), Groq (Llama 3.3 70B) |
| Pipeline | APScheduler, httpx, pymongo |
| Auth | JWT (HS256), bcrypt |

---

## Project Structure

```
VULNERABILITY-AGGREGATION-SYSTEM/
├── backend/
│   ├── app/
│   │   ├── auth/           # JWT authentication
│   │   ├── db/             # MongoDB connection
│   │   ├── models/         # Pydantic schemas
│   │   └── routes/         # API endpoints
│   └── main.py
├── frontend/
│   └── vuln-dashboard/
│       └── src/
│           ├── api/         # API client
│           ├── components/  # Reusable UI components
│           ├── config/      # App configuration
│           ├── contexts/    # React contexts (Auth)
│           ├── data/        # Mock data
│           └── pages/       # Page components
└── pipeline/
    ├── adapters/            # NVD, OSV, GitHub, KEV, EPSS
    ├── deduplicator/        # CVE deduplication logic
    ├── normalizer/          # Data normalization
    ├── risk/                # Risk scoring engine
    └── scheduler/           # APScheduler sync jobs
```

---

## Setup & Installation

### Prerequisites
- Python 3.11+
- Node.js 18+
- MongoDB Atlas account (free tier works)
- Groq API key (free at console.groq.com)

### Backend

```bash
# Clone repo
git clone https://github.com/Audixinterns/VULNERABILITY-AGGREGATION-SYSTEM
cd VULNERABILITY-AGGREGATION-SYSTEM

# Create virtual environment
python -m venv .venv
.venv\Scripts\activate  # Windows
source .venv/bin/activate  # Linux/Mac

# Install dependencies
pip install -r requirements.txt

# Create .env file (see .env.example)
cp .env.example .env
# Fill in your credentials

# Run backend
cd backend
uvicorn app.main:app --reload --env-file ../.env
```

### Frontend

```bash
cd frontend/vuln-dashboard
npm install
npm run dev
```

Open `http://localhost:5173`

---

## Environment Variables

Create a `.env` file in the project root:

```env
MONGODB_URI=mongodb+srv://<user>:<password>@cluster0.xxxxx.mongodb.net/?appName=Cluster0
DB_NAME=vulndb
JWT_SECRET=your_jwt_secret_here
GROQ_API_KEY=your_groq_api_key_here
NVD_API_KEY=your_nvd_api_key_here
GITHUB_TOKEN=your_github_token_here
BACKEND_PORT=8000
FRONTEND_PORT=5173
```

---

## API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/auth/register` | Register new user |
| POST | `/api/auth/login` | Login and get JWT |
| GET | `/api/auth/me` | Get current user |
| GET | `/api/vulnerabilities` | Search/filter CVEs |
| GET | `/api/vulnerabilities/{cve_id}` | Get CVE detail |
| GET | `/api/vulnerabilities/software/{name}/{version}` | Software version lookup |
| GET | `/api/vulnerabilities/search/identifier` | CVE/CWE search |
| GET | `/api/stats` | Dashboard statistics |
| POST | `/api/chat` | RAG AI chatbot |
| GET | `/api/admin/sources` | Data source status |
| GET | `/api/admin/sync-logs` | Sync history |
| POST | `/api/admin/sync/{source}` | Trigger manual sync |
| GET | `/api/admin/users` | List users (admin) |
| PATCH | `/api/admin/users/{id}` | Toggle user status |

---

## Data Sources

| Source | Records | Sync Frequency |
|--------|---------|---------------|
| NVD (National Vulnerability Database) | ~13,500 | Every 60 min |
| OSV (Open Source Vulnerabilities) | ~17,700 | Every 6 hours |
| GitHub Advisory Database | ~615 | Every 12 hours |
| CISA KEV (Known Exploited Vulnerabilities) | ~1,557 | Every 6 hours |
| EPSS (Exploit Prediction Scoring) | ~7,075 | Every 24 hours |

---

## Demo Credentials

```
Admin: admin / admin123
User:  user  / user123
```

---

## License

Internal — Audix © 2026
