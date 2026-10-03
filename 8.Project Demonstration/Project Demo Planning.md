# Project Demo Planning

## Demo Script (minute-by-minute)

| Time | Step | Sample Input |
|---|---|---|
| 0:00–0:30 | Open landing page, walk through hero + three planner cards | — |
| 0:30–1:00 | Register a new account, log in | username/email/password of presenter's choice |
| 1:00–1:15 | Land on dashboard, show empty "Recent Activity" | — |
| 1:15–2:30 | **Home Planner demo** — fill form and submit | Budget: ₹5,000 · 5 lights · 4 fans · 2 furniture pieces · 1 dining table · Living Room + Kitchen |
| 2:30–2:45 | Walk through category cards and click one shopping link (opens in new tab) | — |
| 2:45–4:00 | **Party Planner demo** — fill form and submit | Budget: ₹5,000 · 3 guests · Wedding · Home venue · Catering + Entertainment toggled on |
| 4:00–4:15 | Show the Print/Save button and totals strip | — |
| 4:15–5:45 | **Jewelry Planner demo** — fill form, upload an outfit photo, submit | Budget: ₹5,000 · Birthday · "casual" preferences · an outfit photo |
| 5:45–6:00 | Point out the Outfit Analysis card (colors/style/formality) | — |
| 6:00–6:45 | Open History page, expand one past recommendation's full details | — |
| 6:45–7:00 | Log out, show redirect to login, attempt to revisit `/dashboard` directly (redirects back to login) | — |

## Feature Demonstration Checklist

- [ ] Registration + login with JWT cookie
- [ ] Protected route redirect when logged out
- [ ] Home planner full flow with shopping links
- [ ] Party planner with proportional allocation + venue suggestions
- [ ] Jewelry planner — text only
- [ ] Jewelry planner — with outfit image (multimodal)
- [ ] Fallback banner shown when Gemini is unavailable (toggle by removing `GOOGLE_API_KEY`)
- [ ] History page ordering + expandable details
- [ ] Logout + session invalidation
