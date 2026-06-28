# Bob-Sentry for Keycloak — `.bob/` Directory Scaffold Plan

## Top-Level Overview

**Goal:** Bootstrap the complete `.bob/` agent-configuration directory for Bob-Sentry — an
AI-driven, semi-autonomous security triage firewall built on top of IBM Bob inside the Keycloak
source repository.

**Scope:** Create every file in the `.bob/` tree with production-quality content. No changes to
Keycloak Java/Maven source code.

**Approach:** Each file is authored with full, usable content (no stub placeholders). The four
operating phases from the original brief map directly onto the Bob mode-specific `AGENTS.md`
files, the `cve-analyzer` skill, and the `security-sentry` custom mode.

**Non-Goals:**
- No changes to Keycloak source Java/Maven files.
- No live MCP server wiring (follow-on task after directory scaffold is confirmed).
- No CI/CD pipeline integration in this plan.

---

## Target Directory Structure

```
.bob/
├── plan/
│   └── AGENTS.md                           ← Phase 1 & 2: Strategic planning persona
├── code/
│   └── AGENTS.md                           ← Phase 3: Python script generation standards
├── agent/
│   └── AGENTS.md                           ← Phase 3: Autonomous execution & log analysis
├── skills/
│   └── cve-analyzer/
│       └── SKILL.md                        ← Phase 1: CVE cognitive anchor skill
├── rules/
│   └── security-guardrails.md              ← All phases: Non-negotiable safety boundaries
├── references/
│   ├── keycloak-cve-history.md             ← Phase 1 & 2: 5 real CVE training patterns
│   ├── docker-compose.yml                  ← Phase 3: Reusable sandbox template
│   └── admin-api-schemas.md               ← Phase 3: Keycloak REST API payload library
└── commands/
    └── init-triage.md                      ← /triage slash-command definition
```

Plus in `.bob/`:
```
.bob/custom_modes.yaml                      ← security-sentry mode registration
```

---

## Four-Phase Pipeline Mapping

| Phase | Description | Bob Mode | Key Files |
|-------|-------------|----------|-----------|
| 1 | Setup, Indexing & Cognitive Anchor | Plan | `skills/cve-analyzer/SKILL.md`, `references/keycloak-cve-history.md` |
| 2 | Ingestion, Routing & Target Isolation | Plan → security-sentry | `plan/AGENTS.md`, `.bob/custom_modes.yaml` |
| 3 | Autonomous Exploit Replication | Code + Agent | `code/AGENTS.md`, `agent/AGENTS.md`, `references/docker-compose.yml`, `references/admin-api-schemas.md` |
| 4 | Remediation Report & GitHub Sign-Off | Agent (Orchestrator) | `agent/AGENTS.md`, `commands/init-triage.md` |

---

## Sub-Tasks

---

### Sub-Task 1 — `custom_modes.yaml` (security-sentry mode)

**Intent:** Register the `security-sentry` custom Bob mode that is invoked in Phase 2 to
orchestrate multi-model routing — a lightweight parser extracts the threat profile from a raw
bug bounty report, and a reasoning model maps it against live Java paths and the CVE skill.

**Expected Outcomes:**
- File exists at `.bob/custom_modes.yaml`.
- Defines a mode named `security-sentry` with a role definition that describes the
  Orchestrator persona, references the CVE analyzer skill, and specifies multi-model routing
  behaviour.
- Mode is immediately usable from the Bob mode switcher.

**Todo List:**
1. Create `.bob/custom_modes.yaml`.
2. Define the `security-sentry` mode with:
   - `name`: Security Sentry
   - `slug`: `security-sentry`
   - `roleDefinition`: Senior Security Orchestrator persona covering Phase 2 routing logic.
   - `whenToUse`: Clear description so Bob can auto-select this mode.
   - Instruction to load `@.bob/skills/cve-analyzer/SKILL.md` as the first action.
   - Instruction to use a lightweight model for noise-stripping and a reasoning model for
     code-path mapping.

**Status:** `[x] done`

---

### Sub-Task 2 — `.bob/plan/AGENTS.md`

**Intent:** Define how Bob behaves in **Plan mode** for Phases 1 and 2. This is the strategic
reasoning layer that decomposes a raw GitHub issue or bug bounty report into a structured
4-stage triage pipeline and identifies the precise Keycloak Java components at risk.

