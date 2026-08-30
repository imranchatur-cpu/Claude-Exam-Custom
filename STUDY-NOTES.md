# Claude Certified Architect — Foundations (CCAF) Study Notes

A condensed, exam-focused study guide for the **Claude Certified Architect — Foundations (CCAF)** exam. These notes distill the concepts, discriminators, and common traps that the exam tests, organized by domain.

Written against the public **Exam Guide v1.0 (July 2026)**. Where the guide differs from the current product, a **Drift** section at the end lists the differences — answer the *exam-guide* version on test day.

> ### ⚠️ The real exam is harder than any practice set
> These notes and the mock exams alongside them build fluency; they do **not** mirror the difficulty of the live CCAF exam. The real thing is longer, denser, and **scenario-based** — three of four options look reasonable and only one fixes the root cause, the deciding detail is buried in the stem, and the same concept comes back from an angle you haven't drilled. Learn the *reasoning* below cold, not just the conclusions.

> Not affiliated with or endorsed by Anthropic. Original study material written against the public exam guide.

---

## How to use these notes

- Read a domain section, then test yourself with `flashcards.html` filtered to that domain.
- Each bullet is a **principle + the trap it defends against**. The trap is usually an answer that *sounds* responsible but fixes a symptom, works at the wrong layer, or adds a component you don't need.
- The single most-tested skill is **matching the mechanism to the consequence**: probabilistic guidance (prompt) for style/latency issues, deterministic enforcement (code/hook/schema) when a single failure costs money, security, or compliance.

---

## The five Foundations domains

| | Domain | Theme |
|---|---|---|
| D1 | Agentic Architecture | How agents loop, delegate, and recover |
| D2 | Tool Design & MCP | How the agent chooses and calls tools |
| D3 | Claude Code Configuration | Where instructions live and how they're enforced |
| D4 | Prompt Engineering | Making output correct and structured |
| D5 | Context Management | Keeping the right information available and trusted |

---

## D1 — Agentic Architecture

**The agent loop.** Control the loop by reading `stop_reason`: `tool_use` → keep looping; `end_turn` → stop. That is the only valid loop control. Always wrong: checking whether the reply contains text (a turn can contain text *and* a tool call), counting iterations (a safety net, never the primary control), or scanning for "I'm done."

**Statelessness.** The API is stateless — the server stores nothing between calls. Continuity exists only because the client resends the entire history every call. Long conversations grow expensive and overflow precisely because everything is resent; that is why `/compact`, trimming, and a persistent case-facts block exist. Caching is a billing/speed optimization, not memory.

**Subagent isolation.** A freshly spawned subagent knows *nothing* automatically. It receives only its own system prompt (its AgentDefinition), the prompt string the coordinator writes for it, and its own tool list. It does **not** inherit the coordinator's system prompt, prior messages, or other subagents' results — there is no shared store. If it needs something, the coordinator must put it in the briefing.

**Hub-and-spoke.** Everything routes through the coordinator; subagents never talk to each other. This buys observability, one place to handle errors, and controlled information flow. "Add a message bus so subagents share state" is **always** the wrong answer on this exam.

**Context injection.** When a downstream subagent uses stale data from an upstream one, the root cause is that the coordinator isn't passing the upstream output into the downstream briefing. The fix is context injection — not sequencing (order isn't data-passing), not a message bus (breaks hub-and-spoke), not giving the subagent a lookup tool (makes it rediscover what the coordinator already has).

**Task decomposition.** When a multi-agent report is missing whole topics, blame the coordinator — it never assigned them. Most "the specialist did X wrong" problems trace back to what the coordinator put in, or left out of, the briefing.

**Delegation has a cost.** Delegation is not free: it costs context injection plus result collection. Delegate large or specialist work; let the coordinator handle small, self-contained tasks directly. Don't pay delegation overhead for something trivial.

**Spawning subagents.** The coordinator spawns a subagent by calling the **Task tool**, and `allowedTools` must include `"Task"` — without it the coordinator physically cannot spawn subagents.

