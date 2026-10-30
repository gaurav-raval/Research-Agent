# Project rules (always on)

Project: production-oriented AI research agent (internship take-home). Clarity beats framework complexity.

- Python 3.11+, type hints everywhere, Pydantic v2 for every boundary (LLM output, tool output, final report).
- NO LangGraph / LangChain / CrewAI. Plain custom planner-executor.
- LLM: Gemini through the `google-genai` SDK. Model name comes from env `GEMINI_MODEL`, never hardcoded.
- Search: Tavily primary, `ddgs` fallback. Page fetch: httpx + trafilatura.
- Keep it small: max ~8 source files in `src/`, each under ~200 lines, one responsibility each.
- Never commit secrets. Read keys from `.env` only. Keep `.env.example` updated.
- Ask before adding any new dependency. Update `requirements.txt` when you do.
- Every function that touches the network has a timeout. No bare `except:`.
- Never fabricate results. Do not write numbers, outputs or "what worked" into EVALUATION.md or README unless they came from a real run saved in `sample_outputs/` or `logs/`.
- After each milestone, run the code once and show the real output before moving on.
- Explain non-obvious design choices in short code comments. I (the user) must be able to explain every file.