**Expected Outcomes:**
- File exists at `.bob/plan/AGENTS.md`.
- Contains the Senior Security Architect persona.
- Mandates cross-reference with `@.bob/references/keycloak-cve-history.md` and the
  `cve-analyzer` skill before finalising any plan.
- Produces a structured output: threat summary, affected component (Java class or SPI),
  Keycloak version band, and 4-stage pipeline outline.
- Documents Keycloak component taxonomy so Bob reasons about the right attack surface.

**Todo List:**
1. Create `.bob/plan/` directory and write `AGENTS.md` with:
   - Persona: Senior Security Architect.
   - Mandatory 4-stage pipeline structure (Semantic Parsing → Sandbox Provisioning →
     Execution Strategy → Verification).
   - Instruction to invoke `@.bob/skills/cve-analyzer/SKILL.md`.
   - Instruction to cross-reference `@.bob/references/keycloak-cve-history.md`.
   - Keycloak component taxonomy: auth flows, realms, clients, roles/RBAC, token endpoints,
     federation SPIs, Admin REST API, FGAP policies, OIDC/SAML protocol handlers.
   - Output format contract (threat profile JSON + pipeline outline).
   - Escalation condition: if component cannot be isolated, output `ESCALATE` and stop.

**Relevant Context:**
- Keycloak version in this repo: `999.0.0-SNAPSHOT` (main branch).
- Security contact: `keycloak-security@googlegroups.com` (from `SECURITY-INSIGHTS.yml`).
- Primary attack surfaces from the 5 CVE training patterns:
  - TOCTOU in role management (name-based vs ID-based checks).
  - SSRF via unvalidated `client_session_host` in backchannel logout.
  - FGAP authorization asymmetry (assign vs unassign).
  - HTTP parameter pollution in redirect URI handling.
  - ROPC grant type policy enforcement bypass.

**Status:** `[x] done`

---

### Sub-Task 3 — `.bob/skills/cve-analyzer/SKILL.md`

**Intent:** Create the cognitive anchor skill that is the cornerstone of Phase 1. When invoked,
it directs Bob to load and deeply study the `keycloak-cve-history.md` reference, internalise
the 5 real CVE patterns, and apply that knowledge as a lens when reading new vulnerability
reports. This prevents "cold start" hallucinations and keeps Bob aligned with Keycloak
maintainer conventions.

**Expected Outcomes:**
- File exists at `.bob/skills/cve-analyzer/SKILL.md`.
- Skill description is precise enough for Bob to auto-select it during a triage session.
- Steps instruct Bob to load the CVE history, build an internal pattern-match index, and
  apply it when evaluating a new report.
- Includes a checklist mirroring the "Bug Triage Checklist" from the CVE training document
  (input validation, authorization consistency, state management, protocol implementation,
  trust boundaries).

**Todo List:**
1. Create `.bob/skills/cve-analyzer/` directory and write `SKILL.md` with:
   - Description of when to invoke the skill.
   - Step 1: Load `@.bob/references/keycloak-cve-history.md`.
   - Step 2: For each of the 5 CVE patterns, extract: attack type, affected component,
     confirmation signal, future risk indicators.
   - Step 3: When given a new report, score it against each pattern and output a ranked
     similarity list.
   - Step 4: Output the matched pattern(s), the most likely affected Keycloak component,
     and the recommended triage approach.
   - Bug Triage Checklist (5 sections: Input Validation, Authorization Consistency, State
     Management, Protocol Implementation, Trust Boundaries).

**Relevant Context:**
- The 5 CVE patterns are fully documented in Sub-Task 6 (`keycloak-cve-history.md`); the
  skill file must reference that document rather than duplicate content.
- Pattern categories from the CVE training doc: TOCTOU, SSRF, Authorization Bypass,
  Protocol Vulnerability, Policy Enforcement Gap.

**Status:** `[x] done`

---

### Sub-Task 4 — `.bob/code/AGENTS.md`

**Intent:** Define how Bob behaves in **Code mode** during Phase 3 when generating Python
reproduction scripts. This governs script language, structure, safety constraints, and output
conventions to ensure every generated script is deterministic, isolated, and readable by the
Agent mode's log parser.

