# Planning Orchestrator Implementation Plan

## Objective

Stand up the Planning Orchestrator agent suite (orchestrator + subagents) so it fulfills the responsibilities captured in `IdeaOutline.md` and integrates cleanly with the refreshed execution stack.

## Scope

- Author `agents/Orchestrator.Planning.Main.agent.md` plus supporting subagents (`Subagent.Planning.Ideation`, `Subagent.Planning.Plan`, `Subagent.Planning.Requirements`, `Subagent.Planning.Sprint`, `Subagent.Planning.Doc`, `Subagent.Planning.Dependency`, optional `Subagent.Planning.Sync`/`Subagent.Planning.Git`/`Subagent.Planning.DevOps`).
- Provide developer-facing documentation for planning workflows, prompt patterns, and outputs.
- Ensure planning outputs (plans, requirements, sprints, documentation) can be consumed directly by the Execution Orchestrator.

## Assumptions

- Execution orchestrator alignment tasks from `execution-alignment-plan.md` will be executed in parallel or prior to planning rollout.
- External tracker (GitHub Projects or Azure Boards) remains the source of truth for status; planning SyncAgent is outbound-only.
- All orchestrator interactions occur through GitHub Copilot Chat prompts.
- Subagent delegation follows VS Code's context-isolated subagent guidance (append `#runSubagent` or instruct the orchestrator accordingly).

## Deliverables

1. Planning orchestrator and subagent instruction files under `agents/` with naming consistent with `IdeaOutline.md`.
2. Updated documentation (e.g., repo-level `README.md`) detailing setup, prompt usage, and generated artifacts.
3. Template library (if required) for recurring planning responses (requirements, sprint plans, documentation).
4. Validation checklist confirming execution orchestrator can ingest planning outputs without additional manual formatting.

## Work Breakdown

1. **Design**
   - Confirm required subagent list and optional components (e.g., SyncAgent, GitAgent shared vs dedicated).
   - Define prompt interfaces and expected outputs for each subagent.
   - Establish storage conventions for generated artifacts (`planning/output/...`).
2. **Author Agents**
   - Draft orchestrator instructions focusing on delegation, context management, and human-in-the-loop checkpoints.
   - Create subagent instruction files with concise role descriptions, tool permissions, and response formats.
   - Incorporate outbound sync guidance for the optional SyncAgent.
3. **Documentation**
   - Update `PlanningOrchestrator/README.md` (or create new doc) with quickstart, workflows, and prompt examples.
   - Add references in `IdeaOutline.md` if new implementation details require clarification.
4. **Validation**
   - Run dry-run prompt scenarios to ensure orchestration matches expectations (ideation → plan → requirements → sprint).
   - Verify outputs align with schemas referenced in `IdeaOutline.md` (tables, markdown sections, etc.).
   - Confirm execution orchestrator accepts planning artifacts without extra transformation.

## Risks & Mitigations

- **Scope Creep**: Freeze functionality to what is specified in `IdeaOutline.md` for v1; capture enhancements separately.
- **Agent Overlap**: Cross-check instructions to prevent planning agents from duplicating execution responsibilities.
- **Documentation Drift**: Institute single source (`IdeaOutline.md`) and ensure README content references it explicitly.

## Next Steps

1. Finalize decisions documented in `execution-alignment-plan.md` (discovery subagent retention, sync strategy, tooling defaults).
2. Create agent instruction templates for rapid authoring.
3. Begin drafting `Orchestrator.Planning.Main.agent.md` and `Subagent.Planning.Ideation` instructions.
