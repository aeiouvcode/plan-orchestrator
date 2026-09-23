# Example: ISSEN, a long build run as milestones

[ISSEN](https://github.com/aeiouvcode/issen) is a browser sword-duel game in a sumi-e ink style. The brief (2026-09-17) was a reference video of a production Unity game: build something similar, with a similar art style, without Unity, and make it work across devices. It went through more than 60 milestones between 2026-09-17 and 2026-09-23. Research notes: [research-archive/projects/issen.md](https://github.com/aeiouvcode/research-archive/blob/main/projects/issen.md).

## What went wrong first

The early builds were written from a description of the reference, not from the reference. The owner's verdict: "they are not even close in terms of the way these games function, these are totally different worlds." The first 2D and 3D versions were also lost when their temporary hosts expired.

What changed:

- **The reference became the spec.** The game was rebuilt from the video itself: follow camera, soft watercolor wash, no hard toon outlines, parchment ground, ink figures, slash arcs, dodge afterimages. Each milestone was judged side by side against it.
- **Permanent hosting before anything else.** The rebuild went straight onto the account's own GitHub Pages, so no milestone could disappear again.

## The cadence

- One milestone at a time, each with one goal from the latest gap list: camera, ground, figures, HUD, touch controls, occlusion.
- Each milestone: build, compare against the reference at desktop and at 390px, fix the biggest gaps, compare again, deploy, and check that the live file matches the commit. Report only then.
- Milestones ran roughly hourly while the build was active. The owner saw results, not every attempt.
- Owner feedback went into the next milestone as findings, in his words: "way more animations, assets, rendering engine in the x version... this need further improvement in design, pacing and performance", and "Make it functioning for multiplatform devices".

## What the gates caught

- The multiplatform pass (real touch, gamepad, small screens) found HUD overlap and stale gamepad input that desktop testing never showed.
- Audits at 390px caught later problems: uniform ground speckle, flat grass tufts, a pale bamboo horizon, and the boss standing between the camera and the player.
- The occlusion fix was a design decision recorded in research-archive: the camera slides and lifts slightly and the boss fades to translucent ink. Moving the camera alone could not keep both fighters readable.

## What it would look like under this repo's method

- `TASK_SPEC.md` per milestone, with the reference video as the acceptance bar and non-goals (no engine install, no image assets).
- `REVIEW.md` from a separate reviewer pass, with findings like `F-12 boss occludes player at 390px (high)`.
- `HANDOFF.md` listing failed approaches, for example that a pure camera move could not solve occlusion.
- The gap to name honestly at the end: hand-animated brushwork silhouettes in the reference that runtime ink shaders have not matched.
