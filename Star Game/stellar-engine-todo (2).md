# Stellar Engine Archetype — Master TODO
## Teaching Simulator v4 Build Log
*Session 4 complete. Honest review, all bugs fixed, clean slate going forward.*

---

## ✅ COMPLETED — Sessions 1–4

### Physics Engine
- [x] Mass-Luminosity Law: L/L☉ = (M/M☉)^3.5
- [x] Stefan-Boltzmann: L = 4πσR²T_eff⁴
- [x] MS Lifetime: T_MS = 13×10⁹ × M^−2.5 yr
- [x] Evolutionary branching: <0.08, 0.08–8, 8–WR_thresh, WR_thresh–130, >130 M☉ tracks
- [x] Chandrasekhar limit (1.44 M☉) → Type Ia Supernova
- [x] TOV limit (3.0 M☉) → NS → BH collapse
- [x] Eddington-Sweet circulation: +15% MS lifetime at high rotation
- [x] Von Zeipel theorem: T_pole > T_equator, visualized
- [x] Oblate ellipsoid geometry for rotating stars
- [x] Accretion mass accumulation from companion
- [x] **Pair-instability SN (M > 130 M☉)** — γγ→e⁺e⁻, complete destruction, no remnant
- [x] **Helium flash (M < 2 M☉)** — degenerate He ignition, ~10¹¹ L☉, invisible at surface
- [x] **Metallicity live physics** — Z slider now actively changes:
  - Wind mass-loss: Ṁ ∝ Z^0.7 (Vink et al. 2001)
  - WR threshold: M_WR = 25 + 15×(1 − Z/Z☉) M☉
  - MS lifetime correction: τ × (1 + 0.08×(1 − Z/Z☉))
  - Display in [Fe/H] notation in stats panel
- [x] **Brown dwarf outcome (M < 0.08 M☉)** — failed star, deuterium flash, slow cooling
- [x] **Magnetar variant** — high rotation (>300 km/s) + 8–12 M☉ → B~10¹⁵ G

### Evolutionary Phases (8 death types now complete)
- [x] Main Sequence, Blue Supergiant, LBV, Wolf-Rayet
- [x] Red Giant, Red Supergiant, Planetary Nebula (with proto-WD at center)
- [x] White Dwarf, Neutron Star, **Magnetar**, Black Hole
- [x] Supernova (core-collapse, with neutrino burst annotation), Type Ia SN
- [x] **Pair-Instability Supernova** — distinct animation (void at center, no remnant)
- [x] **Brown Dwarf** — M/L/T spectral cooling sequence

### Teaching Mechanics
- [x] Planck blackbody RGB color
- [x] H-R Diagram with phase-colored animated trail + transition labels
- [x] H-R labeled regions, spectral bands, instability strip, real star overlays (8 stars)
- [x] Spectral subclass display (G2, K5, B3...) with L/T for brown dwarfs
- [x] Narrative event system — 9 event cards with physics equations
- [x] Live equation panel — 9 equations with live values + metallicity physics
- [x] Onion shell — correct H→Fe order, animated fusion fronts, pressure gradient
- [x] Wien's law wavelength indicator
- [x] CNO vs pp-chain display (with rate exponent T^17 vs T^4 shown)
- [x] Wind mass-loss display (live metallicity effect)
- [x] Glossary tooltips
- [x] Timeline milestones with jump-to
- [x] Achievement system (15 badges including new: helium flash, pair-instability, brown dwarf, magnetar)
- [x] Death report with real-world analog and mass-return percentage
- [x] Quiz mode (7 questions including new: helium flash, pair-instability, metallicity/winds)
- [x] Real star comparison panel
- [x] Chandrasekhar/TOV danger warnings

### Gameplay Foundation
- [x] **Nebula gas pool HUD** — 500 M☉ starting pool, remaining/returned display
- [x] **Death type collector** — 8 badge icons, glow on first earn, hover tooltips
- [x] **Quiz reward** — correct answers give +5 M☉ nebula bonus
- [x] **Generation + metallicity tracker** in nebula HUD

### Star View Graphics
- [x] Neutron Star: polar jets, magnetosphere torus, pulsar sweep
- [x] **Magnetar**: purple/magenta jets, intense magnetosphere, giant flare animation
- [x] **Proto-WD at Planetary Nebula center** — heats and blueshifts as shell expands
- [x] **Brown Dwarf** — M→L→T cooling color animation
- [x] **Pair-Instability SN** — distinct animation with expanding void (no remnant)
- [x] **Helium flash pulse** — white bloom animation on star surface
- [x] Supernova with **neutrino burst wavefront** annotation
- [x] Surface convection cells for cool stars
- [x] UV shimmer for hot O/B stars
- [x] WR: 18-stream animated radiation wind
- [x] LBV: animated irregular eruption lobes
- [x] Red Giant/RSG: outer wind nebula halo
- [x] White Dwarf: dual cooling halo rings
- [x] Accretion disk: hot inner ring + color gradient
- [x] Von Zeipel: pole brightening + equatorial darkening + rotation ring
- [x] BH: relativistic jets, photon sphere, Einstein ring, accretion disk

