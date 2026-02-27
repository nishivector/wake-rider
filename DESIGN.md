DESIGN_DOC
game_name: wake-rider
display_name: Wake Rider

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
IDENTITY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Game name:   Wake Rider
Tagline:     The enemy formation is your engine — the moment it
             breaks apart, so do you.
Version:     Round 15 design

What is the player:
  A lone recon wedge — a forward-recon shard ejected from a
  destroyed carrier, no propulsion of its own, surviving entirely
  by parasitizing the pressure waves of enemy patrol ships. It
  does not shoot. It does not dodge. It drafts, or it dies.

World feel (2 sentences, present tense):
  You are skimming the surface of a deep-ocean transit corridor,
  fifty meters above the hull-lights of the enemy convoy — the
  water is black, the ships are gold, and their wakes are the
  only warm thing left in the circuit. Every time you break
  draft the cold presses in immediately, and you feel the exact
  weight of all the speed you borrowed.

Emotional experience: predatory patience

Reference games:
  - Gran Turismo 4 (slipstream as earned physics, not power-up;
    weight and momentum as expressive materials; the cost of
    breaking a drag line is real and felt)
  - Wipeout Pure (vector-neon aesthetic; speed as terrain you
    inhabit, not just a number; desperate recovery from a
    shattered racing line)
  - R-Type (enemy formations ARE the puzzle; you read the
    pattern before you move; one wrong read is total collapse)


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
VISUAL SPEC
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Background color:  #2E5FA3  — mid ocean blue. NOT dark navy.
                   NOT near-black. This is daytime deep water.
                   The background must feel like an environment,
                   not a void.
Primary color:     #E8C84A  — gold. Player wedge body, slipstream
                   corona, UI numerals, speed meter fill.
Secondary color:   #FF4D1C  — danger red. Obstacle ships, out-of-
                   draft warning pulse, wall boundaries, danger
                   UI flash.
Accent color:      #A8E4FF  — ice blue. Used exclusively for:
                   slipstream tunnel interior glow, the shimmer
                   on wake trails at their leading edge, and the
                   "lock" ring that appears when the player
                   enters a valid draft radius. This color should
                   feel like cold air.

Bloom:
  Enabled. Threshold: luminance > 0.72 (only the brightest
  gold/white areas bloom — not the blue background). Strength:
  0.55. Radius: 0.4. Using Three.js UnrealBloomPass with these
  exact values:
    bloomPass.threshold = 0.72
    bloomPass.strength = 0.55
    bloomPass.radius = 0.4
  The slipstream corona on the player should bloom hard. Enemy
  wake trails bloom at half strength (0.28). Background: no bloom.

Camera (Three.js — exact values):
  camera = new THREE.PerspectiveCamera(75, aspect, 0.1, 2000)
  camera position (relative to player, updated every frame):
    camera.position.set(
      player.position.x,
      player.position.y + 3.5,
      player.position.z - 12
    )
  camera.lookAt(
    player.position.x,
    player.position.y + 0.8,
    player.position.z + 4
  )
  This places the camera 12 units behind, 3.5 units above, and
  looks 4 units ahead of the player — producing a 10° above-
  horizon angle. The horizon should be visible in the upper
  third of the screen. Do not lerp the camera laterally —
  it should be locked to player X/Y at all times. Only Z
  (forward position) gets a 3-frame lag to create chase feel.
  Camera Z: camera.position.z = lerp(camera.position.z,
  player.position.z - 12, 0.33) per frame.

Player silhouette:  flat acute arrowhead, no cockpit

Player model detail:
  An isoceles triangle, wider than long (ratio 0.65:1 — width
  to length). Flat, no height, like a stingray pressed to glass.
  The leading point faces forward. Two short stabilizer fins at
  the rear corners, angled 20° outward. The entire surface is
  #E8C84A. The edges glow — 2px inner stroke at #FFFDE8.
  When drafting, a corona of #A8E4FF radiates from the trailing
  edge, 8–18px wide, animated with 2Hz sine oscillation.
  When NOT drafting, no corona. The transition takes 0.3s
  (fade out: linear; fade in: 0.15s snap).

