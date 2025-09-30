<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Satellite Constellations Overview – Developer Readme</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #121212;
      color: #f0f0f0;
      margin: 0;
      padding: 20px;
    }
    h1 { color: #1e90ff; }
    pre {
      background: #1e1e1e;
      padding: 15px;
      border-radius: 8px;
      overflow-x: auto;
    }
    p { line-height: 1.6; }
    code { color: #63b3ff; }
  </style>
</head>
<body>

<h1>Satellite Constellations Overview – Developer Guide</h1>

<p>This page describes the <code>index.html</code> page, its structure, and JavaScript logic in pseudo-code for developers to understand and continue development.</p>

<h2>Page Structure</h2>
<ul>
  <li><strong>Top Banner</strong> (<code>#top-banner</code>)
    <ul>
      <li>Title text: "Constellation Visualization"</li>
      <li>Dropdown (<code>&lt;select&gt;</code>) to choose satellite constellation:
        <ul>
          <li>Options include "All Active Satellites", "OneWeb", "Kuiper", "Starlink", etc.</li>
        </ul>
      </li>
      <li>Orbit Analysis Button (<code>Orbit Analysis</code>):
        <ul>
          <li>Navigates to selected constellation folder's <code>index2.html</code></li>
          <li>Disabled if "All Active Satellites" is selected</li>
        </ul>
      </li>
    </ul>
  </li>

  <li><strong>Main Chart Area</strong> (<code>#chart</code>)
    <ul>
      <li>Placeholder div for rendering 3D globe and satellites using <code>globe.gl</code> or similar</li>
    </ul>
  </li>
</ul>

<h2>JavaScript Logic (Pseudo-Code)</h2>
<pre>
1. Get references to:
   - <code>select</code> dropdown
   - <code>orbitBtn</code> button

2. Function: updateOrbitButton()
   - If selected value is "all":
       - Disable orbitBtn
       - Reduce opacity to indicate disabled state
   - Else:
       - Enable orbitBtn
       - Set normal opacity

3. On page load:
   - Call updateOrbitButton() to set correct button state

4. On dropdown change event:
   - Call updateOrbitButton() to update button dynamically

5. Function: goToOrbitAnalysis()
   - Get selected constellation folder from dropdown
   - If selected is NOT "all":
       - Redirect browser to <folder>/index2.html
   - Else:
       - Do nothing (button is disabled)
</pre>

<h2>Developer Notes</h2>
<ul>
  <li>Use <code>globe.gl</code> or other WebGL libraries to render satellites in <code>#chart</code></li>
  <li>Data for each constellation comes from TLE sources (NORAD/Celestrak)</li>
  <li>Button state logic ensures invalid navigation is prevented</li>
  <li>Dropdown can be expanded to include new constellations easily</li>
  <li>Future improvement: allow multiple constellation selection, real-time satellite updates, and persistent user settings</li>
</ul>

<h2>Folder Structure Suggestion</h2>
<pre>
root/
 ├─ index.html          <-- Current main visualization page
 ├─ readme.html         <-- Developer guide (this page)
 ├─ <constellation_name>/
 │    └─ index2.html    <-- Orbit Analysis page for that constellation
 └─ assets/             <-- Images, scripts, stylesheets
</pre>

<p>This readme is intended to help new developers quickly understand the page flow, UI elements, and core JavaScript behavior so they can continue development or integrate additional features.</p>

</body>
</html>
