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

## Files

- [SKILL.md](SKILL.md): routing and orchestration instructions.
- [agents/openai.yaml](agents/openai.yaml): Codex skill display metadata.

## Validation

The skill passed Codex's skill validator. The full Union Alpha orchestration workflow has not yet been tested end to end.
