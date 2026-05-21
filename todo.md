# Rungeon Dunner — Roadmap

Living list of ideas and improvements. Check items off as they ship.

## 0. Bugs

- [x] **Iron Constitution doesn't stack / persist** — should be buyable repeatedly and the max-HP gains should carry across floors, not reset after floor 1
- [x] **Health rune (store) appears to do nothing** — effect isn't wired up to the stat path
- [x] **Portals can spawn in the single-cell corridor leading into the last room**, leaving tiles permanently unreachable (player would have to walk across the portal cell to access them)
- [x] **Strength Rune had the same cap bug as Iron Constitution** — fixed implicitly by removing the per-floor auto-grow (see design note below)
- [ ] **Watch enemy balance** — automatic per-floor +2 HP / +1 ATK was removed (design call: progression should be intentional). Enemies still scale per floor, so the player may now feel underpowered in mid-to-late runs. Revisit if it plays badly.

Iron Constitution and Vitality Rune had a shared root cause: `nextFloor()`
was running `player.maxhp = Math.min(player.maxhp + 2, 40)` on every floor
transition, clamping max HP back down to 40 and erasing any meta or
in-run bonuses. The same `Math.min(player.atk + 1, 15)` clamp hit the
Strength Rune. Both auto-grow lines were removed entirely — max HP and
ATK now only grow through explicit player choices (Iron Constitution,
runes, elixirs, etc.), so every progression beat feels earned. Portal
bug: exit corners are now filtered to exclude any cell where a corridor
enters the room from outside; the player can no longer be forced to
step on the portal to access the rest of the room. The small +5 HP
between-floor heal was kept as a breather — easy to remove later if it
feels too generous.

## 1. Game feel & dopamine (the through-line)

- [ ] Maximize stimulation feedback (VFX, screen shake, sound, particles, etc.)
- [x] **Post-floor stats screen** (kills, gold, time, damage dealt/taken) + time-based bonus
- [x] **One-tap continue** between floors
- [x] **Store purchase feedback** — animated stat-number tick-up when a stat increases, plus an "upgrade" sound effect
- [x] **Better melee sounds** — hit/crit SFX now layer with the melee streak: each successive hit adds sub-bass, mid harmonic, and sparkle tiers, so the audio escalates alongside the streak bonus (dopamaxxing the chain)
- [x] **Better throwing-knife sound + visualization** — daggers now visually fly from the player to the target (rotating to face direction, spinning during flight) with a new aerodynamic whoosh on release and a sharp thwack on impact
- [x] **Floor-cleared chime** — small triumphant tone when the last monster on a floor dies (skipped on boss kills since bossVictory already plays)
- [x] **Boss-floor stats screen variant** — post-floor stats modal now switches to gold/yellow accents with a "Boss Defeated · Wing N" header when the cleared floor was a boss floor
- [x] **Death-screen replay polish** — small crystalline "ding" plays as each completed floor pops into the stacked pullback view (pitch climbs slightly per step)
- [x] **Death-screen replay timing** — reveal cadence now ramps DOWN: 210ms → ×1.22 per step (capped at 720ms) so first few snaps in fast, later ones settle into a reading pace
- [x] **Death-screen replay layout** — per-floor rotation dropped, all layers stack at the same scale so portals align pixel-perfect at the center; depth is now expressed only via opacity
- [ ] **Portal transition around stats screen** — wrap the post-floor stats modal in a swirling purple portal effect (rotating concentric rings / glow / wisps) so the player feels like they're standing *inside* the portal while reading their stats. Also do a quick fade-out of the previous floor into that portal view, and a quick fade from the portal into the next floor as they continue. Fast but recognizable beats — sells the "you stepped through it" fantasy.
- [ ] **More legible cursor** — current custom cursor renders dark against the mostly-black UI and is often hard to spot; needs a higher-contrast or outlined version
- [ ] **Powerful blast SFX** — the BLAST attack currently plays the death/game-over sound; needs its own dedicated, weighty explosion sound

The post-floor stats screen tracks kills, gold, attacks, throws, spells,
damage dealt, and damage taken. Speed bonus tiers: LIGHTNING DUNNER (<45s),
SWIFT (<90s), ON TIME (<150s), STEADY (slower). Bonus scales with floor.
Modal dismisses on any key or tap.

The stats screen is the single highest-ROI dopamine win in this list — short
build, hits the variable-reward loop hard, and the speed bonus subtly enforces
the "Rungeon" brand promise. Should be near the top of the queue. The general
"more juice" goal isn't really a task — it's a lens we apply to every other
change. Worth defining 2-3 concrete juice rules (e.g. every kill = screen shake
+ flash + sound, every level-up = freeze-frame + zoom) so we don't have to
debate it case by case.

## 2. Art / asset overhaul

- [ ] Custom player + enemy + boss sprites
- [ ] Custom weapon graphics
- [ ] Custom map textures (walls, floors, traps, shops) — per-floor palette preserved
- [ ] Castle aesthetic for boss / mega-boss floors

