# Claude Certified Architect — Custom Practice Exams

Practice material built from Imran's vault miss profile and the Claude Architect exam guides (v1.0, July 2026). Covers both the **Foundations (CCAR-F)** and **Professional (CCAR-P)** exams.

**Quick links**

- **[Foundations mock exam (CCAR-F)](https://imranchatur-cpu.github.io/Claude-Exam-Custom/)** · **[Flashcards](https://imranchatur-cpu.github.io/Claude-Exam-Custom/flashcards.html)**
- **[Professional mock exam (CCAR-P)](https://imranchatur-cpu.github.io/Claude-Exam-Custom/ccar-p-mock-exam.html)**

---

## Retrieval Flashcards — comprehensive taper deck (built 23 Aug 2026)

**[Open flashcards — GitHub Pages](https://imranchatur-cpu.github.io/Claude-Exam-Custom/flashcards.html)**

**96 active-recall cards** covering every documented miss and discriminator from all six assessments (02–23 Aug), plus reference cheat-sheets and the current-state drift list. Filter by domain, by **★ Repeats** (17 concepts that have cost marks 2–4×), **Misses**, or **Focus (needs work)**; progress saved in-browser. Answer the **Exam Guide v1.0** version where it drifts from the current product.

| Deck | Cards | Covers |
|---|---|---|
| ★ Repeats | 17 | access-vs-empty (4×), SP keyword overlap (4×), tool_choice any-vs-name (3×), escalate on authority (3×), inter-step validation (3×), .claude scope (2×), few-shot diversity (2×), stratified sampling (2×) … |
| D1 — Agentic Architecture | 19 | stop_reason, stateless, subagent isolation, hub-and-spoke, context injection, delegation, Task tool, hook placement, Pre-vs-Post, action-boundary, PreCompact, resume/fork/fresh, crash recovery, partition-before-delegation |
| D2 — Tool Design & MCP | 14 | tool description vs system prompt, access-vs-empty, error categories + HTTP map, MCP Resources, tool_choice, forced-lock, deferred tool loading, discovered-vs-ignored, `.mcp.json` scope |
| D3 — Claude Code Config | 16 | `-p`, `--output-format json`/`--json-schema`, `/memory`, CLAUDE.md concatenation, scope, rules-vs-skills, plan mode vs Explore, iterative refinement, `@import` vs rules, Batches |
| D4 — Prompt Engineering | 10 | boundary few-shot + rationale, few-shot diversity, inter-step validation, Pydantic model_validator, schema syntax-vs-semantics, nullable fields, explicit criteria, trust |
| D5 — Context Management | 17 | escalation triggers (3 shapes), handoff fields, error propagation, lost-in-the-middle, case-facts block, scratchpad, stratified sampling, segment accuracy, provenance, temporal modeling, Grep-vs-Glob |
| Reference | 15 | **the five exam hooks**, which hooks block, timing order, enforcement ladder, placement hierarchy, tool_choice, error structure, Batches, cache_control, the 10 traps, the 5 tells, never-right answers, exam-day checklist |
| Drift | 6 | exam-vs-current: 5-hook model (SubagentStart exists, observe-only), stop_reason values, Task→Agent, allowed-tools, subagent comms, skill naming |

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

---

## Professional Exam (CCAR-P) — Bespoke Mock #1

**[Open exam — GitHub Pages](https://imranchatur-cpu.github.io/Claude-Exam-Custom/ccar-p-mock-exam.html)** · [earlier single-page version (Claude artifact)](https://claude.ai/code/artifact/db561b96-5385-48ba-b60c-2eb2c6823c64)

Full timed exam simulator in the **same engine and look as the Foundations mock** — intro screen, 120-minute countdown, domain-grouped question navigator, flagging, a score ring with per-domain breakdown, and a full review with the correct answer, explanation, and named trap on every question. 63 questions across the 7 official domains, weighted to the blueprint. Item formats match the real CCAR-P: **multiple-choice, multiple-response (select TWO), and scenario-matching.** Pitched harder than the standard sets — two-good-answer traps resolved by a binding constraint, plus added technical depth (embedding-space consistency, cache-prefix mechanics, hybrid retrieval, A/B significance, GDPR/HIPAA/FedRAMP).

| # | Domain | Weight | Items |
|---|---|---|---|
| 1 | Solution Design & Architecture | 17% | 11 |
| 2 | Claude Models, Prompting & Context Engineering | 13% | 8 |
| 3 | Integration | 19% | 12 |
| 4 | Evaluation, Testing & Optimization | 16% | 10 |
| 5 | Governance, Safety & Risk Management | 14% | 9 |
| 6 | Stakeholder Communication & Lifecycle Management | 14% | 9 |
| 7 | Developer Productivity & Operational Enablement | 7% | 4 |

**Readiness target:** ≥ 85% every domain = clear book-it signal; ≥ 75% with none under 70% = ready with light review.

### CCAR-P exam facts

- 63 questions · 120 minutes · Pass: 720/1000
- Multiple-choice **and** multiple-response (each item states how many to select)
- 7 weighted domains — no scenario bank; Professional tier ($175)

> Original practice questions written against the public exam guide. Not affiliated with or endorsed by Anthropic, and not drawn from the live item bank. No practice set guarantees a pass.
