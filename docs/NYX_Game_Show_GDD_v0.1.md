# NYX's Game Show — Game Design Document (GDD)

**Version:** 0.1 — Development planning draft  
**Original concept:** Y.K Core  
**Platform:** Roblox  
**Language:** Luau  
**Date:** November 7, 2026  
**Status:** Proposed mechanics require playtesting; original creative direction preserved.

> **Design provenance:** The original 15-page *NYX Game Show* concept is the creative foundation. Sections marked **Original** reflect that document; **Proposed** means a new clarification or implementation choice. The original document is not superseded by this draft.

## 1. Vision and premise

**Original:** Nyx is the oversized-headed, adorable cat of Mr. and Mrs. Bell, who run a café with 50 cats. After the café closes at 8 p.m. and the owners go upstairs, Nyx puts on his tall hat and starts his game show. His personality is described as crazy / Mad Hatter-like. Employees compete to survive and become **Employee of the Month**.

**Design direction:** A multiplayer, creepy-cute game-show party experience combining teamwork, puzzles, reflex challenges, social betrayal, and light horror. Nyx hosts every stage, announces rules, reacts to performance, and conducts theatrical eliminations.

### Design pillars
1. **Nyx is the host, not just a mascot.** Dialogue, rules, ceremonies, and atmosphere should reflect his personality.
2. **Cooperation becomes competition.** Begin in violet and blue teams; finish with solo competition.
3. **Treats create meaningful choices.** Match resources should reward performance and support immunity and tactical advantages.
4. **Elimination is part of the show.** Players who lose can continue participating as mischievous ghosts.
5. **The café gives players a reason to return.** Employee ranks, recognition, and cosmetic progression persist across matches.

## 2. Confirmed planning decisions

- **Target match duration:** 15–20 minutes (aim for ~18 minutes).
- **Elimination:** Team-stage voting, followed by solo-stage performance elimination.
- **Spectators:** Interactive ghosts with prank cooldowns.
- **First playable release:** Three original minigames **and a complete match system**.
- **Creative scope:** Preserve the lore, core systems, and **all original minigame concepts** on the roadmap; do not replace the original vision simply to chase trends.

## 3. Match gameplay loop

### 3.1 High-level loop

```text
Café lobby / queue
    ↓
Nyx's introduction and team assignment
    ↓
Team minigame → treats → immunity window → team vote
    ↓
Second team minigame → treats → immunity window → team vote
    ↓
Solo minigame → score-based elimination
    ↓
Second solo minigame → score-based elimination
    ↓
Final head-to-head challenge
    ↓
Employee of the Month ceremony → rewards → replay
```

Eliminated players enter the ghost spectator role rather than becoming idle.

### 3.2 Proposed 12-player bracket

| Phase | Active players at start | Outcome | Time budget |
|---|---:|---|---:|
| Opening and team assignment | 12 | Violet and blue teams, six each | 1 min |
| Team Round 1 + vote | 12 | One elimination per team → 10 | ~3.5 min |
| Team Round 2 + vote | 10 | One elimination per team → 8 | ~3.5 min |
| Solo Round 1 | 8 | Lowest three eliminated → 5 | ~3.5 min |
| Solo Round 2 | 5 | Lowest three eliminated → 2 | ~3.5 min |
| Final showdown | 2 | One winner | 2 min |
| Results | 1 winner | Awards, progression, return to lobby | 1 min |
| **Total** | | | **~18 min** |

**Proposed, not original:** The precise number eliminated in each round and timing budget. Match duration includes transitions, voting, and scoring; adjust after actual multiplayer tests. For small early-access populations, consider a six-player minimum with a scaled bracket. The original concept requests **at least 12 players, ideally 20**; this remains the full-scale target rather than an MVP hard requirement.

### 3.3 Match states

`LOBBY → INTRO → TEAM_ROUND → TEAM_REWARDS → IMMUNITY → TEAM_VOTE → TEAM_ELIMINATION → [repeat team round] → SOLO_ROUND → SOLO_ELIMINATION → [repeat solo round] → FINALE → RESULTS → LOBBY`