**Hooks — placement and blocking.** Two decisions per hook: *which event*, and *which tool to attach it to*.
- To **prevent** an action (a refund, a merge): `PreToolUse` — it fires before the tool runs and can block. `PostToolUse` only tidies results on the way back; the action already happened.
- Attach the hook at the boundary where the precondition must actually hold. If a check on `create_service` fails because the relevant file arrives in a later commit, gate the *deployment/merge* tool instead. Switching Pre→Post just checks at a different wrong moment.
- To gate subagent creation deterministically *before* it happens: `PreToolUse` on the Task tool (subagents spawn via Task).
- To archive the transcript on `/compact`: `PreCompact`, which receives the full pre-compaction transcript. There is no "Compact tool," so `PreToolUse` can't catch it.

**Recovery.**
- Resume after a heavy refactor → start fresh and inject a structured summary of prior findings. Both `--resume` and `fork_session` carry *stale* tool results from before the refactor.
- `fork_session` creates independent branches from a shared point-in-time baseline; branches diverge and share no live state. The correct statement is always the one asserting branch independence.
- Crash mid-run → persist progress to durable state (a manifest/checkpoint) and, on rerun, redo only the unfinished work. Not a bigger machine, not more retries.

**Partial results.** A subagent that fails partway must return a **structured partial report**, never discard completed work. Report four things: failure type, what it tried, partial results, and alternatives. Anti-patterns: `{status:"failed"}` (throws away work), silent suppression (an empty result marked success), and killing the whole pipeline.

**Error handling at the lowest level.** Handle errors where they can be resolved: local recovery in the subagent for transient failures; escalate only the truly unrecoverable, with the steps tried and partial results attached. Pushing routine error-handling up to the coordinator ("the coordinator pre-validates every document") recreates the exact overload you're trying to remove.

**Partition before delegating.** When two source-specialized subagents investigate the same subtopics and double the token spend, the coordinator should partition the research space up front (by source or subtopic) *before* work begins. "Let them finish, then deduplicate" fixes it too late — the tokens are already spent.

**Parallel with shared context.** When a complex multi-issue request is handled sequentially with redundant data fetches, decompose into separate issues, investigate them in parallel over one shared context, then synthesize a single resolution. Adding "verification checkpoints between stages" adds overhead and fixes neither problem.

**Workflow shape.** Known, predictable, repeatable steps → a fixed prompt chain. Open-ended, where the next step depends on findings → dynamic decomposition. A 5% edge-case rate doesn't make a stable path dynamic — it needs an exception branch to a human, not machine autonomy.

**Action boundaries.** For zero-tolerance, irreversible actions (money, compliance), put the check at the action boundary itself — validate inside or around the tool that performs the action, so it's impossible unless valid. A hook is a strong deterministic checkpoint and often correct, but the strongest form gates the action boundary, never the prompt.

---

## D2 — Tool Design & MCP

**Tool selection = the description.** When an agent picks the wrong tool, fix the tool description first — it's the primary mechanism for tool selection. A good description states what the tool does, its inputs, example queries, and **when *not* to use it** (the part people omit and the exam rewards). Not a routing classifier, not few-shot, not merging tools.

**System-prompt keyword override.** If routing changes *after* a system-prompt update *despite* unchanged tool descriptions, a word in the system prompt is keyword-matching a tool name and overriding the descriptions — rephrase the system prompt. Tell: "after X, Y started happening" ⇒ X is the cause; "despite unchanged descriptions" removes descriptions as the cause. (Its mirror: nothing touched the system prompt and a description is sparse → fix the *description*.)

**Tool-specific dialect.** Guidance about a format/syntax a *specific* system needs (date format, SQL dialect, units) always lives in that tool's description — never the system prompt. `PostToolUse` fixes results coming back, not inputs going out; an enum constrains a value but doesn't teach the per-system rule.

