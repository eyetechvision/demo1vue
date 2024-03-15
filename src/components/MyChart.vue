<template>
    <div>
      <!-- Create a ref where the chart should be inserted -->
      <div ref="chart"></div>
    </div>
  </template>
  
  <script>
  import * as d3 from "d3";
  
  export default {
    data() {
      return {
        future_records: [],
      };
    },
    props: ['futureRecords'],
    watch: {
      futureRecords(newRecords) {
        this.drawPlot(newRecords);
      }
    },
    methods: {
      drawPlot(future_records) {
        console.log('drawPlot')
        // Clear the existing chart
        d3.select(this.$refs.chart).selectAll("*").remove();

 

        var age = future_records.map(function(d) { return parseFloat(d[0]); });
        var mean = future_records.map(function(d) { return parseFloat(d[1]); });

        var margin = {top: 20, right: 20, bottom: 50, left: 70},
        width = 500 - margin.left - margin.right,
        height = 500 - margin.top - margin.bottom;

        var svg = d3
          .select(this.$refs.chart)
          .append("svg")
          .attr("width", width + margin.left + margin.right)
          .attr("height", height + margin.top + margin.bottom)
          .append("g")
          .attr("transform", "translate(" + margin.left + "," + margin.top + ")");


        var xScale = d3
          .scaleLinear()
          .domain([d3.min(age), d3.max(age)])
          .range([0, width]);

        var yScale = d3
          .scaleLinear()
          .domain([d3.min(mean), d3.max(mean)])
          .range([height, 0]);

        var line = d3.line()
          .x(function(d, i) { return xScale(age[i]); })
          .y(function(d) { return yScale(d); });

        svg
          .append("path")
          .datum(mean)
          .attr("fill", "none")
          .attr("stroke", "steelblue")
          .attr("stroke-width", 1.5)
          .attr("d", line);

        svg
          .append("g")
          .attr("transform", "translate(0," + height + ")")
          .call(d3.axisBottom(xScale));

        svg
          .append("g")
          .call(d3.axisLeft(yScale));
      },
    }
  }
  </script>