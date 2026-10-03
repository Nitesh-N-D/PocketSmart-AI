# Solution Requirements

## Functional Requirements

| ID | Requirement | Implementation |
|---|---|---|
| FR-1 | User Registration | `POST /register` — bcrypt-hashed password, duplicate username/email rejected |
| FR-2 | Login / JWT Auth | `POST /token` — issues JWT + httponly `access_token` cookie, 30-minute expiry |
| FR-3 | Home Interior Planner | `POST /home-budget` — Gemini-generated category breakdown with shopping links |
| FR-4 | Party Budget Planner | `POST /party-budget` — proportional budget allocation + venue suggestions |
| FR-5 | Jewelry Planner (image) | `POST /jewelry-budget` — multimodal Gemini call when an outfit photo is uploaded |
| FR-6 | Shopping Links | Per-item links built from `search_terms`, mapped to relevant Indian platforms |
| FR-7 | Recommendation History | `GET /recommendation-history`, `GET /recommendation-details/{id}` |
| FR-8 | Session Management | In-memory `active_sessions`, 30-minute idle expiry, background cleanup task |

## Non-Functional Requirements

| Category | Requirement |
|---|---|
| Usability | Mobile-responsive UI, inline loading states, no raw error pages |
| Security | Bcrypt password hashing, httponly JWT cookie, token blacklisting on logout, input validation at the API boundary |
| Reliability | Gemini calls retried once; static fallback recommendations if the AI call still fails, is rate-limited, or returns unparseable JSON |
| Performance | Typical planner response time dominated by the Gemini round-trip (1–4s); fallback path responds in <50ms |
| Availability | Single-process FastAPI app suitable for demo/dev use; see [Scalability & Future Plan](../8.Project%20Demonstration/Scalability%20%26%20Future%20Plan.md) for production hardening |
| Scalability | In-memory stores are a deliberate v1 tradeoff — swappable for a real database without changing route signatures (see Solution Architecture) |

## Data Flow Diagram

See [Data Flow Diagram.md](./Data%20Flow%20Diagram.md).

## Technology Stack

See [Technology Stack.md](./Technology%20Stack.md).
