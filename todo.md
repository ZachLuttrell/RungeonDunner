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
- [x] **Par-based speed-bonus tiers** — stats-screen bonus tiers (LIGHTNING DUNNER / SWIFT / ON TIME / STEADY) compare clear time against a per-floor par derived from map area, enemy count, and boss presence, so dense floors aren't punished for taking longer. Par is shown next to the clear time.
- [x] **Store purchase feedback** — animated stat-number tick-up when a stat increases, plus an "upgrade" sound effect (later scaled up: bigger pulse, upgrade-pop floating text, particle burst, longer flash, light shake on permanent upgrades)
- [x] **Better melee sounds** — hit/crit SFX now layer with the melee streak: each successive hit adds sub-bass, mid harmonic, and sparkle tiers, so the audio escalates alongside the streak bonus (dopamaxxing the chain). Low-tier base volume bumped for the most-common sounds.
- [x] **Melee weapon-swing animation** — the current weapon's emoji arcs in a 120° ease-out sweep from the player toward the target, pre-rotated tangent to the arc; bigger reach + slower duration on crits
- [x] **Better throwing-knife sound + visualization** — daggers now visually fly from the player to the target (rotating to face direction, spinning during flight) with a new aerodynamic whoosh on release and a sharp thwack on impact
- [x] **Floor-cleared chime** — small triumphant tone when the last monster on a floor dies (skipped on boss kills since bossVictory already plays)
- [x] **Boss-floor stats screen variant** — post-floor stats modal now switches to gold/yellow accents with a "Boss Defeated · Wing N" header when the cleared floor was a boss floor
- [x] **Boss-portal gold/purple swirl** — boss-floor stats portal swaps the slower swirl to gold so purple + gold braid around the panel, matching the gold inner accents
- [x] **Death-screen replay polish** — small crystalline "ding" plays as each completed floor pops into the stacked pullback view (pitch climbs per step; each note bends DOWN within its envelope so floors feel like they whoosh past as the player is sucked out)
- [x] **Death-screen replay timing** — reveal cadence ramps DOWN: 210ms base × 1.16 per step (capped at 720ms) so first few snaps in fast, later ones settle into a reading pace
- [x] **Death-screen replay layout** — per-floor rotation dropped, all layers stack at the same scale so portals align pixel-perfect at the center; depth is now expressed only via opacity
- [x] **Death-screen hold-until-key** — instead of auto-fading 1.4s after the last reveal, a pulsing "Press any key to continue" hint waits for input so players can read / screenshot
- [x] **"Floors Descended: N" headline** — shiny purple headline on the pullback overlay (small label + large UnifrakturMaguntia number) makes the screenshot brag instantly legible
- [x] **Portal transition around stats screen** — stats modal wrapped in two counter-rotating conic-gradient swirls (one purple, one gold on boss floors) over a radial-gradient void backdrop. Soft radial mask fades the swirl to the edges; backdrop blur reduced for mobile. Open: portal fades in then stats pop. Close: stats shrink-and-fade while portal keeps swirling, then portal dissolves as the next floor builds.
- [x] **More legible cursor** — replaced the dark 🗡 emoji cursor with a vector-drawn dagger: lavender blade with white center highlight, purple crossguard and pommel, all wrapped in a 2px black stroke outline so it stays readable on dark and light backgrounds alike. Hotspot pinned to the blade tip.
- [x] **Powerful blast SFX** — replaced the warpOut layer in castBlast (which sounded like the death pullback) with a new dedicated SFX.blast() — sub-bass shockwave + thunderclap transient + mid crunch + debris tail
- [x] **First-descent SFX** — new SFX.firstDescent() plays at startGame: gate-slam transient, descending whoosh, sub-bass landing, low ominous tail. Sells the "you stepped in" moment at the start of every run.
- [x] **Gold counter pulse on gain** — every gold pickup, kill drop, and duplicate-scroll bonus now pulses the HUD gold value with the same scale-and-glow animation used by shop upgrades
- [x] **HP counter pulse on heal/damage** — green `stat-pulse-good` pulse on heals (potion/elixir/full restore/Q-restore/between-floor/boss-kill heal/store heals), red `stat-pulse-bad` pulse on damage taken (monster hits, burn/poison ticks, lava/trap hazards, blast self-cost). pulseStat now accepts a 'good' or 'bad' variant for color-coded glows.
- [x] **Kills counter pulse on kill** — added pulseStat('s-kills') to killMonster alongside the existing gold pulse
- [x] **ATK counter pulse on streak tier change** — pulses on each streak threshold (2/4/6/8) to celebrate the new tier alongside the audio escalation
- [x] **Wing counter pulse on floor change** — pulses s-floor at the end of nextFloor so the new wing number pops as the portal dissolves
- [x] **Floating gold drop text on kill drops** — killMonster now spawns a "+N🪙" floating text at the dying enemy's tile alongside the particles and counter pulse
- [x] **Boss reveal card flavor pass** — on closer inspection the existing card is already polished (pulsing warning banner, big portrait drop-shadow, themed stats, threat stars). Made one small tweak: the subtitle now reads "— DUNGEON GUARDIAN · WING N —" using the current theme name instead of a generic "WING N GUARDIAN".
- [x] **Mobile perf pass v1** — `@media (max-width:720px)` block disables the per-wall-cell rune-glow animation (kept visible but static), drops `backdrop-filter` on shop / overlay / weapon-unlock / ability-tooltip / boss-reveal / meta modals, lowers it to 2px on the stats portal, and disables the HP-bar shimmer. JS-side, spawnParticles caches a phone-breakpoint check at load and scales particle counts to ~60% on phones. Reversible per-rule if any tradeoff feels wrong.

Speed-bonus tiers are computed against a per-floor par time (≈
5s + mapArea/70 + enemies×1.8 + boss×10). LIGHTNING DUNNER fires under
70% par, SWIFT under 110%, ON TIME under 160%, STEADY beyond. Bonus
gold still scales with floor depth.

The general "more juice" goal isn't really a task — it's a lens we
apply to every other change. After tonight's marathon most of the
short-form polish surface is done; the remaining items above are the
last micro-polish pulls before the next chunky tier of work (spell
slot system, combo melee AoE, art slices, etc.).

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
- [x] **Weapon pickup cards** — modal pops up on pickup with the weapon glyph, name, and ATK / CRIT / +throws stats in a 3-column grid; border + accents themed to the weapon's color; tap or any key to continue
- [x] **Triumphant weapon-pickup SFX** — new SFX.weaponPickup() ascending arpeggio (C5-E5-G5-C6) + metallic ring + sub-bass + shimmer tail, layered over the existing pickup blip

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

Tonight knocked out the first two and nearly all the juice work
(items 1, 2, and most of 4). Remaining order:

1. ~~Post-floor stats + speed bonus + one-tap continue~~ — shipped
2. ~~Floor-length randomization~~ — shipped (theme blocks of 3–5 floors)
3. **Spell slot system** (5+1) with current spells — sets up everything else
4. **Juice pass** — mostly shipped; remaining items are the section-1 micro-polishes above (HP / kills / ATK / Wing pulses, kill-drop floating text, boss-reveal card refresh)
5. **First art vertical slice** — one floor (probably castle for visual punch) gets full custom sprites/textures
6. **Combo-melee AoE**
7. **Map manipulation spell** (tunnel, prototype only)
8. **Monster capture v1** (one mob, one companion)
9. **Mega-bosses + larger deep-floor maps** (after art is in)

The meta/outside-core bullet stays parked until it's fleshed out.
