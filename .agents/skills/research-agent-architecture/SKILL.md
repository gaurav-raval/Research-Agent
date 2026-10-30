---
name: research-agent-architecture
description: Defines the planner-executor architecture, module layout, Pydantic schemas and orchestration loop for the research agent. Use when creating or modifying any agent code in src/, including planner, executor, tools, filter or synthesizer.
---

## Goal
Build a small, readable agent: question -> plan -> tool loop -> filter -> structured report.

## Module layout (do not exceed this)
```
src/
  config.py        # env loading, limits (MAX_STEPS, TIMEOUTS, MAX_SOURCES)
  schemas.py       # all Pydantic models
  llm.py           # Gemini wrapper: structured output, retry, token counting
  tools.py         # web_search, fetch_page + registry + result validation
  planner.py       # question -> Plan
  executor.py      # runs plan steps, handles tool failures, replans once
  filter.py        # dedupe, domain blocklist, relevance score, truncation
  synthesizer.py   # evidence -> ResearchReport, citation enforcement
  tracing.py       # JSONL trace writer
  main.py          # CLI: python -m src.main "question" [--baseline]
```

## Schemas (schemas.py)
- `PlanStep`: id, goal, tool (`web_search` | `fetch_page`), input, rationale
- `Plan`: question, sub_questions[], steps[PlanStep] (max 6)
- `ToolResult`: ok, tool, data, error, latency_ms
- `Source`: id (S1, S2...), url, title, snippet, domain, relevance (0-1), fetched (bool)
- `Finding`: claim, source_ids[] (min 1)
- `ResearchReport`: question, short_answer, key_findings[Finding], sources[Source], confidence (`low|medium|high`), confidence_reason, limitations[], assumptions[], next_steps[]

## Orchestration rules
1. Planner returns JSON matching `Plan`. If invalid, retry once with the validation error, then fall back to a default plan: one `web_search` per sub-question.
2. Executor runs steps in order. Tool choice is the planner's, but the executor may add a `fetch_page` for the top 2-3 search hits when snippets are too thin.
3. Hard caps: MAX_STEPS=8 total tool calls, MAX_SOURCES=8 kept, page text truncated to ~4000 chars each.
4. If fewer than 2 usable sources remain after filtering, replan ONCE with a broader or rephrased query. Then stop.
5. Synthesizer only sees filtered sources. Output must validate against `ResearchReport`.
6. `--baseline` flag: single search + single LLM call, same output schema, for comparison in the eval.
7. Every step writes a trace event (see reliability-and-safety skill).

## Output
Print a readable report to the terminal AND save JSON to `sample_outputs/<slug>.json`.
