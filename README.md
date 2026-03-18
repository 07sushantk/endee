RAG Healthcare Assistant Bot
A full-stack healthcare chatbot built with:

FastAPI for the backend API
React + Vite for the frontend
Gemini API for embeddings and response generation
Endee (ChromaDB) as the vector database
The app uses Retrieval-Augmented Generation (RAG) to search a local healthcare knowledge base before generating a response. Each user provides their own Gemini API key in the UI, and that key is used only in-memory per request.

Features
Healthcare chatbot with RAG-based context retrieval
User-provided Gemini API key from the frontend
No server-side API key storage
Endee/ChromaDB-backed semantic search
Context-aware responses using retrieved symptom and medicine data
Frontend chat UI with session-based API-key usage
How It Works
The user enters a Gemini API key in the frontend.
The user sends a healthcare query.
The backend generates a query embedding using Gemini.
Endee/ChromaDB retrieves the most relevant medical records.
The retrieved context is added to the prompt.
Gemini generates the final answer using the retrieved context.
The response and retrieved context are returned to the frontend.
RAG Flow Diagram

Architecture
Frontend
Folder: frontend/
File: frontend/src/app.jsx
Handles chat UI, API-key entry, request submission, and response rendering
Backend
Folder: backend/
File: backend/api.py
Exposes the /rag endpoint
Accepts:
message
api_key
RAG Pipeline
File: backend/rag.py
Builds the retrieval + generation flow
Embeddings
File: backend/embeddings.py
Generates query/document embeddings through Gemini
Vector Database
File: backend/endee_client.py
Wraps ChromaDB PersistentClient
Used here as the Endee vector search layer
Data Ingestion
File: backend/data_ingestion.py
Reads symptom and medicine JSON files
Creates embeddings
Stores documents in Endee/ChromaDB
Knowledge Base Files
backend/symptoms.json
backend/symptoms_new.json
backend/medicines.json
Note:

The current ingestion script reads from symptoms.json and medicines.json.
If you want to use symptoms_new.json instead, update data_ingestion.py or replace symptoms.json with the new dataset before ingestion.
Local Setup
Prerequisites
Node.js 18+
Python 3.10+ recommended
pip
1. Clone the repository
git clone https://github.com/your-username/rag-healthcare-assistant-bot.git
cd rag-healthcare-assistant-bot
2. Install frontend dependencies
cd frontend
npm install
3. Install backend dependencies
cd ../backend
pip install -r requirements.txt
If chromadb fails to install on your machine, you may need:

Python 3.10, 3.11, or 3.12
Microsoft C++ Build Tools on Windows
4. Configure environment variables
Create local .env files inside the backend/ and frontend/ folders.

Example values are provided in:

backend/.env.example
frontend/.env.example
Important:

The main chat UI now accepts the Gemini API key directly from the user.
The backend does not need a permanent server-side Gemini API key for chat requests.
5. Build the vector database
Run ingestion once to populate Endee/ChromaDB:

cd backend
python data_ingestion.py
This creates or updates the local backend/endee_db/ directory.

6. Start the backend
cd backend
python api.py
Backend runs on:

http://127.0.0.1:5000
7. Start the frontend
cd frontend
npm run dev
Frontend runs on:

http://127.0.0.1:3000
How To Use The App
Open the frontend in your browser.
Enter your Gemini API key.
Ask a question about symptoms, medicines, or healthcare advice.
The app retrieves relevant context from Endee/ChromaDB.
Gemini generates the final answer based on that retrieved context.
API Contract
POST /rag
Request body:

{
  "message": "I have a headache and feel dizzy",
  "api_key": "YOUR_GEMINI_API_KEY"
}
Response shape:

{
  "response": "Generated answer here",
  "context": [
    {
      "text": "retrieved source text",
      "similarity": 0.91
    }
  ]
}
Security Notes
Do not commit your real .env file
Do not commit real API keys
The frontend sends the Gemini API key per request
The backend uses the key in-memory only
The key is not stored in the database or API responses
GitHub Repository Readiness
Before pushing to GitHub:

Make sure backend/.env and frontend/.env.local are not committed
Make sure frontend/node_modules/ is not committed
Make sure no real API keys remain in tracked files
Confirm backend/endee_db/ should or should not be versioned based on your preference
Recommended:

Keep backend/endee_db/ out of Git if you want a smaller repo
Rebuild the database locally or during deployment using python data_ingestion.py
Deployment Notes
GitHub Repository
This project is ready to be uploaded to a GitHub repository after removing secrets and committing the project files.

GitHub Hosting
Important:

GitHub Pages can host only static frontend assets
This app also requires a running FastAPI backend and local/persistent ChromaDB storage
So the full app cannot run only on GitHub Pages
Recommended Deployment Setup
Frontend: Vercel, Netlify, or GitHub Pages for static assets
Backend: Render, Railway, Fly.io, or a VPS
Database storage: persistent disk/volume for endee_db
If you want a production deployment:

Host the FastAPI backend separately
Set the frontend API base URL manually
Persist the ChromaDB data directory
Manual URLs You Need To Add
You need to manually set only one frontend URL variable:

VITE_API_BASE_URL

Set it to your deployed backend base URL, for example:

VITE_API_BASE_URL=https://your-backend.onrender.com
The frontend will call:

https://your-backend.onrender.com/api/chat
Locally, if VITE_API_BASE_URL is not set, the app falls back to:

/api/chat
which continues to work with the local server.ts proxy.

Where To Add The URL
For local frontend testing
Create a frontend/.env.local file:

VITE_API_BASE_URL=https://your-backend-url
For Vercel
Add this environment variable in:

Project Settings
Environment Variables
Key: VITE_API_BASE_URL
Value: https://your-backend-url
For Netlify
Add this environment variable in:

Site configuration
Environment variables
Key: VITE_API_BASE_URL
Value: https://your-backend-url
For GitHub Pages
GitHub Pages does not run the backend. You would still need:

frontend hosted on GitHub Pages
backend hosted somewhere else
VITE_API_BASE_URL pointing to that hosted backend
Project Structure
.
├── backend/
│   ├── api.py
│   ├── rag.py
│   ├── embeddings.py
│   ├── endee_client.py
│   ├── data_ingestion.py
│   ├── bot.py
│   ├── generate_data.py
│   ├── generate_new_structure.py
│   ├── medicines.json
│   ├── symptoms.json
│   ├── symptoms_new.json
│   ├── metadata.json
│   └── requirements.txt
├── frontend/
│   ├── index.html
│   ├── package.json
│   ├── package-lock.json
│   ├── server.ts
│   ├── tsconfig.json
│   ├── vite.config.ts
│   └── src/
│       ├── app.jsx
│       ├── main.tsx
│       └── index.css
└── README.md
Future Improvements
Add deployment configuration for frontend and backend
Add better structured response rendering in the frontend
Add automated ingestion commands
Add tests for API and UI
Add authentication if you want managed user sessions later
