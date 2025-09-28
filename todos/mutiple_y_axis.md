# Multi-Y-Axis Implementation Plan for OctoPrint-PlotlyTempGraph

## Overview

```
// In the layout configuration
yaxis: {
  title: 'Temperature (°C)',
  side: 'left'
},
yaxis2: {
  title: 'AQI (1-5)',
  overlaying: 'y',
  side: 'right',
  range: [0, 6]  // Fixed range for AQI
},
yaxis3: {
  title: 'TVOC (ppb) / eCO2 (ppm)',
  overlaying: 'y',
  side: 'right',
  position: 0.85  // Offset from right edge
}

// For each trace, specify which axis:
{
  name: 'AQI',
  y: aqiData,
  yaxis: 'y2'
},
{
  name: 'eCO2',
  y: co2Data,
  yaxis: 'y3'
}
```

## 1. Update Plotly Layout Configuration
- Edit `octoprint_plotlytempgraph/static/js/plotlytempgraph.js`.
- Add definitions for each required y-axis:
  - `yaxis`: Temperature (°C), left side.
  - `yaxis2`: AQI (1-5), right side, overlaying `yaxis`.
  - `yaxis3`: TVOC (ppb) / eCO2 (ppm), right side, offset with `position`.

## 2. Modify Trace Creation
- For each data series, set the `yaxis` property:
  - Temperature traces: `yaxis: 'y'`
  - AQI traces: `yaxis: 'y2'`
  - TVOC/eCO2 traces: `yaxis: 'y3'`
- Ensure trace names and colors are distinct for clarity.

## 3. Adjust Data Handling
- Update data parsing and preparation to support new value types.
- Ensure each value type is mapped to the correct axis and trace.

## 4. Update Templates (if needed)
- Edit `octoprint_plotlytempgraph/templates/plotlytempgraph.jinja2`.
- Update axis labels and legend entries to reflect new axes.

## 5. Test and Validate
- Use example datasets with all value types.
- Render the graph and verify:
  - Each value type appears on its designated axis.
  - Axis scaling and overlaying work as intended.
  - Legends and labels are correct.

## 6. Refactor and Document
- Clean up code for maintainability.
- Add comments explaining axis configuration.
- Update documentation to describe new axes and usage.