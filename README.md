# Ainthamizh AI — Tamil Learning Workspace

A full-stack AI-powered Tamil language learning platform built for the DTEC Hackathon. It combines Tanglish translation, pronunciation feedback, OCR scanning, named entity recognition, and Tamil sentence generation in one workspace — with user authentication, analytics tracking, daily streaks, and a points system.

---

## Table of Contents

- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Setup from ZIP](#setup-from-zip)
  - [1. Extract the ZIP](#1-extract-the-zip)
  - [2. Supabase Setup](#2-supabase-setup)
  - [3. Backend Setup](#3-backend-setup)
  - [4. Frontend Setup](#4-frontend-setup)
  - [5. Run the Project](#5-run-the-project)
- [Environment Variables](#environment-variables)
- [Database Schema](#database-schema)
- [API Reference](#api-reference)
  - [Health](#health)
  - [Auth](#auth)
  - [Translator](#translator)
  - [Entity Recognition (NER)](#entity-recognition-ner)
  - [Sentence Generator](#sentence-generator)
  - [Pronunciation](#pronunciation)
  - [OCR Scanner](#ocr-scanner)
  - [Analytics](#analytics)
- [Postman Collection](#postman-collection)
- [Points & Streak System](#points--streak-system)
- [Dark / Light Theme](#dark--light-theme)

---

## Features

| Feature | Description |
|---|---|
| Tanglish Translator | Converts romanized Tamil (Tanglish) into Tamil Unicode script |
| Pronunciation Validator | Records Tamil speech and gives accuracy score with phoneme-level feedback |
| OCR Scanner | Extracts Tanglish or Tamil text from uploaded images |
| Entity Recognition | Identifies persons, locations, organizations, dates, and concepts in Tamil text |
| Sentence Generator | Builds Tamil sentences from noun, verb, tense, gender, and mode controls |
| Dashboard | Shows usage stats, recent activity, quick actions, and learning progress |
| Profile | Manages display name, preferred level, password, and daily streak data |
| Points & Streaks | Awards +10 points for first session of the day, +5 for every feature use after |
| Dark / Light Theme | Persisted theme toggle across the entire app ||

---

## Tech Stack

**Frontend**
- React 19, Vite, Tailwind CSS
- React Router DOM, Axios, React Hot Toast
- Supabase JS (auth client)

**Backend**
- Node.js, Express 5
- Supabase (PostgreSQL database + auth)
- Google Gemini API (`@google/genai`) with round-robin key rotation
- Tesseract.js (primary OCR engine, `eng+tam`)
- Multer (file uploads)

---

## Project Structure

```
Ainthamizh_AI/
├── backend/
│   ├── config/
│   │   └── supabase.js           # Supabase client init
│   ├── middleware/
│   │   ├── authMiddleware.js     # JWT token verification
│   │   └── errorMiddleware.js
│   ├── routes/
│   │   ├── auth.js               # Register, login, profile, streaks
│   │   ├── analytics.js          # Stats, activity, increment, session
│   │   ├── translator.js         # Tanglish → Tamil
│   │   ├── ner.js                # Named entity recognition
│   │   ├── generator.js          # Sentence generation
│   │   ├── pronunciation.js      # Audio pronunciation validation
│   │   └── ocr.js                # Image OCR
│   ├── services/
│   │   ├── analyticsService.js   # recordFeatureUsage (points + streaks)
│   │   ├── geminiService.js      # Gemini API wrapper with round-robin key rotation
│   │   ├── tanglishService.js    # Transliteration logic
│   │   ├── ocrService.js         # Tesseract.js primary OCR (eng+tam)
│   │   └── pronunciationService.js
│   ├── supabase-schema.sql       # Full database schema
│   ├── server.js                 # Express app entry point
│   ├── package.json
│   └── .env                      # Backend environment variables
│
├── frontend/
│   ├── public/
│   │   └── logo.png              # App logo (favicon + navbar)
│   ├── src/
│   │   ├── components/
│   │   │   ├── Navbar.jsx        # Top navigation with theme toggle
│   │   │   ├── Footer.jsx        # Footer links
│   │   │   ├── Sidebar.jsx
│   │   │   └── ui.jsx            # Shared UI components (Button, Card, etc.)
│   │   ├── context/
│   │   │   └── AuthContext.jsx   # Auth state, login, register, logout
│   │   ├── pages/
│   │   │   ├── Home.jsx
│   │   │   ├── Dashboard.jsx
│   │   │   ├── Login.jsx
│   │   │   ├── Profile.jsx
│   │   │   ├── TanglishTranslator.jsx
│   │   │   ├── EntityAnalyzer.jsx
│   │   │   ├── SentenceGenerator.jsx
│   │   │   ├── PronunciationValidator.jsx
│   │   │   └── OCRScanner.jsx
│   │   ├── services/
│   │   │   ├── api.js            # Axios instance with auth interceptor
│   │   │   └── supabase.js       # Supabase client (anon key)
│   │   ├── utils/
│   │   │   ├── history.js        # Local storage history helpers
│   │   │   ├── notifications.js  # Toast notification helpers
│   │   │   └── transliteration.js
│   │   ├── App.jsx
│   │   ├── main.jsx
│   │   └── index.css             # Tailwind + custom CSS variables
│   ├── index.html
│   ├── package.json
│   ├── tailwind.config.js
│   ├── vite.config.js
│   └── .env                      # Frontend environment variables
│
├── postman_collection.json        # Postman API test collection
└── README.md
```

---

## Prerequisites

Make sure the following are installed on the new system before setup:

- **Node.js** v18 or later — https://nodejs.org
- **npm** v9 or later (comes with Node.js)
- **Git** (optional, for cloning) — https://git-scm.com
- **Postman** (optional, for API testing) — https://www.postman.com

You will also need accounts for:
- **Supabase** — https://supabase.com (free tier is sufficient)
- **Google AI Studio** — https://aistudio.google.com (for Gemini API key)

---

## Setup from ZIP

### 1. Extract the ZIP

Extract the ZIP file to any folder on your system. You should see the `Ainthamizh_AI/` folder with `backend/` and `frontend/` inside.

```
Ainthamizh_AI/
├── backend/
├── frontend/
├── postman_collection.json
└── README.md
```

---

### 2. Supabase Setup

**a. Create a Supabase project**

1. Go to https://supabase.com and sign in
2. Click **New Project**, give it a name (e.g. `ainthamizh-ai`), set a database password, and choose a region
3. Wait for the project to be ready (takes ~1 minute)

**b. Run the database schema**

1. In your Supabase project, go to **SQL Editor** in the left sidebar
2. Click **New Query**
3. Copy the entire contents of `backend/supabase-schema.sql` and paste it into the editor
4. Click **Run** — this creates the `users` and `activity_logs` tables

If you are setting up on a system where the database already has the `users` table but is missing the new streak/points columns, also run:

```sql
ALTER TABLE users ADD COLUMN IF NOT EXISTS points INTEGER DEFAULT 0;
ALTER TABLE users ADD COLUMN IF NOT EXISTS current_streak INTEGER DEFAULT 0;
ALTER TABLE users ADD COLUMN IF NOT EXISTS best_streak INTEGER DEFAULT 0;
ALTER TABLE users ADD COLUMN IF NOT EXISTS last_active_date DATE DEFAULT NULL;
```

**c. Get your Supabase credentials**

1. Go to **Project Settings → API**
2. Copy:
   - **Project URL** (e.g. `https://xxxx.supabase.co`)
   - **anon / public key** (starts with `eyJ...`)
   - **service_role key** (starts with `eyJ...`) — keep this secret

---

### 3. Backend Setup

**a. Navigate to the backend folder**

```bash
cd Ainthamizh_AI/backend
```

**b. Install dependencies**

```bash
npm install
```

**c. Create the environment file**

Create a file named `.env` inside the `backend/` folder with the following content:

```env
PORT=5000
GEMINI_API_KEY_1=your_first_gemini_api_key
GEMINI_API_KEY_2=your_second_gemini_api_key
GEMINI_API_KEY_3=your_third_gemini_api_key
SUPABASE_URL=https://your-project-id.supabase.co
SUPABASE_SERVICE_ROLE_KEY=your_supabase_service_role_key
CORS_ORIGIN=http://localhost:5173,http://127.0.0.1:5173
```

- `GEMINI_API_KEY_1` / `GEMINI_API_KEY_2` / `GEMINI_API_KEY_3` — get these from https://aistudio.google.com/app/apikey. You can use just one key by only setting `GEMINI_API_KEY_1`. The service also accepts a single `GEMINI_API_KEY` as a fallback.
- `SUPABASE_URL` — your Supabase project URL from step 2c
- `SUPABASE_SERVICE_ROLE_KEY` — the service role key from step 2c (not the anon key)
- `CORS_ORIGIN` — comma-separated list of allowed frontend origins

---

### 4. Frontend Setup

**a. Navigate to the frontend folder**

```bash
cd Ainthamizh_AI/frontend
```

**b. Install dependencies**

```bash
npm install
```

**c. Create the environment file**

Create a file named `.env` inside the `frontend/` folder with the following content:

```env
VITE_API_BASE_URL=http://localhost:5000/api
VITE_SUPABASE_URL=https://your-project-id.supabase.co
VITE_SUPABASE_ANON_KEY=your_supabase_anon_key
```

- `VITE_API_BASE_URL` — points to the backend (keep as-is for local dev)
- `VITE_SUPABASE_URL` — same project URL as backend
- `VITE_SUPABASE_ANON_KEY` — the **anon/public** key from step 2c (not the service role key)

**d. Add the app logo**

Place your logo image file as `frontend/public/logo.png`. This is used as both the browser tab favicon and the navbar logo.

---

### 5. Run the Project

Open **two terminal windows** — one for backend, one for frontend.

**Terminal 1 — Backend**

```bash
cd Ainthamizh_AI/backend
npm run dev
```

You should see:
```
Ainthamizh AI backend running on port 5000
```

**Terminal 2 — Frontend**

```bash
cd Ainthamizh_AI/frontend
npm run dev
```

You should see:
```
  VITE v8.x.x  ready in xxx ms
  ➜  Local:   http://localhost:5173/
```

Open http://localhost:5173 in your browser.

---

## Environment Variables

### Backend (`backend/.env`)

| Variable | Required | Description |
|---|---|---|
| `PORT` | No | Server port, defaults to `5000` |
| `GEMINI_API_KEY_1` | Yes (at least one) | First Google Gemini API key |
| `GEMINI_API_KEY_2` | No | Second Gemini API key (round-robin rotation) |
| `GEMINI_API_KEY_3` | No | Third Gemini API key (round-robin rotation) |
| `GEMINI_API_KEY` | No | Single key fallback if numbered keys are not set |
| `SUPABASE_URL` | Yes | Supabase project URL |
| `SUPABASE_SERVICE_ROLE_KEY` | Yes | Supabase service role key (admin access) |
| `CORS_ORIGIN` | No | Comma-separated allowed origins, defaults to `*` |
| `JSON_BODY_LIMIT` | No | Max JSON body size, defaults to `1mb` |
| `NODE_ENV` | No | Set to `production` to hide stack traces |

### Frontend (`frontend/.env`)

| Variable | Required | Description |
|---|---|---|
| `VITE_API_BASE_URL` | Yes | Backend API base URL |
| `VITE_SUPABASE_URL` | Yes | Supabase project URL |
| `VITE_SUPABASE_ANON_KEY` | Yes | Supabase anon/public key |

---

## Database Schema

### `users` table

| Column | Type | Description |
|---|---|---|
| `id` | TEXT (PK) | Supabase auth user ID |
| `email` | TEXT | User email |
| `email_verified` | BOOLEAN | Email verification status |
| `display_name` | TEXT | Display name |
| `photo_url` | TEXT | Avatar URL |
| `preferred_level` | TEXT | Beginner / Intermediate / Advanced |
| `native_language` | TEXT | User's native language |
| `analytics` | JSONB | Feature usage counts |
| `points` | INTEGER | Total earned points |
| `current_streak` | INTEGER | Current daily streak count |
| `best_streak` | INTEGER | All-time best streak |
| `last_active_date` | DATE | Last date the user used any feature |
| `created_at` | TIMESTAMPTZ | Account creation timestamp |
| `updated_at` | TIMESTAMPTZ | Last updated timestamp |

### `activity_logs` table

| Column | Type | Description |
|---|---|---|
| `id` | UUID (PK) | Auto-generated log ID |
| `user_id` | TEXT (FK) | References `users.id` |
| `feature` | TEXT | Feature used (translation, ocr, etc.) |
| `count_field` | TEXT | Analytics field name |
| `label` | TEXT | Human-readable label |
| `amount` | INTEGER | Usage amount |
| `metadata` | JSONB | Feature-specific metadata |
| `created_at` | TIMESTAMPTZ | Log timestamp |

---

## API Reference

All protected routes require a `Bearer` token in the `Authorization` header:
```
Authorization: Bearer <supabase_access_token>
```

The access token is returned from the login response as `access_token`.

---

### Health

#### `GET /`
Check if the backend is running.

**Response**
```json
{ "service": "Ainthamizh AI Backend", "status": "running" }
```

#### `GET /health`
Detailed health check with uptime.

**Response**
```json
{ "ok": true, "uptime": 123.45, "timestamp": "2025-01-01T00:00:00.000Z" }
```

---

### Auth

#### `POST /api/auth/register`
Register a new user account.

**Body**
```json
{
  "email": "user@example.com",
  "password": "Password123!",
  "displayName": "Your Name"
}
```

**Response** `201`
```json
{
  "message": "Registration successful.",
  "user": { ... },
  "session": { ... },
  "access_token": "eyJ..."
}
```

---

#### `POST /api/auth/login`
Sign in with email and password.

**Body**
```json
{
  "email": "user@example.com",
  "password": "Password123!"
}
```

**Response** `200`
```json
{
  "message": "Login successful",
  "user": { ... },
  "session": { ... },
  "access_token": "eyJ..."
}
```

> Copy the `access_token` value and set it as the `access_token` variable in Postman to use protected endpoints.

---

#### `GET /api/auth/me` 🔒
Get the current user's profile.

**Response** `200`
```json
{
  "user": {
    "id": "uuid",
    "email": "user@example.com",
    "display_name": "Your Name",
    "points": 45,
    "current_streak": 3,
    "best_streak": 7,
    ...
  }
}
```

---

#### `POST /api/auth/profile` 🔒
Update display name, photo, preferred level, or native language. Email cannot be changed.

**Body**
```json
{
  "displayName": "New Name",
  "photoURL": "https://example.com/avatar.png",
  "preferredLevel": "Intermediate",
  "nativeLanguage": "Tamil"
}
```

**Response** `200`
```json
{ "message": "User profile updated.", "user": { ... } }
```

---

#### `GET /api/auth/streaks` 🔒
Get the current user's streak and points data.

**Response** `200`
```json
{
  "streaks": {
    "current": 3,
    "best": 7,
    "points": 45,
    "lastActiveDate": "2025-01-15"
  }
}
```

---

#### `POST /api/auth/change-password` 🔒
Change password (requires old password verification).

**Body**
```json
{
  "oldPassword": "OldPassword123!",
  "newPassword": "NewPassword456!"
}
```

**Response** `200`
```json
{ "message": "Password updated successfully." }
```

---

#### `GET /api/auth/verify` 🔒
Verify that the current token is valid.

**Response** `200`
```json
{ "valid": true, "user": { ... } }
```

---

### Translator

#### `POST /api/translator` 🔒
Convert Tanglish (romanized Tamil) to Tamil Unicode script.

**Body**
```json
{ "text": "naan tamil pesuven" }
```

**Response** `200`
```json
{
  "success": true,
  "data": {
    "tamilText": "நான் தமிழ் பேசுவேன்",
    "tokens": [
      { "source": "naan", "tamil": "நான்" },
      { "source": "tamil", "tamil": "தமிழ்" }
    ]
  }
}
```

**Limits:** Max 2000 characters.

---

### Entity Recognition (NER)

#### `POST /api/ner` 🔒
Identify named entities in Tamil text with Tamil explanations.

**Body**
```json
{ "text": "ரவி சென்னை நகரில் தமிழ் கற்றுக்கொள்கிறான்." }
```

**Response** `200`
```json
{
  "success": true,
  "data": {
    "language": "ta",
    "summary": "தமிழில் சுருக்கம்",
    "entities": [
      {
        "text": "ரவி",
        "type": "PERSON",
        "explanationTamil": "ரவி என்பது ஒரு ஆண் பெயர்.",
        "confidence": 0.95
      },
      {
        "text": "சென்னை",
        "type": "LOCATION",
        "explanationTamil": "சென்னை தமிழ்நாட்டின் தலைநகரம்.",
        "confidence": 0.98
      }
    ]
  }
}
```

**Entity types:** `PERSON`, `LOCATION`, `ORGANIZATION`, `DATE`, `CONCEPT`

**Limits:** Max 6000 characters.

---

### Sentence Generator

#### `POST /api/generator` 🔒
Generate a Tamil sentence or dialogue from grammar parameters.

**Body**
```json
{
  "noun": "மாணவன்",
  "verb": "படி",
  "tense": "present",
  "gender": "male",
  "mode": "sentence"
}
```

| Field | Values |
|---|---|
| `tense` | `past`, `present`, `future` |
| `gender` | `male`, `female`, `neutral`, `plural` |
| `mode` | `sentence`, `dialogue` |

**Response** `200`
```json
{
  "success": true,
  "data": {
    "mode": "sentence",
    "sentence": "மாணவன் படிக்கிறான்.",
    "dialogue": [],
    "transliteration": "Maanavan padikkiraan.",
    "grammarNoteTamil": "ஆண்பால் வினைமுற்று..."
  }
}
```

---

### Pronunciation

#### `POST /api/pronunciation` 🔒
Submit an audio recording and receive pronunciation accuracy feedback.

**Headers**
```
Authorization: Bearer <token>
X-Target-Text: நான் தமிழ் பேசுகிறேன்
Content-Type: multipart/form-data
```

**Body (form-data)**
```
file: <audio file (.webm, .mp3, .wav)>
```

Alternatively send as raw binary with `Content-Type: audio/webm`.

**Response** `200`
```json
{
  "success": true,
  "data": {
    "targetText": "நான் தமிழ் பேசுகிறேன்",
    "transcript": "நான் தமிழ் பேசுகிறேன்",
    "accuracy": 92,
    "confidence": 0.88,
    "feedback": "Strong pronunciation. Keep the same pace and clarity.",
    "phonemeIssues": [
      {
        "sound": "ழ்",
        "issue": "slightly unclear",
        "feedback": "Curl the tongue slightly when pronouncing ழ்."
      }
    ]
  }
}
```

**Limits:** Max 15MB audio file.

---

### OCR Scanner

#### `POST /api/ocr` 🔒
Extract Tanglish or Tamil text from an uploaded image.

**Headers**
```
Authorization: Bearer <token>
Content-Type: multipart/form-data
```

**Body (form-data)**
```
file: <image file (.png, .jpg, .webp)>
```

**Response** `200`
```json
{
  "success": true,
  "data": {
    "ocr": {
      "extractedText": "naan tamil pesuven",
      "lines": ["naan tamil pesuven"],
      "confidence": 0.91,
      "notes": ""
    },
    "tamil": {
      "tamilText": "நான் தமிழ் பேசுவேன்",
      "tokens": [ ... ]
    }
  }
}
```

**Limits:** Max 10MB image file.

**OCR engine:** Tesseract.js (`eng+tam`) is used as the primary extraction engine. If Tesseract returns no text, Gemini vision is used as a fallback.

---

### Analytics

#### `GET /api/analytics/stats` 🔒
Get the current user's feature usage totals.

**Response** `200`
```json
{
  "userId": "uuid",
  "stats": {
    "totalTranslations": 12,
    "ocrScans": 3,
    "pronunciationAttempts": 7,
    "sentenceGenerations": 5,
    "entityAnalyses": 2
  }
}
```

---

#### `GET /api/analytics/activity?limit=12` 🔒
Get the most recent activity logs for the current user.

**Query Params**

| Param | Type | Default | Max |
|---|---|---|---|
| `limit` | integer | 20 | 50 |

**Response** `200`
```json
{
  "userId": "uuid",
  "activity": [
    {
      "id": "uuid",
      "feature": "translation",
      "label": "Translation",
      "amount": 1,
      "metadata": { "inputLength": 18 },
      "createdAt": "2025-01-15T10:30:00.000Z"
    }
  ]
}
```

---

#### `POST /api/analytics/session` 🔒
Record a daily session. Awards +10 points and updates the streak for the first call of each day. Subsequent calls on the same day return the current values without modification.

**Response** `200`
```json
{
  "message": "Session recorded.",
  "points": 10,
  "pointsAdded": 10,
  "currentStreak": 1,
  "bestStreak": 1
}
```

---

#### `POST /api/analytics/increment` 🔒
Manually increment a feature counter. Awards +10 points for first use of the day, +5 for subsequent uses.

**Body**
```json
{
  "feature": "translation",
  "amount": 1,
  "metadata": { "inputLength": 18 }
}
```

**Features:** `translation`, `ocr`, `pronunciation`, `sentence`, `entity`

**Response** `200`
```json
{
  "message": "Analytics updated.",
  "stats": { ... },
  "points": 15,
  "pointsAdded": 5,
  "currentStreak": 1,
  "bestStreak": 1
}
```

---

## Postman Collection

A ready-to-use Postman collection is included at the root of the project:

```
postman_collection.json
```

**Import steps:**
1. Open Postman
2. Click **Import** (top left)
3. Drag and drop `postman_collection.json` or click **Upload Files** and select it
4. The collection **Ainthamizh AI Backend API** will appear in your sidebar

**Set up the access token variable:**
1. Call **Auth Login** with your credentials
2. Copy the `access_token` from the response
3. In Postman, go to your **collection → Variables** tab
4. Add a variable named `access_token` and paste the token as its value
5. All protected endpoints use `{{access_token}}` automatically

**Endpoints included in the collection:**

| Name | Method | URL |
|---|---|---|
| Health Check | GET | `/` |
| Health Status | GET | `/health` |
| Auth Register | POST | `/api/auth/register` |
| Auth Login | POST | `/api/auth/login` |
| Auth Profile | POST | `/api/auth/profile` |
| Auth Me | GET | `/api/auth/me` |
| Auth Verify | GET | `/api/auth/verify` |
| Auth Streaks | GET | `/api/auth/streaks` |
| Auth Change Password | POST | `/api/auth/change-password` |
| Translator | POST | `/api/translator` |
| NER | POST | `/api/ner` |
| Sentence Generator | POST | `/api/generator` |
| Pronunciation | POST | `/api/pronunciation` |
| OCR | POST | `/api/ocr` |
| Analytics Stats | GET | `/api/analytics/stats` |
| Analytics Activity | GET | `/api/analytics/activity` |
| Analytics Session | POST | `/api/analytics/session` |
| Analytics Increment | POST | `/api/analytics/increment` |

---

## Points & Streak System

Points and streaks are tracked automatically on every feature use and on login. No manual configuration is needed.

| Event | Points Awarded |
|---|---|
| First feature use or login of the day | +10 points |
| Every feature use after the first on the same day | +5 points |

**Streak rules:**
- Streak increments by 1 each time you use the app on a new day
- If you skip a day, the streak resets to 1 on the next use
- Best streak is preserved even after a reset

**Viewing your stats:**
- Go to **Profile** page to see Current Streak, Best Streak, and Total Points
- Go to **Dashboard** to see total feature usage counts and recent activity

---

## Dark / Light Theme

The theme toggle button is in the top navigation bar (sun/moon icon). The selected theme is saved to `localStorage` and persists across page reloads and sessions.

The app also respects the system's `prefers-color-scheme` setting on first load — if your OS is in dark mode, the app defaults to dark theme automatically.

---

## Built For

**DTEC Hackathon** — Ainthamizh AI Tamil Learning Workspace