**Access failure ≠ empty result.** A tool that returns `{data:[], status:"ok"}` when its credentials expired makes the agent say "no data found" and move on. The tool must distinguish the two by returning `isError:true` with `errorCategory:"permission"`. "Found nothing" is not "couldn't look." A prompt rule ("treat empty as error") can't tell them apart and breaks every legitimate no-results case. You own the tool layer even when a vendor's flag lies.

**Error categories (the retry map).** The category answers "could retrying ever work?", not the HTTP code:
- `500 / 503 / timeout` → **transient** (retry)
- `429` rate-limit → **transient** (retry after a wait)
- `400 / 422` malformed → **validation** (fix input, then retry)
- `401 / 403` → **permission** (no retry)
- `404` missing / policy-limit exceeded → **business** (no retry)

Expose this as structured fields (`errorCategory`, `isRetryable`) in the tool response, not as a status code buried in a message string.

**MCP Resources.** When an agent makes exploratory calls before nearly every query (e.g. connected to 40 tables), expose the schema/catalogue as an **MCP Resource**. Tools = what the agent can *do*; Resources = what it can *see* (read-only), discovered automatically at connection time. Resources are defined in the server's code.

**tool_choice.** Three modes:
- `{"type":"auto"}` — may reply in prose (default)
- `{"type":"any"}` — must call a tool; the model picks which
- `{"type":"tool","name":"x"}` — forces that specific tool

Know which tool must be called → force by name. Type varies / the model must pick → `"any"`. Both guarantee structured output; they differ only in *who* picks the schema. **Forcing one tool locks out all others** — it's a session-wide lock, only for "exactly one tool, every turn." Route between similar tools by structural differentiation (distinct names, environment in the description, a scoped rule), not by forcing.

**Composite tools vs bundling.** If an agent wastes loops requesting `get_customer` and `lookup_order` in separate turns, just instruct it to bundle requests into one turn. Building a composite `get_customer_with_orders` works but is more effort and multiplies tools (degrading selection). Here an extra loop only costs latency → ask. (Contrast: when one failure costs money, build a gate.)

**Scoped local tools.** An explicit split like "85% simple, 15% complex" signals: handle the common case locally (give it a scoped tool), escalate the exception through the coordinator. Ceiling: ~4–5 tools per agent.

**MCP configuration & secrets.** `.mcp.json` at the repo root = team (committed). `~/.claude.json` = personal (a file, no slash after `claude`). Credentials via `${ENV_VAR}` expansion so real secrets are never committed. Evaluate community MCP servers before building custom.

**Deferred tool loading (scale).** When many MCP servers are connected and selection degrades from tool *discovery* (not weak descriptions), use deferred/lazy tool loading (a Tool Search Tool): definitions aren't all loaded up front; the model searches by intent and only matching tools load on demand. Distinguish from: too many tools for one role → scoped access; a tool ignored because its description is weak → fix the description.

**Discovered vs ignored.** Not discovered at all → a config/discovery issue (scope, registration). Discovered but ignored → a description/selection issue. Fixing the description when the tool was never discovered (or the reverse) is the trap.

---

## D3 — Claude Code Configuration

**`-p` / `--print`.** Non-interactive mode (do the work, print to stdout, exit). Required in CI or the job hangs waiting for a human. It does **not** restrict files or tools — to restrict tools use `--allowedTools` / `--disallowedTools`.

**Structured output in CI.** `--output-format json` plus `--json-schema` produces `structured_output` a pipeline can parse and act on. (Note: this is a *different* schema from a tool's `input_schema` — they merely share the JSON Schema format. `input_schema` constrains the arguments Claude sends into a tool; `--json-schema` constrains the whole run's final answer.)

**CLAUDE.md asks; a hook enforces.** `CLAUDE.md` (project-root or `.claude/CLAUDE.md`, committed) is the home for team standards, but it only *asks*. If a rule must hold 100% of the time, enforce it with a hook or `settings.json` — never rely on `CLAUDE.md` alone.