### Bug Fixes (Session 4)
- [x] H-R trail clears when M_ini slider changes (was accumulating across mass changes)
- [x] Play loop extends to tFrac=1.19 (was capping at 1.0, missing WD/NS/BH phases)
- [x] Onion RAF loop guards view correctly during play
- [x] `Teff_override` undeclared variable fixed (was implicit global)
- [x] `feHstr` string comparison fixed (was comparing string >= 0, always true)
- [x] `star.vr` undefined in drawStar label (destructure now includes `vr`)
- [x] `sR` display missing 'R☉' unit for large stars
- [x] PI SN `M=0` division hazard fixed (set to 0.001 with display override)
- [x] PN pnAge division fixed (clamped to 0–1 range, proto-WD temp seeded from absolute value)
- [x] Helium flash timing window widened (was too narrow to reliably trigger)

---

## 🔴 PRIORITY 1 — Gameplay Loop (most impactful gap)

### Honest Assessment of Current State
The simulator is now scientifically solid with 8 death types, live physics, and a good visual engine. The **main gap is that it's still a sandbox with no game loop**. The nebula HUD and death collector are placeholders for the loop but the loop itself doesn't close. A player has no reason to click "▶ PLAY" a second time. This is the most important thing to build next.

### Core Loop to Implement
- [ ] **"Birth a star" button** — clicking it spends M_ini from nebula pool and starts a new evolution run. This turns the mass slider from "configure" to "spend resource."
- [ ] **Mass return on death** — implement actual mass return to nebula pool when a death type is reached:
  - WD: +50% of M0 returned
  - Core-collapse SN: +90%
  - Type Ia SN: +100%
  - Pair-instability SN: +100% (max yield)
  - BH: +20% (jets/wind only)
  - Brown Dwarf: +100% (no consumption)
  - Magnetar: +90%
- [ ] **Z enrichment on death** — each death type increases nebula Z by a small amount (per nucleosynthesis yields table in architecture notes)
- [ ] **"New generation" trigger** — when player clicks a button after a death, the nebula Z carries forward, generation counter increments, and a new run begins
- [ ] **Run objectives** — 2-3 objectives per run drawn from the pool (see priority list in previous design doc). Show in a small HUD panel. Completion triggers visual reward + nebula bonus.
- [ ] **Gas pool enforcement** — currently the pool display doesn't actually prevent spending more gas than available; wire it to disable birth button when pool < M_ini

---

## 🔴 PRIORITY 2 — Science Gaps (high teaching impact)

