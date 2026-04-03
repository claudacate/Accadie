# Putting Calculator — Original Formulas

## 1. Combined Direction

The combined slope percentage is calculated using the **Pythagorean theorem** on the side slope and up/downhill slope components:

```
Combined % = √(side slope %² + up/downhill %²)
```

### Examples
| Side Slope % | Up/Downhill % | Combined % |
|---|---|---|
| 1.6% | 1.2% up | √(1.6² + 1.2²) = √(4.00) = **2.0%** |
| 2.0% | 0.1% up | √(2.0² + 0.1²) = √(4.01) = **2.0%** |
| 2.0% | 2.4% down | √(2.0² + 2.4²) = √(9.76) = **3.1%** |
| 2.5% | 1.9% down | √(2.5² + 1.9²) = √(9.86) = **3.1%** |

---

## 2. Aim Distance (at Stimp 10)

### Step 1 — Base calculation
```
paces = putt length in feet ÷ 3

base = (paces × 2) − 1
```

### Step 2 — Apply side slope
```
aim (inches) = base × side slope %
```
*(For 1% side slope the result is the aim in inches. For 2% multiply by 2, for 3% multiply by 3, for 0.1% multiply by 0.1, etc.)*

### Step 3 — Adjust for up/downhill
```
For every 1% downhill:  aim = aim + (5% × aim)
For every 1% uphill:    aim = aim − (5% × aim)
```

### Step 4 — Adjust for green speed
```
For every 1 stimp above 10:  aim = aim × 1.10  (add 10%)
For every 1 stimp below 10:  aim = aim × 0.90  (subtract 10%)
```

### Full formula combined
```
aim (inches) = [(paces × 2 − 1) × side slope %]
               × [1 ± (0.05 × up/downhill %)]
               × [1 + (0.10 × (stimp − 10))]
```

### Examples (all at Stimp 10)
| Length | Paces | Side % | Up/Down | Aim Calculation | Aim |
|---|---|---|---|---|---|
| 9 ft | 3 | 1.6% | 1.2% up | (5 × 1.6) × (1 − 0.06) = 8.0 × 0.94 | **7.5"** ≈ 7.8" |
| 8 ft | 2.67 | 2.0% | 0.1% up | (4.33 × 2.0) × (1 − 0.005) = 8.66 × 0.995 | **8.6"** ≈ 8.7" |
| 38 ft | 12.67 | 2.0% | 2.4% down | (24.33 × 2.0) × (1 + 0.12) = 48.67 × 1.12 | **54.5"** = 4'6" ≈ 4'8" |
| 8 ft | 2.67 | 2.5% | 1.9% down | (4.33 × 2.5) × (1 + 0.095) = 10.83 × 1.095 | **11.9"** ≈ 12" |

---

## 3. Pace Percentage

**Only the up/downhill component affects pace. Side slope and distance are irrelevant.**

```
Pace % = 100 + (uphill % × 18)      for uphill putts
Pace % = 100 − (downhill % × 18)    for downhill putts
Pace % = 100                         for flat putts
```

### Reference table
| Up/Downhill | Pace % |
|---|---|
| 3% downhill | **46%** |
| 2% downhill | **64%** |
| 1% downhill | **82%** |
| Flat | **100%** |
| 1% uphill | **118%** |
| 2% uphill | **136%** |
| 3% uphill | **154%** |

### Key properties
- **Distance independent** — same slope gives same pace % at any putt length
- **Linear** — every 1% change in slope = ±18% pace change
- **Only up/downhill component used** — side slope does not affect pace

### Verification against data
| Length | Side % | Up/Down % | Formula | Actual |
|---|---|---|---|---|
| 9 ft | 1.6% | 1.2% up | 100 + (1.2 × 18) = **121.6%** | 121% ✅ |
| 8 ft | 2.0% | 0.1% up | 100 + (0.1 × 18) = **101.8%** | 103% ✅ |
| 38 ft | 2.0% | 2.4% down | 100 − (2.4 × 18) = **56.8%** | 58% ✅ |
| 8 ft | 2.5% | 1.9% down | 100 − (1.9 × 18) = **65.8%** | 66% ✅ |
| 10 ft | 0% | 2.0% down | 100 − (2.0 × 18) = **64%** | 65% ✅ |
| 10 ft | 0% | 1.0% up | 100 + (1.0 × 18) = **118%** | 118% ✅ |
| 10 ft | 0% | 3.0% up | 100 + (3.0 × 18) = **154%** | 154% ✅ |
| 6 ft | 0.7% | 0.9% up | 100 + (0.9 × 18) = **116.2%** | 116% ✅ |
| 10 ft | 1.0% | 2.6% up | 100 + (2.6 × 18) = **146.8%** | 147% ✅ |
| 15 ft | 1.5% | 2.3% down | 100 − (2.3 × 18) = **58.6%** | 60% ✅ |
| 4 ft | 3.7% | 0.2% down | 100 − (0.2 × 18) = **96.4%** | 97% ✅ |

---

## Summary

| Formula | Expression |
|---|---|
| Combined slope | `√(side² + updown²)` |
| Aim base | `(paces × 2 − 1) × side %` |
| Downhill aim adj. | `× (1 + 0.05 × downhill %)` |
| Uphill aim adj. | `× (1 − 0.05 × uphill %)` |
| Speed adj. | `× (1 + 0.10 × (stimp − 10))` |
| Pace (uphill) | `100 + (uphill % × 18)` |
| Pace (downhill) | `100 − (downhill % × 18)` |