**Scope — the most-tested split.** `.claude/` = team / in the repo / ships on clone. `~/.claude/` = personal / your machine only. The exam rarely says "project-scoped"; it describes the outcome: "every developer / on clone / the team" → `.claude/`; "personal / without affecting teammates / my own" → `~/.claude/`. Read the `~` character by character. Everywhere the split appears:
- Standing instructions: `.claude/CLAUDE.md` vs `~/.claude/CLAUDE.md`
- Path rules: `.claude/rules/` vs `~/.claude/rules/`
- Skills: `.claude/skills/<name>/SKILL.md` vs `~/.claude/…`
- MCP: `.mcp.json` (repo root) vs `~/.claude.json` (a file)
- Settings: `.claude/settings.json` vs `~/.claude/settings.json` (managed policy always wins)
- `CLAUDE.local.md` = personal, one project, gitignored — no team equivalent

**Where an instruction lives (four homes).**
- Apply always, everywhere → `CLAUDE.md`
- One file-type scattered across many folders → `.claude/rules/` + a `paths:` glob
- One specific folder → a directory-level `CLAUDE.md`
- When doing a particular task → a **Skill**

Rules = *which files*. Skills = *which task*. CLAUDE.md = *always*. `paths:` takes globs only — no content/grep trigger.

**Rules vs Skills.** Guidance for a *task* (PR review, deploy, migration, "creating a new endpoint") → a Skill, invoked on demand by keyword and loaded only for that task. A `paths:` rule triggers on a *file type* and over-fires: it loads on every touch of that path, including debugging and review.

**Skills vs commands.** A Skill is a *folder* with `SKILL.md`; a command is a single flat `.md`. On a name clash, the skill wins. A loose `.md` dropped in `skills/` does nothing. Frontmatter worth knowing: `context: fork` (isolate noisy output), `allowed-tools`, `argument-hint`.

**Personal skill shadowing.** To customize a team skill without affecting others, put a personal copy at `~/.claude/skills/<name>/SKILL.md` with the **same name** — a personal skill shadows the project skill of the same name, keeping the familiar command. (A different name works but loses the command name.)

**Plan mode vs Explore.** Plan mode is for *ambiguity*, not difficulty: use it to set a consistent strategy and terminology before executing, then switch to direct execution in the same session. An Explore subagent finds files without polluting context but does **not** create consistency. Discriminator: "I don't know where to look" → Explore; "I'm making inconsistent changes" → plan mode first.

**Iterative refinement / ordering.** Resolve dependencies first (the change that reshapes the others), then batch what interacts, then sequence what's independent. "All in one message" ignores the dependency.

**`@import` vs `.claude/rules/`.** To *reduce loaded context*, use `.claude/rules/` with `paths:` — it lazy-loads only when a matching file is open. `@import` only *organizes*; imported content loads eagerly (always), so it does not cut context. Use `@import` for tidiness/reuse, rules for token reduction.

**Batch API — when.** Use the Message Batches API when nobody is waiting: PR checks that block a merge are synchronous; a weekly audit read Monday or nightly test-gen reviewed the next day are batch. "Nightly" *feels* urgent but has all night — frequency is irrelevant; the only question is whether anyone is waiting. Batch = ~50% cheaper, up to 24h, no latency SLA, no multi-turn tool calling.

**CI test criteria.** When Claude writes unit tests that mock the database instead of the required integration tests, the root cause is an under-specified instruction — tighten the `CLAUDE.md` rule with concrete criteria (real DB connections, contract assertions) plus example fixtures. Specify what's *required*; don't ban a useful pattern (a hook that rejects any test containing mocks is over-broad — mocks are valid in unit tests).

**Parallel instances on a shared file.** Sequence it: instance A completes and merges first, then instance B rebases onto updated main and makes its change with full awareness of A's edits. Coordinate shared-file changes up front. Git has no built-in file locking for standard workflows.

---

## D4 — Prompt Engineering