Enemy patrol ships:
  Elongated hexagonal prisms — cigar-shaped from above, flat
  from the side. Roughly 3× the player's length. Color: #1A3B6E
  (dark blue-grey — they read as silhouettes against the
  background). Leading edge: thin #E8C84A rim light (1.5px).
  No markings, no windows. Their wake trail is a narrowing
  chevron of #A8E4FF that fades over 1.2 seconds behind them.

Motion blur:
  Applied to the player only. Use AfterimagePass with damp: 0.82
  when speed > 220px/s (in-draft speeds). Damp: 0.0 when speed
  < 160px/s (no trail when slow). Linear interpolation between
  those speeds for intermediate damp values.

Wall / boundary:
  The circuit has invisible walls made visible only as a #FF4D1C
  grid that flashes on contact. The grid is perspective-
  projected (lines converge to horizon). Flashes for 0.4s at
  full opacity then fades. No permanent visible walls during
  normal play — the world should feel boundless until you
  touch the edge.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
SOUND SPEC
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Music:
  138 BPM. Minimal techno / electro hybrid. Key of F minor.
  Instrumentation: pulsing synth bass (sawtooth, heavily
  filtered), sparse hi-hat on every 16th, kick on 1 and 3,
  long pad underneath. No melody — pure groove and atmosphere.

  The music has two states, transitioned by crossfade (0.8s):

  STATE: OUT_OF_DRAFT
    Bass filter cutoff: 400 Hz (muffled, threatening)
    Pad volume: 0.6
    Hi-hat volume: 0.3
    Overall mix: dark, compressed, anxious

  STATE: IN_DRAFT
    Bass filter cutoff: ramp to 2800 Hz over 0.8s (opens up,
    brightens, pressure-releases)
    A second bass layer enters (one octave up, +8dB, filtered
    at 5kHz) — this is the "slipstream singing"
    Pad volume: 0.9
    Hi-hat volume: 0.7
    Subtle high-frequency shimmer added (Tone.js AutoFilter,
    depth: 0.4, frequency: 4, waveform: sine)
    Overall mix: energized, flying, certain

  Transition back to OUT_OF_DRAFT is immediate on the snap
  (see SFX: draft_break below), then bass re-filters over 0.3s.

  Implementation: generate procedurally using Tone.js.
  Do NOT use audio files. All instruments must be Tone.js
  synthesized.

Sound Effects (6) — all Tone.js, no audio files:

  1. draft_enter
     Triggered: player enters draft radius of any ship
     Sound: a low, warm whoosh rising in pitch (0.4s)
     Tone.js: new Tone.Synth({ oscillator: { type: 'sine' },
       envelope: { attack: 0.05, decay: 0.15, sustain: 0.6,
       release: 0.2 } }). Frequency glide from 180Hz to 340Hz
       over 0.4s. Volume: -8dB.
     Feel: the moment of being grabbed by the slipstream

  2. draft_break
     Triggered: player exits draft radius (manually or by
     host ship diverging)
     Sound: a sharp, brittle crack followed immediately by a
     descending pressure whoosh (0.6s total)
     Tone.js: Tone.MembraneSynth({ pitchDecay: 0.04,
       octaves: 4 }) hit at 200Hz for the crack (0.05s),
       then a Tone.Synth sine glide from 280Hz to 90Hz over
       0.45s, volume: -4dB.
     Feel: the snap of a tow-rope going slack. This is the
     most important sound in the game. Make it hurt.

  3. speed_bleed
     Triggered: continuous while player is OUT of draft and
     decelerating, loops every 1.2s
     Sound: a soft, rhythmic throb — like a heart rate
     slowing. Low frequency pulse.
     Tone.js: Tone.LFO connected to a Tone.Synth's volume,
       frequency: 1.4Hz, waveform: sine, depth: 14dB.
       Carrier: 60Hz sine, volume: -18dB.
     Feel: the quiet dread of losing momentum

  4. chain_lock
     Triggered: player lines up on a new host ship while
     already in another's draft (chaining)
     Sound: a clean, high harmonic ping — satisfied, precise
     Tone.js: Tone.Synth({ oscillator: { type: 'triangle' },
       envelope: { attack: 0.01, decay: 0.3, sustain: 0,
       release: 0.1 } }). Frequency: 880Hz, then 1320Hz,
       0.15s apart (a perfect fifth). Volume: -10dB.
     Feel: chess piece clicking into place

  5. wall_hit
     Triggered: player contacts boundary
     Sound: a heavy, dense thud with digital distortion
     Tone.js: Tone.MetalSynth({ frequency: 40, envelope:
       { attack: 0.001, decay: 0.4, release: 0.2 },
       harmonicity: 3.1, modulationIndex: 12, resonance: 3200,
       octaves: 1.2 }). Volume: -2dB (loud — this should
       feel like hitting a wall).
     Feel: expensive mistake

  6. formation_split (THE MOMENT — see below)
     Triggered: once, on lap 3, when lead pacer splits
     Sound: a single sustained chord that fractures into 5
     detuned copies, each panning to a different position
     across the stereo field over 1.5s, then silence for
     0.3s before the music reasserts
     Tone.js: Tone.PolySynth with 5 voices. All start at
       unison (220Hz), then each voice glides to a different
       frequency: [180, 210, 220, 245, 270] Hz over 1.5s,
       with individual panning: [-1, -0.5, 0, 0.5, 1].
       Envelope: long release (1.2s). This sound should make
       the player feel vertigo.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
