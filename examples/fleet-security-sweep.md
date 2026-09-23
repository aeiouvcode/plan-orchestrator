# Example: a security sweep across the fleet

On 2026-09-18 the owner set new security rules for every project: local-first, no secrets anywhere, safe to run on localhost, and encrypted local data where apps store anything personal. About a dozen active repos had to adopt them without stopping their own work. The baseline that came out of it is in [research-archive/topics/local-first-security.md](https://github.com/aeiouvcode/research-archive/blob/main/topics/local-first-security.md).

## How it ran

1. **The rule as a checklist.** Strict CSP with `default-src 'none'` and `connect-src` limited to what the app really calls. No analytics, trackers or remote assets unless pinned with SRI or vendored. No `innerHTML` sinks fed by untrusted input. Secret scan of every tracked file. Outbound-capability scan (fetch, XHR, WebSocket, EventSource, sendBeacon). Localhost run instructions. AES-256-GCM with a PBKDF2-derived key for personal data.
2. **Inventory before work.** One pass checked which repos actually had an API-key field or a model call. Of the repos checked in that pass, only one (Afternoon) did. The others got the baseline but no invented key-handling work.
3. **Per repo, as its own milestone.** Each project applied the checklist on its own branch and ran its own gates. Examples from that day:
   - Clock Out vendored Three.js locally, set `connect-src 'none'`, and added a mandatory `prepush-secret-scan.sh` that refuses a push on a hit.
   - FrameForge got a localhost launcher bound only to 127.0.0.1 with security headers. It has no key field, so it needed no provider integration.
   - NoCatch and O Empire got hash-pinned CSPs with every fetch directive set to `'none'`, and script and style hashes recomputed against the exact inline bytes.
   - FrameForge's encryption work was scoped with an explicit rule: do not claim encryption until ciphertext at rest, wrong-passphrase rejection, reload and unlock, export/import and media recovery all pass.
4. **Blocked is not done.** The browser budget ran out mid-sweep. Builds that passed static checks were staged and their deploys scheduled for when the budget reset. None of them were reported as live before the live check ran.
5. **Per-repo report** of pass or not applicable, with scan results.

## What it cost later

A strict CSP on one app blocked its own model downloads because the model host redirects to a CDN origin the policy did not allow. The fix and the rule ("exercise every network path after writing a CSP") are in [agent-specific-notes/notes/security.md](https://github.com/aeiouvcode/agent-specific-notes/blob/main/notes/security.md).

## The pattern

- Rule once, checklist form, pass/fail items.
- Inventory first, so no one invents work where the rule does not apply.
- Each repo is its own task with its own gates.
- Add the checklist to each repo's definition of done so new work keeps it.
- Log the breakage the rule caused as a failure note, not just the rule.