**Code vs prompt — the big decision.** If one failure causes financial loss, a security breach, or a compliance violation → programmatic (deterministic) enforcement. Otherwise prompt guidance is proportionate. A stated failure *rate* means a probabilistic mechanism is already losing — a firmer prompt goes 12%→5%, never 0%. Reverse trap: building a gate for a style or latency problem is over-engineering.

**Boundary few-shot with rationale.** For a router that misroutes ambiguous cases at type boundaries, give 4–6 examples *at* the boundary, each with the rationale for why one tool was chosen over plausible alternatives. The rationale is what generalizes. Grouping examples by tool/type just teaches categories the model already knows.

**Varied few-shot.** When instructions don't fix inconsistent output, add few-shot examples across *varied* scenarios (async, edge cases, error handling) with reasoning — not one repeated case type. Varied examples teach generalization; uniform ones teach replication. Linting gates output but never teaches the model to generalize.

**Inter-step validation.** When step 2 misclassifies and step 3 then extracts the wrong fields, keep the steps separate and validate the classification *between* them. Merging the steps hides the boundary; "more examples in step 2" only lowers the rate — whatever slips through still cascades. Damage happens at the boundary, so fix the boundary.

**Cross-field validation.** A per-field validator can't see a relationship between two fields, so `total ≠ sum(line_items)` passes silently. Add a cross-field check (e.g. a Pydantic `@model_validator(mode="after")`) comparing the fields; its specific error message is what makes a retry useful.

**Schema ≠ correctness.** A JSON schema or `tool_use` kills *syntax* errors (always a valid shape) but not *semantic* ones (wrong sums, misplaced values, invented data). Build the check in — extract a calculated value alongside the stated one and flag mismatches, or run a validator afterwards.

**Required fields can cause hallucination.** If the source lacks a field marked `required`, the model is cornered into inventing a plausible value — you engineered the fabrication. Make fields **nullable** where the source may not contain them. An honest null is visible downstream; a fabrication isn't. An "unclear" enum plus an "other + free-text" option catches the rest.

**Guaranteeing structured output.** Define a tool whose `input_schema` *is* your extraction schema and force it with `tool_choice`; the arguments Claude supplies *are* your data. This beats "respond ONLY with valid JSON" — the structure is enforced by the mechanism, not requested in prose. The tool may do nothing on execution; it exists to impose shape.

**Explicit criteria.** "Be conservative / only high-confidence findings" produces confident, inconsistent output. Replace vague instructions with explicit categorical criteria — exactly what to flag, what to skip, the precise trigger; for severity, give real code examples.

**Trust and filtering.** When one high-false-positive category makes developers dismiss *all* findings without reading, disable the noisy categories and keep the high-precision ones. Trust doesn't stay in its lane — one bad category erodes trust in the accurate ones. "Show confidence scores" fails when the stem says people have stopped reading.

---

## D5 — Context Management

**Escalation triggers — escalate on authority, not emotion.** The only valid triggers:
1. An explicit request for a human
2. A genuine policy gap or ambiguity (the policy "is silent on" it, or admits "two valid readings")
3. A genuine inability to make progress

Invalid triggers: customer frustration or sentiment, transaction size (a dollar amount is not a trigger), emotional difficulty, or a self-reported confidence score. An explicit request for a human is mandatory *even for a trivial task* — the agent has no authority to decide the customer doesn't need a human.

**Resolvable ambiguity ≠ escalation.** If a lookup returns three accounts with the same name, ask the customer for more identifying information first. Escalate only when the agent *cannot* progress — not when it merely doesn't know yet. Escalating a solvable problem wastes human capacity.

**Handoff fields.** An escalation must carry actionable state, because the human cannot see the chat transcript: customer ID, what happened (actions taken), root cause, amount, and the recommended next action. Not a verbatim transcript (noise), not a confidence score.