MECHANIC SPEC
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Core loop (one sentence):
  Steer into the wake of an enemy ship to borrow its speed,
  plan your next host before your current one turns away,
  and complete laps of the circuit without bleeding to zero.

Primary input:
  Mouse/pointer — document.addEventListener('pointermove').
  Player follows cursor X position (horizontal steering only
  at low skill; at higher levels, vertical offset also affects
  draft acquisition). No keyboard. No touch alternative in spec
  (future iteration).

  Steering mapping:
    Player X = lerp(player.X, cursor.X * 0.6, 0.12) per frame
    (damped — player does not snap to cursor; there is weight)
    Cursor X range: 0 to window.width → player X range: -18 to 18
    (world units, centered on track)

Speed model (world units per second):
  BASE_SPEED: 140 u/s  (out of draft, no acceleration)
  DRAFT_SPEED: 210 u/s (inside draft radius, interpolated)
  MAX_SPEED:   280 u/s (after 2+ seconds continuous drafting,
               speed ramps from 210 to 280 over 2s — the "sling")
  BLEED_RATE:  38 u/s² deceleration when out of draft
               (not linear — it's squared: the faster you were,
               the faster you lose it)
  FLOOR_SPEED: 80 u/s  (minimum — below this, lap fails)
  SPEED_FLOOR_GRACE: 2.5 seconds below floor before fail

  Speed to draft_speed transition: lerp with factor 0.04/frame
  Speed from draft_speed: immediate deceleration at BLEED_RATE²

Draft mechanic:
  DRAFT_RADIUS: 110 px (screen space, rear arc only — 180°
    behind the host ship; the player cannot draft from the side
    or front of a ship)
  DRAFT_ARC: 180° centered on the host ship's reverse heading
  DRAFT_DEPTH: The draft field is not uniform. It is a teardrop:
    - At 0–40px behind ship: 100% strength (DRAFT_SPEED)
    - At 40–80px behind ship: lerp 100% to 60% strength
    - At 80–110px behind ship: lerp 60% to 0% (soft edge)
    - Lateral: falls off as cos(angle)² where angle is measured
      from the ship's heading axis
  LOCK_RING: When player is within 130px of a valid draft zone
    (the "approach threshold"), render the #A8E4FF lock ring
    around the target ship. Ring: 2px stroke, 80px radius,
    dashed (12px dash, 8px gap), rotates at 45°/s.
    This tells the player they are about to draft — it is NOT
    confirmation that they ARE drafting.

Chain drafting:
  A player is "chaining" if they enter a new ship's draft within
  1.5 seconds of leaving the previous one.
  Chain bonus: speed floor is 20 u/s higher per chain link
  (so: chain-2 floor is 100, chain-3 floor is 120, etc., cap
  at 160). This rewards pre-planned routes — a player who never
  breaks draft can run the circuit at speeds a reactive player
  cannot reach. Chain counter displayed in upper-left: 0–8 links.
  Chain resets on any out-of-draft gap > 1.5 seconds or wall hit.

