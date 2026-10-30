---
name: eval-and-docs
description: Guides writing the evaluation runner, EVALUATION.md, README, sample outputs and AI-usage disclosure for the research agent. Use when creating eval scripts, test queries, README, EVALUATION.md or submission documents.
---

## Goal
Honest, evidence-backed documentation. Reviewers value discussed failures more than hidden ones.

## Eval runner (eval/run_eval.py)
- Reads `eval/queries.json` (id, query, expected_behavior, category).
- Runs agent AND `--baseline` for each query. Saves outputs to `eval/results/<id>_{agent,baseline}.json`.
- Computes simple automatic checks per run: schema valid, >=1 source, every finding cites an existing source, confidence present, latency, tool calls, tokens.
- Writes `eval/results/summary.md` as a table. No fabricated numbers.

## Required test queries (>=5, use eval/queries.json)
Cover: a normal comparison, an Indian-startup research query (hard for web search), a vague query, a nonsense/unanswerable query, an injection fixture, and one forced tool failure (invalid API key or blocked domain).

## EVALUATION.md
Per query: query, expected behavior, actual behavior, what worked, what failed, fix or next step. Add a short agent-vs-baseline comparison. Only use results from real runs.

## README headings (must match the assignment)
Setup | Architecture | Why an agentic approach | Tools and why | Bad tool results | Reducing hallucinations | Prompt injection | Production readiness | What to monitor | Key tradeoffs | Known limitations | Future improvements | AI tool usage

## AI tool usage section
List each AI tool used (Antigravity + model, Gemini API for runtime), what it generated, what I designed/wrote/reviewed myself, and how I verified it. Leave a TODO for the user to fill personal details; do not invent them.