**Expected Outcomes:**
- File exists at `.bob/code/AGENTS.md`.
- Enforces Python 3 (using `requests` library) as the scripting language.
- Specifies a two-script structure: **Script A** (realm + client + user setup via Admin REST
  API) and **Script B** (exploit payload execution).
- All HTTP calls target `http://localhost:8080` only.
- Defines exit-code contract and structured console output for Agent-mode parsing.
- References `@.bob/references/admin-api-schemas.md` for all Keycloak API payloads.

**Todo List:**
1. Create `.bob/code/` directory and write `AGENTS.md` with:
   - Persona: Senior Security Engineer / Exploit Developer.
   - Two-script mandate (setup script + exploit script).
   - Script structure: Phase labels printed to stdout (`[SETUP]`, `[EXPLOIT]`, `[ASSERT]`).
   - Localhost-only rule for all requests.
   - Admin REST API base: `http://localhost:8080/admin/realms`.
   - Token endpoint: `http://localhost:8080/realms/{realm}/protocol/openid-connect/token`.
   - Exit code contract: `0` = clean run, `1` = assertion failed (vuln confirmed or patched),
     `2` = setup error (environment not ready).
   - Instruction to output a final JSON summary line: `RESULT: {...}` for Agent parsing.

**Relevant Context:**
- Python `requests` library is universally available and matches the original brief's
  language choice.
- The Admin REST API schemas in Sub-Task 8 (`admin-api-schemas.md`) provide the exact JSON
  payloads to reference.

**Status:** `[x] done`

---

### Sub-Task 5 — `.bob/agent/AGENTS.md`

**Intent:** Define how Bob behaves in **Agent mode** for Phase 3 (sandbox execution) and
Phase 4 (report generation + GitHub sign-off). This is the most autonomous mode and needs
the strictest guardrails woven directly into the persona definition.

**Expected Outcomes:**
- File exists at `.bob/agent/AGENTS.md`.
- References the `cve-analyzer` skill and the `security-guardrails.md` rules as mandatory
  pre-execution loads.
- Defines the container lifecycle: spin up → poll readiness → run setup script → run exploit
  script → capture output → tear down.
- Defines the Phase 4 output: a structured Markdown triage report saved locally to
  `.bob/reports/triage-<issue-number>-<date>.md`. **No GitHub write operations of any kind.**
- Specifies the verdict schema (JSON) and escalation conditions.

**Todo List:**
1. Create `.bob/agent/` directory and write `AGENTS.md` with:
   - Persona: Autonomous Security Triage Agent / Orchestrator.
   - Pre-execution checklist: load rules, load skill, confirm Docker is running.
   - Container lifecycle using `@.bob/references/docker-compose.yml`.
   - Readiness poll: `docker logs keycloak 2>&1 | grep "Admin console listening"` with
     30-second timeout.
   - Script execution order: Script A (setup) → Script B (exploit).
   - Output capture to `/tmp/keycloak-triage/result.log`.
   - Verdict schema: `{ "cve_pattern": "", "confirmed": bool, "http_evidence": "",
     "affected_component": "", "suggested_fix_area": "", "severity": "" }`.
   - Phase 4: draft the triage report as a local Markdown file at
     `.bob/reports/triage-<issue-number>-<date>.md`. **Never post, comment, push, or write
     to GitHub in any form. GitHub is READ-ONLY throughout the entire pipeline.**
   - Escalation: if any `500` error appears or setup script exits with code `2`, emit
     `ESCALATE` verdict and halt — do not run exploit script.
   - Mandatory cleanup: `docker compose down -v` always executes last.

**Relevant Context:**
- Keycloak readiness string confirmed from existing test resources:
  `Admin console listening on http://0.0.0.0:8080`.
- Port `8080` is standard across all Keycloak test infrastructure in this repo.
- GitHub CLI (`gh`) is used **read-only**: `gh issue view` to fetch issue content only.
  Writing to GitHub (comments, PRs, pushes) is explicitly prohibited.

**Status:** `[x] done`

---

### Sub-Task 6 — `.bob/references/keycloak-cve-history.md`

**Intent:** Populate the CVE pattern knowledge base using the 5 real training CVEs provided.
This is the most critical reference document — it grounds Bob's reasoning in actual Keycloak
vulnerability history and provides the triage checklist.

