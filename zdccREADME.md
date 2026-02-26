# ⬡ Zero Debris Compliance Checker

A self-contained, rule-based spacecraft and mission compliance checker built against current ESA and EU space debris mitigation regulations. No API, no server, no dependencies — open the HTML file in any browser and it works.

---

## What It Does

The tool takes a mission design profile as input (orbit, mass, disposal strategy, manoeuvring capability, etc.) and scores it against a deterministic rule engine derived directly from the regulatory documents listed below. It produces:

- An **overall compliance score** (0–100)
- A **risk classification** (Medium / High / Very High per ESSB-ST-U-007)
- A **pass/partial/fail verdict** per category
- A **per-rule breakdown** showing exactly which check passed or failed and why
- A **gap list** of non-compliances with regulatory references
- **Actionable recommendations** for each gap
- A list of **formal deviations or waivers** that would be required

---

## Regulatory References

The rule engine is built from the following documents. The version encoded in the tool is noted for each — this is important to check when regulations are updated.

| Document | Version Encoded | Key Areas Covered |
|---|---|---|
| **ESA Space Debris Mitigation Policy** `ESA/ADMIN/IPOL(2023)1` | 2023 | Applicability scope, SDMP requirement, waiver/deviation process, SDMA Board |
| **ESA Space Debris Mitigation Requirements** `ESSB-ST-U-007` | Issue 1, Revision 1 — October 2025 | LEO 5-year rule, PSD ≥90%, collision probability thresholds, passivation, DfD, DfR, constellation rules, GEO graveyard, lunar orbit provisions |
| **ESA Space Debris Mitigation Compliance Verification Guidelines** `ESSB-HB-U-002` | Issue 3.1 — September 2025 | Verification methodologies, phase-by-phase compliance expectations |
| **EU Space Act (Draft)** | Draft — June 2025 | Trackability, life cycle assessment, cybersecurity, market access for non-EU operators. Application from 1 January 2030. |
| **Zero Debris Charter** | 2023 | Net-zero debris contribution by 2030, responsible operator standards, technology advancement |
| **UN Long-term Sustainability Guidelines** `UN LTS` | 2019 | Background reference for international best practices |
| **IADC Space Debris Mitigation Guidelines** | 2002 / Rev 1 2007 | Historical baseline for 25-year rule context |

> **Note:** The EU Space Act is still in draft legislative procedure as of the tool's last update. Weights for EU Space Act rules are intentionally lower (10%) to avoid penalising pre-2030 missions. Increase the weight once the Act is formally adopted.

---

## File Structure

The entire tool is a single HTML file:

```
zero-debris-checker-v2.html   ← everything lives here
README.md                      ← this file
CHANGELOG.md                   ← track regulation updates (create this yourself)
```

For GitHub Pages deployment, rename the HTML file to `index.html` and enable Pages in your repository settings. The tool will be live at `https://<your-username>.github.io/<repo-name>/`.

---

## How the Scoring Works

The overall score is a **weighted average** of 6 category scores:

| Category | Weight | Primary Regulation |
|---|---|---|
| Post-Mission Disposal | 25% | ESSB-ST-U-007 §5 |
| Collision Avoidance & STM | 20% | ESSB-ST-U-007 §7 |
| Passivation & Safety | 20% | ESSB-ST-U-007 §4, §5.5 |
| Design & Re-entry | 15% | ESSB-ST-U-007 §5.5–5.6 |
| EU Space Act Alignment | 10% | EU Space Act Draft 2025 |
| Zero Debris Charter | 10% | Zero Debris Charter |

Each individual rule returns one of three verdicts:

| Verdict | Points |
|---|---|
| PASS | 100 |
| PARTIAL | 50 |
| FAIL | 0 |
| N/A (rule not applicable to this mission) | Excluded from average |

Category score = weighted average of its rules. Overall score = weighted average of categories.

---

## Where to Make Changes

All rules, thresholds, weights, and references live in the `<script>` section of the HTML file inside two arrays: `RULES` and `CATEGORIES`. You do not need to touch anything else.

### Changing a Numerical Threshold

Find the rule by its `id` and edit the `check()` function. Example — if the LEO decay rule tightens from 5 years to 3 years:

```javascript
// Rule id: d1 — LEO Post-Mission Disposal
check: (f) => {
  if (!['leo'].includes(f.orbit)) return null;
  const d = parseFloat(f.decay);
  if (isNaN(d)) return {s:'partial', note:'Decay time not provided'};
  if (d <= 3) return {s:'pass',    note:`${d} yr — meets new 3-year rule`};   // changed from 5
  if (d <= 5) return {s:'partial', note:`${d} yr — meets old 5-year rule but not new 3-year rule`};
  return      {s:'fail',           note:`${d} yr — non-compliant`};
}
```

### Updating a Regulatory Reference

Find the rule and update its `ref` field:

```javascript
{ id:'d1', ...
  ref: 'ESSB-ST-U-007 Issue 2 §5.2',   // update version when new issue published
  ...
}
```

### Changing a Category Weight

Find the category in the `CATEGORIES` array and update its `weight` value. Weights are in percentage points and should sum to 100:

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

Add a new object to the `RULES` array. The rule will automatically appear in the checker engine, the Rules Explorer tab, and the Scoring Logic tab — no other changes needed.

