# Proposed Solution

| Aspect | Description |
|---|---|
| **Problem statement** | Budget-constrained decisions (home interior, events, jewelry) require manual cross-platform research with no structured, India-specific guidance. |
| **Proposed solution** | A FastAPI web app where a user enters a budget and context; Gemini 3.5 Flash-Lite returns a structured JSON budget plan, which the app enriches with real shopping search links for Indian platforms. |
| **Novelty / Uniqueness** | Combines (a) structured, India-INR-priced AI recommendations, (b) multimodal outfit-to-jewelry matching, and (c) ready-to-click shopping links in one flow — rather than a generic chat interface. |
| **Social impact** | Helps users — especially first-time homeowners, event planners, and budget-conscious shoppers — make confident purchase decisions without needing a financial advisor or hours of manual research. |
| **Business model** | Freemium: core planners free; future tiers could add live price tracking, multi-currency support, or vendor partnerships (affiliate links) as documented in Scalability & Future Plan. |
| **Scalability** | In-memory storage is a deliberate v1 choice for a demo-scale submission. The data access patterns (`users_db`, `active_sessions`, `user_recommendations`) are isolated in `auth.py` / `gemini_utils.py` so swapping to Postgres/Redis later doesn't require route-level rewrites. |
