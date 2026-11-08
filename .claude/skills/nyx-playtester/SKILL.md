---
name: nyx-playtester
description: "Plan or analyze playtests for NYX's Game Show. Use when testing a full match, match pacing, multiplayer flows, minigames, voting, ghosts, mobile usability, progression, or reporting reproducible gameplay issues."
---

# NYX Playtester

Help determine whether NYX's Game Show is understandable, fair, stable, and fun in actual play. Treat the GDD as a design reference, not proof that a feature works.

## Baseline

- Target match duration is 15–20 minutes, with about 18 minutes proposed.
- The full-scale target is 12–20 players. A six-player minimum is a proposed early-access/testing option, not a confirmed release requirement.
- The first playable release should complete a match with Rat Memory, Mazed, Laser Pointer, team voting, solo eliminations, a finale, ghosts, and a persistent winner reward.
- Eliminated players become interactive ghosts with cooldown-based, minigame-permitted pranks.
- The server controls match state, timers, teams, scoring, voting, rewards, and ghost permissions.
- Several mechanics remain open, including minimum player count, voting eligibility, exact scoring, tie handling, finale format, and Employee of the Month duration.

## Workflow

1. Clarify the playtest goal, build/version, player count, platform, and whether the test is live, automated, source-based, or based on supplied observations. Do not imply access to a Roblox session that is not available.
2. Choose a focused test matrix that covers the requested feature and its neighboring match states. Include expected outcome and observable evidence for each case.
3. Include representative paths: normal completion, failure/timeout, disconnect or join during play, team/solo context where applicable, and repeat-match behavior.
4. Observe player understanding as well as correctness: Were the rules, timer, team, score, danger, vote eligibility, and round transition clear without extra explanation?
5. Evaluate fairness and usability across players. When relevant, include mobile controls/readability and different network or participation conditions.
6. Record exact steps, initial conditions, expected result, actual result, and evidence. Separate reproducible defects from subjective feedback and unresolved design questions.
7. Rank actionable findings by player and match impact. Suggest the smallest next test or change that can confirm the cause.

## Core match coverage

When testing a full match, consider:

- Lobby/queue, match start threshold, team assignment, and late join behavior.
- State transitions, timers, round completion, and return to the lobby.
- Team contribution rewards, immunity purchase boundaries, voting eligibility, duplicate votes, ties, and elimination.
- Solo scoring reset and ranking; timeout and tie behavior.
- Transition from active contestant to ghost, spectator interaction, prank cooldowns/allowlists, and ghost departure.
- Finale, winner selection, award persistence, and successful start of a subsequent match.
- Disconnects at sensitive points and recovery when the active player count becomes too small.

Adapt this list to the tested scope; do not claim a case passed unless it was actually exercised.

## Reporting format

Provide:

1. **Test scope** — build, mode, player count/platform, and what was not available.
2. **Results** — pass, fail, blocked, or not tested for each case.
3. **Findings**, ordered by severity. Each finding includes steps to reproduce, expected vs. actual behavior, impact, evidence, and confidence.
4. **Player feedback** — direct observations and subjective impressions kept distinct.
5. **Open design questions** — identify GDD proposals and undecided rules without treating them as bugs.
6. **Next test** — the most useful follow-up validation.

Use Blocker / High / Medium / Low for issue severity. A design suggestion is not a defect unless it breaks an adopted requirement or causes a demonstrated player problem.

## Boundaries

- Do not fabricate playtest results, player quotes, timing measurements, or test-account behavior.
- Do not assume recommended mechanics in the GDD are final. Mark them as proposals or open decisions.
- Preserve the original creative direction and all five minigame concepts on the roadmap.
- Report server-authority concerns as player-impacting risks with concrete scenarios; do not rely on client-reported success as evidence of a valid result.
