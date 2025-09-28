# Multi-Tool Orchestration Challenge — **"Orchestrate & Deliver"**

**Goal (short):** Design, implement, and demonstrate a reproducible automated workflow that coordinates **multiple AI tools** to deliver a small engineering pipeline end-to-end. The emphasis is on **workflow design, tool-chain orchestration, automation, observability, and reproducibility** — not on producing a large product.

---

## Scenario / Context
You are an SRE / Platform Engineer building a toolchain for a product team. The team needs a reliable pipeline that will:

1. Generate a small microservice scaffold (server + simple logic).
2. Produce unit tests and integration tests.
3. Run static analysis and apply trivial auto-fixes.
4. Build, containerize, and run the service in a local staging environment.
5. Run tests; on failures automatically triage logs and propose remediation.
6. Produce human-readable and machine-readable reports/artifacts documenting the run.

Your task is to **orchestrate at least three distinct AI tools** (different categories) so each tool’s output feeds the next stage automatically, with minimal manual steps.

---

## Mandatory goals (must satisfy)
- Use **≥ 3 different AI tools** from different categories (e.g., LLM for codegen, AI test generator, static analyzer with AI suggestions, LLM triage agent, CI/orchestrator with AI step).
- Provide an **automated orchestrator** (single CLI or script) that runs the entire pipeline end-to-end locally.
- Capture and save **artifacts** for every tool step (prompts, outputs, diffs, logs).
- Document every AI prompt & tool invocation and justify each tool choice.
- Produce a `JSON` summary report at the end with `status`, `time_taken`, `tools_used`, `tests_passed`, `failures`, and `recommendations`.
- Provide a short **postmortem** with failure modes, cost/latency tradeoffs, and suggested improvements.

---

## Deliverables (what to hand in)
A repository (or zip) that contains:

- `orchestrate.sh` or `orchestrator/cli` — single command to run the full pipeline locally.
- `ci/` directory with a CI config (GitHub Actions or GitLab CI) that mirrors the orchestrator.
- `service/` — the generated microservice code (small: 1–2 endpoints).
- `tests/` — unit and e2e tests (may be AI generated and improved).
- `artifacts/` — saved outputs from each tool step:
  - `artifacts/tool-usage-log.md` (timestamped: tool name, version, prompt used, short note).
  - saved LLM outputs, static analysis results, diffs, test logs.
  - `artifacts/summary-<timestamp>.json`.
- `ORCHESTRATION_PLAN.md` — architecture diagram (ASCII or markdown), tool roles, I/O contracts between steps.
- `README.md` — exact reproducible run instructions (assume reviewer has Docker installed).
- `FIXES.md` — record of fixes (AI or human) and why each was applied.
- `postmortem.md` — lessons learned, security & cost considerations, proposed improvements.
- Export your AI chats and add them in an `/ai_chats/` folder (to help us evaluate your AI usage capabilities)
- Optional but recommended: a short terminal recording/screencast or a transcript of the orchestrator run.

---

## Required phases (suggested)
Follow these phases (you may improve on them), capture prompts and outputs every step:

### Phase 0 — Plan (deliverable: `ORCHESTRATION_PLAN.md`)
- Pick the AI tools and their roles.
- Define input/output formats (file names, JSON schemas) between tools.
- Choose runtime environment (Docker Compose recommended for local reproducibility).

### Phase 1 — Seed & Generate
- Use an LLM/codegen tool to generate a microservice scaffold and core logic.
- Persist the prompt and raw LLM output in `artifacts/`.

### Phase 2 — Test Generation
- Use an AI test-generator (LLM or specialized tool) to create unit tests for core functions.
- Save tests and the prompts that generated them.

### Phase 3 — Static Analysis & Auto-Fix
- Run static analyzers / linters (e.g., ruff/flake8/ESLint/CodeQL).
- Use an AI fixer or script to apply trivial fixes automatically (format, imports, common lint).
- Save before/after diffs in `artifacts/`.

