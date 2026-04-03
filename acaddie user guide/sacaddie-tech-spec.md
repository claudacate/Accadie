# Acaddie — Product & Technical Specification
### Version 2 · Short Game Advisor

---

## 1. What This Document Is

This is a combined **Product Requirements Document (PRD)** and **Technical Specification**. It contains everything a developer needs to rebuild Acaddie from scratch in any programming language or framework — iOS (Swift), Android (Kotlin), React Native, Flutter, or any web stack.

- **PRD section** — what the app does, why, and how users interact with it
- **Tech Spec section** — data structures, decision logic, all rules and formulas

---

## 2. Product Overview

### Purpose
Acaddie is a short game golf advisor. It works like a caddie — the user describes the situation and gets specific, plain-English advice on what shot to play, which club to use, and how to execute it.

### Target User
Amateur to mid-handicap golfers who know their way around a course but want a second opinion on tricky short game situations.

### Core Principle
**Tap-first, minimal typing.** The entire input should be completable in under 10 seconds. Only the distance requires typing — everything else is a single tap.

### Platform
Currently: Single-file HTML hosted via GitHub Gist + htmlpreview, used on iPhone Safari. Intended for mobile-first use on the golf course.

---

## 3. App Flow

```
SCREEN 1: INPUT
  ├── Enter distance (number, yards to pin)
  ├── Select lie (6 options)
  ├── Select lie detail (contextual sub-menu, 3–6 options)
  ├── Select slope/stance (5 options)
  └── Select obstacle (3 options, default = none)
        │
        ▼ [GET ADVICE button — enabled when distance + lie + slope filled]
        │
SCREEN 2: OUTPUT
  ├── Situation summary bar
  ├── Shot name + Club recommendation
  ├── Numbered instructions (3–6 steps)
  ├── Slope adjustment card (conditional)
  └── Warning card (conditional)
        │
        ├── [← EDIT] → back to Screen 1 with all inputs preserved
        └── [↺ NEW SHOT] → reset everything, back to Screen 1
```

---

## 4. Input Fields

### 4.1 Distance
- **Type:** Numeric input, tap to type
- **Unit:** Yards to the pin (not to the green edge)
- **Range:** 1–100
- **Required:** Yes

### 4.2 Lie
- **Type:** Single-select tap grid
- **Required:** Yes
- **Options:**

| Value | Label | Triggers sub-menu |
|---|---|---|
| `fairway` | Fairway / Fringe | No |
| `rough-light` | Light Rough | Yes — rough detail |
| `rough-deep` | Deep Rough | Yes — rough detail |
| `sand` | Sand / Bunker | Yes — sand detail |
| `water` | Shallow Water | Yes — water detail |
| `hardpan` | Hardpan / Bare | No |

### 4.3 Lie Detail (contextual, shown based on lie selection)

**Rough detail** (shown for `rough-light` and `rough-deep`):

| Value | Label |
|---|---|
| `with-grain` | With Grain |
| `against-grain` | Against Grain |
| `vertical` | Vertical / Matted |

**Sand detail** (shown for `sand`):

| Value | Label |
|---|---|
| `sand-clean` | Clean / Sitting up |
| `sand-third` | ⅓ Buried |
| `sand-fried` | Fried Egg |
| `sand-buried` | Fully Buried |
| `sand-plug` | Deep Plug |
| `sand-chip` | Near Edge (chip out) |

**Water detail** (shown for `water`):

