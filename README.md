# Claude Certified Architect — Foundations (CCAF) Practice Exams & Study Resources

A free, self-contained set of practice exams, flashcards, and reference material for the **Claude Certified Architect — Foundations (CCAF)** certification. Everything is plain HTML — no build step, no dependencies, no tracking. Open the files locally or host them on GitHub Pages.

Written against the public **Exam Guide v1.0 (July 2026)**. These are original practice questions — not affiliated with or endorsed by Anthropic, and not drawn from the live exam.

> ### ⚠️ The real exam is harder than this
> Use these sets to build fluency, **not** to predict your score. The live CCAF exam is **noticeably harder than any practice material here**: longer, denser, **scenario-based** questions where three of the four options look reasonable and only one fixes the root cause. Distractors are close, the stems bury the deciding detail, and time pressure is real. Treat a strong score on these mocks as a **floor, not a guarantee** — and make sure you can explain *why* each answer is right, because the exam will test the same idea from an angle you haven't seen. No practice set guarantees a pass.

---

## What's inside

| File | What it is |
|---|---|
| `index.html` | Landing page — Mock Exam 3 (the timed exam engine) |
| `mock-exam-3.html` | Mock 3 — 60 questions, scenario-grouped |
| `mock-exam-4.html` | Mock 4 — Practical Test, 60 questions |
| `flashcards.html` | Retrieval deck — active-recall cards across all five domains |
| `recall-sheet.html` | One-page Q→A crib (API + Claude Code + Agent SDK) with a Quiz mode |
| `STUDY-NOTES.md` | Condensed study guide — every domain's key concepts, discriminators, and common traps |

Each mock exam runs a full timed simulator: an intro screen, a 120-minute countdown, a scenario-grouped question navigator, question flagging, a score ring with a per-domain breakdown, and a full review showing the correct answer and explanation on every question. Progress and flashcard state are saved in your browser (`localStorage`) — nothing leaves your machine.

---

## How to use it

**Just open the files.** Download the repo (or clone it) and open any `.html` file in your browser. That's the whole setup.

**Or host it on GitHub Pages** so you can study from any device:

1. Fork or create a repo from these files.
2. In the repo, go to **Settings → Pages**.
3. Under **Build and deployment**, set **Source** to *Deploy from a branch*, pick your default branch, folder `/ (root)`, and save.
4. After a minute, your site is live at `https://<your-username>.github.io/<your-repo>/`.

All internal links are relative, so the site works the same locally or on Pages.

**A suggested order:** read `STUDY-NOTES.md` → drill `flashcards.html` by domain → sit `mock-exam-3.html` and `mock-exam-4.html` under time → review every explanation, including the ones you got right.

---

## The exam

### The five domains

| | Domain | Approx. weight |
|---|---|---|
| D1 | Agentic Architecture | ~27% |
| D2 | Tool Design & MCP | ~18% |
| D3 | Claude Code Configuration | ~20% |
| D4 | Prompt Engineering | ~20% |
| D5 | Context Management | ~15% |

### Exam facts

- 60 questions · 120 minutes · Pass: **720 / 1000 (72%)**
- Single-answer, four-choice
- Scenario-based — a handful of scenarios, each with a cluster of questions
- Drawn from a larger scenario bank, so no two sittings are identical

---

## Flashcards — retrieval deck

**[Open `flashcards.html`](flashcards.html)** — active-recall cards covering the key discriminators and common traps in every domain, plus reference cheat-sheets and a "drift" list (where the Exam Guide v1.0 differs from the current product).

Filter by domain, by **★ Repeats** (the highest-yield concepts), by **Traps**, or by **Focus (needs work)**. Progress is saved in-browser.

Controls: `←`/`→` move · `Space` flip · `G` got it · `A` again · `S` shuffle.

---

## Mock exams

### Mock Exam 3

**[Open `index.html`](index.html)** — 60 questions · 120 minutes. Full domain breakdown and review on finish.

| Scenario | Questions | Domain focus |
|---|---|---|
| Meridian Bank — Refund Processing Agent | Q1–15 | D1 + D2 |
| DevStream Platform — Developer Tooling | Q16–30 | D3 + D1 |
| InvoiceAI — Document Processing Pipeline | Q31–45 | D4 + D5 |
| Nexus Research — Multi-Agent Research | Q46–60 | D2 + D5 + D1 |

### Mock Exam 4 — Practical Test

**[Open `mock-exam-4.html`](mock-exam-4.html)** — 60 questions · 120 minutes · same engine and look as Mock 3. Built from four practical-test scenarios, 15 questions each, tagged to the five domains for the results breakdown.

| Scenario | Questions | Theme |
|---|---|---|
| Multi-agent Research System | Q1–15 | Coordinator/subagent architecture, decomposition, error propagation |
| Claude Code for Continuous Integration | Q16–30 | CLI in CI, structured output, Batches API, review calibration |
| Code Generation with Claude Code | Q31–45 | CLAUDE.md, skills, slash commands, MCP config, scope |
| Customer Support Agent | Q46–60 | Tool selection, agent loop, escalation, context management |

---

## Reference — Study notes & recall sheet

**[Read `STUDY-NOTES.md`](STUDY-NOTES.md)** — a condensed study guide covering every domain (D1–D5), a cross-cutting reference section (hooks, the enforcement ladder, the placement hierarchy, `tool_choice`, the error taxonomy, Batches, caching, trap patterns), and the exam-vs-current "drift" table.

**[Open `recall-sheet.html`](recall-sheet.html)** — a one-page, exam-style Q→A crib covering the API, Claude Code, and the Agent SDK, with a Quiz mode and the turn-lifecycle flow.

---

## A note on the content

These sets are anchored to **Exam Guide v1.0 (July 2026)**. They deliberately exclude product features that post-date the exam window, and keep all model references generic (smallest / mid-tier / frontier). Where the exam guide drifts from the current product, answer the **exam-guide** version (the study notes list the known differences).

> Original practice questions written against the public exam guide. Not affiliated with or endorsed by Anthropic, and not drawn from the live item bank. No practice set guarantees a pass.

---

## License

Released under the [MIT License](LICENSE) — free to use, fork, and share.
