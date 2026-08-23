# Claude Certified Architect Foundations — Custom Practice Exams

Practice material built from Imran's vault miss profile and the CCAR-F exam guide (v1.0, July 2026).

---

## Retrieval Flashcards — taper deck (built 23 Aug 2026)

**[Open flashcards — GitHub Pages](https://imranchatur-cpu.github.io/Claude-Exam-Custom/flashcards.html)**

42 active-recall cards distilled from every assessment (02–23 Aug), for the Phase-3 taper. Filter by domain, by **★ Repeats** (the four concepts that have beaten me twice), or **Focus (needs work)**; progress is saved in-browser. Weighted to **D1** (weakest at 79%, 27% of the exam) and the repeat concepts.

| Deck | Cards | Covers |
|---|---|---|
| ★ Repeats | 4 | CI validation criteria / `-p`, dialect-in-tool-description, stratified sampling, subagent partial results |
| D1 — Agentic Architecture | 10 | hook placement, PreToolUse-on-Task, resume/fork/fresh, hub-and-spoke, delegation, crash recovery |
| D2 — Tool Design & MCP | 7 | tool description vs system prompt, error categories, MCP Resources, `tool_choice`, per-dev credentials |
| D3 — Claude Code Config | 7 | `-p`, `--output-format json`, PreCompact, Batches API, `batch_id` vs `custom_id`, CLAUDE.md |
| D4 — Prompt Engineering | 5 | boundary few-shot + rationale, Pydantic model_validator, inter-step validation, layer decision |
| D5 — Context Management | 6 | escalation triggers, handoff fields, lost-in-the-middle, `/compact` vs `/clear`, Grep vs Glob |
| Reference | 7 | **all hook types**, `tool_choice` modes, error structure, Batches facts, layer table, exam-day checklist |

Controls: `←`/`→` move · `Space` flip · `G` got it · `A` again · `S` shuffle.

---

## Mock Exam 3 — Sat 16 Aug 2026

**[Open exam — GitHub Pages](https://imranchatur-cpu.github.io/Claude-Exam-Custom/)** · [Claude artifact (backup)](https://claude.ai/code/artifact/0dae1bb5-b5d0-4bc1-940b-46c77dc021ec)**

60 questions · 120 minutes · Single-answer, four-choice · Full domain breakdown and review on finish

| Scenario | Questions | Domain focus |
|---|---|---|
| Meridian Bank — Refund Processing Agent | Q1–15 | D1 + D2 |
| DevStream Platform — Developer Tooling | Q16–30 | D3 + D1 |
| InvoiceAI — Document Processing Pipeline | Q31–45 | D4 + D5 |
| Nexus Research — Multi-Agent Research | Q46–60 | D2 + D5 + D1 |

**Gate target:** ≥ 80% (48/60) to proceed with Phase 2.

---

## Exam facts

- 60 questions · 120 minutes · Pass: 720/1000 (72%)
- Single-answer, four-choice — no multi-select on the live exam
- 4 of 8 scenarios drawn per sitting
- Exam booked: **Friday 28 August 2026**
