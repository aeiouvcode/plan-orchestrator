# plan-orchestrator

A working method for running several coding agents on real projects at the same time without losing quality. It started from the agent contract in [Matter-does/dupe](https://github.com/Matter-does/dupe/blob/main/AGENTS.md), then got generalized and tightened by running a fleet of parallel builds on this account.

It is plain markdown. There is nothing to install. Copy the files into a project and fill them in.

## What is in here

| File | Use it for |
| --- | --- |
| [AGENTS.md](AGENTS.md) | The contract template. Drop it in a project root and fill in the bracketed parts. It covers read order, the repo as state, probing instead of guessing, scope fences and the definition of done. |
| [ORCHESTRATION.md](ORCHESTRATION.md) | Running more than one agent: isolation, one writer per branch, implementer and reviewer roles, finding IDs, cadence and gates. |
| [templates/](templates/) | `CURRENT_TASK.md`, `CHECKPOINT.md`, `HANDOFF.md`, `TASK_SPEC.md` and `REVIEW.md`. These state files carry a project between sessions. |
| [external-patterns/](external-patterns/) | Other orchestration systems, read for ideas worth copying. Currently one page: [Google AX](external-patterns/google-ax.md). It covers sandbox per task, warm workspace spec, default-deny network and checkpointing. |
| [examples/](examples/) | Three worked examples from real work: a 60-plus milestone game build, a security sweep across a dozen repos, and a design sweep across the whole portfolio. |

## Quick start

1. Copy `AGENTS.md` into the project root and fill in the project-specific sections: stack, pinned versions, non-goals, definition of done.
2. Create an `agent/` folder and copy the templates into it.
3. Write the first `TASK_SPEC.md` before anyone writes code. No acceptance criteria, no start.
4. When a second agent joins, read [ORCHESTRATION.md](ORCHESTRATION.md) first.

## How it fits with the other two repos

These three repos work as one system:

- **[agent-specific-notes](https://github.com/aeiouvcode/agent-specific-notes)** is the failure log: what went wrong, why, and the rule that came out of it. Check the matching file before the matching operation.
- **plan-orchestrator** (this repo) is the working method: how a task is set up, run, checked and handed off.
- **[research-archive](https://github.com/aeiouvcode/research-archive)** is the research: references, findings and technical decisions for each project.

When a failure repeats, it goes into agent-specific-notes. If the fix is a change to how work runs, the method here gets updated too. A task spec links to the project's research-archive file for its references.

## Differences from the original contract

The dupe contract was written for one project with a fixed tool lineup and a pinned language version. What changed here:

- Roles are defined by job (implementer, reviewer, coordinator), not by which model or tool fills them.
- Pass/fail gates with a rollback rule. A milestone that fails verification is reverted, not patched forward.
- Reviewer findings get stable IDs and a severity, and commits cite them.
- A cadence for long builds: small milestones, each one verified and reported, and no pinging the owner for every increment.
- A sweep pattern for applying one rule across many repos without drifting.
- The reference artifact the owner sends is the acceptance bar. An internal spec does not replace it.