### Phase 4 — Build & Deploy
- Containerize the service (Dockerfile) and run with Docker Compose (or kind).
- Ensure a healthcheck/readiness probe is in place.
- The orchestrator should build and start the service automatically.

### Phase 5 — Test, Triage & Remediate
- Run unit + integration tests. If tests fail:
  - Automatically gather logs and stack traces.
  - Call an LLM-based triage agent (or equivalent) with a concise prompt containing relevant logs.
  - Attempt safe automated remediations for trivial issues (e.g., missing imports, wrong assertions).
  - Re-run tests and capture outcomes.

### Phase 6 — Report & Hardening
- Produce `artifacts/summary-<timestamp>.json` and a human-readable report summarizing steps, tools, timings, failures, remediation.
- Propose at least **3 productionizing improvements** (secrets handling, caching LLM results, observability).

---

## Constraints & non-goals
- **No real secrets** committed. Use `.env.example` only.
- Prefer local/free tools; if paid tools are referenced, document cost and provide sample outputs only.
- Do not send PII to third-party tools.
- Focus is on **orchestration quality**, not feature completeness.

---

## Example minimal toolchain (you may change, but document your choices)
1. **LLM Codegen** (e.g., OpenAI GPT) — scaffold code + helper functions.
2. **Test generator** (LLM or Diffblue-like) — generate tests.
3. **Static analyzer / linter** (ruff, flake8, ESLint, or CodeQL) — find issues.
4. **AI auto-fixer** (LLM or Copilot CLI) — apply trivial fixes.
5. **Triage agent** (LLM) — analyze failing logs and suggest remediation.
6. **Orchestrator** — a Bash/Python script or GitHub Actions workflow that pulls it all together.

---

## Artifacts & Documentation requirements
- Capture every AI prompt and response saved under `artifacts/`.
- `artifacts/tool-usage-log.md` must contain timestamped entries: tool name, version (if known), prompt used, and a 1–2 sentence rationale for choosing that tool at that stage.
- Save diffs before/after automated fixes.
- Produce a final JSON summary: `artifacts/summary-<timestamp>.json` with at least:
  ```json
  {
    "status": "success" | "partial" | "failed",
    "time_taken_seconds": 123,
    "tools_used": [{"name":"GPT-4", "version":"4.x"}, ...],
    "tests_passed": 12,
    "tests_failed": 0,
    "failures": [],
    "recommendations": ["..."]
  }
  ```

---

## Evaluation rubric (weights)
- **Orchestration design & clarity — 30%**: clear plan, modular handoffs, reproducibility.
- **Tool selection & justification — 20%**: appropriateness, cost/latency/security reasoning.
- **Automation & reproducibility — 20%**: single command to run pipeline, CI mirroring the run.
- **Triage & remediation capability — 15%**: automatic failure detection, classification, actionable remediation.
- **Documentation & artifacts — 10%**: prompts, logs, runbook, summary report.
- **Bonus — 5%**: smart caching, parallelism, failure injection, or a short demo recording.

---

## Scoring checklist (practical)
- [ ] Orchestrator runs end-to-end locally (or CI simulates it).
- [ ] `artifacts/tool-usage-log.md` with prompts & timestamps.
- [ ] JSON summary produced and valid.
- [ ] CI config mirrors the orchestrator.
- [ ] Postmortem + recommended improvements present.
- [ ] ≥ 3 different AI tools used and documented.
- [ ] No secrets in the repo; `.env.example` present.

---

## Timebox (suggested)
- Core task: **4 hours**  
- Extras & hardening: **+2 hours**

---

## Final acceptance criteria
- The orchestrator runs and produces the JSON summary. If some test or step still fails, document the residual failure in `postmortem.md` with reproduction steps.
- All AI prompts & outputs used in the run are saved in `artifacts/`.
- The repo contains clear run instructions and `ORCHESTRATION_PLAN.md`.
- The candidate provides a concise `FIXES.md` explaining all human/AI changes.

---

**Goal reminder:** demonstrate *how* you make multiple AI tools collaborate reliably — emphasize automation, observability, reproducibility, and safe remediation rather than building a feature-complete product. Good luck!