Working in a repo makes this dramatically easier — we can drop a `/sprites` and
`/textures` folder, reference image files instead of emoji glyphs, and version
them. This is the biggest "feels like a real game" lever AND the longest pole.
Do it in vertical slices (one floor's full art pack at a time) rather than
swapping all emoji enemies at once, so we always have a playable state. Castle
floors are a natural showcase to start the art experiment on — high visual
contrast for low scope.

## 3. Spell system rework

- [ ] Upgraded spells as **books** dropped by late bosses (vs scrolls for new spells)
- [ ] **5 spell slots + 1 blast slot** → loadout-driven builds
- [ ] Spells found on map *and/or* locked inside castle structures
- [ ] **Player map manipulation** spells (tunnel, explosion clears nearest-neighbor blocks)

The slot constraint is what makes the system replayable — but only matters once
we have meaningfully more than 6 spells. Implementation order is probably:
1) add the slot system (even with current spells), 2) build out more spells /
upgrade tiers, 3) gate them behind drops and castle exploration. Map-manipulation
spells are exciting but a big design risk — they break level generator
assumptions (sealed rooms, hazard layouts). Worth prototyping early with the
smallest possible version (e.g. single tunnel spell, 3-block range, only carves
non-structural walls) to feel out the implications before committing.

## 4. Melee / combo

- [ ] Upgraded melee weapons that hit **multiple adjacent cells** as combo heats up
- [ ] **Weapon pickup cards** (same pattern as new-spell cards) — show weapon art + quick stats, tappable through so repeat players aren't bogged down
- [ ] **Triumphant sound** plays on new weapon pickup

Small, self-contained, and the kind of mechanic that's instantly satisfying once
it triggers. Pairs beautifully with juice (the moment a combo ticks into AoE
range should be a *moment*). Good mid-priority pick. The weapon-pickup card +
sound creates parallelism with the spell-pickup beat and makes weapons feel
like first-class loot instead of silent stat boosts.

## 5. Level pacing & structure

- [x] **Randomize floor lengths** — themes now group into blocks of 3–5 floors (2–4 normal + 1 boss at the end), so music gets breathing room and boss floors close out a theme
- [ ] **Larger maps** the deeper you go
- [ ] New **mega-boss tier** introduced ~3rd boss stage
- [ ] **Mega-boss tactical design** — current bosses are "harder basic mobs" and that's fine for them, the player can blast + knife them down even at higher floors. Mega-bosses should be a different category: smart enough (in their own behavior, and in what they force the player to do) that the player has to use their full spell kit creatively and tactically to win. Both sides of the fight need more brains. Vague but the direction is "puzzle-fight, not bigger HP bar."
- [ ] Mega-boss floors on bigger maps with castle aesthetics
- [ ] *Revisit later:* tune the theme-block range (currently 3–5). 2–4 or 3–6 are easy alternatives if pacing feels off after more content lands.
- [ ] *Revisit later:* consider surfacing block progress in the UI ("Wing 2 of 4 · Catacombs") — keeps the boss arrival a surprise vs. lets the player anticipate the climax. Worth a separate playtest pass.

Larger maps deeper down is conceptually simple but interacts with everything
(camera, spawn density, energy/heal economy, run length) — needs a quick
playtest pass after we ship it. Mega-bosses depend on having a real visual
upgrade in place, so they probably want to come after the art slice for
castles lands.

## 6. Monster capture (raise spell rework)

- [ ] **Capture, don't summon** — weaken target below HP threshold
- [ ] Spell tier gates which mobs are capturable (low-level bosses require high raise tier)
- [ ] **Persistent companion** that gains stats across battles
- [ ] Each kill scored by the companion boosts it

The most ambitious cluster — essentially a whole sub-system (capture UX,
companion AI, persistence, stat curves, balance against player power). Tons of
identity-defining potential though. Scope a "minimum lovable version" first: one
capturable enemy type, one persistent companion that levels up, no
roster/swapping yet. Get the feel right, then expand. Don't try to do it the
same sprint as anything else big.

## 7. Meta / outside the core loop

- [ ] "More to do outside of the core game" — needs fleshing out

The most vague item. Possibilities to consider: a town hub between runs,
unlockable cosmetics, a bestiary that fills in as you encounter enemies, daily
challenges, an expanded Codex with lore unlocks. Each is a very different
scope — revisit when ideas firm up.

---

## Suggested shipping order

1. **Post-floor stats + speed bonus + one-tap continue** — fastest dopamine hit, defines the game's identity as "Rungeon"
2. **Floor-length randomization** — one-day change, fixes the music pacing complaint
3. **Spell slot system** (5+1) with current spells — sets up everything else
4. **Juice pass** — codify the VFX/audio rules and apply them across existing events
5. **First art vertical slice** — one floor (probably castle for visual punch) gets full custom sprites/textures
6. **Combo-melee AoE**
7. **Map manipulation spell** (tunnel, prototype only)
8. **Monster capture v1** (one mob, one companion)
9. **Mega-bosses + larger deep-floor maps** (after art is in)

The meta/outside-core bullet stays parked until it's fleshed out.