```javascript
{ id:'g4',              // unique id — use cat prefix + number
  cat:'design',         // must match a category id: disposal|cola|passivation|design|eu|zd
  icon:'🔭',
  weight: 2,            // 1–5 scale, relative importance within the category
  name:'Brightness limit for astronomical interference (Dark & Quiet Skies)',
  threshold:'Albedo < 0.1 in visible band',
  ref:'ESSB-ST-U-007 §9 (future provision)',
  applies:'LEO constellations with > 25 spacecraft',
  check: (f) => {
    // f contains all form values — see full list below
    const n = parseInt(f.num_sats) || 1;
    if (n < 25 || f.orbit !== 'leo') return null; // null = N/A, excluded from scoring
    // return one of:
    return {s:'pass',    note:'Brightness compliance confirmed'};
    return {s:'partial', note:'Brightness not yet assessed'};
    return {s:'fail',    note:'No brightness mitigation planned'};
  }
},
```

### Adding a New Category

Add to both `CATEGORIES` and make sure your new rules reference the new `cat` id. Adjust all weights so they still sum to 100.

---

## Form Field Reference (`f` object in check functions)

When writing rule `check()` functions, the `f` parameter contains all form values:

| Field | Type | Description |
|---|---|---|
| `f.name` | string | Mission name |
| `f.operator` | string | `esa`, `eu_agency`, `eu_commercial`, `non_eu`, `university`, `defence` |
| `f.num_sats` | string (parse to int) | Number of spacecraft — ≥ 25 triggers constellation rules |
| `f.orbit` | string | `leo`, `meo`, `geo`, `heo`, `lunar`, `interplanetary` |
| `f.altitude` | string (parse to float) | Mean altitude in km |
| `f.mass` | string (parse to float) | Spacecraft mass in kg |
| `f.lifetime` | string (parse to float) | Design lifetime in years |
| `f.disposal` | string | `controlled_reentry`, `natural_decay`, `graveyard`, `self_disposal`, `none`, `tbd` |
| `f.decay` | string (parse to float) | Natural orbital decay after EoL in years |
| `f.psd` | string (parse to float) | Probability of Successful Disposal in % |
| `f.passivation` | string | `none`, `partial`, `full`, `verified` |
| `f.proppass` | string | `no_propulsion`, `not_planned`, `planned`, `verified` |
| `f.manoeuvre` | string | `none`, `limited`, `cola`, `full` |
| `f.data_sharing` | string | `none`, `passive`, `active`, `full_stm` |
| `f.screening` | string | `none`, `monthly`, `weekly`, `daily` |
| `f.cola_threshold` | string | `none`, `1e3`, `1e4`, `1e5` |
| `f.dfd` | string | `none`, `partial`, `full`, `controlled` |
| `f.casualty` | string | `not_assessed`, `compliant`, `marginal`, `non_compliant` |
| `f.dfr` | string | `none`, `considered`, `implemented` |
| `f.sdmp` | string | `none`, `draft`, `approved` |
| `f.trackability` | string | `none`, `partial`, `full` |
| `f.lca` | string | `none`, `planned`, `completed` |
| `f.cyber` | string | `none`, `partial`, `full` |

Return `null` from a `check()` function when the rule does not apply to the mission (it will be excluded from scoring entirely).

---

## Recommended CHANGELOG.md Format

Create a `CHANGELOG.md` in the same repository to track when rules are updated and why:

```markdown
# Changelog

## [2.1.0] — YYYY-MM-DD
### Changed
- Rule d1: Updated LEO decay threshold from 5yr to 3yr following ESSB-ST-U-007 Issue 2 publication
- Category weight: EU Space Act increased from 10% to 20% following formal adoption

## [2.0.0] — 2026-02-26
### Initial release
- Rules based on ESSB-ST-U-007 Issue 1 Rev 1 (October 2025)
- EU Space Act draft (June 2025)
- ESA/ADMIN/IPOL(2023)1
```

---

## Limitations

- **Rule-based, not AI-assisted.** The tool applies deterministic if/else logic. It cannot reason about mission-specific edge cases or provide contextual nuance beyond what is encoded in the rules.
- **Not a substitute for formal review.** Results should be used as an early-stage screening tool. All ESA missions require independent assessment by the ESA Independent Safety Office (TEC-QI) and formal SDMA Board review for deviations.
- **EU Space Act rules are indicative.** The Act was in draft legislative procedure as of the tool's encoding date. Verify current legislative status before using EU compliance scores for formal purposes.
- **No orbital mechanics simulation.** Decay time must be entered by the user — the tool does not compute it from orbital parameters. Use ESA's DRAMA tool for accurate decay estimation.
- **Constellation inter-satellite rules not fully encoded.** Rules for collision probability between constellation satellites and specific frequency coordination requirements require dedicated simulation and are flagged as manual checks.

---

## Contact & Regulatory Resources

| Resource | Link |
|---|---|
| ESA Space Debris Mitigation Policy | `technology.esa.int` |
| ESSB-ST-U-007 Requirements | `esamultimedia.esa.int` |
| ESA DRAMA Tool | `sdup.esoc.esa.int` |
| Zero Debris Charter | `esa.int/zero-debris` |
| ESA Space Debris team | `space.debris.mitigation@esa.int` |

---

*Rules encoded as of: February 2026 — ESSB-ST-U-007 Issue 1 Rev 1 (October 2025)*
