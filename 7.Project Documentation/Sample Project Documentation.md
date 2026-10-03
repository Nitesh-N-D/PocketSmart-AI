# PocketSmart AI — Project Documentation

## Abstract

PocketSmart AI is a GenAI-powered budget planning assistant for three common Indian consumer scenarios: furnishing a home interior, planning a party/event, and buying jewelry for an occasion. Given a budget and context, Google Gemini 3.5 Flash-Lite returns a structured, itemized plan in INR, which the application enriches with real shopping search links across major Indian e-commerce and service platforms.

## Introduction

Budget-constrained purchase decisions typically require manual research across many websites, with no single tool reasoning about *how* to split a fixed budget across categories in a way specific to Indian pricing and platforms. PocketSmart AI closes this gap by combining structured LLM reasoning with a deterministic shopping-link layer.

## Objectives

1. Let a user describe a budget scenario (home, party, or jewelry) and receive a structured, India-priced plan.
2. Attach real, clickable shopping search links so the plan is immediately actionable.
3. Support multimodal input (an outfit photo) for jewelry matching.
4. Persist each user's recommendation history for later reference.
5. Degrade gracefully — never show a raw error if the AI service is unavailable.

## Existing Solutions / Literature

- **Generic budgeting apps** (e.g. expense trackers) record spend but don't recommend products or vendors.
- **Generic AI chat assistants** can answer "how should I budget for a party?" conversationally, but return unstructured prose with no India-specific shopping links and no persisted history.
- **E-commerce search** requires the user to already know what to search for and in what proportion to spend.

PocketSmart AI differs by returning a *structured* plan (categories, items, prices, percentages) rather than prose, and by attaching real search URLs rather than leaving "go shopping" as a manual next step.

## Proposed System

See [Proposed Solution](../3.%20Project%20Design%20Phase/Proposed%20Solution.md) and [Solution Architecture](../3.%20Project%20Design%20Phase/Solution%20Architecture.md).

## Architecture

See the Mermaid diagrams in [Solution Architecture.md](../3.%20Project%20Design%20Phase/Solution%20Architecture.md) and [Data Flow Diagram.md](../2.%20Requirement%20Analysis/Data%20Flow%20Diagram.md).

## Implementation

The application is a single FastAPI service (`Project/main.py`) with three supporting modules (`auth.py`, `gemini_utils.py`, `models.py`), server-rendered Jinja2 templates, and vanilla JS for client-side result rendering. See [Coding & Solution.md](../5.%20Project%20Development%20Phase/Coding%20%26%20Solution.md) for code excerpts.

## Results

- All three planners return structured INR budget breakdowns with shopping links.
- The jewelry planner correctly branches to a multimodal Gemini call when an outfit image is provided.
- A full pytest suite (11 tests) passes, covering registration, login, protected routes, history, logout token blacklisting, and input validation.
- Fallback recommendations render correctly when no `GOOGLE_API_KEY` is configured, confirming the app never 500s due to an AI outage.
- Verified live against a real Gemini API key: all three planners (Home, Party, Jewelry — including the multimodal outfit-image path) returned genuine `source: "gemini"` responses with India-specific, structured content. The brief's original model (`gemini-1.5-flash`) has been retired by Google, so the app now targets `gemini-3.5-flash-lite` (see Technology Stack.md).

*(Screenshot placeholders: see `docs`/README for where to attach UI screenshots of the dashboard, each planner's result view, and the history page once a live demo is recorded.)*

## Conclusion

PocketSmart AI demonstrates a complete, working GenAI budget-recommendation flow across three real-world scenarios, with a production-minded approach to auth, session management, and AI-failure resilience, within the scope appropriate for a submission-ready build.

## Future Scope

See [Scalability & Future Plan.md](../8.Project%20Demonstration/Scalability%20%26%20Future%20Plan.md).

## References

- Google Generative AI Python SDK documentation (`google-generativeai`)
- FastAPI documentation (fastapi.tiangolo.com)
- `python-jose`, `passlib[bcrypt]` documentation for JWT/password handling