Enemy ships — movement:
  Patrol ships follow pre-defined bezier paths. Each level's
  formation is a set of ships on shared loop paths (see Level
  Design). Ships do not react to the player. They are the clock,
  not the adversary.
  SHIP_SPEED: Level 1: 160 u/s, scaling per level (see below)
  Ships loop their path continuously. The player must synchronize
  with their timing — draft entry depends on being behind the
  right ship at the right moment.

Win condition:
  Complete 3 laps of the circuit without letting speed drop to
  FLOOR_SPEED for more than SPEED_FLOOR_GRACE. A "lap" = player
  crosses the start line after completing one full circuit.
  At lap completion: brief freeze frame (0.08s), time-score
  displayed, chain count saved.

Lose condition:
  Speed stays at or below FLOOR_SPEED for 2.5 continuous
  seconds. The wedge begins to fragment (particle dissolve,
  0.6s). No lives. No retry prompt. The player is taken to
  the results screen showing their best chain, best speed,
  laps completed.

HUD elements (always visible):
  - Speed meter: left side, vertical bar, #E8C84A fill,
    scale: 80–280 u/s. A thin #FF4D1C line at FLOOR_SPEED.
  - Current chain count: upper-left, large numerals, #E8C84A
  - Lap counter: upper-center, "LAP 1/3", minimal
  - No minimap. The player must read the world directly.

Difficulty curve (5 levels):

  LEVEL 1 — "First Wake"
    Ships: 4 patrol ships in a loose oval formation
    Ship speed: 160 u/s
    Draft radius: 110 px (default)
    BLEED_RATE: 28 u/s² (slower penalty — forgiving)
    Formation: all ships on same oval path, evenly spaced
    New: nothing. Learn to draft.

  LEVEL 2 — "The Lane"
    Ships: 6 patrol ships in two staggered columns (3 per lane)
    Ship speed: 175 u/s
    Draft radius: 110 px
    BLEED_RATE: 32 u/s²
    Formation: two parallel oval paths, 28u apart
    New: player must change lanes — the two columns go slightly
    different speeds (lane A: 175, lane B: 180), creating
    natural opportunities for cross-lane drafting

  LEVEL 3 — "The Split" (THE MOMENT level — see below)
    Ships: 7 patrol ships; 6 in formation + 1 lead "ghost pacer"
    Ship speed: 190 u/s
    Draft radius: 105 px (slightly reduced)
    BLEED_RATE: 36 u/s²
    Formation: figure-8 circuit with crossing point at center
    New: formation crossing (ships from two paths converge,
    requiring the player to pick which stream to follow through
    the crossing). The lap 3 split event occurs here.

  LEVEL 4 — "Compression"
    Ships: 8 patrol ships in a tightening spiral (path shrinks
    radius every 45 seconds before expanding — breathing circuit)
    Ship speed: 205 u/s
    Draft radius: 100 px
    BLEED_RATE: 40 u/s²
    New: gaps between ships are uneven (some 1.8 seconds apart,
    some 3.4 seconds). The player must identify which gaps close
    in time. Close gap = must switch; wide gap = safe to hold.

  LEVEL 5 — "Thread the Needle"
    Ships: 10 patrol ships in an oblique spiral that tightens
    to near-zero gap at the circuit's inner apex
    Ship speed: 220 u/s
    Draft radius: 95 px
    BLEED_RATE: 44 u/s²
    New: at the apex (innermost point of spiral), draft windows
    are 0.9 seconds wide. The player must be pre-positioned.
    A 10x player has planned the apex entry from 4 ships back.
    A 1x player arrives late, misses the window, and bleeds.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
THE MOMENT — FORMATION SPLIT (Level 3, Lap 3)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Setup:
  For laps 1 and 2 of Level 3, the ghost pacer (a ship 15%
  larger than others, rimmed in brighter gold) leads the
  formation from the front. The player naturally tucks behind
  it — it's the fastest, most reliable draft host on the circuit.
  Its path is a clean, predictable line. It becomes safety.

