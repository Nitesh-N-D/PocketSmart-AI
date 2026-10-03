# Demonstration of Proposed Features

| Feature | How to Demonstrate | What to Look For |
|---|---|---|
| AI-generated home budget breakdown | Submit the Home Planner form | Categories (lighting/fans/furniture/dining) sum close to the total budget, with per-item shopping links |
| Proportional party budget allocation | Submit the Party Planner form with different `needs_*` toggles | Categories not toggled on still appear with reduced/zero allocation; contingency always present |
| Multimodal jewelry matching | Upload an outfit photo in the Jewelry Planner | Response includes an `outfit_analysis` block (colors/style/formality) not present in the text-only path |
| Shopping links across 18 platforms | Inspect any item's "Shopping Links" row | Each link opens the respective platform's real search page pre-filled with the item's search term |
| Fallback resilience | Temporarily remove/invalidate `GOOGLE_API_KEY` in `.env` and restart the server, then submit any planner | Response still renders fully, with a visible "fallback" banner instead of an error |
| Session expiry | Wait 30+ minutes idle (or inspect `cleanup_expired_sessions` logic/logs) | Session is removed from `active_sessions` and its token is blacklisted |
| Recommendation history | Submit 2–3 different planner requests, then open `/history` | Entries appear newest-first, color-coded by type, each expandable to full JSON detail |
