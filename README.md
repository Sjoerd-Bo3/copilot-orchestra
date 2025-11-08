# Copilot Orchestra

Copilot Orchestra is a dual-orchestrator system for prompt-driven software
planning and execution with GitHub Copilot Chat. A human developer remains in
control, while two complementary orchestrators coordinate context-isolated
subagents to transform intent into shipping software:

- **Planning Orchestrator** – shapes requirements, plans, and documentation.
- **Execution Orchestrator** – implements, validates, and ships the code.

Both orchestrators live entirely inside Copilot Chat. They respond to structured
prompts, delegate to focused subagents with `#runSubagent`, and maintain shared
state so work stays traceable from idea to deployment.

---

## Table of Contents

1. [System Overview](#system-overview)
2. [Setup](#setup)
3. [Planning Orchestrator](#planning-orchestrator)
4. [Execution Orchestrator](#execution-orchestrator)
5. [Shared Operating Practices](#shared-operating-practices)
6. [Prompt Patterns](#prompt-patterns)
7. [Repository Layout](#repository-layout)
8. [Implementation Roadmap](#implementation-roadmap)

---

## System Overview

The developer always steers the workflow through GitHub Copilot Chat:

1. **Prompt** – The developer selects the appropriate orchestrator in chat and
   issues a structured request.
2. **Delegate** – The orchestrator calls specialized subagents (via
   `#runSubagent` or natural-language delegation) to explore, plan, build, test,
   or document.
3. **Pause & Decide** – Mandatory checkpoints surface approvals, open
   questions, risks, and next actions.
4. **Sync (Optional)** – When requested, outbound updates flow to GitHub
   Projects or Azure Boards. External tools remain the source of truth for
   tracking.
5. **Handoff** – The orchestrators exchange context through developer prompts,
   ensuring execution aligns with the latest approved plan.

Typical lifecycle:

```txt
Idea → Ideation → Plan → Requirements → Sprint → Dependencies
           ↓                       ↓
        Execution ← Implementation ← Review/Test/Build ← Deploy ← Status
```

---

## Setup

1. **Install the custom agents**
   - Copy the `.agent.md` files from `.github/agents/` into VS Code Insiders via
     *Configure Custom Agents* or by placing them in your User prompts folder.
   - Ensure the Planning and Execution orchestrators, plus all subagents, are
     available from the Copilot Chat participant picker.

2. **Configure context-isolated delegation**
   - Use `#runSubagent` when referencing a subagent in chat.
   - Provide only the excerpts or files needed for the subagent's task to keep
     context tight and focused.

3. **Optional workspace folders**
   - Create `planning/` (with subfolders like `plans/`, `requirements/`,
     `status/`) if you want artifacts saved to disk. Otherwise, keep everything
     inside chat threads.

4. **Linting**
   - Run `npx markdownlint` against touched Markdown files before considering a
     task complete.

---

## Planning Orchestrator

The Planning Orchestrator converts developer intent into actionable plans while
maintaining traceability and pause points.

### Responsibilities & Focus

- Clarify goals, constraints, stakeholders, and timelines.
- Produce plans, requirements, sprints, and documentation tailored to the
  audience.
- Surface dependencies, risks, and mitigation options early.
- Maintain concise status updates and open questions for the developer.

### Core Workflow

1. **Intake & Alignment** – Confirm intent, gather missing facts, and decide
   whether to launch `Subagent.Planning.Ideation`.
2. **Structuring** – Delegate to `Subagent.Planning.Plan` and
   `Subagent.Planning.Requirements` to capture the path forward, acceptance
   criteria, and traceability.
3. **Scheduling** – Use `Subagent.Planning.Sprint` when capacity and timing
   matter.
4. **Documentation** – Call `Subagent.Planning.Doc` for briefs, summaries, or
   release notes aimed at stakeholders.
5. **Dependencies & Sync** – Launch `Subagent.Planning.Dependency` to map risks;
   opt in to `Subagent.Planning.Sync` when outbound tracker updates are needed.
6. **Review & Handoff** – Present consolidated findings, pause for approval, and
   prepare execution-ready context for the Execution Orchestrator.

### Subagent Reference

- `Subagent.Planning.Ideation` – Explore problem space, open questions, and
   assumptions.
- `Subagent.Planning.Plan` – Build phased plans with milestones and exit
   criteria.
- `Subagent.Planning.Requirements` – Capture functional and non-functional
   requirements plus acceptance criteria.
- `Subagent.Planning.Sprint` – Map capacity, velocity, and sprint commitments.
- `Subagent.Planning.Doc` – Draft stakeholder-facing documentation.
- `Subagent.Planning.Dependency` – Record cross-team, technical, or sequencing
   risks with owners and mitigations.
- `Subagent.Planning.Sync` *(optional)* – Push Copilot-authored summaries to
   project trackers.
- `Subagent.Planning.Git` / `Subagent.Planning.DevOps` *(optional)* – Package
   artifacts or call out tooling and environment needs.

### State Tracking

Each Planning Orchestrator response includes:

- **Current Phase** – Intake / Structuring / Scheduling / Documentation /
  Dependencies / Review / Complete.
- **Work Items** – `{current} of {total}` to show overall progress.
- **Last Action** – Most recent meaningful step.
- **Next Action** – Planned step once the developer approves.

---

## Execution Orchestrator

The Execution Orchestrator drives implementation through a disciplined pipeline
while enforcing TDD, review, and approval gates.

### Phase Responsibilities

- Coordinate discovery, implementation, test, build, review, deploy, status, and
  git phases.
- Ensure every phase logs its results in `workflow_state` so progress is
  recoverable.
- Stop at mandatory checkpoints for developer approval or clarification.
- Respect optional Sync decisions when realignment is required.

### Phase Sequence

1. **Discovery** – Gather context with `Subagent.Execution.Discovery` and pause
   for confirmation.
2. **Sync (Optional)** – Realign with stakeholders before modifying code.
3. **Implement** – Delegate scoped work to `Subagent.Execution.Implement`,
   enforcing TDD and recording commands run.
4. **Test** – Run the requested suites via `Subagent.Execution.Test`; cycle back
   on failures.
5. **Build** – Use `Subagent.Execution.Build` to package artifacts and capture
   warnings.
6. **Review** – Invoke `Subagent.Execution.Review` with the diff; act on
   feedback before continuing.
7. **Deploy** – Proceed only with explicit approval; log smoke checks and
   rollback posture via `Subagent.Execution.Deploy`.
8. **Status** – Summarize progress using `Subagent.Execution.Status` before
   closing out.
9. **Git** – Finish by delegating version-control operations to
   `Subagent.Execution.Git` (branch, commit message, push expectations).

### Pause Gates

- After Discovery (and Sync if used).
- After Test and Build to confirm outcomes before Review.
- After Review to decide on revisions or Deploy.
- Before and after Deploy.
- Before Git actions or whenever a subagent reports blockers.

---

## Shared Operating Practices

- **Delegation** – Use `#runSubagent` and provide only the context required for
  the task at hand.
- **Traceability** – Keep plan documents, dependency registers, and review notes
  in sync with orchestrator state fields so work can resume after a pause.
- **Optional Sync** – Outbound-only; external tools stay authoritative. Skip the
  Sync agents when you do not need tracker updates.
- **Human Authority** – The developer approves scope, commits, deployment, and
  risk decisions. Orchestrators must pause rather than assume consent.

---

## Prompt Patterns

Well-structured prompts accelerate the loop. Examples:

- **Planning**
  - "Plan a 2-week sprint starting 2025-11-15 focused on authentication and rate
    limiting; team capacity: 3 devs, one on vacation week 2."
  - "Draft requirements for customer profile management with CRUD operations,
    pagination (limit 50), status filter, and sub-500ms response target."
- **Execution**
  - "Implement TSK-001 login endpoint in FastAPI (Python 3.11) with JWT, bcrypt,
    validation, error handling, and unit tests."
  - "Review the authentication module for OWASP risks, performance <200ms, 80%
    coverage, and PEP 8 compliance."
- **Status / Cross-Orchestrator**
  - "Show sprint progress, blocked tasks with dependencies, at-risk items, and
    workload distribution."
  - "Summarize alignment so execution understands the latest approved deliverables."

Pair prompts with decision commands such as `approve`, `reject with reason`,
`modify: …`, `proceed`, or `cancel` to keep the conversation deterministic.

---

## Repository Layout

```txt
.github/agents/  # Custom agent definitions for planning and execution
plans/           # Optional workspace artifacts written by orchestrators
IdeaOutline.md   # Source outline used to craft this README
package.json     # npm metadata (markdownlint tooling)
README.md        # This repo-level guide
package-lock.json, node_modules/ (markdownlint tooling)
```

The orchestrators primarily interact through Copilot Chat; file artifacts are
optional and only created when you prompt for persisted outputs.

---

## Implementation Roadmap

- **Phase 1 – Foundation**: Chat command parser, orchestrator routing, prompt
   templates, and response formatting.
- **Phase 2 – Planning Orchestrator**: Planning prompt handlers, approval
   flows, sprint tooling, and context preservation.
- **Phase 3 – Execution Orchestrator**: Execution prompt handlers, code
   generation patterns, test formatting, and deployment confirmations.
- **Phase 4 – Integration**: Cross-orchestrator commands, context switching,
   session management, and shared progress tracking.
- **Phase 5 – Enhancement**: Natural language understanding, prompt
   suggestions, autocomplete, and chat history analysis.

Stay aligned with `IdeaOutline.md` and keep agents updated as the roadmap
advances.
