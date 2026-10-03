# Brainstorming & Idea Prioritization

## Raw brainstorm list

1. AI-generated budget breakdown by category (home, party, jewelry)
2. Direct shopping search links per item, per Indian platform
3. Multimodal outfit-photo analysis for jewelry matching
4. Persistent recommendation history per user
5. Session-based auth with auto-expiry for security
6. Multi-currency support (beyond INR)
7. Real-time price-tracking integration with live e-commerce APIs
8. Mobile app (native iOS/Android)
9. Collaborative planning (multiple users on one budget plan)
10. PDF export / print-friendly budget plans
11. Voice-based budget input
12. Vendor rating/review aggregation
13. Calendar integration for event planners
14. Price drop alerts for saved items

## Prioritization grid (Importance vs. Feasibility)

| Idea | Importance | Feasibility (for this build) | Priority |
|---|---|---|---|
| AI-generated budget breakdown (1) | High | High | **Build now** |
| Shopping search links (2) | High | High | **Build now** |
| Multimodal jewelry matching (3) | High | High (Gemini 3.5 Flash-Lite supports images) | **Build now** |
| Recommendation history (4) | High | High | **Build now** |
| Session-based auth (5) | High | High | **Build now** |
| PDF export / print (10) | Medium | High (browser print works) | **Build now** (party planner) |
| Multi-currency (6) | Medium | Medium | Future |
| Real-time price APIs (7) | High | Low (needs partner API access) | Future |
| Vendor ratings (12) | Medium | Low | Future |
| Mobile app (8) | Medium | Low | Future |
| Collaborative planning (9) | Low | Low | Future |
| Voice input (11) | Low | Low | Future |
| Calendar integration (13) | Low | Medium | Future |
| Price drop alerts (14) | Medium | Low (needs scheduled jobs + live prices) | Future |

## Decision

Scope for this build: ideas 1, 2, 3, 4, 5, and 10 (print-friendly results). Everything else is documented in [Scalability & Future Plan](../8.Project%20Demonstration/Scalability%20%26%20Future%20Plan.md) as a deliberate next-phase roadmap rather than an unfinished stub in the current code.
