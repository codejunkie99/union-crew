---
name: union-crew
description: Coordinate work with Union Alpha as the orchestrator and DeepSeek V4.1 Flash subagents. Use when the user invokes /union-crew or $union-crew, or requests this specific model team.
---

# Union Crew

Use Union Alpha for planning, task assignment, integration, and final acceptance.
Use DeepSeek V4.1 Flash for delegated investigation, implementation, tests, and
independent review. Apply this workflow to the user's actual task.

## Model setup

- Orchestrator: `opencode-go-messages/union-alpha`.
- Workers: `opencode-go/deepseek-v4.1-flash`.
- Preferred worker role: `router_opencode_go_deepseek_v4_1_flash`.

A skill supplies instructions; it cannot switch the current model. Check the
active session's model information when available. A default in config.toml is
not proof of the current task's model. If another model is active, explain that
the user must select Union Alpha in the model picker and invoke this skill again.
If identity is unavailable, disclose that limitation and ask the user to confirm
the selection before claiming this model team is running. Do not present model
roleplay as a switch or start a separate task to work around this requirement.

Check the live spawn tool schema for the preferred worker role. Its configured
model must be DeepSeek V4.1 Flash. If the role is unavailable, use another exposed
role only when its configuration confirms that same model. An explicit model
override is an option only when the current tool schema accepts the exact worker
model. Do not substitute DeepSeek V4 Flash, V4 Pro, an inherited parent model, or
another provider route silently. Report unavailable routing as a blocker.

## Orchestration

1. Identify the requested outcome, inspect the relevant project instructions,
   and define the checks that will establish completion.
2. Split substantial work into bounded assignments with clear file or module
   ownership. Start with one or two workers, increasing only when independent
   tasks and available concurrency justify it. Keep dependent work sequential.
   For a trivial task with no useful delegation, handle it directly and say so.
3. Spawn DeepSeek workers through the native collaboration tools. Use only
   argument names offered by the current tool schema. Prefer a fresh context
   with a self-contained assignment so model selection and scope are explicit.
4. While workers run, resolve decisions, inspect integration points, or prepare
   acceptance checks. Avoid duplicating the same investigation or editing files
   currently owned by a worker.
5. Read the returned evidence and inspect the actual changes. Reuse a worker for
   focused corrections. After two attempts with no useful progress, narrow the
   task or diagnose the blocker before trying again. Provider or balance failures
   are routing failures, not permission to change models.
6. Integrate the work and run checks proportionate to the change. For changes
   benefiting from independent review, assign a fresh DeepSeek worker to inspect
   the final diff and relevant evidence. Union Alpha decides acceptance.
7. Finish with the outcome, verification performed, and any remaining limitation.
   Distinguish configured routing from a successful live worker call.

## Worker assignment

Include the objective, relevant context and paths, ownership, constraints,
acceptance criteria, and the expected evidence. Tell every worker:

- You share the workspace with other agents. Preserve existing changes, do not
  revert others' edits, and keep edits within your assigned ownership.
- Follow applicable project instructions. Do not spawn further agents.
- Perform the assigned work and verify it. Return changed files, findings with
  file and line references, checks and outcomes, and unresolved blockers.
- If blocked, report the concrete cause rather than broadening scope.

Example for the collaboration schema exposing `task_name`, `agent_type`,
`fork_turns`, and `message`:

```json
{
  "task_name": "inspect_failure",
  "agent_type": "router_opencode_go_deepseek_v4_1_flash",
  "fork_turns": "none",
  "message": "Investigate the reported failure in <project path>. Read applicable project instructions. Ownership: inspection only, no edits. Reproduce with <focused check> and return the cause with file:line evidence and a proposed fix. You share this workspace; preserve others' changes. Do not spawn agents."
}
```

The role pins the model; omit a redundant model override. Adapt the assignment
and schema to the live tools. Keep parallel writers on disjoint files; coordinate
shared files explicitly. A worker's completion message is evidence to inspect,
not a substitute for checking the final artifact.