**The three context-failure modes are structural.** Attention dilution, lost-in-the-middle, and context degradation are structural problems — "use a larger context window" or "upgrade to a more capable model" is *always* wrong for them.
- **Lost-in-the-middle** (cites the start and end, loses the middle, *window not full*) → a key-findings summary at the top plus explicit section headings. "Not full" rules out trimming/summarization (those relieve space pressure).
- **Context degradation** (early precision decays into vague "typical patterns") → maintain scratchpad files recording key findings. Not a token-limit problem.
- Key amounts/dates/order numbers lost across a long conversation → a **persistent case-facts block** that is never summarized. Statelessness means anything that must survive, you store and re-supply.

**Stratified sampling by risk.** With limited review capacity and segments labeled by risk, do a full review of the high-risk set, moderate for mid, lightest for high-volume low-risk. Uniform random sampling *sounds* unbiased but ignores the risk tiers the stem handed you. Volume ≠ risk.

**Segment before you trust an aggregate.** 97% overall accuracy can hide 45% on one document type when the average is dominated by the highest-volume segment. Always break accuracy down by segment before automating. (Pairs with stratified sampling: measure by segment, allocate by risk.)

**Provenance.** When two sources conflict, annotate both with their dates and surface the conflict — never silently pick one.

**Temporal modeling.** For a contract amended twice or a policy that changed mid-year, store *multiple* values, each with its source and effective/collection date — don't overwrite or auto-pick "the latest." "Newer" ≠ "correct"; it depends on the as-of date. A schema that holds only one value is the wrong *shape* for the domain — redesign it.

**Grep vs Glob.** Grep finds what's *in* files (content); Glob finds files by *name/path*. "Find all `*.test.ts`" → Glob. "Find where `processRefund` is called" → Grep.

**Finding all callers.** Read the wrapper/library/re-export modules first, list all aliases, then Grep each. General order: Search → Read → Trace imports/callers → Modify → Test. A single Grep for one name misses consumers via barrel files and re-exports. Don't read the whole codebase up front, and don't trust one grep snippet.

**Edit recovery.** When an `Edit` fails because the anchor text appears in several places, widen `old_string` with surrounding context until it matches one place, or set `replace_all: true`. Read+Write is the last resort.

---

## Cross-cutting reference

### The five exam hooks

| Hook | Fires | Can it block? |
|---|---|---|
| `PreToolUse` | before a tool runs | **Yes** — block / modify / redirect |
| `PostToolUse` | after a tool runs, before Claude sees the result | No — transform only |
| `SubagentStart` | a subagent spawns | No — log / inject context only |
| `SubagentStop` | a subagent finishes | **Yes** — block completion with a reason |
| `PreCompact` | before `/compact` | Receives the full transcript to archive |

Timing order: `PreToolUse → [tool executes] → PostToolUse → SubagentStart → SubagentStop → PreCompact`. Only `PreToolUse` and `SubagentStop` can block. Picking the *event* is half the job — you also pick the right *tool* to attach it to.

### The enforcement ladder (weakest → strongest)

**Ask (probabilistic):** 1 system prompt · 2 few-shot · 3 tool description · 4 CLAUDE.md · 5 `.claude/rules/`
**Force (deterministic):** 6 schema (shape) · 7 validator (content) · 8 `tool_choice` forcing · 9 hook · 10 `settings.json`

The line between **5 and 6** is the single most-tested boundary. Match the rung to the consequence in the stem.

### The placement hierarchy (stop at the first yes)

1. About one specific tool? → the tool description
2. About how a tool reports failure? → the tool response structure (`isError` / `errorCategory` / `isRetryable`)
3. Only certain file types? → `.claude/rules/` + `paths:`
4. A task, not always? → a Skill
5. Everything, always? → `CLAUDE.md`
6. Must hold 100%? → a hook or `settings.json` (never `CLAUDE.md`)

### Message Batches API

