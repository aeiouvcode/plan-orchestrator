# Google AX: patterns worth copying

Source: [github.com/google/ax](https://github.com/google/ax) (README and [docs/concepts.md](https://github.com/google/ax/blob/main/docs/concepts.md), read 2026-09-23).

## What it is, and what it is not

AX is a declarative orchestrator for running agent workloads on a Kubernetes cluster, on top of Agent Substrate for sandboxing. Its manifests are `ax.io/v1alpha1`, and the README warns that core concepts and protocols will likely have major breaking changes before a stable release. The project describes itself as built for billions of tasks per cluster. That is their claim; nothing here tests it.

It is cluster-first alpha software. Do not adopt it for a laptop or a small fleet. The value for us is the model it uses. Four ideas carry over to local multi-agent work without any of the infrastructure.

## The split

| AX primitive | What it does in AX | What to copy locally |
| --- | --- | --- |
| **Task** | Smallest unit of isolated execution: image, command, CPU/memory limits, env, one gateway, one or more workspaces. Cheap to create, suspend and throw away. | One sandbox per task. Give every agent task its own worktree, its own container or at least its own directory and process, and its own limits. Throw it away when the task closes. |
| **Workspace** | Declares repos (at a branch), MCP servers and skill registries once. It gets materialized in each sandbox before the command starts. An optional plain-language `goal` finishes setup, such as installing a toolchain. | A warm workspace spec. Write down, once per project, what an agent needs before its first useful action: repo and branch, pinned tool versions, setup commands, reference docs. Every new task starts from it instead of rediscovering it. |
| **Gateway** | Listeners plus an egress allowlist of hosts and ports the sandbox may reach. | Default-deny network. List the hosts a task may call (model provider, git host, package registry) and block the rest. Same idea as a strict CSP `connect-src`, applied to the agent itself. |
| **Model** | A named model configuration (provider, model ID, parameters, secret reference) kept in one place instead of in every task's environment. | One model config file per project. Agents reference it by name. Rotating a key or pinning a new model is one edit, and keys never sit in task files. |

Plus the lifecycle verbs:

- **`ax suspend` / `ax resume`** checkpoint an idle agent and pick up where it left off. Locally, this is what `CHECKPOINT.md` and `HANDOFF.md` do by hand: stop cleanly, resume from recorded state, not from memory.
- **Conditions over guesses.** A task reports `WorkspaceReady`, `GatewayReady` and `Ready`, and you wait on `Ready`. Locally: do not start the task until the workspace setup has actually finished and been checked.
- **`ax ssh`** into a running sandbox (debug only). Locally: keep every task's state inspectable. A task whose state you cannot see is a task you cannot review.

## One thing to watch

The README's sample `ax get gateways` output shows a default gateway with egress `*`, which means everything is allowed. The allowlist only protects you if you actually write it. Copy the idea of default-deny, not the sample.

## How this maps onto this repo

- Sandbox per task: [ORCHESTRATION.md section 1](../ORCHESTRATION.md#1-isolate-the-work) (one writer per branch, worktree per agent).
- Warm workspace spec: the Stack and Read sections of [AGENTS.md](../AGENTS.md). Consider a `docs/WORKSPACE.md` for setup commands.
- Default-deny network: a task spec can list allowed hosts under non-goals / constraints.
- Checkpointing: [templates/CHECKPOINT.md](../templates/CHECKPOINT.md) and [templates/HANDOFF.md](../templates/HANDOFF.md).
