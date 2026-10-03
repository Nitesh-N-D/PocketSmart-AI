# Code Layout, Readability and Reusability

## Module responsibilities (single-purpose files)

| File | Owns |
|---|---|
| `main.py` | HTTP routes, request/response wiring, auth dependency declarations |
| `auth.py` | Password hashing, JWT issuance/verification, session lifecycle |
| `gemini_utils.py` | All Gemini prompting, JSON parsing, shopping-link construction, fallbacks |
| `models.py` | Pydantic schemas + field validation, shared by routes and the AI layer |

No file mixes concerns — `main.py` never constructs a Gemini prompt directly, and `gemini_utils.py` never touches JWTs or cookies.

## Naming conventions

- Route handler functions are named after the resource + action (`home_budget`, `recommendation_details`), matching their URL path.
- Private helpers are prefixed with `_` (`_generate_with_retry`, `_home_fallback`, `_links_for`) to signal they're internal to `gemini_utils.py`.
- Platform-specific URL builders (`_amazon`, `_flipkart`, ...) are one-liners registered in a single `PLATFORM_BUILDERS` dict, so adding a new platform means adding one function + one dict entry — not touching every call site.

## Reuse of shared helpers

- **`extract_json_from_response`** is called identically by all three planner functions (`get_home_recommendations`, `get_party_recommendations`, `get_jewelry_recommendations`) — one implementation, one place to fix if Gemini's response format shifts.
- **`_links_for(platforms, query)`** builds a shopping-link dict for any item in any planner; the home, party, and jewelry planners all call it with a different platform list rather than duplicating URL-building logic.
- **`save_to_history`** is the single write path into `user_recommendations` for all three planners, keeping the `RecommendationItem` shape consistent across the history page and recommendation-details endpoint.
- **`_generate_with_retry`** centralizes the retry-once behavior so no planner forgets to retry transient Gemini failures before falling back.

## Docstrings and section comments

Every module opens with a module-level docstring stating its purpose. `main.py` and `auth.py` use `# ---------- Section ----------` banner comments (`# ---------- Auth ----------`, `# ---------- Session ----------`, `# ---------- Planner APIs ----------`) to make the file skimmable without an IDE outline view.
