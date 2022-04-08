<script setup>
import { onMounted, watch } from "vue";
import { extent } from "d3-array";
import { axisBottom, axisLeft } from "d3-axis";
import { scaleLinear } from "d3-scale";
import { select } from "d3-selection";

const props = defineProps({
  points: Array,
  labelX: String,
  labelY: String,
});

// D3 needs to select the chart SVG DOM element. So each one needs a unique ID.
const chartId = `chart-${crypto.randomUUID()}`;

const width = 500;
const height = 500;

// These variables need to be in this upper scope as they're used at mount time
// and run time.
let chart;
let x;
let y;
let xAxis;
let yAxis;

// All the chart pieces that change when data changes
const plot = () => {
  // Domain of x and y scales change with data. Bounded by min and max.
  x.domain(extent(props.points, d => d.x));
  y.domain(extent(props.points, d => d.y));

  // Axis changes when domain changes
  xAxis.call(axisBottom(x));
  yAxis.call(axisLeft(y));

  // Join data points with <g> elements. Each contains a single <circle> that is
  // translated to the corresponding position on the chart
  chart.selectAll(".point")
    .data(props.points)
    .join("g")
    .attr("class", "point")
    .attr("transform", d => `translate(${x(d.x)} ${y(d.y)})`)
    .append("circle")
    .attr("r", 5);
};

// All the chart pieces that don't change in response to data changes
onMounted(() => {
  // Select the chart DOM element by its unique ID
  const svg = select(`#${chartId}`);

  // Leave room for axis and labels
  const margin = { top: 20, right: 20, bottom: 50, left: 50 };
  chart = svg.append("g")
    .attr("transform", `translate(${margin.left} ${margin.top})`);

  // The range of x and y scales are determined by dimensions of the <svg>
  x = scaleLinear().range([0, width - margin.left - margin.right]);
  y = scaleLinear().range([height - margin.top - margin.bottom, 0]);

  // Axes need a <g> to bind to. These are places and translated now. Axes are
  // actually called later once data is loaded.
  xAxis = chart.append("g")
    .attr(
      "transform",
      `translate(0 ${height - margin.top - margin.bottom})`,
    );

  yAxis = chart.append("g");

  // Axis labels. Using text from props
  chart.append("text")
    .attr("font-size", 10)
    .attr("text-anchor", "middle")
    .attr("x", (width - margin.left - margin.right) / 2)
    .attr("y", height - margin.top - margin.bottom + 40)
    .text(props.labelX);

  chart.append("text")
    .attr("font-size", 10)
    .attr("text-anchor", "middle")
    .attr("transform", "rotate(-90)")
    .attr("x", 0 - ((height - margin.top - margin.bottom) / 2))
    .attr("y", -40)
    .text(props.labelY);

  // Now that all non-changing chart pieces are set up, load data and display
  // the initial state of the chart.
  plot();
});

// Watch for changes in the data points. Plot again when a change occurs
watch(() => props.points, plot, { deep: true });
</script>

<template>
  <svg :id="chartId" :viewBox="`0 0 ${width} ${height}`"></svg>
</template>
