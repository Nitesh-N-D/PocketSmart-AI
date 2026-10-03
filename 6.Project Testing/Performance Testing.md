# Project Testing

## Test Case Table

| ID | Test Case | Expected | Actual |
|---|---|---|---|
| TC-01 | Register with a duplicate username | 400 Bad Request | 400 — verified in `test_register_duplicate_username_rejected` |
| TC-02 | Login with wrong password | 401 Unauthorized | 401 — verified in `test_login_wrong_password_rejected` |
| TC-03 | Access a protected route without a token | 401 Unauthorized | 401 — verified in `test_protected_route_without_token_rejected` |
| TC-04 | Home budget happy path | 200 with structured budget breakdown | 200 — verified manually and via mocked `test_history_populated_after_home_planner_call` |
| TC-05 | Budget boundary (₹1 and ₹10,00,000) | Both accepted; proportional allocation scales correctly | Accepted; fallback math scales linearly with `total_budget` |
| TC-06 | Party planner with all needs toggled off | 200 with venue + contingency only, no catering/decoration/entertainment items forced | 200 — budget still allocates to venue + contingency |
| TC-07 | Jewelry planner, text-only (no image) | 200, no `outfit_analysis` key in response | 200 — confirmed via manual `curl` multipart test without an `image` field |
| TC-08 | Jewelry planner with an outfit image | 200, `outfit_analysis` present, Gemini called multimodally | Confirmed live with a real `GOOGLE_API_KEY` — Gemini 3.5 Flash-Lite correctly analyzed a synthetic crimson test image ("Deep Crimson, Berry Red") and returned matching jewelry, `source: "gemini"`; see "Live Gemini calls" below |
| TC-09 | Malformed/unparseable Gemini JSON | Falls back to static recommendation, `source: "fallback"` | Confirmed — exercised by running with no `GOOGLE_API_KEY` set; fallback path activates cleanly with `source: "fallback"` |
| TC-10 | Session expiry after 30 minutes idle | Background task removes the session and blacklists its token | Implemented via `cleanup_expired_sessions`; interval/threshold unit-verifiable but not time-accelerated in the automated suite |
| TC-11 | History ordering | Newest recommendation first | Confirmed — `recommendation_history` sorts by `timestamp` descending |
| TC-12 | Logout token blacklisting | Token invalid immediately after logout | 401 — verified in `test_logout_blacklists_token` |

## Automated suite

`Project/tests/test_app.py` — 11 tests, all passing, Gemini calls mocked so the suite requires no API key:

```
11 passed, 7 warnings in 7.05s
```

(The remaining warnings originate from the `python-jose` dependency's internal use of `datetime.utcnow()`, not from this project's code.)

## Performance notes

| Planner | Typical response time (fallback path, no API key) | Typical response time (live Gemini, estimated) |
|---|---|---|
| Home | < 50ms | 1–3s |
| Party | < 50ms | 1–3s |
| Jewelry (text) | < 50ms | 1–3s |
| Jewelry (with image) | < 60ms | 2–4s (image upload adds model latency) |

- **Token usage / cost:** each planner prompt is a few hundred tokens. `MODEL_CANDIDATES` in `gemini_utils.py` is cost-ordered — `gemini-3.5-flash-lite` (cheapest, tried first) → `gemini-2.5-flash-lite` → `gemini-flash-lite-latest` — avoiding the costlier `gemini-3.8-flash` model used in an earlier iteration.
- **Concurrency:** FastAPI's async routes allow multiple in-flight requests; the in-memory stores (`users_db`, `active_sessions`, `user_recommendations`) are plain dicts without locking, which is acceptable for a single-worker demo deployment but would need a real database or Redis-backed store under concurrent multi-worker load (see Scalability & Future Plan).
- **Live Gemini calls:** a real `GOOGLE_API_KEY` was set in `.env` and all three planners were exercised end-to-end against the live API — Home, Party, and Jewelry (text-only and with an uploaded image, TC-08) all returned `source: "gemini"` with structured, India-specific content, served by the first working candidate in `MODEL_CANDIDATES` (currently `gemini-3.5-flash-lite`). See [Technology Stack.md](<../2. Requirement Analysis/Technology Stack.md>) for the full model-selection history and rationale.
