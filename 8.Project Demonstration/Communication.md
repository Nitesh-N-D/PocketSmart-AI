# Communication Plan

| Channel | Purpose |
|---|---|
| Project README (`Project/README.md`) | Primary source of truth for setup, run instructions, and environment variables |
| Numbered documentation folders (this repo) | Phase-by-phase deliverables (ideation → testing → demonstration) for review/grading |
| In-code docstrings and section comments | Day-to-day developer communication about module responsibilities |
| `tests/test_app.py` | Executable documentation of expected API behavior (auth, history, validation) |

## Status updates

Progress was communicated milestone-by-milestone during development: scaffolding → models/auth → Gemini AI layer → planner routes → templates/CSS/JS → pytest suite → documentation folders → final verification — each confirmed working (import check, manual `curl` smoke tests, and `pytest`) before moving to the next.