- [ ] **Supernova asymmetric ejecta** — current SN animation is perfectly spherical. Add random lobe distortion (3-5 asymmetric blobs). Real SNe are strongly aspherical (observed via polarimetry). Low effort, high realism.
- [ ] **Onion shell radius labels** — add shell boundary radii in R☉ (for MS/giant) or km (for pre-SN). Currently only shows temperature and pressure. Critical for understanding the scale difference between shells.
- [ ] **LBV: L/L_Edd ratio visual** — the math panel shows L as % of L_Edd but the star view doesn't reinforce this. Add a small bar or annotation on the LBV showing how close L is to the Eddington limit, which directly drives the eruption.
- [ ] **Common envelope / binary evolution note** — when accretion is active, add an event card explaining Roche lobe overflow and why some binaries merge vs survive. Currently accretion just "happens" with no narrative.
- [ ] **Horizontal Branch after helium flash** — after the flash, a low-mass star settles on the Horizontal Branch (stable He core burning, L~50 L☉). Currently the sim jumps straight to PN. The HB is teachable (it's where RR Lyrae variables live, in the instability strip).
- [ ] **Red Giant Branch tip** — the RGB tip before helium flash is the most luminous pre-flash point. Currently the sim smooths over this. Add a brief RGB tip milestone.
- [ ] **Wolf-Rayet subtypes** — WN (nitrogen-rich), WC (carbon-rich), WO (oxygen-rich). Currently all WR are identical. Mass and metallicity should determine subtype, which affects nucleosynthesis yield.

---

## 🟡 PRIORITY 3 — Polish & Depth

### Visual Polish
- [ ] **Supernova: full phase sequence** — currently a static looping animation. Add distinct phases: core bounce → shock breakout → ejecta expansion → fade. Could be time-gated over ~10 seconds.
- [ ] **Black hole: true Einstein ring** — current implementation is a static circle approximation. A proper lensing arc with a few background "star" points bent around the photon sphere would be much more striking and teachable.
- [ ] **Neutron star: precessing magnetic axis** — ~10° tilt from rotation axis with a slow wobble (wobble period tied to time). Makes the pulsar beam sweep look more physically correct.
- [ ] **Star granulation: animated convection cells** — currently static seed-based. Cells should slowly drift/evolve.
- [ ] **LBV: Eta Carinae bipolar geometry** — current LBV has symmetric eruption lobes. Real LBVs often show bipolar (hourglass) geometry due to equatorial disk + polar outflow. Replace symmetric lobes with bipolar shape.
- [ ] **Accretion: Roche lobe particle stream** — a visible particle ribbon from the companion point source to the accreting star would clarify the accretion mechanism visually.
- [ ] **Kilonova visual** — for when NS+NS merger is eventually added. Blue-to-red fading glow + brief gamma spike.

### UI Polish
- [ ] **Mobile layout** — sidebar collapses to a drawer on narrow screens. Currently overflows.
- [ ] **Tooltip repositioning** — glossary tooltips use `position:fixed; left:300px` which clips on some screen sizes. Should position relative to the hovered element.
- [ ] **Onion label clamping** — labels can still clip on very small windows (documented, not fully fixed). Needs a proper text overflow strategy.
- [ ] **H-R trail phase labels** — the phase transition labels on the H-R trail can overlap. Need simple collision avoidance (offset alternating labels up/down).

### Content Depth
- [ ] **Knowledge tree** (40-node concept graph) — nodes unlock as player encounters events. Branches: Fusion → Energy transport → Winds → Remnants → Nucleosynthesis → Binary evolution. This is the major "learning progress" UI that ties the whole educational experience together.
- [ ] **Stellar bestiary entries** — 8 real star comparison entries already exist; expand to full cards with: real photo thumbnail (linked externally), discovery story, current observational status (is Betelgeuse still about to explode?), estimated fate timeline.
- [ ] **Run history** — last 10 runs with: name, deaths achieved, peak mass, final Z, generation count. Let player name runs.
- [ ] **Contextual quiz triggers** — currently quiz is a separate panel. Wire specific questions to fire naturally after specific events (helium flash → "What kept helium from igniting sooner?", BH → "Why only 90 km Schwarzschild radius for 30 M☉?").

---

## 🟢 PRIORITY 4 — Advanced Features

### Advanced Physics
- [ ] **Binary star mode UI** — pair two stars; show Roche lobes, mass transfer direction, orbital period. Enables Type Ia, X-ray binary, kilonova scenarios naturally rather than via accretion slider hack.
- [ ] **NS+NS merger / kilonova** — after two NSs are in the remnant pool, allow merger trigger. r-process nucleosynthesis, Au/Pt/Ir produced. GW170817 reference.
- [ ] **Prestige: Pop III mode** (Big Bang Reset) — after all 8 death types collected, unlock a Z=0 starting condition. Different physics: no CNO cycle, weaker winds, different mass thresholds, direct-collapse BH possible above ~40 M☉ without SN.
- [ ] **Millisecond pulsar** — NS + accretion spinup. If a NS accretes enough mass at high rate, it spins up to ms periods (fastest spin in universe). Should be an achievement and collectible.
- [ ] **AGB thermal pulses** — for intermediate-mass stars on the AGB before PN ejection, thermal pulses (helium shell flashes repeating ~10,000 yr apart) produce s-process elements and drive the superwind that creates the PN. Currently AGB is skipped.

### Game Mechanics
- [ ] **Instability choice at LBV** — when LBV eruption fires, give player a choice: "Suppress eruption" (spend 20 M☉ nebula gas, star survives longer) vs "Let it erupt" (star loses mass, dramatic event, gets science card). First real player agency moment.
- [ ] **Supernova natal kick** — NS born from SN gets a random kick velocity (up to 500 km/s). Roll a die: NS escapes system (lost) or stays (becomes binary pulsar candidate). Display result dramatically. Teaches observed pulsar proper motions.
- [ ] **Unlock gates** — start with fewer controls, unlock advanced sliders as player demonstrates understanding:
  - Rotation slider: unlocked after first WR observed
  - Metallicity slider (with live physics): already implemented; gate it after 3rd death type
  - Binary mode: after first NS
  - PI SN (>130 M☉ slider range): after Z > 0.02 achieved

---

## 🐛 REMAINING KNOWN ISSUES

- [ ] **Onion label clamping** — still clips on small windows; needs proper layout
- [ ] **Tooltip `left:300px`** — hardcoded position clips on narrow viewports
- [ ] **WR/LBV RAF can stack** — rapid view switching can stack animation frames (add animation ID tracking with cancel before new RAF)
- [ ] **H-R trail on mass change** — trail clears but `firedEvts` also clears, so events re-fire immediately on next pass through a phase. Consider whether this is desired behavior or if events should only fire once globally.
- [ ] **Magnetar detection is immediate at remnant phase** — no transition period; should ideally fire only once with a dramatic event card (currently no magnetar-specific event card exists)
- [ ] **Metallicity slider not synced to nebula Z** — player can set Z slider independently of what the nebula has accumulated; when game loop is implemented, the Z slider should be driven by nebula Z (or shown as a separate choice cost)

---

## 📐 ARCHITECTURE NOTES

**Mass thresholds (current):**
- M < 0.08 → Brown Dwarf
- 0.08–2 → WD track with helium flash at RGB tip
- 2–8 → WD track, no helium flash (non-degenerate He ignition)
- 8 to WR_THRESH(Z) → NS track (Magnetar variant: vr>300 + 8–12 M☉)
- WR_THRESH(Z) to 130 → BH track via WR → SN
- >130 → Pair-Instability SN (no remnant)
- WR_THRESH(Z) = 25 + 15×(1 − Z/Z☉) M☉

**Nucleosynthesis yields (for future element forge):**
- RGB dredge-up: C, N, O
- Planetary Nebula: C, N, O + s-process (Ba, Sr, Pb)
- Core-collapse SN: O, Ne, Mg, Si, S, Fe (incomplete), Ni-56
- Type Ia SN: Fe, Ni (dominant iron-peak source in universe)
- Pair-instability SN: enormous O, Si, Fe — highest per-event yield
- Magnetar: same as NS + possible r-process contribution
- Kilonova (future): Au, Pt, Ir, U, Th — all r-process

**Game state keys (current):**
```js
S.nebulaMass    // display only — not yet enforced
S.nebulaZ       // enriches on death (partially wired)
S.generation    // display only
S.massReturned  // tracks bonus returned mass
S.deathsEarned  // Set of earned death type IDs (drives collector)
```

**Key constants:**
- Z☉ = 0.0142 (Asplund 2009)
- M_Ch = 1.44 M☉ | M_TOV = 3.0 M☉ | L_Edd = 3.2×10⁴(M/M☉) L☉
- VCRIT = 500 km/s (breakup velocity reference)

---

## 🎯 HONEST REVIEW — Current State (Session 4 end)

### What's working well
- Physics is genuinely accurate and educational. The metallicity, mass thresholds, helium flash, and pair-instability SN are all correct and clearly communicated.
- The visual engine is good: 8 distinct death type animations, each visually distinct. The PI SN void is particularly effective.
- The event card system works well as a pacing tool — it stops play at dramatic moments and explains the physics.
- The H-R diagram with phase-colored evolutionary trail is the best teaching feature in the sim.
- The death collector is a strong hook — players will want to see all 8 types.

### What's not working yet
- **No game loop.** The nebula HUD exists but is cosmetic. Nothing actually closes the loop between "star dies" and "start next star." This is the biggest gap.
- **No player agency during evolution.** Once you set the sliders and hit play, there's nothing to do. The LBV choice mechanic (Priority 3) would fix this.
- **Quiz is detached.** It's a separate panel, not organically triggered by events. This reduces its educational impact.
- **Onion view doesn't animate during play.** The RAF fix makes it animate when playing but only if already in onion view — the animation loop is driven by the onion draw itself, which is fine, but transitions from other views while playing don't smoothly reconnect.

### Addiction loop status (per design philosophy)
1. **Collection** ✅ — Death collector is working and compelling
2. **Discovery** 🟡 — Event cards exist; quiz exists; knowledge tree not yet built
3. **Optimization** ❌ — No resource decisions yet; nebula pool is display-only
4. **Narrative** 🟡 — Death cards and event cards are good; bestiary not expanded
5. **Prestige** ❌ — Pop III mode not yet implemented

### Next session recommendation
Focus entirely on closing the game loop: "Birth star" → evolve → die → enrich nebula → new star. Even a minimal version of this loop (no objectives, just the resource cycle) will make the sim feel like a game rather than a demo.

---

*Science references: Vink et al. 2001 (mass-loss); Asplund 2009 (Z☉); Heger & Woosley 2002 (pair-instability); Abbott et al. 2017 (GW170817); Crowther 2007 (WR thresholds)*
