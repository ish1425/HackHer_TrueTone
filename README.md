# HackHer TrueTone

HackHer TrueTone is a spam call detection prototype built with a FastAPI backend and a mobile-style frontend simulator. The app analyzes call text or uploaded audio, classifies the call as spam or legitimate, shows a risk score, and lets users report suspicious numbers to a local reputation database.

## What It Does

- Detects spam calls from transcribed text using a trained ML model
- Accepts audio uploads and transcribes them with OpenAI Whisper before classification
- Simulates an incoming iPhone-style call UI for demos
- Stores reported calls and caller reputation in a local SQLite database
- Returns clear actions such as `ALLOW`, `WARN`, or `BLOCK`

## Tech Stack

- Backend: FastAPI
- Frontend: HTML, CSS, JavaScript
- ML inference: pickled spam classifier + TF-IDF vectorizer
- Speech-to-text: Whisper
- Database: SQLite

## Project Structure

```text
HackHer_TrueTone-1/
|-- backend/
|   `-- api_server_fastapi.py
|-- frontend/
|   `-- templates/
|       `-- iphone_call_simulator.html
|-- README.md
`-- IH25035 CYBER GUARDIAN.pdf
```

## How It Works

1. A user uploads a call recording in the frontend simulator.
2. The frontend sends the file to the backend `POST /analyze-audio` endpoint.
3. Whisper transcribes the audio into text.
4. The TF-IDF vectorizer transforms the text for the spam classifier.
5. The classifier predicts whether the call is spam and returns a confidence score.
6. The UI displays the result, recommended action, and transcription.
7. If the user reports the call, the number is stored in SQLite for reputation tracking.

## Setup

### 1. Create a virtual environment

```powershell
python -m venv .venv
.venv\Scripts\activate
```

### 2. Install dependencies

There is no `requirements.txt` in the repo right now, so install the core packages manually:

```powershell
pip install fastapi uvicorn python-multipart pydantic
```

For audio transcription support, also install Whisper and its runtime dependencies:

```powershell
pip install openai-whisper
```

If Whisper or its underlying system dependencies are unavailable, the API can still start, but speech-to-text endpoints will be disabled.

### 3. Add model files

The backend expects these files to be available when the server starts:

- `spam_detector_model.pkl`
- `tfidf_vectorizer.pkl`

If they are missing, text and audio analysis endpoints will return a server error.

### 4. Run the backend

From the project root:

```powershell
python backend\api_server_fastapi.py
```

The API will start on:

```text
http://127.0.0.1:8000
```

## Frontend Demo

The demo UI lives here:

- [frontend/templates/iphone_call_simulator.html](/c:/Users/DELL/Desktop/Hackher-fraud/HackHer_TrueTone-1/frontend/templates/iphone_call_simulator.html)

You can open that file directly in a browser for a static frontend demo, as long as the backend is running on `http://127.0.0.1:8000`.

## API Endpoints

### `GET /`

Health check endpoint.

Response:

```json
{
  "message": "Spam Call Detection API",
  "status": "running"
}
```

### `POST /analyze-call`

Analyze plain text from a call.

Request body:

```json
{
  "phone_number": "+1234567890",
  "text": "Congratulations, you have won a prize!",
  "caller_name": "Unknown"
}
```

### `POST /analyze-audio`

Upload an audio file for transcription and spam analysis.

Form field:

- `file`: audio upload

### `GET /check-caller/{phone}`

Checks whether a phone number has already been reported as spam.

### `POST /report-call`

Stores call history and increments spam reports for flagged callers.

Example request:

```json
{
  "phone_number": "+1-800-XXX-XXXX",
  "is_spam": true,
  "transcription": "Sample transcription text"
}
```

## Sample Response

```json
{
  "is_spam": true,
  "confidence": 0.94,
  "risk_score": 94,
  "action": "BLOCK",
  "message": "This call appears to be spam",
  "transcription": "You have been selected for a free reward"
}
```

## Important Notes

- The backend route `GET /incoming-call` currently looks for `incoming_call.html` in the project root.
- The actual frontend file in this repo is `frontend/templates/iphone_call_simulator.html`.
- To use that route as-is, you will need to either rename or copy the HTML file to `incoming_call.html`, or update the backend route to point to the existing template path.
- The SQLite database file `spam_calls.db` is created automatically on startup.
- CORS is currently open to all origins, which is convenient for demos but not ideal for production.

## Future Improvements

- Add a `requirements.txt` or `pyproject.toml`
- Serve the frontend directly through FastAPI templates or static files
- Add authentication and rate limiting
- Improve caller reputation logic with thresholds and history analytics
- Add model training scripts and sample datasets
- Add tests for API endpoints and model loading

## Screenshots

The original README screenshots are preserved below:

<img width="537" height="805" alt="image" src="https://github.com/user-attachments/assets/5e156f1f-10e2-4b39-b657-26566272e0b7" />
<img width="467" height="817" alt="image" src="https://github.com/user-attachments/assets/a39adf1e-28fb-4670-bf3e-b3414b81b3d3" />
<img width="590" height="818" alt="image" src="https://github.com/user-attachments/assets/71765bf9-b171-4dfa-a657-cc638946df85" />
<img width="545" height="815" alt="image" src="https://github.com/user-attachments/assets/73ab7b31-874e-46b6-afac-806254b17f00" />
<img width="471" height="814" alt="image" src="https://github.com/user-attachments/assets/a57905b3-4374-4198-9043-e0cfde0560ad" />
<img width="495" height="819" alt="image" src="https://github.com/user-attachments/assets/b261c879-9d46-4763-a289-183922290500" />
<img width="507" height="821" alt="image" src="https://github.com/user-attachments/assets/69a27dda-98dd-4258-88ec-9a608e2d41c4" />
<img width="479" height="821" alt="image" src="https://github.com/user-attachments/assets/ef77fa38-563d-4afb-bac6-d80cdca6e73c" />