Each state must have an explicit start condition, timer, completion condition, and disconnect/failure behavior. The server alone authorizes state changes.

## 4. Treats, scoring, and elimination

### 4.1 Original systems

- Winning teams receive treats; the top three contributors earn additional treats.
- Team-stage treats may purchase immunity, temporary hints, or boosts.
- Both teams vote to eliminate a contestant; the selected contestant is executed/eliminated as part of Nyx's show.
- In the solo stage, players earn treats from winning challenges; the lowest earners are eliminated, with tiebreaker challenges suggested.
- The original document suggests baseline rewards to all contributing winning-team members.

### 4.2 Proposed economy separation

| Resource | Used for | Lifetime |
|---|---|---|
| **Team Treats** | Immunity, hints, temporary boosts | Match/team phase |
| **Solo Score** | Ranking solo performance and survival | Each solo round |
| **Café Coins** | Cosmetics and café progression | Persistent |

**Rationale:** Spending survival currency during team play should not accidentally weaken a contestant's later solo ranking. The original calls both team and solo rewards “treats”; this draft separates their internal accounting for clarity. Final naming is open.

### 4.3 Proposed team voting rules

1. Each active contestant casts one vote against an eligible contestant **on their own team**.
2. Immunity purchases close before ballots open; immune contestants cannot be selected.
3. One contestant from each team is eliminated at each voting ceremony.
4. Ties trigger a short sudden-death challenge or deterministic fallback if a challenge cannot start.
5. Votes and purchases are validated by the server; a player cannot vote twice or spend a negative balance.
6. Disconnects during voting must not stall the match; use a timer and eligible-voter snapshot.

**Open:** The original says both teams vote for one player but does not conclusively establish whether votes are within teams or cross-team. Same-team voting is a recommendation, not an original rule.

### 4.4 Proposed solo scoring

`RoundScore = PerformancePoints + CompletionBonus`

- Define scoring independently for each minigame.
- Reset round scores at the start of each solo round, to keep later rounds competitive.
- Eliminate the lowest-scoring players according to the active bracket.
- Resolve ties using a short tiebreaker; never choose a winner by client-reported score alone.
- A dedicated final challenge can be added later; MVP may use a harder variation of an existing minigame.

## 5. Minigame specifications

### 5.1 Rat Memory — first implementation

**Original:** Rats face left, right, up, or down. Players view a pattern for five seconds and reproduce it. More rats appear as rounds progress; the concept describes 10 rounds, with six rats by round two, eight by round five, and ten by round eight. The original reward example is 50 treats for first, 25 for second, and 15 for positions three through ten.

**Proposed implementation:**
- Server generates a sequence of directions and sends the same challenge to eligible contestants.
- Display pattern for five seconds; hide it and accept direction choices.
- Score correct directions; optionally award a speed bonus for fully correct submissions.
- Allow both team aggregate scoring and individual scoring.
- Prevent late submissions and reject malformed or duplicate answers.

**Acceptance:** A round starts, players submit, the server scores deterministically, results display, and the minigame returns control to the match manager.

### 5.2 Mazed — second implementation

**Original:** Players enter a maze; Nyx gives approximately ten seconds of grace before pushing a ball into it. A player caught by the ball is briefly stunned, letting others catch up.

**Proposed implementation:**
- Spawn contestants at a common start point and show a countdown.
- Release the rolling ball after the grace period.
- Apply a short server-controlled stun on valid contact; prevent repeated stun-locking.
- Rank finishers by arrival order, with a defined outcome for players who time out.
- Support team results and solo placement.

**Acceptance:** The ball moves predictably enough to play around, stuns work, finish detection is reliable, and all players exit the arena at round end.

### 5.3 Laser Pointer — third implementation

**Original:** A red dot moves randomly across the ground and changes direction frequently. The document does **not** finalize scoring or failure rules.

