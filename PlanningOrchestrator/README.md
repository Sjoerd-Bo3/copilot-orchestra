# Planning Orchestrator Guide

The Planning Orchestrator translates developer intent into structured plans,
requirements, and status summaries. It supports strategic decision making while
the human developer retains final approval authority, then feeds execution-ready
work to the Execution Orchestrator.

## Core Responsibilities

- Interpret planning prompts delivered through GitHub Copilot Chat.
- Launch context-isolated planning subagents using `#runSubagent` or natural
  language delegation per the VS Code Copilot guidance.
- Keep requirements, risks, and status artifacts traceable and lightweight.
- Pause at key checkpoints so the developer can approve or redirect work.
- Hand off clear outputs to execution without duplicating implementation steps.

## Collaboration Flow

1. Intake: confirm goals, constraints, and deadlines with clarifying questions.
2. Exploration: call the Ideation subagent when the problem space needs mapping.
3. Structuring: produce backlogs, plans, and requirement sets with the Plan and
   Requirements subagents.
4. Scheduling: align capacity and timelines through the Sprint subagent.
5. Documentation: generate audience-appropriate docs via the Doc subagent.
6. Dependencies: surface risks and cross-team impacts with the Dependency
   subagent.
7. Optional Sync: push Copilot-authored summaries to external trackers on demand
   while treating those systems as the source of truth.
8. Handoff: summarize outcomes, list open questions, and provide references for
   the Execution Orchestrator.

## Planning Subagents

- `planning.orchestrator`: coordinates the planning stack, manages pause points,
  and mirrors execution-style state tracking (current phase, work items, last
  action, next step) so updates stay consistent across orchestrators.
- `planning.ideation`: explores solution spaces, gathers unknowns, and returns
  structured findings.
- `planning.plan`: produces phased delivery plans, success metrics, and key
  milestones.
- `planning.requirements`: author requirements, acceptance criteria, and
  traceability matrices.
- `planning.sprint`: maps capacity, schedules, and sprint commitments.
- `planning.doc`: drafts documentation, release notes, and stakeholder updates.
- `planning.dependency`: catalogs risks, external dependencies, and mitigation
  steps.
- `planning.sync` (optional): pushes Copilot-authored summaries to GitHub
  Projects or Azure Boards when outbound updates are needed.
- `planning.git` / `planning.devops` (optional): package planning artifacts or
  seed external work items when automation is desired.

Each subagent operates in its own context window. Provide only the files or
notes it needs to stay focused.

## Pause Checkpoints

- Before committing to a scope: confirm priorities, constraints, and deadlines.
- After ideation: review key findings, open questions, and proposed direction.
- After plan or requirements drafts: approve, adjust, or reject the proposal.
- Before sync delegation: confirm outbound updates and the target system.
- Before handoff to execution: summarize outputs, highlight risks, and state the
  expected next step.

## Artifact Expectations

- Plans: concise markdown outlining phases, risks, and success measures.
- Requirements: numbered acceptance criteria with traceability references.
- Sprint decks: capacity, commitments, and risk callouts aligned to timeboxes.
- Status briefs: bullets covering progress, blockers, and upcoming decisions.
- All markdown artifacts live under `planning/output/` (create folders as
  needed) so they are easy to discover and archive.

## Delegation Patterns

- Use `#runSubagent` when launching a planning subagent from chat.
- Provide a clear objective, relevant excerpts, and explicit deliverables.
- Capture subagent responses, merge them into the orchestrator state, and repeat
  until the developer approves the outcome.
- When questions remain unresolved, list them under **Open Questions** so
  follow-up prompts stay focused.

## Quality Gates

- Lint touched markdown with `npx markdownlint` before reporting completion.
- Ensure outputs include dates, owners, and success metrics where relevant.
- Highlight assumptions and dependency tracking so execution can react quickly.
- Keep responses concise; fall back to linked artifacts for deep context.

## Setup Checklist

1. Install the planning `.agent.md` files in VS Code Insiders via the custom
   agent UI or by copying them into `.github/agents/`.
2. Configure context-isolated subagents in chat per the VS Code documentation.
3. Optionally create `planning/output/` (and subfolders like `plans/`,
   `requirements/`, `status/`) if you want artifacts persisted to disk; the
   orchestrator can also keep everything in chat when that better fits your
   workflow.
4. Confirm the Execution Orchestrator knows where to find planning artifacts.

## Working Agreement

- The human developer remains decision maker for scope, priority, and approvals.
- Planning focuses on strategy and documentation; execution handles code and
  deployments.
- Optional Sync stays outbound-only unless the developer specifies otherwise.
- Planning updates should note the latest approved baseline and changes since
  the previous touchpoint.

Keep this README aligned with IdeaOutline and the planning agent instructions so
new collaborators can onboard quickly.