Trigger:
  Exactly at the start of lap 3, when the ghost pacer crosses
  the start line, it executes the split:

  Frame 0:    Ghost pacer begins to shimmer (brightness × 2.5,
              flicker at 12Hz — clearly wrong, clearly about to
              happen). Duration: 0.7 seconds.
  Frame 42:   Ghost pacer emits a burst of #A8E4FF particles
              (48 particles, radiate outward at 60 u/s, fade
              over 0.4s). The formation_split SFX fires.
  Frame 42:   The single ghost pacer becomes 5 identical copies,
              each at the same starting position, each taking a
              different bezier path that diverges over the next
              3 seconds. The 5 paths fan out like fingers from
              a fist. Paths diverge by 24°, 12°, 0°, -12°, -24°
              from the original heading.
  Frame 42+:  Music cuts for 0.3s (just the pad continues,
              at -12dB). Then reasserts.

Player experience:
  The player was in the ghost pacer's draft. The split ejects
  them from all 5 wakes simultaneously — no single wake is
  strong enough to compensate (each copy has 60% of the
  original's draft strength, so only if the player is within
  40px of one of the 5 will they still have partial draft).
  The player's speed immediately begins to bleed. They have
  approximately 2.3 seconds to identify which of the 5 copies
  leads toward a viable circuit path and pick its slipstream.
  The wrong choice leads into the boundary.

  The 5 copies persist for 8 seconds then recombine back into
  the ghost pacer, which rejoins its original path. This is the
  only time this event occurs in the entire game.

Design intent:
  This moment works because it doesn't give the player time to
  think — only time to commit. The player who survives does so
  not by being clever in the moment but by having internalized
  enough of the circuit's geometry to feel which direction is
  right. It rewards groove. It punishes pure reaction.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
EMOTIONAL ARC
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

First 30 seconds:
  The player is slow. Embarrassingly slow. The patrol ships
  pull ahead. There is no tutorial — just the lock ring
  appearing faintly around the nearest ship. The player chases
  it. The first moment they enter the draft, the music opens up,
  the corona ignites on the wedge, and the speed ramps from 140
  to 210 in under a second. The player feels the pull physically.
  They think: oh. That's the whole game. But they don't know yet
  what they don't know.

After 2 minutes:
  The player has crashed into the boundary twice, lost their
  chain both times, and rebuilt it from scratch. They are now
  reading the formation rather than chasing individual ships.
  They have a mental model of the circuit — three ships they
  trust, one they've learned to avoid (it turns early). They
  feel like a pilot, not a passenger.

Near win (approaching final lap):
  The player knows the circuit. They know the ghost pacer's
  timing. They're threading the split formation from memory,
  not reaction. Their chain counter reads 5. Their speed has
  not dipped below 200 in thirty seconds. The circuit has
  become a language they speak. The boundary flashes #FF4D1C
  twice — they graze it both times and recover both times.
  When the finish line pulses, it doesn't feel like relief.
  It feels like proof.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
THIS GAME'S IDENTITY IN ONE LINE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  You have no engine. Your enemies are your engine.
  You are not racing them — you are reading them.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
