# Customer Journey Map

```mermaid
journey
    title PocketSmart AI — User Journey
    section Discover
      Land on homepage: 4: User
      Read planner descriptions: 4: User
    section Register
      Create account: 3: User
      Log in: 5: User
    section Choose Planner
      Pick Home / Party / Jewelry planner: 5: User
    section Enter Budget
      Fill budget + details form: 4: User
      (Optional) Upload outfit photo: 4: User
    section Get AI Plan
      Submit form: 5: User
      Wait for Gemini response: 3: User
      View structured budget breakdown: 5: User
    section Shop via Links
      Click shopping links per item: 5: User
      Compare prices across platforms: 4: User
    section Revisit History
      Open History page: 4: User
      Reopen a past recommendation: 5: User
```

## Stage-by-stage notes

| Stage | User goal | System response | Friction point addressed |
|---|---|---|---|
| Discover | Understand what the tool does | Landing page explains 3 planners + testimonials | Builds trust before signup |
| Register | Create an account | JWT-backed registration, bcrypt-hashed password | No external OAuth dependency to configure |
| Choose Planner | Pick the relevant scenario | Dashboard with 3 clear entry points | Avoids a single overloaded form |
| Enter Budget | Describe constraints | Grouped form sections, validated inputs | Prevents invalid budgets (≤0, negative counts) |
| Get AI Plan | Receive an actionable plan | Gemini 3.5 Flash-Lite (+ fallback on failure) | Never shows a raw error to the user |
| Shop via Links | Act on the plan | Pre-built search URLs per platform | Removes manual searching |
| Revisit History | Recall previous plans | Per-user in-memory history, sorted newest-first | No loss of past planning work within a session |
