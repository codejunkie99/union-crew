# Union Crew

A Codex skill for using Union Alpha as an orchestrator with DeepSeek V4.1 Flash subagents.

Union Alpha handles planning, delegation, integration, and final acceptance. DeepSeek workers handle bounded investigation, implementation, tests, and review.

## Requirements

- Codex with native subagent tools enabled.
- Codex Router with authenticated access to `opencode-go-messages/union-alpha` and `opencode-go/deepseek-v4.1-flash`.
- The worker role `router_opencode_go_deepseek_v4_1_flash`, or an exposed equivalent pinned to the same DeepSeek model.

The skill uses existing routes. It does not install a provider, configure credentials, or change the active model.

## Install

On a machine where this skill is not already installed, authenticate GitHub CLI and run:

```sh
gh repo clone codejunkie99/union-crew "${CODEX_HOME:-$HOME/.codex}/skills/union-crew"
```

Reopen Codex if the new skill does not appear.

## Use

Select **Union Alpha** in the model picker, then invoke the skill with your task:

```text
/union-crew Implement the requested change and verify it.
```

You can also explicitly reference the skill:

```text
Use $union-crew to investigate this bug and implement a tested fix.
```

Workers receive explicit ownership and acceptance criteria. Independent assignments can run concurrently; dependent work runs sequentially. The orchestrator inspects the results and checks the final artifact before reporting completion.

## DeepSeek sub-agent routing

Use the pinned agent role, not a DeepSeek name in the optional `model` field.
Codex can expose DeepSeek agent roles while excluding DeepSeek from its model
override list. That does not prevent spawning the role.

The worker remains **DeepSeek V4.1 Flash** (`opencode-go/deepseek-v4.1-flash`),
using `agent_type: "router_opencode_go_deepseek_v4_1_flash"`. DeepSeek V4 Flash
is a different model and must not be used as a substitute.

For the native collaboration tool, use this argument shape:

```json
{
  "task_name": "inspect_failure",
  "agent_type": "router_opencode_go_deepseek_v4_1_flash",
  "fork_turns": "none",
  "message": "Inspect <project path> for <specific failure>. Read project instructions. Ownership: read-only inspection. Run a focused check and return findings with file:line evidence. Preserve others' changes. Do not spawn agents."
}
```

Omit `model` and `reasoning_effort`; the role pins the model. Use
`fork_turns: "none"` and provide a self-contained assignment because full-history
forks inherit the parent model.

If the role is missing from the live tool schema, refresh the Codex Router model
catalog and reopen the session. A role configuration on disk alone is not enough.
If spawning succeeds but the worker returns a provider error, inspect Router
logs for that error. HTTP 400 tool-schema errors, credentials, and balance issues
need separate fixes; they are not evidence that the DeepSeek role is unsupported.

## Files

- [SKILL.md](SKILL.md): routing and orchestration instructions.
- [agents/openai.yaml](agents/openai.yaml): Codex skill display metadata.

## Validation

The skill passed Codex's skill validator. On September 17, 2026, a native worker
spawn using the pinned V4.1 role, `fork_turns: "none"`, and no model override
completed a read-only inspection with tool calls. Codex Router logs recorded
`opencode-go/deepseek-v4.1-flash` requests with HTTP 200. Both JSON spawn examples
were also checked for valid JSON, the pinned role, fresh context, and no overrides.

This verifies the worker routing path. The full Union Alpha orchestration
workflow has not yet been tested end to end.
