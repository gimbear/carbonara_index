# D3.js Line Chart Tutorial

This tutorial shows how to create an interactive multi-line chart using D3.js v7. We'll build a chart that displays multiple data series with a legend and proper axes.

## Setup

First, include D3.js in your HTML:

```html
<script src="https://d3js.org/d3.v7.min.js"></script>
```

Add a container for your chart:

```html
<figure>
  <div id="chart"></div>
</figure>
```

## Load and Parse Data

```javascript
// Load JSON data
d3.json("carbonara_index.json").then(function (data) {
  // Parse dates from string format
  const parseDate = d3.timeParse("%Y-%m");
  data.forEach((d) => {
    d.date = parseDate(d.date);
  });
});
```

**Note**: This async Promise pattern creates a data context scope for the entire visualization. All D3 code must be inside the `.then()` callback to ensure data is available before creating scales or visual elements.

## Set Up Chart Dimensions

```javascript
// Define margins and dimensions
const margin = { top: 50, right: 150, bottom: 60, left: 50 };
const width = 900 - margin.left - margin.right;
const height = 500 - margin.top - margin.bottom;
```

## Create Scales

```javascript
// X scale for dates
const x = d3
  .scaleTime()
  .domain(d3.extent(data, (d) => d.date))
  .range([0, width]);

// Y scale for values
const y = d3
  .scaleLinear()
  .domain([
    90,
    d3.max(data, (d) =>
      Math.max(d.Pasta, d.Pork, d.Eggs, d["Hard Cheese"], d.Carbonara_index)
    ),
  ])
  .range([height, 0]);

// Color scale for different lines
const color = d3
  .scaleOrdinal()
  .domain(["Pasta", "Pork", "Eggs", "Hard Cheese", "Carbonara_index"])
  .range(["#FF6B35", "#F7931E", "#FFD23F", "#06FFA5", "#1B263B"]);
```

## Create SVG Container

```javascript
const svg = d3
  .select("#chart")
  .append("svg")
  .attr("width", width + margin.left + margin.right)
  .attr("height", height + margin.top + margin.bottom)
  .append("g")
  .attr("transform", `translate(${margin.left},${margin.top})`);
```

## Add Axes

```javascript
// X axis
svg
  .append("g")
  .attr("transform", `translate(0,${height})`)
  .call(d3.axisBottom(x).tickFormat(d3.timeFormat("%Y")));

// Y axis
svg.append("g").call(d3.axisLeft(y));

// Axis labels
svg
  .append("text")
  .attr("transform", "rotate(-90)")
  .attr("y", 0 - margin.left)
  .attr("x", 0 - height / 2)
  .attr("dy", "1em")
  .style("text-anchor", "middle")
  .text("Price Index");

svg
  .append("text")
  .attr("transform", `translate(${width / 2}, ${height + margin.bottom})`)
  .style("text-anchor", "middle")
  .text("Year");
```

## Create Line Generator

```javascript
const line = d3
  .line()
  .x((d) => x(d.date))
  .y((d) => y(d.value))
  .curve(d3.curveMonotoneX);
```

## Prepare Data for Lines

```javascript
const ingredients = ["Pasta", "Pork", "Eggs", "Hard Cheese", "Carbonara_index"];
const lineData = ingredients.map((ingredient) => ({
  name: ingredient,
  values: data.map((d) => ({
    date: d.date,
    value: d[ingredient],
  })),
}));
```

## Draw Lines

```javascript
const lines = svg
  .selectAll(".line")
  .data(lineData)
  .enter()
  .append("g")
  .attr("class", "line");

lines
  .append("path")
  .attr(
    "class",
    (d) =>
      `line-path line-${d.name
        .toLowerCase()
        .replace(/\s+/g, "-")
        .replace(/_/g, "-")}`
  )
  .attr("d", (d) => line(d.values))
  .style("stroke", (d) => color(d.name))
  .style("stroke-width", (d) => (d.name === "Carbonara_index" ? 3 : 1.5))
  .style("fill", "none")
  .style("opacity", 0.2)
  .style("transition", "opacity 0.3s ease");
```

## Add Legend

```javascript
const legend = svg
  .selectAll(".legend")
  .data(ingredients)
  .enter()
  .append("g")
  .attr("class", "legend")
  .attr("transform", (d, i) => `translate(40, ${20 + i * 25})`);

legend
  .append("rect")
  .attr("x", 0)
  .attr("width", 18)
  .attr("height", 18)
  .style("fill", color);

legend
  .append("text")
  .attr("x", 24)
  .attr("y", 9)
  .attr("dy", ".35em")
  .style("text-anchor", "start")
  .text((d) => d.replace("_", " "));
```

## Add Title

```javascript
svg
  .append("text")
  .attr("x", width / 2)
  .attr("y", 0 - margin.top / 2)
  .attr("text-anchor", "middle")
  .style("font-size", "16px")
  .style("font-weight", "bold")
  .text("Carbonara Ingredients Price Index (2015-2025)");
```

## Interactive Functions

```javascript
function highlightLine(lineClass) {
  // Reset all lines to low opacity
  d3.selectAll(".line-path").style("opacity", 0.2);

  // Highlight specific line
  d3.select(`.line-${lineClass}`).style("opacity", 1);
}

// Example usage:
// highlightLine('pasta'); // Highlights the pasta line
```

## Complete Example

The complete code combines all these sections within the `d3.json()` promise. The chart will display multiple time series lines with different colors, a legend, proper axes, and the ability to highlight individual lines programmatically.

## Key D3 Concepts Used

- **Scales**: `d3.scaleTime()`, `d3.scaleLinear()`, `d3.scaleOrdinal()`
- **Line Generator**: `d3.line()` with curve interpolation
- **Data Binding**: `selectAll().data().enter().append()`
- **Axes**: `d3.axisBottom()`, `d3.axisLeft()`
- **Time Parsing**: `d3.timeParse()`, `d3.timeFormat()`
- **Selections**: `d3.select()`, `d3.selectAll()`
