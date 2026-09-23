# Example: a design sweep across the portfolio

## The trigger

On 2026-09-19 the owner rejected the whole portfolio's look at once: every app shared the same template, "basically green and black for the most." Two days later, after the redesign, came a second verdict: "Your logic maybe fine, but the only thing you miss out on is the design... They feel 3d and real, they have depth, yours dont in the same way."

A per-app fix would have repeated the failure: each agent fixing its own app while reusing the same default look. It had to be a sweep with a shared rule and separate art direction for each app.

## The rules

These are written down in [agent-specific-notes/notes/design-rules.md](https://github.com/aeiouvcode/agent-specific-notes/blob/main/notes/design-rules.md):

- The dark-terminal, green-accent, boxes-inside-boxes skin is banned.
- Every project gets its own palette, type and composition, drawn from its subject.
- Minimal by default. Visual weight only on the parts that matter.
- Judge an accent against its background. A cobalt accent on warm paper was rejected ("Blue doesnt fit in good with the bg color") and replaced with a warm mineral rust.
- References the owner sends are the target, not a mood board.
- Component libraries (shadcn/ui, Magic UI and similar) are sources of interaction quality to reimplement natively, not components to paste in.
- For physical subjects, depth, lighting and optics are requirements.
- Checked at 390px before it counts.

## How it ran

- **Each app was its own task.** Its art direction came from its subject and its reference. No shared palette.
- **Review against the reference, not against the previous version.** Improving on the last build is not the bar. The bar is the artifact the owner sent. After the Stillwater water-sim argument ("this is nothing like the video") the owner made it a standing order for every project: a self-critique loop that keeps building until the result is "reasonably better than the x post one", with no increments sent for his grading.
- **Design inside the loop.** Design checks became gates for each milestone, alongside parse, run and security checks, not a polish pass at the end.

## The pattern

- When the owner rejects the portfolio, the problem is a shared default. Fix it with a rule that forces difference, then apply the rule per project.
- Write the rejection down in the owner's words. It becomes the acceptance test.
- Put the design gate in every milestone's definition of done so the next build does not slide back.
