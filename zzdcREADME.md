# ⬡ Zero Debris Compliance Checker

A self-contained, rule-based spacecraft and mission compliance checker built against current ESA, EU, and Japanese space debris mitigation regulations. No API, no server, no dependencies — open the HTML file in any browser and it works.

---

## What It Does

The tool takes a mission design profile as input (orbit, mass, disposal strategy, manoeuvring capability, operational organisation, etc.) and scores it against a deterministic rule engine derived directly from the regulatory documents listed below. It produces:

- An **overall compliance score** (0–100)
- A **risk classification** (Medium / High / Very High per ESSB-ST-U-007)
- A **pass/partial/fail verdict** per category
- A **per-rule breakdown** showing exactly which check passed or failed and why
- A **gap list** of non-compliances with regulatory references
- **Actionable recommendations** for each gap
- A list of **formal deviations or waivers** that would be required

---

## Regulatory References

The rule engine is built from the following documents. The version encoded is noted — check this whenever regulations are updated.

| Document | Version Encoded | Key Areas Covered |
|---|---|---|
| **ESA Space Debris Mitigation Policy** `ESA/ADMIN/IPOL(2023)1` | 2023 | Applicability scope, SDMP requirement, waiver/deviation process, SDMA Board |
| **ESA Space Debris Mitigation Requirements** `ESSB-ST-U-007` | Issue 1, Rev 1 — October 2025 | LEO 5-year rule, PSD ≥90%, collision probability thresholds, passivation, DfD, DfR, constellation rules, GEO graveyard, lunar orbit |
| **ESA Space Debris Mitigation Compliance Verification Guidelines** `ESSB-HB-U-002` | Issue 3.1 — September 2025 | Verification methodologies, phase-by-phase compliance |
| **EU Space Act (Draft)** | Draft — June 2025 | Trackability, life cycle assessment, cybersecurity, market access. Application from 1 January 2030. |
| **Zero Debris Charter** | 2023 | Net-zero debris by 2030, responsible operator standards, technology advancement |
| **Japan Cabinet Office Guidelines for Collision Avoidance** `guidelines_ca` | 27 February 2025 | COLA organisation, satellite radar trackability (size thresholds), orbit selection, autonomous manoeuvre override, anomaly reporting, data sharing |
| **JAXA Standard for Spacecraft Collision Risk Management** `JMR-016(E)` | December 2022 | Three-level risk escalation (MONITOR/URGENT/CRITICAL), Pc thresholds, COLA team structure, collision avoidance management plan, propellant sizing for COLA, post-TCA monitoring |
| **UN Long-term Sustainability Guidelines** `UN LTS` | 2019 | Background reference for international best practices |
| **IADC Space Debris Mitigation Guidelines** | Rev 1 2007 | Historical baseline for 25-year rule context |

> **Note on Japan/JAXA documents:** These apply directly to JAXA-operated and Japanese-licensed satellites. They are included because they represent mature, detailed operational best practices — particularly around COLA operations organisation — that are increasingly adopted internationally. The Japan CA Guidelines explicitly reference NASA, ESA, CNES, and IOAG practices as sources, making them broadly applicable as a benchmark. For non-Japanese missions, rules c6–c14 are treated as best-practice indicators rather than hard regulatory requirements.

---

## File Structure

```
zero-debris-checker-v3.html   ← full tool, rename to index.html for GitHub Pages
README.md                      ← this file
CHANGELOG.md                   ← track regulation updates (create this yourself)
```

---

## How the Scoring Works

The overall score is a **weighted average** of 6 category scores:

| Category | Weight | Rules | Primary Regulation |
|---|---|---|---|
| Post-Mission Disposal | 25% | d1–d6 | ESSB-ST-U-007 §5 |
| Collision Avoidance & STM | 20% | c1–c14 | ESSB-ST-U-007 §7, Japan CA Guidelines, JMR-016 |
| Passivation & Safety | 20% | p1–p4 | ESSB-ST-U-007 §4 & §5.5 |
| Design & Re-entry | 15% | g1–g3 | ESSB-ST-U-007 §5.5–5.6 |
| EU Space Act Alignment | 10% | e1–e4 | EU Space Act Draft 2025 |
| Zero Debris Charter | 10% | z1–z3 | Zero Debris Charter |

Each individual rule returns one of:

| Verdict | Points |
|---|---|
| PASS | 100 |
| PARTIAL | 50 |
| FAIL | 0 |
| N/A (rule not applicable) | Excluded from average |

Category score = weighted average of its rules. Overall score = weighted average of categories.

---

## JAXA JMR-016 Collision Risk Levels

The tool checks whether your operational procedures implement the JMR-016 three-level escalation system. Levels are defined by **two parameters: Pc and time remaining to TCA**:

| Level | Name | Pc Threshold | Time Window | Required Action |
|---|---|---|---|---|
| **1** | MONITOR | Pc ≥ 1×10⁻⁵ | 5 days before TCA | Watch brief; CA team reports to COLA team and CMO |
| **2** | URGENT | Pc ≥ 1×10⁻⁴ (≤2 days) or ≥ 1×10⁻⁵ (>2 days) | Primary decision time onward | Manoeuvre prepared; mandatory if Pc ≥ 1×10⁻³ |
| **3** | CRITICAL | Pc ≥ 1×10⁻³ AND manoeuvre not executable | Within 2 days TCA | Full crisis response; CMO activated |

---

## Where to Make Changes

All rules, thresholds, weights, and references live in the `<script>` section inside two arrays: `RULES` and `CATEGORIES`. Nothing else needs to change.

### Changing a Numerical Threshold

Find the rule by its `id` and edit the `check()` function. Example — LEO decay tightens from 5 to 3 years:

```javascript
// Rule id: d1
check: (f) => {
  const d = parseFloat(f.decay);
  if (d <= 3) return {s:'pass',    note:`${d} yr — meets new 3-year rule`};
  if (d <= 5) return {s:'partial', note:`${d} yr — meets old rule but not new 3-year rule`};
  return      {s:'fail',           note:`${d} yr — non-compliant`};
}
```

### Updating a Regulatory Reference

```javascript
{ id:'d1', ...
  ref: 'ESSB-ST-U-007 Issue 2 §5.2',   // update when new issue published
}
```

### Changing a Category Weight

Find the category in `CATEGORIES` and update `weight`. Weights should sum to 100:

```javascript
const CATEGORIES = [
  { id:'disposal',    icon:'🗑', name:'Post-Mission Disposal',     weight: 25 },
  { id:'cola',        icon:'🛰', name:'Collision Avoidance & STM', weight: 20 },
  { id:'passivation', icon:'🔒', name:'Passivation & Safety',      weight: 20 },
  { id:'design',      icon:'🔧', name:'Design & Re-entry',         weight: 15 },
  { id:'eu',          icon:'🇪🇺', name:'EU Space Act Alignment',   weight: 10 }, // increase to 20 post-2030
  { id:'zd',          icon:'🌍', name:'Zero Debris Charter',       weight: 10 },
];
```

### Adding a New Rule

Add an object to the `RULES` array. It will automatically appear in the checker engine, Rules Explorer tab, and Scoring Logic tab — no other changes needed:

```javascript
{ id:'c15',           // unique id — use category prefix + next number
  cat:'cola',         // must match: disposal | cola | passivation | design | eu | zd
  icon:'🔭',
  weight: 2,          // 1–5 scale, relative importance within category
  name:'Dark & Quiet Skies — brightness limit',
  threshold:'Albedo < 0.1 in visible band',
  ref:'ESSB-ST-U-007 §9 (future provision)',
  applies:'LEO constellations > 25 spacecraft',
  check: (f) => {
    const n = parseInt(f.num_sats) || 1;
    if (n < 25 || f.orbit !== 'leo') return null; // null = N/A, excluded from scoring
    return {s:'partial', note:'Dark & Quiet Skies compliance not yet assessed'};
  }
},
```

Then add a recommendation in `getRecommendation()`:

```javascript
'c15': `Assess satellite brightness. Target albedo < 0.1. Consider darkening treatments or sun-shade design.`,
```

### Adding a New Form Field

1. Add the HTML `<select>` or `<input>` to the relevant form section
2. Add the field to the `f` object in `runCheck()`:
   ```javascript
   my_new_field: gv('f_my_new_field'),
   ```
3. Reference `f.my_new_field` in your rule's `check()` function

---

## Form Field Reference

