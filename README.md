# Claude Certified Architect — Practice Exams & Study Resources

A free, self-contained set of practice exams, flashcards, and reference sheets for the **Claude Certified Architect** certification. Covers both the **Foundations (CCAR-F)** and **Professional (CCAR-P)** exams. Everything is plain HTML — no build step, no dependencies, no tracking. Open the files locally or host them on GitHub Pages.

Written against the public **Exam Guide v1.0 (July 2026)**. These are original practice questions — not affiliated with or endorsed by Anthropic, and not drawn from the live exam. No practice set guarantees a pass.

---

## What's inside

| File | What it is |
|---|---|
| `index.html` | Landing page — Foundations Mock Exam 3 (also the home of the timed exam engine) |
| `mock-exam-3.html` | Foundations Mock 3 — 60 questions, scenario-grouped |
| `mock-exam-4.html` | Foundations Mock 4 — Practical Test, 60 questions |
| `ccar-p-mock-exam.html` | Professional Mock 1 — 63 questions across 7 domains |
| `mock-exam-p2.html` | Professional Mock 2 — harder set, 63 all-new questions |
| `flashcards.html` | Retrieval deck — active-recall cards across all five Foundations domains |
| `recall-sheet.html` | One-page Q→A crib (API + Claude Code + Agent SDK) with a Quiz mode |

Each mock exam runs a full timed simulator: an intro screen, a 120-minute countdown, a scenario- or domain-grouped question navigator, question flagging, a score ring with a per-domain breakdown, and a full review showing the correct answer and explanation on every question. Progress and flashcard state are saved in your browser (`localStorage`) — nothing leaves your machine.

---

## How to use it

**Just open the files.** Download the repo (or clone it) and open any `.html` file in your browser. That's the whole setup.

**Or host it on GitHub Pages** so you can study from any device:

1. Fork or create a repo from these files.
2. In the repo, go to **Settings → Pages**.
3. Under **Build and deployment**, set **Source** to *Deploy from a branch*, pick your default branch, folder `/ (root)`, and save.
4. After a minute, your site is live at `https://<your-username>.github.io/<your-repo>/`.

All internal links are relative, so the site works the same locally or on Pages.

---

## Foundations exam (CCAR-F)

### The five domains

| | Domain |
|---|---|
| D1 | Agentic Architecture |
| D2 | Tool Design & MCP |
| D3 | Claude Code Configuration |
| D4 | Prompt Engineering |
| D5 | Context Management |

### Flashcards — retrieval deck

**[Open `flashcards.html`](flashcards.html)** — active-recall cards covering the key discriminators and common traps in every domain, plus reference cheat-sheets and a "drift" list (where the Exam Guide v1.0 differs from the current product).

Filter by domain, by **★ Repeats** (the highest-EV concepts), by **Misses**, or by **Focus (needs work)**. Progress is saved in-browser.

Controls: `←`/`→` move · `Space` flip · `G` got it · `A` again · `S` shuffle.

### Mock Exam 3

**[Open `index.html`](index.html)** — 60 questions · 120 minutes · single-answer, four-choice. Full domain breakdown and review on finish.

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

### Foundations exam facts

- 60 questions · 120 minutes · Pass: 720/1000 (72%)
- Single-answer, four-choice — no multi-select on the live exam
- 4 of 8 scenarios drawn per sitting

---

## Professional exam (CCAR-P)

Both Professional mocks use the same engine and look as the Foundations mocks — intro screen, 120-minute countdown, domain-grouped navigator, flagging, score ring with per-domain breakdown, and a full review with the correct answer, explanation, and named trap on every question. 63 questions each, weighted to the blueprint. Item formats match the real exam: **multiple-choice, multiple-response (select TWO), and scenario-matching** (each item states how many to select).

### The seven domains

| # | Domain | Weight | Items |
|---|---|---|---|
| 1 | Solution Design & Architecture | 17% | 11 |
| 2 | Claude Models, Prompting & Context Engineering | 13% | 8 |
| 3 | Integration | 19% | 12 |
| 4 | Evaluation, Testing & Optimization | 16% | 10 |
| 5 | Governance, Safety & Risk Management | 14% | 9 |
| 6 | Stakeholder Communication & Lifecycle Management | 14% | 9 |
| 7 | Developer Productivity & Operational Enablement | 7% | 4 |

### Mock 1

**[Open `ccar-p-mock-exam.html`](ccar-p-mock-exam.html)** — 63 questions across the 7 official domains, weighted to the blueprint. Pitched harder than the standard sets: two-good-answer traps resolved by a binding constraint, plus added technical depth (embedding-space consistency, cache-prefix mechanics, hybrid retrieval, A/B significance, GDPR/HIPAA/FedRAMP).

### Mock 2 — harder set

**[Open `mock-exam-p2.html`](mock-exam-p2.html)** — 63 all-new questions (zero stem overlap with Mock 1), same item-format mix (45 single · 13 multiple-response · 5 scenario-matching). Every item is built so **all four options sound responsible and exactly one fixes the root cause.** Recurring seams it drills:

- **Root cause over symptom** — the answer that adds a log, a confirmation, or "a stronger prompt" is the distractor.
- **Least privilege at the source** — remove the capability, don't compensate for it downstream.
- **Humans on the irreversible** — no answer that strips oversight for speed is ever correct; rubber-stamp "human-in-the-loop" is not oversight.
- **Treat retrieved/external content as untrusted** — the exfiltration path (untrusted input + private data + an outbound tool); enforce access control at retrieval; tenant/user scoping.
- **When *not* to use an LLM** — sub-50 ms real-time scoring, deterministic arithmetic, and "the model is not a vector index."
- **Silent quality drift** on a model upgrade → a version-attributed eval set that gates promotion; **accepted ≠ correct**; stratify before you trust an aggregate.
- **Measure before you assert** — bias evaluation is required even without fine-tuning; an anecdote is not a distribution.
- **Define targets before you build**; name the trade-off and hand stakeholders real options (defer / descope / extend).
- **Config scope** — managed settings individuals can't override, project scope under version control, personal preferences in user scope, **credentials never in a committed file**.

### Professional exam facts

- 63 questions · 120 minutes · Pass: 720/1000
- Multiple-choice **and** multiple-response (each item states how many to select)
- 7 weighted domains

---

## Reference — Recall Sheet

**[Open `recall-sheet.html`](recall-sheet.html)** — a one-page, exam-style Q→A crib covering the API, Claude Code, and the Agent SDK, with a Quiz mode and the turn-lifecycle flow.

---

## A note on the content

These sets are anchored to **Exam Guide v1.0 (July 2026)**. They deliberately exclude product features that post-date the exam window, and keep all model references generic (smallest / mid-tier / frontier). Where the exam guide drifts from the current product, answer the **exam-guide** version.

> Original practice questions written against the public exam guide. Not affiliated with or endorsed by Anthropic, and not drawn from the live item bank. No practice set guarantees a pass.

---

## License

Released under the [MIT License](LICENSE) — free to use, fork, and share.