**Proposed implementation:**
- Move the target within clearly marked arena bounds.
- Players score by reaching the active dot before it relocates.
- Add a short lockout after each successful capture to prevent repeated accidental scores.
- Tune speed, target lifetime, and spawn placement for both mobile and desktop.
- Team mode sums eligible player scores; solo mode ranks individuals.

**Acceptance:** Players can see and reach the target, scores cannot be spoofed, and the game ends on a timer.

### 5.4 Remaining original minigames — preserved for later

- **Sorting Game (page 14):** Early sketch marked as a solo game; rules and scoring still need definition.
- **Meow Tower (page 15):** Team/solo obstacle tower sketch featuring elements such as ladders, trampoline, portals, and hazards; needs a full level specification and playtest.

**Important:** All five concepts remain in scope for the full game. Only the first three are required for the initial playable release.

## 6. Ghost spectators

**Original:** Eliminated contestants become spectators, and the sabotage menu may be available to them.

**Confirmed addition:** Spectators become ghosts with interactive pranks and cooldowns; they are chaotic troublemakers, not a separate objectives-based role.

| Proposed prank | Effect | Initial cooldown |
|---|---|---:|
| Flicker | Briefly flicker nearby lights | 30 sec |
| Fake Footsteps | Play misleading footsteps | 25 sec |
| Door Trick | Open/close a permitted door briefly | 45 sec |
| Nyx's Laugh | Play an unsettling laugh near active players | 40 sec |

**Fairness rules:**
- Server checks ghost status, cooldowns, prank availability, and the current minigame.
- Pranks must not force a loss, permanently block progress, or change official scoring.
- Minigames specify allowed pranks; e.g., door tricks may work in Mazed but should not alter Rat Memory answers.
- Ghost interaction is free gameplay; paid options should be cosmetic rather than guaranteed wins or eliminations.

## 7. Lobby, lore, and presentation

- **Original:** The café is run by Mr. and Mrs. Bell, houses 50 cats, and closes at 8 p.m. Nyx secretly runs the show at night.
- **Original:** Nyx has an eccentric Mad Hatter-like personality and an oversized head.
- **Proposed:** The lobby is the café after hours; Nyx introduces rounds, narrates failures, handles votes, and celebrates the winner.
- **Proposed:** Eliminations use theatrical effects appropriate for a Roblox audience rather than graphic violence.
- **Proposed:** Prioritize voice/text timing, lighting, audio, and expressive animations before elaborate custom assets.

## 8. Persistent progression and recognition

**Original:** The winner becomes Employee of the Month and receives café-wall recognition and an employee name tag. The source contains inconsistent durations (a day on page 5 versus one game on page 10); **duration remains undecided**.

**Original employee ranks:**

| Rank | Wins required in original draft |
|---|---:|
| Pooper Scooper | 0 |
| Cat Care Assistant | 1 |
| Café Assistant / Runner | 3–5 |
| Cat Specialist | 10 |
| Barista | 15 |
| Barista Lead | 20 |
| Shift Supervisor | 25–30 |
| Assistant Manager | 50 |
| General Manager | 100 |
| Manager | 150 |
| CEO | 300 |

**Proposed:** Persist wins, rank, cosmetics, and café currency with Roblox DataStoreService. Confirm exact promotion thresholds and Employee of the Month display duration during balancing.

## 9. Monetization roadmap

**Original concepts:** Mystery crates, accessories, aprons, seasonal items, perks, and a sabotage menu including freeze, trip, visual gags, jumpscares, instant elimination, and automatic victory. The original also suggests rewards through wins, daily challenges, login bonuses, and achievements.

**Proposed release policy:**
- Start with cosmetic accessories, outfits, name-tag customization, and noncompetitive ghost effects.
- Do not sell automatic wins or instant eliminations in competitive matches.
- Treat paid gameplay boosts and random paid crates as **unapproved** until fairness, player experience, and current Roblox platform requirements have been reviewed.
- Measure retention and satisfaction before optimizing monetization; revenue is possible, not guaranteed.

## 10. Technical architecture

