# Agent contract: [project name]

This repository is the source of truth for [project name]. Chat sessions are for planning and research. They are not project state. If something is not in the repo, assume the next agent will not know it.

## Read before changing anything

1. `AGENTS.md` (this file)
2. `docs/PROJECT.md`: what the project is and who it is for
3. `docs/REFERENCE.md`: pinned versions, API or language reference, the reference artifact
4. `agent/CURRENT_TASK.md`
5. `agent/CHECKPOINT.md`
6. `agent/HANDOFF.md`

Then look at the actual state:

- `git status`
- `git log --oneline -10`
- `git diff` for any uncommitted work
- the source, test and workflow files the task touches, at their current version

If any of these disagree with what you remember from a conversation, trust the repo.

## Stack and pinned versions

- [language / runtime and version]
- [framework / engine and version, how it is loaded]
- [hosting target]

Do not change a pinned version or swap the stack unless a task explicitly says so.

## Probe, don't guess

- Never invent syntax, library APIs, endpoint behavior, permissions or platform limits.
- If behavior is uncertain, write the smallest probe that proves it: one call, one render, one saved value read back. Run it, then build on the result.
- Record what the probe showed in `agent/HANDOFF.md` under Discoveries.

## Scope fences

What this project is: [one or two sentences].

Non-goals. Do not build these unless a task explicitly asks:

- [feature area]
- [feature area]

Standing rules:

- Preserve working code unless there is a concrete defect or an accepted design change.
- Keep dependencies minimal. Pin or vendor anything remote.
- Treat user data and the filesystem as read-only unless a task defines a destructive operation and its safety contract.
- No secrets in the repo or its history.

## Definition of done

A task is done only when every applicable item has evidence:

- [ ] Acceptance criteria in the task spec are met, each with evidence
- [ ] Formatter / linter / parser pass
- [ ] Relevant tests pass
- [ ] Runs in the real target (browser, device, build output), not just in theory
- [ ] Checked at [desktop width] and [phone width, e.g. 390px] if there is a UI
- [ ] Security checks pass: secret scan, outbound-call check, CSP matches real usage
- [ ] Compared against the reference artifact, if there is one
- [ ] `git diff` reviewed line by line for stray edits, debug code and secrets
- [ ] Deployed version verified live (the served file matches the commit)
- [ ] `agent/CHECKPOINT.md` and `agent/HANDOFF.md` updated

"Should work" is not evidence. If a gate cannot be verified, the task is not done. Say so.

## Task discipline

- Work only on the current task. Adjacent changes only when a blocking defect forces them, and write down why.
- Do not redo steps already listed in `CHECKPOINT.md`.
- Update the checkpoint after each meaningful atomic step.
- Record failed approaches, with the reason each one failed, in the handoff.
- If a milestone fails its gates, revert to the last accepted state and record the failure. Do not stack fixes on an unverified base.

## Git discipline

- One coherent commit per atomic task or reviewable change.
- Build each commit on the current head. Never force-push a shared branch.
- Cite finding IDs in commit messages (`Fix F-03: boss occludes player on phone`).
- Do not rewrite unrelated history. Do not commit generated output unless the task says to keep it.
- Parallel agents work in separate branches or worktrees. See `ORCHESTRATION.md`.

## Roles

- **Implementer:** builds the task, runs the gates, updates state files.
- **Reviewer:** adversarial. Compares against the reference and the spec, hunts for failures, files findings with IDs. Does not fix.
- **Coordinator:** writes task specs, assigns work, merges reviewed changes, decides scope.

One agent can hold different roles on different tasks, but never implementer and reviewer on the same change.

## Resuming work

1. Read the files above.
2. Check `git status`, recent commits and the current diff.
3. Read `CURRENT_TASK`, `CHECKPOINT` and `HANDOFF`.
4. Resume the first unfinished atomic action.
5. Verify before declaring completion.
6. Update the checkpoint and handoff before stopping.
