# Scenario 1: Starlink-Scale Constellation

## Economic Impact of Collision Avoidance for Large LEO Constellations

### Maneuvering Costs ($27.4M annually)
Annual maneuver cost = Maneuvers per day × Days per year × Cost per maneuver  
= 75 maneuvers/day × 365 days × $1,000 (median estimate)  
= $27,375,000 ≈ $27.4M

### Expected Losses Avoided ($106.2M annually)
Expected loss avoided = High-risk events × Risk reduction % × Collision cost × Average collision probability  
= 1,000 events/year × 0.9 × $1,180M × 1e-4  
= $106,200,000

### Net Benefit ($78.8M annually)
Net benefit = Expected losses avoided − Maneuvering costs  
= $106.2M − $27.4M  
= $78.8M

## Key Parameter Sources

- **75 maneuvers/day**: Observed from Starlink FCC filings (~5,000 satellites)
- **$1,000 per maneuver**: Median estimate from propellant cost model
- **1,000 high-risk events/year**: Derived from CDM filtering (CP > 1e-4, MD < 200 m)
- **90% risk reduction**: Assumed maneuver effectiveness
- **$1,180M collision cost**: Median value from SpaceNav environmental burden studies
- **1e-4 CP**: Average collision probability for high-risk events

## Key Assumptions
1. 90% of high-risk conjunctions are successfully mitigated
2. Collision cost uses median literature values ($580M–$2.3B range)
3. High-risk count extrapolated from 3-month CDM data
4. Excludes regulatory penalties, reputation damage, insurance effects, and mission-life reduction

## Conclusion
For a Starlink-scale constellation operating in dense LEO (480–550 km), active collision avoidance delivers roughly a **3:1 return on investment**, saving ~$106M annually at a cost of ~$27M.