| Field | Type | Description |
|---|---|---|
| `f.name` | string | Mission name |
| `f.operator` | string | `esa` · `eu_agency` · `eu_commercial` · `non_eu` · `university` · `defence` |
| `f.num_sats` | string (→int) | Number of spacecraft — ≥ 25 triggers constellation rules |
| `f.orbit` | string | `leo` · `meo` · `geo` · `heo` · `lunar` · `interplanetary` |
| `f.altitude` | string (→float) | Mean altitude in km |
| `f.mass` | string (→float) | Spacecraft mass in kg |
| `f.lifetime` | string (→float) | Design lifetime in years |
| `f.disposal` | string | `controlled_reentry` · `natural_decay` · `graveyard` · `self_disposal` · `none` · `tbd` |
| `f.decay` | string (→float) | Natural orbital decay after EoL in years |
| `f.psd` | string (→float) | Probability of Successful Disposal in % |
| `f.passivation` | string | `none` · `partial` · `full` · `verified` |
| `f.proppass` | string | `no_propulsion` · `not_planned` · `planned` · `verified` |
| `f.manoeuvre` | string | `none` · `limited` · `cola` · `full` |
| `f.data_sharing` | string | `none` · `passive` · `active` · `full_stm` |
| `f.screening` | string | `none` · `monthly` · `weekly` · `daily` |
| `f.cola_threshold` | string | `none` · `1e3` · `1e4` · `1e5` |
| `f.cola_plan` | string | `none` · `draft` · `approved` — Collision Avoidance Management Plan |
| `f.cola_team` | string | `none` · `informal` · `formal` — dedicated COLA organisation |
| `f.cola_propellant` | string | `none` · `partial` · `sized` — propellant margin reserved for COLA |
| `f.radar_track` | string | `below_threshold` · `beacon_only` · `compliant` — radar trackability |
| `f.auto_override` | string | `no_autonomous` · `no_override` · `override_capable` · `pre_notified` |
| `f.anomaly_report` | string | `none` · `internal` · `external` · `full` |
| `f.orbit_selection` | string | `none` · `partial` · `documented` — orbit rationale documentation |
| `f.post_tca` | string | `none` · `informal` · `formal` — post-TCA status monitoring |
| `f.risk_levels` | string | `none` · `basic` · `tiered` — JMR-016 escalation levels |
| `f.dfd` | string | `none` · `partial` · `full` · `controlled` |
| `f.casualty` | string | `not_assessed` · `compliant` · `marginal` · `non_compliant` |
| `f.dfr` | string | `none` · `considered` · `implemented` |
| `f.sdmp` | string | `none` · `draft` · `approved` |
| `f.trackability` | string | `none` · `partial` · `full` — EU Space Act trackability |
| `f.lca` | string | `none` · `planned` · `completed` |
| `f.cyber` | string | `none` · `partial` · `full` |

Return `null` from `check()` when the rule does not apply — excluded from scoring entirely.

---

## Recommended CHANGELOG.md Format

```markdown
# Changelog

## [3.0.0] — 2026-02-27
### Added
- 9 new COLA rules (c6–c14) from Japan CA Guidelines (Feb 2025) and JAXA JMR-016 (Dec 2022)
- New form fields: COLA plan, COLA team, COLA propellant, radar trackability,
  autonomous manoeuvre override, anomaly reporting, orbit selection rationale,
  post-TCA monitoring, three-level risk escalation
- JAXA JMR-016 risk level table on Scoring Logic page
- Japan/JAXA framework pills and Rules Explorer sidebar note

## [2.0.0] — 2026-02-26
### Initial release
- ESA ESSB-ST-U-007 Issue 1 Rev 1 (Oct 2025)
- EU Space Act draft (June 2025)
- ESA/ADMIN/IPOL(2023)1
- Zero Debris Charter
```

---

## Limitations

- **Rule-based, not AI-assisted.** Deterministic if/else logic. Cannot reason about edge cases beyond what is encoded.
- **Not a substitute for formal review.** Use as early-stage screening only. All ESA missions require independent assessment by ESA's Independent Safety Office (TEC-QI) and SDMA Board review for deviations.
- **Japan/JAXA rules (c6–c14) are best-practice benchmarks** for non-Japanese missions. For JAXA-licensed missions they are mandatory.
- **EU Space Act rules are indicative** — Act in draft as of encoding date. Verify current legislative status.
- **No orbital mechanics simulation.** Decay time must be entered manually. Use ESA DRAMA for accurate estimation.
- **Constellation inter-satellite collision probability** requires dedicated simulation — flagged for manual verification.

---

## Regulatory Resources

| Resource | Where |
|---|---|
| ESA Space Debris Mitigation Policy | `technology.esa.int` |
| ESSB-ST-U-007 Requirements | `esamultimedia.esa.int` |
| ESA DRAMA Tool | `sdup.esoc.esa.int` |
| Zero Debris Charter | `esa.int/zero-debris` |
| Japan Cabinet Office Space Policy | `www8.cao.go.jp/space/english` |
| JAXA Space Tracking and Communication Centre | `www.jaxa.jp/projects/stcc` |
| NASA CARA Handbook | search "NASA Spacecraft Conjunction Assessment Handbook" |
| ESA Space Debris team | `space.debris.mitigation@esa.int` |

---

*Rules encoded as of: February 2026*
*ESA: ESSB-ST-U-007 Issue 1 Rev 1 (Oct 2025) · Japan CA Guidelines (Feb 2025) · JAXA JMR-016 (Dec 2022)*
