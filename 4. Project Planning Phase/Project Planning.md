# Project Planning Phase

## Sprint Plan

| Sprint | Focus | Key Deliverables |
|---|---|---|
| Sprint 1 | Auth & Scaffolding | FastAPI app skeleton, `models.py`, `auth.py` (JWT, bcrypt, sessions), register/login/logout routes, base templates + design system |
| Sprint 2 | Gemini Integration + Home Planner | `gemini_utils.py` core (prompting, JSON extraction, shopping links), `/home-budget` route + `home_planner.html`, retry + fallback logic |
| Sprint 3 | Party + Jewelry Planners | `/party-budget` with category-aware shopping links, `/jewelry-budget` with multimodal image path, upload handling + validation |
| Sprint 4 | UI Polish, History, Testing | Dashboard recent activity, History page with expandable details, full pytest suite with mocked Gemini calls, final verification pass |

## User Stories (with story points)

| ID | Story | Points |
|---|---|---|
| US-1 | As a new user, I can register with a username/email/password so I can access the planners. | 3 |
| US-2 | As a registered user, I can log in and stay authenticated via a secure cookie. | 3 |
| US-3 | As a user, I can enter a home budget and constraints and receive an itemized plan with shopping links. | 8 |
| US-4 | As a user, I can enter party details and receive a proportional budget breakdown with vendor suggestions. | 8 |
| US-5 | As a user, I can upload an outfit photo and get jewelry recommendations that consider its color/style. | 8 |
| US-6 | As a user, if the AI service fails, I still see a usable fallback plan instead of an error page. | 5 |
| US-7 | As a user, I can view my past recommendations and reopen the full details. | 5 |
| US-8 | As a user, my session expires automatically after 30 minutes of inactivity. | 3 |

**Total estimated points:** 43

## Burndown notes

- Sprints 1–2 carried the highest risk (Gemini prompt reliability, JSON parsing robustness) and were prioritized first so fallback behavior could be validated early.
- Sprint 3's image upload path reused the `save_upload_file` / shopping-link-builder patterns established in Sprint 2, completing faster than estimated.
- Sprint 4's test suite surfaced one real bug (a shared `TestClient` leaking cookies across tests) — fixed by clearing cookies in a `pytest` fixture, confirming the value of writing tests before declaring the backend "done."
