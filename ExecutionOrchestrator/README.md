# Execution Orchestrator

The Execution Orchestrator coordinates specialized execution subagents to
deliver predictable, test-driven shipping. This guide explains the updated
flow, optional Sync branch, and the state data the orchestrator must
maintain.

## Workflow Overview

The orchestrator advances through these stages in order:

1. Discovery
2. Implement
3. Test
4. Build
5. Review
6. Deploy
7. Status
8. Git

A Sync decision point is evaluated between discovery and implementation when
fresh alignment is needed.

## Phase Details

### Discovery

- Delegate to `execution.discovery.subagent` with `#runSubagent`.
- Collect project context, impacted files, and test surfaces.
- Update `workflow_state.discovery` with findings and open questions.
- Pause for developer confirmation before moving forward.

### Sync (optional)

- Launch Sync with `#runSubagent` when realignment is needed.
- Update `workflow_state.sync_ran` and capture follow-up tasks.
- Skip Sync when the current discovery data is still accurate.

### Implement

- Delegate the phase goal to `execution.implement.subagent` (`#runSubagent`).
- Require TDD and record the exact commands the subagent runs.
- Store changes, tests, and follow-ups in `workflow_state.implementation`.
- Pause if the subagent reports blockers or needs clarification.

### Test

- Delegate test commands to `execution.test.subagent` (`#runSubagent`).
- Capture results and logs in `workflow_state.test_results`.
- Return to Implement when tests fail; do not advance the pipeline.

### Build

- Delegate build steps to `execution.build.subagent` (`#runSubagent`).
- Track commands, artifacts, and warnings in `workflow_state.build`.
- Halt the pipeline on build failures and send fixes back to Implement.

### Review

- Launch `execution.review.subagent` with diff context from Implement.
- Record status and feedback in `workflow_state.review`.
- Cycle back to Implement for revisions before retesting and rebuilding.

### Deploy

- Enter Deploy only after approval and explicit developer consent.
- Delegate deployment steps to `execution.deploy.subagent` (`#runSubagent`).
- Log steps, smoke checks, and rollback posture in `workflow_state.deploy`.
- Pause for user acknowledgment before promoting changes further.

### Status

- Ask `execution.status.subagent` for the latest status snapshot.
- Save summary, risks, and next steps in `workflow_state.status_updates`.
- Share the update with the developer before starting git tasks.

### Git

- Delegate git actions to `execution.git.subagent` after plan approval.
- Include branch, commit message, and staging expectations in the request.
- Log commands, final `git status`, and follow-ups in `workflow_state.git`.

## Pause Checkpoints

The orchestrator must stop and confirm with the developer at each of these
moments:

- After Discovery (and optional Sync) to validate scope and inputs.
- After Test and Build to confirm the results before Review.
- After Review to decide whether to revise or proceed to Deploy.
- Before starting Deploy and again after it finishes.
- Before executing Git commands, especially commits or pushes.
- Whenever a subagent reports failures, blockers, or unexpected effects.

## Subagent Reference

- `execution.discovery.subagent.agent.md`
- `execution.implement.subagent.agent.md`
- `execution.test.subagent.agent.md`
- `execution.build.subagent.agent.md`
- `execution.review.subagent.agent.md`
- `execution.deploy.subagent.agent.md`
- `execution.status.subagent.agent.md`
- `execution.git.subagent.agent.md`
- Sync subagent (optional; add `execution.sync.subagent.agent.md` if used).

Each file lives in `.github/agents` and can be customized for your
environment.

## Delegating With #runSubagent

- Append `#runSubagent` in Copilot Chat to spawn the requested subagent.
- Provide the phase goal, required commands, and success criteria.
- Reference relevant artifacts or plan files so the subagent has context.
- Log each delegation result in `workflow_state` or the matching plan file.

## State Tracking Requirements

- Maintain `workflow_state` data for every phase and surface it in updates.
- Keep `plans/<task>-plan.md` and phase docs in sync with the stored state.
- Reload the latest state before resuming a paused workflow.
- Record pause reasons, approvals, and outstanding actions for recovery.