~50% cheaper · up to 24h · **no** latency SLA · **no** multi-turn tool calling. `batch_id` (`msgbatch_…`) identifies the whole job; `custom_id` is a per-request id you assign to correlate results back to inputs and to reprocess a subset. Failure handling: identify failures by `custom_id`, resubmit only those with fixes, and test prompts on a 5–10-item sample before the full batch.

### Prompt caching (`cache_control`)

A billing/speed optimization, not memory. It caches the processed form of a stable *prefix* (system prompt, tools, a long reference doc); an ephemeral breakpoint lasts ~5 minutes since last use. Mechanical rule: matching is prefix-first from the very start — put **static** content before the breakpoint and **volatile** content (the user's changing message) after it, or the prefix differs every request and nothing matches.

### Ten trap patterns

1. Near-miss path (one character different)
2. Right idea, wrong layer
3. Generally-good practice, wrong here
4. An option that contradicts the stem
5. Over-engineering (adds a component)
6. More of what's already failing
7. True but irrelevant
8. A mechanism that doesn't exist
9. Timing shift (Pre↔Post when the issue is *which tool*)
10. Partial answer (2 of 3 requirements)

### Five tells to read before choosing

1. "After X, Y started" → X is the cause
2. "Despite unchanged descriptions / correct before" → descriptions aren't the cause
3. "X% of the time it fails" + money/security → go deterministic
4. "The context window is not full" → lost-in-the-middle
5. "Policy is silent on / two valid readings" → escalate to a human

### A five-second checklist per question

1. What is the question sentence *actually* asking? ("first step" ≠ "strongest guarantee" ≠ "root cause")
2. What has the stem ruled out? (already / unchanged / after X / rejected)
3. Is there a number? (% failure → deterministic · 85/15 → local + escalate · a volume-weighted average → segment)
4. Which layer is broken?
5. Read paths and values last, character by character — a single `~` can decide the answer.

### Answers that are almost always wrong

- "Fine-tune / retrain the model" on this architecture exam
- "Use a bigger context window / more capable model" for an *organization* or *attention* problem
- "Add stronger prompt wording / be more careful / just trust the model" where correctness matters
- Escalating because the customer is angry (sentiment) or because confidence is low
- Treating "tool failed" and "found nothing" the same
- "Let subagents talk directly / add a message bus"
- Batch API for anything someone is waiting on; forcing a tool for an open-ended task

When two options say "add more / try harder" and two say "scope it / enforce it / structure it," the latter usually wins.

---

## Drift — Exam Guide v1.0 vs the current product

Answer the **exam** version on test day; the current-product note is for real-world context.

| Topic | Exam Guide v1.0 | Current product |
|---|---|---|
| Hooks | 5 hooks, including `SubagentStart` (observe-only) | ~9 hooks (adds `UserPromptSubmit`, `Stop`, `SessionStart/End`, `Notification`); **no** `SubagentStart` |
| `stop_reason` | `tool_use` and `end_turn` only | Also `pause_turn`, `max_tokens`, `stop_sequence`, `refusal`, `model_context_window_exceeded` |
| Subagent-spawning tool | the **Task** tool | renamed **Agent** (Task still aliases) |
| Skill `allowed-tools` | *restricts* which tools a skill may use | *pre-approves* tools for promptless use; `disallowed-tools` is the real boundary |
| Subagent communication | never — always the wrong answer | nested delegation is possible |

---

## Sources & disclaimer

These notes are anchored to **Exam Guide v1.0 (July 2026)** and keep all model references generic (smallest / mid-tier / frontier). They deliberately exclude product features that post-date the exam window. Practice exams and flashcards that exercise this material live alongside this file — see the [README](README.md).

**Study the reasoning, not the answer key.** The live CCAF exam re-tests each of these ideas through fresh scenarios that are harder than any mock here. If you can state *why* the wrong options are wrong — which layer they fix, which constraint they miss — you're ready for the version you haven't seen.

> Original study material written against the public exam guide. Not affiliated with or endorsed by Anthropic, and not drawn from the live exam. No study resource guarantees a pass.
