# Solution Architecture

```mermaid
flowchart TD
    Browser[Browser\nHTML + Vanilla JS]

    subgraph FastAPI App
        Routes[main.py\nRoutes, Auth Dependencies, Sessions]
        AuthMod[auth.py\nJWT + bcrypt + Sessions]
        GeminiMod[gemini_utils.py\nPrompting, JSON extraction,\nShopping-link builder, Fallbacks]
        Jinja[Jinja2 Templates]
        Uploads[(static/uploads/\nOutfit Images)]
        History[(user_recommendations\nin-memory history store)]
    end

    Gemini[Google Gemini 3.5 Flash-Lite\nvia google-generativeai]
    Platforms[[Amazon, Flipkart, IKEA, Myntra, Ajio,\nSwiggy, Zomato, BigBasket, BookMyShow,\nOYO, MakeMyTrip, Meesho, Tanishq,\nBlueStone, CaratLane, Melorra, Google,\nBooking.com, NoBroker]]

    Browser -->|1. Login / Register| Routes
    Routes --> AuthMod
    Browser -->|2. Submit planner form\n+ optional image| Routes
    Routes -->|3. save image| Uploads
    Routes -->|4. build prompt| GeminiMod
    GeminiMod -->|5. generate_content()| Gemini
    Gemini -->|6. JSON text response| GeminiMod
    GeminiMod -->|7. extract_json_from_response| GeminiMod
    GeminiMod -->|8. attach shopping_links| GeminiMod
    GeminiMod -->|9. structured result| Routes
    Routes -->|10. save_to_history| History
    Routes -->|11. render| Jinja
    Jinja -->|12. HTML/JSON| Browser
    Browser -->|13. click shopping link| Platforms
```

## Component responsibilities

| Module | Responsibility |
|---|---|
| `main.py` | FastAPI app init, all HTTP routes, auth dependencies (`get_current_user`, `get_current_active_user`), session lifecycle, startup cleanup task |
| `auth.py` | Password hashing/verification, JWT creation/decoding, in-memory `users_db` / `active_sessions` / `blacklisted_tokens`, session create/touch/end helpers |
| `gemini_utils.py` | Prompt construction per planner, `extract_json_from_response`, shopping-link builders per platform, retry-once wrapper, static fallback recommendations, `save_to_history` |
| `models.py` | Pydantic schemas shared across routes (request bodies, session/recommendation records) with field-level validation |
| `templates/*.html` | Server-rendered pages; planner pages fetch their respective `POST` endpoint via `fetch()` and render results client-side without a full page reload |
| `static/styles.css` / `static/js/app.js` | Shared design system and frontend helpers (currency formatting, banners, shopping-link rendering) reused across all planner pages |

## Why this shape

Keeping all Gemini orchestration in one module (`gemini_utils.py`) means the JSON-extraction and shopping-link logic is written once and reused identically by all three planners — reducing the chance of the three planners drifting out of sync in how they handle a malformed Gemini response.
