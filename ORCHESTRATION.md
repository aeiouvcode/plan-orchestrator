# Running agents in parallel

Adding agents makes a project faster only if each one works on isolated state and every change still passes the same gates. Without that, parallel agents overwrite each other, grade their own work, and ship faster in a worse state. This guide covers the rules that prevent that.

## 1. Isolate the work

- **One writer per branch.** Each agent gets its own branch or git worktree (`git worktree add ../proj-task-12 -b task-12`). Nobody commits straight to `main`.
- **One task per agent.** A task spec (`templates/TASK_SPEC.md`) defines what the agent may touch. If two tasks touch the same files, run them one after the other, not side by side.
- **Commit on the current head.** When committing through an API, create the commit on the head you just read, then update the ref without force. A conflicting update should fail loudly. Re-read and rebase. Never overwrite.
- **Separate projects need no coordination.** Separate repos can run fully in parallel. Coordination only matters inside one repo or when a rule applies to all of them (see sweeps below).

## 2. Split building from judging

The builder is the worst judge of the build. Friendly self-grades are the most common way quality slips when many agents run at once.

- **Implementer** builds against the spec and runs the gates.
- **Reviewer** gets the spec, the reference artifact and the diff. Its job is to find problems: side-by-side against the reference, edge cases, phone width, security checks. It files findings in `templates/REVIEW.md`. It does not fix anything.
- **Implementer** fixes, citing finding IDs in commits. **Reviewer** re-checks and closes or reopens each finding.
- The same agent can review one task and implement another. It never does both on one change.

If only one agent is available, run the review as a separate pass with a fresh read of the spec and reference, and write the findings down before fixing any of them.

## 3. Finding IDs

- Every finding gets an ID (`F-01`, `F-02`...), a severity (high / medium / low), and a status (open / fixed in `<commit>` / won't fix, with a reason).
- IDs are stable. A reopened finding keeps its ID.
- Commits and handoffs cite IDs. Anyone can trace a change back to the problem it fixes, and see what is still open.
- High findings block the milestone. Medium findings block unless the coordinator defers them in writing. Low findings go to the backlog.

## 4. Gates and rollback

- Every milestone has hard gates: it parses, runs in the real target, passes phone-width checks and security checks, the live deploy matches the commit, and it has been compared against the reference.
- **Pass:** accept, update the checkpoint, report.
- **Fail:** revert to the last accepted state, record the failed approach and why in the handoff, and report the failure plainly. Do not patch forward on an unverified base and do not report a feature that did not pass.
- If a gate cannot be run (a tool is down, a budget is exhausted), the milestone is blocked, not done. Queue the verification and resume it when the tool comes back.

## 5. Cadence for long builds

- Small milestones (M1, M2...), each with one clear goal from the spec or the latest review.
- Each milestone goes through a loop: build, compare against the reference, fix the biggest gaps, compare again. It reports only when the result can be defended.
- The owner sets the bar and gets results. Do not send every increment for grading. That turns the owner into the review loop.
- Owner feedback becomes findings in the next milestone's spec, in the owner's words.

## 6. Sweeps: one rule across many repos

When a new rule applies to the whole portfolio (a security baseline, a design rule):

1. Write the rule once as a checklist with pass/fail items.
2. Take an inventory first: which repos the rule actually applies to. Do not invent work where it does not fit.
3. Apply it per repo, as its own task, on its own branch. Each repo runs its own gates.
4. Report per repo: pass, fail, or not applicable, with evidence.
5. Add the rule to the definition of done in each repo's `AGENTS.md`, so it keeps applying to new work.
6. If the sweep came from a failure, log it in [agent-specific-notes](https://github.com/aeiouvcode/agent-specific-notes).

## 7. The coordinator's job

- Write task specs with acceptance criteria and non-goals before work starts.
- Keep one current task per agent and make sure the handoffs stay current.
- Merge only reviewed changes whose high findings are closed.
- Decide scope. Implementers suggest extra features; they do not build them.
- Watch for shared limits (API rate limits, browser or deploy budgets) and schedule around them, so agents are not all blocked at once.

## Failure modes this prevents

| Failure | Rule that prevents it |
| --- | --- |
| Two agents overwrite each other | One writer per branch, commit on current head, no force |
| Work redone after a restart | Checkpoint file, resume from repo state |
| Rejected approach tried again | Failed approaches logged in the handoff |
| Friendly self-grades | Separate reviewer, findings written before fixes |
| Feature reported but never worked | Hard gates, rollback on failure |
| Scope creep across the fleet | Non-goals in every spec, coordinator owns scope |
| One rule applied unevenly | Sweep checklist, per-repo report, rule added to definition of done |