**Core rule:** The Roblox server is authoritative for match state, timers, teams, scoring, votes, inventory changes, rewards, and ghost permissions. Clients render the HUD and submit player actions; they do not decide outcomes.

### Proposed Luau structure

```text
src/
├── server/
│   ├── MatchService.luau
│   ├── PlayerStateService.luau
│   ├── TeamService.luau
│   ├── VotingService.luau
│   ├── TreatService.luau
│   ├── GhostService.luau
│   ├── ProgressionService.luau
│   └── Minigames/
│       ├── RatMemory.luau
│       ├── Mazed.luau
│       └── LaserPointer.luau
├── client/
│   ├── HUDController.luau
│   ├── VotingController.luau
│   ├── GhostController.luau
│   └── NyxDialogueController.luau
└── shared/
    ├── GameConfig.luau
    ├── GameTypes.luau
    └── MinigameRegistry.luau
```

### Minigame contract (proposed)

Every minigame must provide a consistent lifecycle:

1. `Initialize(context)` — prepare arena, players, and rules.
2. `Start()` — begin timer and enable inputs.
3. `HandleInput(player, payload)` — validate permitted player actions.
4. `Finish()` — lock input, calculate authoritative results.
5. `Cleanup()` — remove temporary objects and reset player state.

Define the precise module interface when implementing the first minigame. Do not overbuild an abstraction before Rat Memory works end to end.

### Data model (initial)

- **PlayerProfile:** user ID, total wins, rank, café currency, owned cosmetics.
- **Match:** ID, state, timer, active player IDs, teams, current minigame, winner.
- **MatchPlayer:** state (`ACTIVE`, `GHOST`, `DISCONNECTED`), team, team treats, solo score, immunity.
- **Vote:** voter ID, target ID, ceremony ID; server-validated and one per voter.
- **GhostCooldown:** ghost ID, prank ID, next eligible time.
- **MinigameResult:** round ID, player ID, score, finish status, placement.

### Edge cases to test

- Players joining during an active match (spectate or wait until next match).
- Player disconnects during team assignment, voting, or the finale.
- A team has no eligible nonimmune candidate.
- Vote ties and solo-score ties.
- Minimum player count falls below the safe threshold.
- Duplicate network events, client-reported impossible scores, and repeated purchases.
- Minigame timeout or cleanup failure.
- Ghost pranks triggering after the ghost has left or after a round ends.

## 11. Development phases and backlog

Use each checkbox as a candidate GitHub Issue. Suggested labels: `foundation`, `gameplay`, `presentation`, `progression`, `testing`, `release`.

### Phase 0 — Design and setup

- [ ] Preserve original 15-page concept document in project documentation.
- [ ] Confirm minimum players, voting eligibility, final-round format, and tie rules.
- [ ] Define exact scoring for Rat Memory, Mazed, and Laser Pointer.
- [ ] Define game-state transition table and disconnect behavior.
- [ ] Set up Roblox Studio project, source control, and Luau project structure.
- [ ] Create basic multiplayer test environment with placeholder assets.

### Phase 1 — Match foundation

- [ ] Implement server-authoritative match state machine and timers.
- [ ] Implement lobby queue, join-in-progress behavior, and match start.
- [ ] Implement violet/blue team assignment.
- [ ] Implement player state transitions (`ACTIVE`, `GHOST`, `DISCONNECTED`).
- [ ] Implement common minigame lifecycle and result reporting.
- [ ] Implement baseline HUD for timer, team, treats, round, and status.

### Phase 2 — Three minigames

- [ ] Implement Rat Memory pattern generation and five-second reveal.
- [ ] Implement Rat Memory input, scoring, and difficulty progression.
- [ ] Implement Mazed arena, ten-second grace period, and rolling ball.
- [ ] Implement Mazed collision, stun protection, finish order, and timeout.
- [ ] Implement Laser Pointer arena and moving target.
- [ ] Implement Laser Pointer scoring, target relocation, and round timeout.
- [ ] Verify each minigame supports required team/solo modes.

### Phase 3 — Competition and ghosts