**Expected Outcomes:**
- File exists at `.bob/references/keycloak-cve-history.md`.
- Contains all 5 CVE entries with full detail: description, attack pattern, root cause,
  triage approach, confirmation criteria, future risk indicators.
- Contains the complete Bug Triage Checklist (5 sections).
- Contains the severity assessment guidelines.

**Todo List:**
1. Create `.bob/references/` directory and write `keycloak-cve-history.md` with all 5 CVEs:
   - **CVE-2026-9796** — Admin Role Rename TOCTOU Bypass (Issue #49427).
   - **CVE-2026-4874** — SSRF via OIDC Token Endpoint `client_session_host` (Issue #47935).
   - **CVE-2026-11986** — FGAP Authorization Bypass, assign vs unassign asymmetry (#49766).
   - **CVE-2026-9689** — HTTP Parameter Pollution, OIDC redirect URI duplication (#49430).
   - **CVE-2026-9792** — ROPC Grant Type Client Policy Bypass (#49436).
2. Include per-CVE: attack type, prerequisites, attack steps, root cause, future risk
   indicators, and confirmation criteria.
3. Append the full Bug Triage Checklist and severity guidelines from the training document.

**Status:** `[x] done`

---

### Sub-Task 7 — `.bob/references/docker-compose.yml`

**Intent:** Provide a standardised, safe Keycloak sandbox compose template. Using a
known-good template prevents Bob from hallucinating container parameters during Phase 3.

**Expected Outcomes:**
- File exists at `.bob/references/docker-compose.yml`.
- Uses `quay.io/keycloak/keycloak` with a **version resolution strategy**:
  1. Agent mode first checks the SV report for an explicit affected version → use that tag.
  2. If no version stated, Agent derives the version from the issue's filing date (e.g.
     issues filed June 2026 → `26.1` series).
  3. Falls back to `latest` only if neither of the above can be determined.
- The image line uses a `{{KEYCLOAK_VERSION}}` placeholder; Agent mode substitutes it before
  copying the file to `/tmp/keycloak-triage/`.
- Binds port `8080` to `127.0.0.1` only (never `0.0.0.0`).
- Sets bootstrap admin credentials (`admin`/`admin`) via environment variables.
- Container name is explicitly `keycloak` (required by the Agent mode's readiness poll).
- Starts in `start-dev` mode.
- Includes a `keycloak-net` bridge network scoped to the compose project.

**Relevant Context:**
- Official image registry: `quay.io/keycloak/keycloak` (from `SECURITY-INSIGHTS.yml`).
- Port `8080` matches all existing Keycloak test infrastructure in this repo.
- The 5 training CVEs are all `26.x` series (filed June 2026); Agent should resolve those
  to tag `26.1`.
- Existing docker-compose in `testsuite/` binds on `127.0.0.1:5000` — same localhost-only
  pattern applied here.

**Status:** `[x] done`

---

### Sub-Task 8 — `.bob/references/admin-api-schemas.md`

**Intent:** Provide a curated library of Keycloak Admin REST API request/response JSON
payloads. Code mode consults this during Phase 3 script generation to avoid hallucinating
API shapes.

**Expected Outcomes:**
- File exists at `.bob/references/admin-api-schemas.md`.
- Contains annotated, copy-paste-ready JSON for: obtain admin token, create realm, create
  OIDC client, create user, assign realm role, get client secret, trigger authorization code
  flow, refresh token with custom `client_session_host`, trigger backchannel logout.
- Each payload is annotated with HTTP method, endpoint path, and templated variables
  (e.g. `{{REALM_NAME}}`).
- Includes a section on FGAP-specific API calls (assign/unassign role endpoints) to support
  CVE-2026-11986 triage.
- Includes the ROPC token request payload to support CVE-2026-9792 triage.

**Todo List:**
1. Write the file with annotated JSON blocks for each operation, organised by phase
   (Setup operations first, then exploit-specific operations).
2. Include a templating legend explaining `{{PLACEHOLDER}}` conventions.

**Status:** `[x] done`

---

### Sub-Task 9 — `.bob/rules/security-guardrails.md`

**Intent:** Write the non-negotiable safety and isolation boundaries that apply to ALL Bob
modes during a triage session. These rules prevent accidental execution against production
systems, credential leakage, or non-deterministic results.

**Expected Outcomes:**
- File exists at `.bob/rules/security-guardrails.md`.
- Six rules covering: network isolation, mock external IdPs, deterministic failure codes,
  no real credentials, secret scanning, and mandatory cleanup.
- Written as unambiguous imperatives.

**Todo List:**
1. Create `.bob/rules/` directory and write `security-guardrails.md` with:
   - **Rule 1 — Absolute Isolation:** all requests to `http://localhost:8080` or Docker
     network alias only. Never target a remote host.
   - **Rule 2 — Mock External IdPs:** WireMock for any OIDC/SAML federation scenario.
     Never use real external IDP credentials or live URLs.
   - **Rule 3 — Deterministic Failure Codes:** `401`/`403` = expected failure (patch
     working); `200` on exploit = vulnerability confirmed; `500` = escalate.
   - **Rule 4 — No Real Credentials:** use only well-known test defaults (`admin`/`admin`,
     `test-realm`, `test-client`). Never use production secrets.
   - **Rule 5 — Secret Scan:** before executing any generated script, scan its source text
     for real-looking JWTs (three `.`-delimited base64 segments) or long base64 strings.
     Abort and flag if found.
   - **Rule 6 — Mandatory Cleanup:** `docker compose down -v` must always execute,
     regardless of test outcome.

**Status:** `[x] done`

---

### Sub-Task 10 — `.bob/commands/init-triage.md`

**Intent:** Define the `/triage` slash-command so an engineer can kick off the full 4-phase
pipeline with a single command: `/triage <github-issue-number>`.

**Expected Outcomes:**
- File exists at `.bob/commands/init-triage.md`.
- Defines command name, argument, description, and exact 5-step instruction sequence.
- Steps reference the plan/code/agent AGENTS.md files and the cve-analyzer skill by path.
- Contains a prominent `## ⚠️ Read-Only Mode` notice — GitHub is READ-ONLY throughout.
  No write operations to GitHub in any step.
- Final output is a local `.bob/reports/triage-<issue-number>-<date>.md` file only.

**Todo List:**
1. Create `.bob/commands/` directory and write `init-triage.md` with:
   - Command: `/triage`
   - Argument: `<github-issue-number>` (integer; targets `keycloak/keycloak` repo).
   - Prominent notice: GitHub access is READ-ONLY. No comments, no PRs, no pushes.
   - Step 1: **Read** the GitHub issue using `gh issue view <number> --repo keycloak/keycloak`.
     Display the full issue body and any linked comments. STOP here if `gh` is unavailable.
   - Step 2: Switch to Plan mode, invoke `@.bob/skills/cve-analyzer/SKILL.md`, produce
     threat profile and 4-stage pipeline outline.
   - Step 3: Switch to Code mode, resolve Keycloak image version, generate Script A (setup)
     and Script B (exploit) using `@.bob/references/admin-api-schemas.md`.
   - Step 4: Switch to Agent mode, execute the sandbox lifecycle, capture verdict JSON.
   - Step 5: Draft the triage report as `.bob/reports/triage-<issue-number>-<date>.md`.
     Present path to engineer. **Do not post, comment, or write to GitHub.**

**Status:** `[x] done`

---

## Execution Order

Sub-tasks should be implemented in this sequence to ensure cross-file references are never
broken:

```
Sub-Task 9  (rules)
  → Sub-Task 6  (cve-history reference)
  → Sub-Task 7  (docker-compose reference)
  → Sub-Task 8  (admin-api-schemas reference)
  → Sub-Task 3  (cve-analyzer skill — references Sub-Task 6)
  → Sub-Task 4  (code AGENTS.md — references Sub-Task 8)
  → Sub-Task 5  (agent AGENTS.md — references Sub-Tasks 3, 7, 9)
  → Sub-Task 2  (plan AGENTS.md — references Sub-Tasks 3, 6)
  → Sub-Task 1  (custom_modes.yaml — references Sub-Task 3)
  → Sub-Task 10 (init-triage command — references all above)
```

All ten files together form a complete, self-consistent Bob-Sentry configuration ready for
Phase 1 of the hackathon build.
