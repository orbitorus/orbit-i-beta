# Scenario 3: Delayed Maneuver Decision

## Initial Conditions
- Lead time: 48 hours to TCA
- Initial collision probability: 5e-5
- Initial miss distance: 350 m
- Status: Below immediate action threshold

---

## Option 1: Immediate Maneuver
- Cost: $1,000 (certain)
- Outcome: Eliminates conjunction, but may be unnecessary

---

## Option 2: Wait 24 Hours for Refined Data

### Outcome A: Risk Decreases (60%)
- CP < 1e-5, MD > 500 m
- Cost: $0

### Outcome B: Risk Stable (30%)
- CP ~5e-5, MD ~350 m
- Cost: $1,000

### Outcome C: Risk Increases (10%)
- CP > 1e-4, MD < 200 m
- Emergency maneuver cost: $3,000

---

## Expected Cost of Waiting
E[Cost] =  
(0.6 × $0) + (0.3 × $1,000) + (0.1 × $3,000)  
= $600

---

## Comparison
- Immediate maneuver: $1,000
- Wait-and-assess: $600  
**Savings:** $400 per event (40%)

---

## When Waiting Is Optimal
- Lead time ≥ 48 hours
- Moderate initial risk
- High orbit determination uncertainty
- Manageable emergency cost multiplier

## When Waiting Is Not Viable
- Lead time < 24 hours
- Initial CP > 1e-4 and MD < 200 m

---

## Strategic Implications
Waiting for refined data has quantifiable economic value.  
For constellations facing hundreds of conjunctions annually, this approach can save **hundreds of thousands of dollars** without increasing collision risk.
