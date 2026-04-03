# Acaddie — Product Requirements Document (PRD)
### Version 2 · Short Game Advisor

---

## 1. Product Summary

**Acaddie** is a mobile short game golf advisor that works like a caddie. The user describes their current situation on the course — where the ball is, how far to the pin, what the ground looks like — and Acaddie tells them exactly what shot to play, which club to use, and how to execute it in plain language.

---

## 2. Problem Statement

Amateur golfers frequently face short game situations they are unsure how to handle — a buried lie in a bunker, a ball against the grain in deep rough, a 25-yard pitch over a hazard. Most golfers either guess or fall back on the same shot every time, losing strokes unnecessarily.

A caddie solves this by reading the situation and giving specific, confident advice. Most amateur golfers do not have a caddie. Acaddie fills that role.

---

## 3. Target User

- Amateur to mid-handicap golfers (roughly 8–24 handicap)
- Golfers who play regularly but lack short game expertise
- Players who own a rangefinder or GPS watch and already know their distance to the pin
- Mobile users — the app is used on the course, outdoors, likely with one hand

---

## 4. Goals

| Goal | Description |
|---|---|
| Speed | User can complete all inputs and receive advice in under 10 seconds |
| Clarity | Advice is written in plain English — no unexplained jargon |
| Accuracy | Shot and club recommendations are grounded in established short game technique |
| Simplicity | Single screen input, minimal typing, tap-first interaction |
| Trust | The app gives confident, specific advice — not vague suggestions |

---

## 5. Non-Goals

- This is not a full swing or approach shot advisor (shots over 100 yards)
- This is not a putting advisor (separate app — Putting Calculator)
- This does not track scores, statistics, or round history
- This does not require an account or internet connection to function
- This does not provide video instruction

---

## 6. User Journey

### Typical scenario
> A golfer hits their approach shot and ends up 18 yards from the pin in light rough, with a bunker between the ball and the green, on a downhill lie. They open Acaddie, type 18, tap Light Rough, tap Against Grain, tap Downhill, tap Hazard, and tap Get Advice. Acaddie recommends a Lofted Pitch with a Sand Wedge and gives 5 numbered steps. A slope adjustment card tells them to set their shoulders parallel to the slope. They read it, step up, and play the shot.

### Flow
```
Open app
  → Enter distance to pin (yards)
  → Select lie type
  → Select lie detail (if applicable)
  → Select slope / stance
  → Select obstacle (optional, defaults to none)
  → Tap GET ADVICE
  → Read shot name, club, and instructions
  → Read slope adjustment (if shown)
  → Read warning (if shown)
  → Tap ← Edit to adjust, or ↺ New Shot to start fresh
```

---

## 7. Input Requirements

### Distance
- User types the number of yards to the pin
- Required field
- Range: 1–100 yards

### Lie
Single tap from 6 options:
- Fairway / Fringe
- Light Rough
- Deep Rough
- Sand / Bunker
- Shallow Water
- Hardpan / Bare ground

Required field.

### Lie Detail
Appears automatically after certain lie selections. Single tap from a contextual sub-menu.