- [ ] Implement team treat awards and contribution bonuses.
- [ ] Implement immunity purchases and eligibility checks.
- [ ] Implement voting UI, server vote validation, and ceremony timer.
- [ ] Implement tie handling and team-stage eliminations.
- [ ] Implement solo scoring, ranking, and elimination.
- [ ] Implement final head-to-head challenge and winner selection.
- [ ] Implement ghost spectator camera/movement rules.
- [ ] Implement allowed ghost pranks and server-enforced cooldowns.

### Phase 4 — Presentation and persistence

- [ ] Build placeholder café lobby and minigame staging areas.
- [ ] Implement Nyx dialogue for introductions, transitions, and results.
- [ ] Implement theatrical but non-graphic elimination presentation.
- [ ] Implement winner ceremony and Employee of the Month display.
- [ ] Persist wins, ranks, and café currency with DataStoreService.
- [ ] Add employee name tags and first-pass progression UI.

### Phase 5 — Playtest and release readiness

- [ ] Test a full 12-player match, including eliminations and finale.
- [ ] Test smaller-player bracket and disconnect recovery.
- [ ] Confirm normal matches finish in 15–20 minutes.
- [ ] Test mobile controls and UI readability.
- [ ] Validate all remote actions server-side.
- [ ] Test repeated matches without restarting the server.
- [ ] Gather playtester feedback on pacing, fairness, Nyx's personality, and ghost fun.
- [ ] Review Roblox policies before enabling monetization or paid random rewards.

### Post-MVP content roadmap

- [ ] Fully specify and implement original Sorting Game.
- [ ] Fully specify and implement original Meow Tower.
- [ ] Expand café environment and custom Nyx assets/animations.
- [ ] Expand cosmetics, seasonal content, and employee progression.
- [ ] Add daily challenges, achievements, and optional noncompetitive shop systems.
- [ ] Introduce analytics for match completion, player retention, and purchase conversion.
- [ ] Develop promotional clips and launch marketing plan.

## 12. First playable release — definition of done

The initial release is complete when:

- [ ] Players can join, be assigned teams, and complete an entire match without manual developer intervention.
- [ ] Rat Memory, Mazed, and Laser Pointer are fully playable and scored by the server.
- [ ] Team voting, immunity, solo eliminations, and a final winner all work.
- [ ] Eliminated players can spectate and use permitted cooldown-based pranks.
- [ ] A winner receives a persistent progression reward.
- [ ] Match duration is normally 15–20 minutes, measured in multiplayer playtests.
- [ ] A subsequent match starts successfully without a server restart.
- [ ] Core gameplay remains understandable and fun with placeholder artwork.

## 13. Open design decisions

| Question | Current recommendation | Status |
|---|---|---|
| Minimum players | Support 6 in testing; aim for 12–20 at scale | Open |
| Voting targets | Same-team contestants only | Open |
| Team vote result | One eliminated per team per ceremony | Open |
| Team immunity | Limited purchases, expires after ceremony | Open |
| Solo scores | Reset for each solo round | Open |
| Finale | Harder variation of existing minigame for MVP | Open |
| Employee of the Month duration | Decide between original one-game / one-day variants | Open |
| Original rank threshold ranges | Set exact values after balancing | Open |
| Ghost prank availability | Minigame-specific allowlist | Open |
| Monetization | Cosmetic-first; competitive paid boosts deferred | Open |

## 14. Immediate development order

1. **Start with the match controller:** states, timers, teams, player status, and round transitions using placeholder events.
2. **Build Rat Memory end to end:** one working challenge, authoritative score, and clean return to the match controller.
3. **Add voting and elimination:** validate the transition from active contestant to ghost.
4. **Build Mazed and Laser Pointer:** integrate them through the same minigame interface.
5. **Wire the full bracket and finale:** play an entire match with test accounts.
6. **Polish Nyx's presence:** dialogue, audio, timing, ceremony, and café environment.

**Guiding principle:** The mechanics make the show playable. Nyx makes it memorable.
