# Satellite Constellations Overview - Developer Readme

This document describes the `index.html` page, its structure, and JavaScript logic in pseudo-code for developers to understand and continue development.

---

## Table of Contents

- [Page Structure](#page-structure)
- [JavaScript Logic (Pseudo-Code)](#javascript-logic-pseudo-code)
- [Developer Notes](#developer-notes)
- [Folder Structure Suggestion](#folder-structure-suggestion)

---

## Page Structure

### Top Banner (`#top-banner`)

- **Title text:** "Constellation Visualization"
- **Dropdown (`<select>`)** to choose satellite constellation:
  - Options include:
    - "All Active Satellites"
    - "OneWeb"
    - "Kuiper"
    - "Starlink"
    - "Iridium-NEXT"
    - "Hulianwang"
    - "Qianfan"
    - "Planet"
- **Orbit Analysis Button:**
  - Navigates to the selected constellation folder's `index2.html`
  - Disabled if "All Active Satellites" is selected

### Main Chart Area (`#chart`)

- Placeholder div for rendering the 3D globe and satellites
- Uses `globe.gl` or similar WebGL library
- Satellite positions updated in real time based on TLE data

---

## JavaScript Logic (Pseudo-Code)

1. Get references to the dropdown and the Orbit Analysis button
2. `updateOrbitButton()` function:
   - If selected value is "all":
     - Disable the Orbit Analysis button
     - Reduce its opacity to indicate disabled state
   - Else:
     - Enable the button
     - Restore normal opacity
3. On page load:
   - Call `updateOrbitButton()` to set initial button state
4. On dropdown change event:
   - Call `updateOrbitButton()` to dynamically update the button
5. `goToOrbitAnalysis()` function:
   - Get selected constellation folder from dropdown
   - If selected is NOT "all":
     - Redirect browser to `<folder>/index2.html`
   - Else:
     - Do nothing (button is disabled)

---

## Developer Notes

- **Rendering:** Use `globe.gl` or another WebGL library to render satellites in the `#chart` div
- **Data Source:** Satellite data comes from TLE sources such as NORAD or Celestrak
- **Button Logic:** Orbit Analysis button state prevents invalid navigation
- **Extensibility:** Dropdown can be easily expanded to include new constellations
- **Future Improvements:**
  - Allow multiple constellation selection
  - Add real-time satellite updates
  - Persist user settings across sessions

---

## Folder Structure Suggestion

- `root/`
  - `index.html` — Main visualization page
  - `readme.md` — Developer guide (this file)
  - `<constellation_name>/`
    - `index2.html` — Orbit Analysis page for that constellation
  - `assets/` — Images, scripts, and stylesheets

This structure helps developers quickly locate the main visualization, per-constellation pages, and assets.
