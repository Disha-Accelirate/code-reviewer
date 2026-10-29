# Python Code Reviewer

A minimal Python code review app with a standalone browser frontend and separately deployable FastAPI backend. Users paste Python code into the page; the backend sends it to OpenAI for a short text analysis. Submitted code is not compiled or executed.

Deployed Backend: https://code-reviewer-ekwl.vercel.app/docs#/

## Project layout

- `backend/main.py` — FastAPI API and AI provider request
- `backend/requirements.txt` — backend dependencies
- `frontend/index.html` — standalone frontend page

## Run the backend locally

From the `backend` folder, install dependencies and start the API:

```powershell
python -m pip install -r requirements.txt
$env:OPENAI_API_KEY = "your-openai-api-key"
python -m uvicorn main:app --reload
```

The API runs at `http://127.0.0.1:8000`. Swagger UI is at `http://127.0.0.1:8000/docs`, and the OpenAPI schema is at `http://127.0.0.1:8000/openapi.json`.

## Run the frontend locally

Open `frontend/index.html` in a browser, enter `http://127.0.0.1:8000` in the Backend API URL field, paste Python code, and click **Review code**. The page remembers the backend URL in that browser's local storage.

## Deploy separately

1. Deploy the `backend` folder as a Python service. Install `requirements.txt` and start it with `uvicorn main:app --host 0.0.0.0 --port $PORT` (use the host's equivalent for its port variable).
2. Set `OPENAI_API_KEY` in the backend host's secret/environment settings.
3. Set `CORS_ORIGINS` on the backend to the frontend's exact origin, for example `https://your-frontend.example.com`. For local development, the default allows all origins.
4. Host `frontend/index.html` as a static site. In the page, enter the deployed backend base URL, such as `https://your-api.example.com`.

### Render shortcut

The root `render.yaml` is a Render Blueprint for the backend. Push the project to a Git provider, create a Blueprint in Render from that repository, and enter `OPENAI_API_KEY` as the prompted secret. After the service is live, use its generated `https://...onrender.com` URL as the frontend's Backend API URL. Add the frontend's origin as `CORS_ORIGINS` in the Render service environment if you host the page on a separate domain.

The API endpoint accepts `POST /review` with JSON such as `{ "code": "...", "model": "gpt-6-luna" }` and returns the analysis plus input, output, and total token counts. The code field is limited to 10,000 characters, the analysis is capped at 250 output tokens, and the default is the fast, lower-cost GPT-6 Luna option. The frontend offers GPT-6 Luna, GPT-6.1 Sol, and GPT-6 Astra. The backend API key stays on the server and is never placed in the frontend.