| Value | Label |
|---|---|
| `water-third` | ⅓ Submerged |
| `water-two-thirds` | ⅔ Submerged |
| `water-full` | Fully Under (<2") |

- **Required:** No (lie detail is optional — advice engine handles null)
- **Behaviour:** Clearing the lie selection also clears the lie detail

### 4.4 Slope / Stance
- **Type:** Single-select tap grid
- **Required:** Yes
- **Options:**

| Value | Label |
|---|---|
| `flat` | Flat |
| `uphill` | Uphill |
| `downhill` | Downhill |
| `ball-above` | Ball Above Feet |
| `ball-below` | Ball Below Feet |

### 4.5 Obstacle
- **Type:** Single-select tap grid
- **Required:** No
- **Default:** `none` (pre-selected on load and after reset)
- **Options:**

| Value | Label | Effect on advice |
|---|---|---|
| `none` | None | No restriction |
| `carry` | Hazard | Eliminates low-running shots |
| `tree` | Tree / Low Ceiling | Forces bump-and-run or low trajectory |

---

## 5. Validation Rules

The **Get Advice** button is enabled only when ALL THREE of these are filled:
1. `distance` is a number greater than 0
2. `lie` is selected (not null)
3. `slope` is selected (not null)

Lie detail and obstacle are optional and do not block the button.

---

## 6. State Object

All inputs are stored in a single state object:

```
state {
  distance:   number | null      // yards to pin
  lie:        string | null      // lie type value
  lieDetail:  string | null      // sub-detail value
  slope:      string | null      // slope value
  obstacle:   string             // default = 'none'
}
```

---

## 7. Advice Engine — Full Decision Logic

The engine takes the state object as input and returns an **advice object**:

```
advice {
  shot:         string        // shot name
  club:         string        // club recommendation
  instructions: string[]      // ordered list of 3–6 steps
  slopeNote:    string        // slope adjustment text (empty if flat)
  warning:      string        // warning text (empty if none)
}
```

### 7.1 Primary Decision Tree

The engine branches first by **lie**, then by **lie detail**, then by **distance** and **obstacle**.

---

#### LIE = `sand`

```
IF lieDetail = 'sand-plug'
  → Shot: Deep Plug Escape
  → Club: SW (56°)
  → Instructions:
      1. Square the clubface — do NOT open it.
      2. Play ball back in your stance.
      3. Swing hard and drive the club into the sand behind the ball.
      4. Let the club stick in the sand — no follow-through.
      5. Expect the ball to come out low with a lot of roll.
  → Warning: No follow-through on this shot. Ball will run a long way — plan for a lot of roll after landing.

ELSE IF lieDetail = 'sand-buried'
  → Shot: Buried Lie Blast
  → Club: SW (56°)
  → Instructions:
      1. Square the clubface — opening it will cause it to bounce off the sand.
      2. Place the ball in the center of your stance.
      3. Aim straight at the target — no left offset.
      4. Dig 2 inches into the sand with maximum force.
      5. Commit fully — this shot needs power to move the sand.
  → Warning: Ball will come out low with little spin and roll much farther than normal. Aim short of the pin.

ELSE IF lieDetail = 'sand-fried'
  → Shot: Fried Egg Blast
  → Club: SW (56°)
  → Instructions:
      1. Open the clubface halfway — not fully open.
      2. Place ball 2 inches behind your left heel.
      3. Aim your body and swing line about 12° left of the target.
      4. Make a full swing and dig the club 1 inch into the sand.
      5. Commit to a full finish — stopping kills this shot.
  → Warning: Fried egg lies come out with almost no spin. Expect more roll than a clean blast.

ELSE IF lieDetail = 'sand-third'
  → Shot: 1/3 Buried Blast
  → Club: SW (56°)
  → Instructions: (same as fried egg above, no warning)

ELSE IF lieDetail = 'sand-chip'
  → Shot: Sand Chip
  → Club: distance ≤ 15 → SW (56°) | distance > 15 → GW (52°)
  → Instructions:
      1. Square the clubface — this is a chip, not a blast.
      2. Play ball just behind center in your stance.
      3. Keep your head level and maintain a constant swing radius.
      4. Make a smooth chipping motion — no digging.
      5. Aim to land the ball 3 feet onto the green and let it roll.

ELSE (lieDetail = 'sand-clean' or null — clean sand, distance governs)
  IF distance ≤ 15
    → Shot: Standard Sand Blast
    → Club: SW (56°)
    → Instructions:
        1. Open the clubface wide — face pointing to the sky.
        2. Place ball inside your left heel.
        3. Aim your body and swing line 17° left of the target.
        4. Make a 9 o'clock backswing (firm, not a full swing).
        5. Commit to a world-class full finish — the club never touches the ball.

  ELSE IF distance ≤ 30
    → Shot: Full Sand Blast
    → Club: SW (56°)
    → Instructions:
        1. Open the clubface wide — face pointing to the sky.
        2. Place ball inside your left heel.
        3. Aim your body and swing line 17° left of the target.
        4. Make a full backswing with a full committed finish.
        5. The club enters the sand 2 inches behind the ball — never touch the ball directly.

  ELSE (distance > 30)
    → Shot: Long Sand Blast
    → Club: GW (52°)
    → Instructions:
        1. Open the clubface slightly less than a standard blast.
        2. Place ball inside your left heel.
        3. Aim 17° left of target with a full aggressive swing.
        4. The lower loft of the GW gives you extra distance.
        5. Full finish — drive all the way through.
    → Warning: For very long bunker shots, a GW gives more distance but less height. Make sure you have enough green to work with.
```

---

#### LIE = `water`

```
IF lieDetail = 'water-full'
  → Shot: Submerged Ball Blast
  → Club: SW (56°)
  → Instructions:
      1. Square the clubface — do not open it.
      2. Place ball in the center of your stance.
      3. Aim straight at the target.
      4. Use maximum power to move both the water and ball.
      5. Make a full committed swing — hesitation costs distance.
  → Warning: Remove your watch and roll up your sleeve. The splash will be enormous. Only attempt if ball is less than 2 inches under.

ELSE IF lieDetail = 'water-two-thirds'
  → Shot: Water Recovery
  → Club: SW (56°)
  → Instructions:
      1. Open the clubface 45°.
      2. Play ball slightly back in your stance.
      3. Swing harder than a normal pitch to cut into the water.
      4. Keep your head down — the splash will be significant.
      5. Aim to get the ball moving forward — distance control is secondary.
  → Warning: High-risk shot. Consider taking a penalty drop if the lie is unfavorable.

ELSE (lieDetail = 'water-third' or null)
  → Shot: Water Splash Shot
  → Club: LW (60°)
  → Instructions:
      1. Open the clubface fully — face pointing to the sky.
      2. Play ball forward in your stance.
      3. Let the club bounce and splash off the water surface.
      4. Make a full swing — the club skips through the water like a sand blast.
      5. High follow-through to keep ball trajectory up.
  → Warning: This only works when the ball is sitting high in water. Club bounces off the surface — do not dig.
```

---

#### LIE = `rough-deep`

```
IF lieDetail = 'vertical'
  → Shot: The Drop
  → Club: SW (56°) or LW (60°)
  → Instructions:
      1. Stand directly over the ball.
      2. Drop the club straight down onto the ball — no backswing.
      3. No follow-through — just a downward chop.
      4. The ball will pop up out of the grass.
  → Warning: Very little distance control on this shot. Just escape the grass safely.

ELSE IF lieDetail = 'against-grain'
  → Shot: Against-Grain Rip
  → Club: distance ≤ 20 → SW (56°) | distance > 20 → GW (52°)
  → Instructions:
      1. Choke down 2 inches on the grip for control.
      2. Play ball slightly back in your stance.
      3. Make a big backswing (9 o'clock or higher).
      4. Rip aggressively through impact — the grass WILL grab the club.
      5. Commit to a full finish no matter what.
  → Warning: Treat this shot as if the pin is 25–50% farther than it actually is — the grass kills distance significantly.

ELSE (with-grain, or null detail)
  IF obstacle = 'carry'
    → Shot: Deep Rough Blast
    → Club: LW (60°)
    → Instructions:
        1. Play ball forward in your stance.
        2. Open the clubface and make a 9 o'clock swing.
        3. Let the club scoot under the ball like a sand blast.
        4. High follow-through — keep the club moving up after impact.
        5. The clubhead will slide under the grass and pop the ball up.

  ELSE
    → Shot: The Chop
    → Club: SW (56°)
    → Instructions:
        1. Play ball back in your stance.
        2. Use a full wrist cock early in the backswing.
        3. Chop straight down into the grass behind the ball.
        4. Take a divot after impact — drive through the grass.
        5. Follow through as much as the grass allows.
    → Warning: Ball will come out lower and with less spin than normal. Expect some run after landing.
```

---

#### LIE = `rough-light`

```
IF lieDetail = 'against-grain'
  adjDist = distance × 1.35   // treat as 35% farther
  → Shot: Against-Grain Pitch
  → Club: adjDist ≤ 15 → PW (46°) | adjDist ≤ 25 → SW (56°) | adjDist > 25 → GW (52°)
  → Instructions:
      1. The grass will grab your club — commit to the swing.
      2. Use slightly more club than the distance suggests.
      3. Ball position: center of stance.
      4. Follow-through fully — do not decelerate.
      5. Expect the ball to fly a bit lower with reduced spin.
  → Warning: Against-grain rough costs 25–35% of distance. This club choice already accounts for that.

ELSE IF obstacle = 'carry'
  → Shot: Lofted Pitch
  → Club: distance ≤ 20 → SW (56°) | distance > 20 → GW (52°)
  → Instructions:
      1. Open your stance slightly and flare your lead foot 30–45°.
      2. Ball in the center of your stance.
      3. Make a short-to-long swing — follow-through 50% longer than backswing.
      4. Keep a high finish to maximize loft.
      5. Land the ball 3 feet onto the green to avoid the hazard.

ELSE (with-grain or no detail, no obstacle)
  IF distance ≤ 8
    → Shot: Chip and Run
    → Club: PW (46°) or 8-iron (~38°)
    → Instructions:
        1. Very narrow stance — feet about 4–6 inches apart.
        2. Play ball off your back ankle.
        3. Hands forward over your left thigh at address.
        4. Triangle motion — arms and shoulders move together, no wrist break.
        5. Follow-through 20% longer than backswing.

  ELSE IF distance ≤ 20
    → Shot: Standard Chip
    → Club: GW (52°) or PW (46°)
    → Instructions:
        1. Narrow stance, ball off back ankle.
        2. Hands forward at address — shaft leans toward target.
        3. No wrist cock — triangle motion only.
        4. Land ball just on the green and let it roll.
        5. Follow-through 20% longer than backswing.

  ELSE (distance > 20)
    → Shot: Standard Pitch
    → Club: distance ≤ 30 → SW (56°) | distance > 30 → GW (52°)
    → Instructions:
        1. Athletic stance — feet about 10–15 inches apart.
        2. Ball exactly centered in your stance.
        3. Lead foot flared 30–45° toward the target.
        4. Short-to-long swing — follow-through 50% longer than backswing.
        5. Finish high but abbreviated — not a full swing finish.
```

---

#### LIE = `hardpan`

```
(Distance governs club only — lie always gives Pinch Shot)
→ Shot: Pinch Shot
→ Club: distance ≤ 20 → PW (46°) | distance > 20 → GW (52°)
→ Instructions:
    1. Play ball slightly back in your stance.
    2. Hands well forward — shaft lean is critical.
    3. Make a descending blow — hit ball first, then ground.
    4. Keep wrists firm through impact.
    5. Expect the ball to come out lower with more roll than a soft lie.
→ Warning: Avoid using a SW or LW from hardpan — the bounce will cause a thin shot. PW or GW only.
```

---

#### LIE = `fairway` (also covers `fringe`)

```
IF obstacle = 'tree'
  → Shot: Bump and Run
  → Club: 5-iron (~27°) or 6-iron (~30°)
  → Instructions:
      1. Play ball in the center of your stance.
      2. Stand tall and close to the ball.
      3. Low sweeping motion — not a descending blow.
      4. Partial wrist cock for power on longer shots.
      5. Ball will run low under any obstacles and roll to the pin.
  → Warning: Avoid landing on any bumps or humps — find a smooth entry point on the green.

ELSE IF obstacle = 'carry'
  IF distance ≤ 20
    → Shot: Cut Lob
    → Club: LW (60°)
    → Instructions:
        1. Open the clubface 45° before taking your grip.
        2. Aim your swing line to the left of the target.
        3. Ball centered relative to your swing line.
        4. Make an aggressive swing — 9 o'clock backswing for a 15-yard carry.
        5. Keep hands and arms soft — let the loft do the work.
        6. Land ball 3 feet onto the green.
    → Warning: This is a high-risk shot. If not practiced, consider a safer angle of attack.

  ELSE (distance > 20)
    → Shot: Standard Pitch
    → Club: distance ≤ 30 → SW (56°) | distance > 30 → GW (52°)
    → Instructions:
        1. Athletic stance, ball centered, lead foot flared 30–45°.
        2. Short-to-long swing — follow-through 50% longer than backswing.
        3. Finish high to maximize height and carry the hazard.
        4. Land ball just past the hazard on the green.

ELSE (no obstacle)
  IF distance ≤ 5
    → Shot: Chiputt
    → Club: Putter or 7-iron (~35°)
    → Instructions:
        1. Use your putter if the path is clear and fringe is short.
        2. For longer fringe, use a 7-iron with a chipping motion.
        3. Chipping stance — narrow, ball slightly ahead of center.
        4. Pendulum stroke — same as putting.
        5. Get the ball rolling immediately.

  ELSE IF distance ≤ 12
    → Shot: Chip and Run
    → Club: 7-iron (~35°) to PW (46°)
    → Instructions:
        1. Lower loft = more roll = easier to judge distance.
        2. Very narrow stance — feet 4–6 inches apart.
        3. Ball off your back ankle, hands forward.
        4. Triangle motion — no wrist break.
        5. Follow-through 20% longer than backswing.

  ELSE IF distance ≤ 25
    → Shot: Standard Chip
    → Club: PW (46°) or GW (52°)
    → Instructions:
        1. Narrow stance, ball off back ankle.
        2. Hands forward — shaft leans toward target.
        3. No wrist cock — pure triangle motion.
        4. Land ball just onto the green and let it run.
        5. Follow-through 20% longer than backswing.

  ELSE IF distance ≤ 40
    → Shot: Standard Pitch
    → Club: SW (56°)
    → Instructions:
        1. Athletic stance — feet 10–15 inches apart.
        2. Ball exactly centered in your stance.
        3. Lead foot flared 30–45° toward target.
        4. Short-to-long swing — follow-through 50% longer than backswing.
        5. Finish high but abbreviated — never a full swing finish.

  ELSE (distance > 40)
    → Shot: Long Pitch
    → Club: GW (52°) or PW (46°)
    → Instructions:
        1. Athletic stance, ball centered, lead foot flared.
        2. Bigger backswing for distance — but still short-to-long.
        3. Follow-through 50% longer than backswing.
        4. Keep body rotation smooth — dead hands, let the body turn do the work.
        5. Aim to land ball 5–8 feet short of pin and let it release.
```

---

### 7.2 Slope Notes (applied after shot is determined)

Slope notes are independent of lie. They stack on top of whatever shot the engine selects and are displayed in a separate card.

```
IF slope = 'downhill'
  → "Set your shoulders parallel to the slope — lean into the hill. Keep your balance and walk forward down the slope through the finish. Ball will come out lower and run more than normal. Use more loft than you think you need."

IF slope = 'uphill'
  → "Set your shoulders parallel to the slope. Use one extra club (less loft) for every 5° of uphill angle — the slope adds effective loft. Ball will fly higher and stop quicker than flat ground."

IF slope = 'ball-above'
  → "Aim right of your target — the slope will pull the ball left. Use a square clubface and a firm grip to prevent the club twisting at impact."

IF slope = 'ball-below'
  → "Stand close to the ball and stay down throughout the swing — resist the urge to stand up at impact. Aim further left than feels natural to compensate for the right-pull tendency."

IF slope = 'flat'
  → (no slope note — card hidden)
```

---

## 8. Output Display Rules

| Element | Shown when |
|---|---|
| Situation summary bar | Always |
| Shot name | Always |
| Club recommendation | Always |
| Numbered instructions | Always |
| Slope adjustment card | slope ≠ `flat` |
| Warning card | warning string is not empty |

---

## 9. Club Reference

| Club | Abbreviation | Approximate Loft |
|---|---|---|
| 5-iron | — | ~27° |
| 6-iron | — | ~30° |
| 7-iron | — | ~35° |
| 8-iron | — | ~38° |
| Pitching Wedge | PW | 46° |
| Gap Wedge | GW | 52° |
| Sand Wedge | SW | 56° |
| Lob Wedge | LW | 60° |
| Putter | — | 3–4° |

**Display format:** `Sand Wedge (SW · 56°)` — always show both generic name and loft.

**Key restriction:** No woods or hybrids recommended. In sand, even for long distances, maximum loft club is GW (52°).

---

## 10. UX Principles

1. **Tap-first.** Every field except distance is a single tap. No dropdowns, no sliders.
2. **One screen input.** All fields visible on one scrollable screen — no pagination.
3. **Contextual sub-menus.** Lie detail only appears after lie is selected, specific to the lie type chosen.
4. **Progressive validation.** GET ADVICE button only activates when minimum required fields are filled. Show visual feedback (dot indicators) on each required field.
5. **Output replaces input.** After GET ADVICE, input screen is hidden and output screen is shown. Back button restores input with all selections preserved.
6. **Reset is clean.** New Shot clears all state, resets obstacle to default (none), hides all sub-menus.

---

## 11. Design System (Current HTML Version)

| Token | Value |
|---|---|
| Background | `#0a0f0d` |
| Surface | `#111a14` |
| Surface 2 | `#162019` |
| Border | `#1f2e23` |
| Green accent | `#4ade80` |
| Green dark | `#16a34a` |
| Green dim | `#1a3324` |
| Amber | `#fbbf24` |
| Text | `#e8f0ea` |
| Dim text | `#5a7560` |

**Fonts:**
- Display / headings: Bebas Neue
- Body: DM Sans (400, 500, 700)
- Labels / monospace: DM Mono (400, 500)

**Theme:** Dark caddie — deep green-black background, green accents, amber for warnings.

---

## 12. Knowledge Sources

The advice engine logic is based on:
- **Dave Pelz Short Game Bible** — Chapters 7 (Pitch), 8 (Chip), 9 (Sand)
- General short game instruction: flight-to-roll ratios, club loft selection by distance, rough grain effects

**Key reference rules from Pelz:**
- Standard blast: face wide open, ball inside left heel, aim 17° left, full finish
- 1/3 buried: face halfway open, aim 12° left, dig 1 inch
- Fully buried: face square, aim at target, dig 2 inches, maximum power
- Deep plug: face square, ball back, swing hard, NO follow-through
- Against-grain rough: treat as 25–50% farther than actual distance
- Uphill slope: one extra club per 5° of slope
- Short-to-long swing: follow-through always longer than backswing to prevent deceleration

---

## 13. Rebuilding in Another Language — Simple Process

Follow these steps in order:

**Step 1 — State model**
Create a data model with 5 fields: `distance`, `lie`, `lieDetail`, `slope`, `obstacle`. All nullable except obstacle (default = none).

**Step 2 — Input screen**
Build the input UI using Section 4 as the spec. The only rules are: lie detail panels are contextual (show/hide based on lie), obstacle defaults to none, and the submit button requires distance + lie + slope.

**Step 3 — Advice engine**
Implement the decision tree in Section 7 as a pure function: takes state, returns advice object. No UI logic inside this function. Test each branch independently before connecting to UI.

**Step 4 — Output screen**
Build the output UI using Section 8. Two conditional cards (slope, warning) — hide them when their strings are empty.

**Step 5 — Navigation**
Implement three transitions: Input → Output (on submit), Output → Input (edit, preserve state), Output → Input (reset, clear state).

**Step 6 — Polish**
Apply design tokens from Section 11. Ensure tap targets are minimum 44×44pt for mobile usability.

---

*Acaddie v2 · Based on Dave Pelz Short Game Bible Chapters 7–9*
*Technical Specification — March 2026*
