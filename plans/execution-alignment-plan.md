# Execution Orchestrator Alignment Plan

## Goal

Bring the existing execution orchestrator agents and documentation into compliance with the expectations described in `IdeaOutline.md`.

## Current Assets

- `.github/agents/execution.orchestrator.agent.md`
- `.github/agents/execution.discovery.subagent.agent.md`
- `.github/agents/execution.implement.subagent.agent.md`
- `.github/agents/execution.review.subagent.agent.md`
- `.github/agents/execution.git.subagent.agent.md`
- `ExecutionOrchestrator/README.md`
- `ImplementationAgents/README.md`

## Key Requirements From IdeaOutline

- Execution stack includes orchestrator plus Implement, Review, Test, Build, Deploy, Status subagents (Sync optional).
- Orchestrator handles execution-focused prompts when the developer selects the Execution Orchestrator chat participant, consumes planning outputs, and keeps the developer in control via conversational checkpoints.
- Focus on implementation execution; planning/ideation responsibilities belong to the Planning Orchestrator.
- Optional SyncAgent only pushes Copilot-authored updates to the chosen external tracker (GitHub Projects or Azure Boards).
- Delegation to subagents should leverage VS Code's context-isolated subagents (see [VS Code documentation](https://code.visualstudio.com/docs/copilot/chat/chat-sessions#_contextisolated-subagents)) via natural-language instructions or the `#runSubagent` tool.

## Gap Analysis

1. **Missing Subagents**
   - No dedicated Test, Build, Deploy, or Status subagents/instructions.
   - Discovery subagent exists but is not referenced in IdeaOutline (needs decision).
2. **Overlapping Responsibilities**
   - Execution orchestrator handles discovery, plan generation, and phase documentation currently stored under `plans/`, overlapping with Planning Orchestrator duties.
3. **Documentation Drift**
   - `ExecutionOrchestrator/README.md` and `ImplementationAgents/README.md` describe legacy multi-phase workflow with mandatory plan files, Anthropic model defaults, and VS Code Insiders requirements that are not present in the new outline.
4. **External Sync Assumptions**
   - Current instructions discuss git usage but lack the optional SyncAgent guidance and outbound-only sync framing.

## Decisions Required

- Should the Discovery subagent be retained (possibly renamed/repositioned) or removed in favor of planning-side ideation/research?
- What tooling defaults (models, IDE requirements) should remain documented versus moved to a separate setup guide?
- Will we introduce a SyncAgent for execution, or rely on manual updates from planning outputs?

## Proposed Remediation Tasks

1. Update `ExecutionOrchestrator/README.md` to reflect the IdeaOutline architecture, agent roster, and optional SyncAgent behavior.
2. Rewrite `.github/agents/execution.orchestrator.agent.md` instructions to remove plan-generation duties and align hand-offs with the expected subagents.
3. Author new subagent definitions:
   - `execution.test.agent.md`
   - `execution.build.agent.md`
   - `execution.deploy.agent.md`
   - `execution.status.agent.md`
   - (Optional) `execution.sync.agent.md`
4. Retire or reposition the discovery subagent based on the decision above.
5. Refresh `ImplementationAgents/README.md` (or replace it) so it mirrors the updated execution workflow and references the new agent files.
6. Verify prompt examples and documentation references now describe selecting the Execution Orchestrator chat participant rather than relying on `@` mentions.

## Acceptance Criteria

- Execution orchestrator and subagent instructions match naming, responsibilities, and flows in IdeaOutline.
- Supporting README content reflects the updated architecture without conflicting legacy guidance.
- Any optional agents (discovery, sync) have explicit justification or removal notes.
- Developer-facing prompts and pause points align with the conversational workflow described in the outline.
