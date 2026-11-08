---
name: nyx-minigame-reviewer
description: "Review or develop minigame designs and Luau implementations for NYX's Game Show. Use when checking Rat Memory, Mazed, Laser Pointer, future minigames, scoring, round lifecycle, fairness, or multiplayer edge cases."
---

# NYX Minigame Reviewer

Evaluate whether a minigame is fun, understandable, fair, and compatible with the authoritative match system in NYX's Game Show. Review the design and implementation that exist; distinguish defects from open design choices.

## Project requirements

- The first playable release includes Rat Memory, Mazed, and Laser Pointer, plus a complete match system.
- Sorting Game and Meow Tower remain in scope for the full game roadmap.
- A minigame must support the match's required team and/or solo mode, produce results that the server can trust, and return players and the arena to a clean state.
- The GDD proposes the lifecycle `Initialize(context)`, `Start()`, `HandleInput(player, payload)`, `Finish()`, and `Cleanup()`. The exact interface is not finalized; follow existing code rather than imposing a speculative abstraction.
- The server owns match state, timers, scoring, and outcomes. Client input is a request, never proof of score or eligibility.
- Ghost pranks are minigame-specific and must not force a loss, block progress, or alter official scoring.

## Workflow

1. Establish whether the request is a design review, implementation review, playtest analysis, or specification. Inspect relevant source, configuration, tests, and GDD sections.
2. Record which decisions are original/confirmed, which are proposed, and which remain open. Do not report a conflict with a proposed rule as a defect unless the project has adopted it.
3. Trace the full round: eligibility and setup, instructions, start signal, input window, scoring, timeout/tie handling, results, and cleanup.
4. Review the minigame's scoring and failure rules for determinism and fairness across team and solo modes. Check that score-affecting values are calculated or validated by the server.
5. Check multiplayer and edge cases: late/duplicate/malformed input, disconnects, too few players, timeout, ties, repeated events, collision or stun edge cases, and repeated rounds.
6. Check player experience: readable instructions, clear start/end states, reasonable pacing, feedback, and mobile-friendly controls and visibility.
7. Check interaction with match state and ghost permissions. Confirm effects and temporary objects are disabled or removed when a round ends.
8. Report only supported findings with evidence. If asked to implement, make the smallest complete change and run the narrowest relevant tests.

## Minigame-specific checks

- **Rat Memory:** same server-generated challenge for eligible players; five-second pattern reveal; bounded direction submissions; late and duplicate submissions rejected; deterministic scoring; difficulty increases as intended.
- **Mazed:** clear grace-period and ball-release cues; predictable ball behavior; brief stun without repeated stun-locking; reliable finish order and timeout result; arena exits and player state restored.
- **Laser Pointer:** target remains within visible bounds; captures and relocations are server-validated; scoring cannot be spoofed; timeout yields complete results; settings work on mobile and desktop.
- **Sorting Game / Meow Tower:** preserve their original concepts and identify missing rules as design questions, not implementation requirements invented by the reviewer.

## Review output

Start with a short verdict and scope. List findings from highest impact to lowest, each with:

- **Severity:** Blocker, High, Medium, or Low
- **Evidence:** file and line/symbol, or the exact observed design behavior
- **Impact:** what a player or match experiences
- **Reproduction or scenario**
- **Recommended fix or decision needed**
- **Confidence**

Then summarize coverage, unverified assumptions, and any open design decisions. If there are no findings, say what was reviewed and what was not verified; do not imply a live multiplayer test occurred when it did not.

## Boundaries

- Do not invent final scoring, tie rules, or the finale format where the GDD leaves them open. Offer clearly labeled options when requested.
- Do not overbuild a shared framework before the first minigame works end to end.
- Do not treat visual polish as a substitute for server-side validation or reliable cleanup.