- **Sand:** Clean, ⅓ Buried, Fried Egg, Fully Buried, Deep Plug, Near Edge
- **Rough (light or deep):** With Grain, Against Grain, Vertical / Matted
- **Water:** ⅓ Submerged, ⅔ Submerged, Fully Under (<2")

Optional — advice engine handles cases where no detail is selected.

### Slope / Stance
Single tap from 5 options:
- Flat
- Uphill
- Downhill
- Ball Above Feet
- Ball Below Feet

Required field.

### Obstacle
Single tap from 3 options. Defaults to None.
- None
- Hazard (bunker, pond, or rough strip between ball and green — ball must carry over)
- Tree / Low Ceiling (ball must stay low)

Optional.

---

## 8. Output Requirements

### Always shown
- **Situation summary bar** — one-line recap of what the user entered (distance, lie, slope, obstacle)
- **Shot name** — the name of the recommended technique
- **Club** — recommended club shown as generic name and loft in degrees, e.g. *Sand Wedge (SW · 56°)*
- **Numbered instructions** — 3 to 6 plain-English steps on how to play the shot

### Conditionally shown
- **Slope Adjustment card** — specific stance, aim, and club adjustments for non-flat lies. Hidden when slope is Flat.
- **Heads Up warning card** — important expectations or risks for this shot type (e.g. extra roll, high-risk technique). Hidden when no warning applies.

---

## 9. Key Product Rules

1. **Tap-first.** Only distance requires typing. All other inputs are a single tap.
2. **Required fields block submission.** GET ADVICE button is disabled until distance, lie, and slope are all filled.
3. **Obstacle defaults to none.** User only needs to tap it if something is in the way.
4. **Lie detail is contextual.** Sub-menus only appear for lies that have meaningful detail (sand, rough, water). They disappear and reset when lie is changed.
5. **Output replaces input.** After GET ADVICE, the input screen is fully replaced by the output screen. No split screen.
6. **Edit preserves state.** The ← Edit button returns to the input screen with all previous selections intact.
7. **Reset is complete.** The ↺ New Shot button clears all fields including distance and resets obstacle to none.
8. **Slope always shown separately.** Slope adjustments are never mixed into the main instructions — they always appear as a distinct card so the user can apply them on top of the base technique.
9. **Club always shows loft.** Every club recommendation includes the loft in degrees so golfers who don't know wedge names can still identify the right club.
10. **No woods in sand.** Even for long bunker shots, the maximum loft club recommended is a Gap Wedge (52°).

---

## 10. Advice Logic Summary

The advice engine uses a decision tree with the following priority order:

1. **Lie** — primary branch (determines the shot family)
2. **Lie detail** — secondary branch (refines technique within the family)
3. **Distance** — tertiary (selects club and swing size)
4. **Obstacle** — override (eliminates low-running options when hazard or tree is present)
5. **Slope** — additive (always layered on top as a separate adjustment, never replaces the main shot)

**Key rules:**
- Buried sand lies override distance — lie always wins
- Against-grain rough: treat distance as 35% farther when selecting club
- Hardpan always results in a Pinch Shot regardless of distance
- Hazard obstacle forces minimum Sand Wedge loft on all shots
- Tree obstacle always forces a Bump and Run (low iron)

---

## 11. Screens

### Screen 1 — Input
Single scrollable screen. All fields visible without pagination. Contextual sub-menus expand in-place below their parent field. GET ADVICE button is fixed at the bottom of the screen.

### Screen 2 — Output
Replaces Screen 1. Shows situation summary, shot card, instructions, and conditional cards. Two navigation buttons fixed at the bottom: ← Edit and ↺ New Shot.

---

## 12. Design Principles

- **Dark theme** — used outdoors in bright sunlight; dark background reduces glare
- **Large tap targets** — minimum 44×44pt per Apple HIG guidelines
- **High contrast** — green accents on dark background for readability
- **Minimal scrolling on output** — most advice fits on one screen

---

## 13. Future Features (Backlog)

| Feature | Description |
|---|---|
| Putting module | Integrate the existing Putting Calculator as a second tab |
| Session save | Remember last stimp rating and last situation entered |
| Club distance profile | User inputs their own club distances for personalized recommendations |
| Practice mode | Quiz mode — user sees a situation and guesses the shot before seeing advice |
| In-app help | Condensed user instructions accessible via a "?" button |
| Favorites / history | Save frequently used scenarios for quick re-entry |

---

## 14. Out of Scope for Current Version

- User accounts or cloud sync
- Multiplayer or sharing features
- GPS or course map integration
- Full swing advice
- Video or animated instruction
- Subscription or payment

---

## 15. Success Criteria

The app is successful if a golfer can:
- Complete all inputs in under 10 seconds
- Read and understand the advice without needing to look anything up
- Feel confident playing the recommended shot
- Return to the app on the next shot without confusion

---

*Acaddie v2 · Based on Dave Pelz Short Game Bible Chapters 7–9*
*Product Requirements Document — March 2026*
