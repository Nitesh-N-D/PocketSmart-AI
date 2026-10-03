# Scalability & Future Plan

## Current limitations (by design, for a submission-scale build)

- All storage (`users_db`, `active_sessions`, `user_recommendations`) is in-memory and resets on server restart.
- Single-process deployment; no horizontal scaling or load balancing.
- Shopping "links" are constructed search URLs, not live price/availability data.
- Currency fixed to INR; no multi-currency support.

## Future Plan

| Area | Planned Improvement |
|---|---|
| Persistence | Replace in-memory dicts with a real database (PostgreSQL for users/history, Redis for session state) behind the same `auth.py`/`gemini_utils.py` function signatures |
| Live pricing | Integrate real e-commerce partner APIs (where available) to show actual current prices instead of AI-estimated ranges |
| Price tracking | Scheduled jobs to re-check saved recommendation prices and alert users on drops |
| Multi-currency | Extend `usd_to_inr`-style conversion into a general currency layer driven by user locale |
| Mobile app | React Native or Flutter client consuming the same FastAPI JSON endpoints |
| Caching | Cache repeated Gemini prompts (e.g. identical budget/occasion combos) to reduce latency and API cost |
| Rate limiting | Add per-user/per-IP rate limiting on planner endpoints to control Gemini API spend |
| Collaborative planning | Allow multiple users to co-edit a single party/home budget plan |
| Vendor ratings | Aggregate review data to prioritize higher-rated vendors in suggestions |

## Scalability approach

The current architecture deliberately isolates all storage access behind a small set of functions (`create_session`, `save_to_history`, `users_db` lookups) rather than scattering dict access across route handlers. This means migrating to a real database is a localized change in `auth.py` and `gemini_utils.py`, not a rewrite of `main.py`'s routes.
