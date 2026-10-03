# Technology Stack

| Layer | Technology | Why |
|---|---|---|
| Backend framework | FastAPI + Uvicorn | Async-friendly, automatic request validation via Pydantic, minimal boilerplate |
| AI model | Gemini 3.5 Flash-Lite, with automatic fallback to 2.5 Flash-Lite / Flash-Lite Latest (`google-generativeai`) | Lowest-cost flash-tier tier, multimodal (text + image) in one model |
| Templating | Jinja2 (`fastapi.templating.Jinja2Templates`) | Server-rendered HTML without a separate frontend build step |
| Frontend | HTML + CSS + vanilla JS | No build tooling required; keeps the submission self-contained and easy to run |
| Auth | JWT (`python-jose`) + bcrypt (`passlib`) | Stateless token verification, industry-standard password hashing |
| Session transport | Httponly cookie (`access_token`) | Protects the token from XSS-based theft while still working with plain `fetch()` |
| Storage | In-memory Python dicts | No external DB needed for a demo-scale submission; structured for an easy swap to Postgres/Mongo later |
| Config | `python-dotenv` | Keeps `GOOGLE_API_KEY` / `SECRET_KEY` out of source control |
| Image handling | Pillow (`PIL.Image`) | Required to pass outfit images into Gemini's multimodal `generate_content` call |
| Testing | pytest + FastAPI `TestClient` | Fast, dependency-light test runs; Gemini calls mocked so no API key is needed in CI |

## Why FastAPI (not Flask)

The source project document's architecture narrative mentions Flask, but every code excerpt in that document is written against FastAPI's `APIRouter`/`Depends` patterns, `OAuth2PasswordBearer`, and Pydantic models. This build follows the actual code shown in the document and implements **FastAPI** throughout.

## Model selection history and the multi-model fallback chain

The original brief specified Gemini 1.5 Flash. By the time this was built, Google had retired that model for API access (`404 NotFound` on a live key). The build went through several iterations before settling on its current approach:

1. **`gemini-1.5-flash`** — retired, `404 NotFound`.
2. **`gemini-2.5-flash`** — also retired for new API keys, `404 NotFound` (Google's own error message recommended `gemini-3.8-flash` instead).
3. **`gemini-3.8-flash`** — worked, but is a premium-tier model with a **free-tier quota of only 20 requests/day**, easily exhausted during normal testing/demo use, after which the API returns `429 ResourceExhausted` and every planner falls back to static data. Also the costliest option per token of the models tried.
4. **Current approach — a cost-ordered fallback chain**, defined as `MODEL_CANDIDATES` in `gemini_utils.py`:
   - `gemini-3.5-flash-lite` (primary — cheapest, confirmed working for text + multimodal)
   - `gemini-2.5-flash-lite` (secondary — currently `404` for new API keys/projects, kept in the chain in case it becomes available again or works on other keys)
   - `gemini-flash-lite-latest` (safety net — a "latest" alias Google keeps pointed at a current lite model)

`_generate_with_retry` in `gemini_utils.py` walks this list in order: a `NotFound` error (model retired/unavailable for this key) skips immediately to the next candidate with no wasted retry; any other error (e.g. quota exhaustion) retries once on the same model before moving to the next candidate. Only if every candidate fails does the planner fall back to static recommendations — and that fallback is now logged (`logger.warning`) with the real exception, so a quota/auth/parsing failure is visible server-side in the uvicorn console instead of being silently swallowed.