START SCREEN
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Idle animation (no interaction required):
  Three enemy patrol ships orbit a central point in a loose
  triangular formation. Their wake trails (#A8E4FF, opacity 0.5)
  linger for 1.5 seconds behind each. The player wedge is parked
  at bottom-center, motionless, but its corona pulses slowly
  (2Hz sine, opacity 0.3–0.7) as if the slipstream is already
  nearby. Every 4 seconds, the nearest ship's lock ring briefly
  appears around it (dashed circle, fades in 0.3s, holds 0.5s,
  fades out 0.3s). The whole scene loops. Background: #2E5FA3.
  The title text is centered, upper third.

Click to start:
  On any pointer interaction: the camera snap-zooms to the
  player wedge, the ships accelerate and spread out, and
  the game begins. No loading screen. No menu. The start
  animation IS the menu.

────────────────────────────────────────────────────────────────
SVG OPTION A — Glowing Title

  Dimensions: 600 × 160 px, viewBox="0 0 600 160"
  Background: none (transparent — placed over #2E5FA3 bg)

  Title text: "WAKE RIDER" in all-caps, centered
  Font spec: 72px, weight 800, letter-spacing: 0.18em
  Font family: 'Orbitron', monospace (Google Fonts, or fallback
    to 'Courier New' bold italic — but Orbitron is required
    for the right angularity)
  
  Text fill: #E8C84A
  Text stroke: none
  
  Glow effect (SVG filter, applied to the text):
    <filter id="glow" x="-20%" y="-30%" width="140%" height="160%">
      <feGaussianBlur in="SourceGraphic" stdDeviation="6"
        result="blur1"/>
      <feGaussianBlur in="SourceGraphic" stdDeviation="2"
        result="blur2"/>
      <feMerge>
        <feMergeNode in="blur1"/>
        <feMergeNode in="blur2"/>
        <feMergeNode in="SourceGraphic"/>
      </feMerge>
    </filter>
  
  Subtitle below title: "DRAFT OR DIE"
  Font: 18px, weight 400, letter-spacing: 0.4em, all-caps
  Fill: #A8E4FF, opacity 0.85
  Vertical gap: 14px below title baseline
  
  "Click to begin" blinking text below subtitle:
  Font: 13px, weight 400, letter-spacing: 0.2em
  Fill: #E8C84A, opacity animated: 0→1→0 over 1.4s loop
  Vertical gap: 28px below subtitle

────────────────────────────────────────────────────────────────
SVG OPTION B — Wedge Silhouette Icon

  Dimensions: 80 × 80 px, viewBox="0 0 80 80"
  Background: none

  Shape: isoceles triangle, wider than tall
    Points: (8,72) (72,72) (40,18)
    (this gives width 64, height 54, ratio ≈ 1.19:1 wide-to-tall)
  Fill: #E8C84A
  
  Rear stabilizer fins (two triangles at base corners):
    Left fin:  points (8,72) (4,80) (20,72)
    Right fin: points (72,72) (76,80) (60,72)
    Fill: #E8C84A, opacity 0.7
  
  Corona (radial gradient, behind the shape):
    <radialGradient id="corona" cx="50%" cy="65%" r="50%">
      <stop offset="0%"   stop-color="#A8E4FF" stop-opacity="0.7"/>
      <stop offset="60%"  stop-color="#A8E4FF" stop-opacity="0.15"/>
      <stop offset="100%" stop-color="#A8E4FF" stop-opacity="0"/>
    </radialGradient>
    Ellipse behind wedge: cx=40 cy=72 rx=36 ry=14
    Fill: url(#corona)
    The corona ellipse renders BEHIND the main triangle.
  
  Inner edge highlight: 1px stroke on triangle edges,
  color: #FFFDE8, opacity 0.6.
  
  Usage: 80×80 icon in upper-left of start screen (or favicon).
  Can be scaled to 32×32 without losing readability.


━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
IMPLEMENTATION NOTES FOR PROGRAMMER
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

These are decisions that affect architecture. Do not leave them
to chance:

1. Speed is a first-class state variable — not derived.
   Compute player speed each tick based on current draft status
   and bleed rate, then update position from speed. Do NOT
   compute position from cursor distance.

2. Draft detection is 2D world-space, not screen-space.
   DRAFT_RADIUS of 110 is in world units, not pixels. The
   perspective projection means screen-space distances are
   unreliable for physics.

3. Enemy ships use pre-authored bezier paths. Do not use
   physics-based steering. The paths are the track. Author
   them by hand per level, stored as arrays of control points.

4. The formation split copies share the original ship's
   state at the moment of split. They are 5 new ship instances
   initialized with the ghost pacer's position/velocity, then
   each assigned a unique diverging bezier. The original ghost
   pacer is hidden (removed from scene) for the 8-second
   split duration.

5. Chain detection: track `lastDraftExitTime` per ship.
   Chain count increments when `currentTime - lastDraftExitTime
   < 1500ms` at the moment of entering a new ship's draft.

6. The music is a single Tone.js Transport that runs from
   game start. It never stops or restarts. State changes are
   filter/volume adjustments, not track switches.

7. All visual effects (bloom, motion blur) are Three.js
   EffectComposer post-processing. Compose in this order:
   RenderPass → AfterimagePass → UnrealBloomPass → OutputPass.

8. Performance target: 60fps at 1920×1080. Budget:
   - Player: 1 mesh + 1 particle system (corona)
   - Enemy ships: up to 10 meshes + 10 trail lines
   - No shadows. No reflections. No textures (all geometry/color).
   - Lock rings: LineLoop geometries, updated per frame.

END_DESIGN_DOC
