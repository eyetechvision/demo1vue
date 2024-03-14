<template>
  <form id="myForm" @submit.prevent="submitForm">
    <h2>Future Records</h2>
    <ul>
      <li v-for="(record, index) in future_records" :key="index">
        {{ index }}: {{ record }}
      </li>
    </ul>

    <h2>Medical Records</h2>
    <ul>
      <li v-for="(record, index) in medical_records" :key="index">
        <ul>
          <li v-for="(value, key) in record" :key="key">
            {{ key }}: {{ value }}
          </li>
        </ul>
      </li>
    </ul>

    <h2>Patient</h2>
    <ul>
      <li v-for="(value, key) in patient" :key="key">
        {{ key }}: {{ value }}
      </li>
    </ul>

    <h2>Warning Level</h2>
    <p>{{ warning_level }}</p>

    <button type="submit" id="myButton">Get API Result</button>
  </form>
  <div>
    <!-- Create a ref where the chart should be inserted -->
    <div ref="chart"></div>
  </div>
</template>

<script>
import axios from "axios";
import * as d3 from "d3";

export default {
  data() {
    console.log("start data")
    return {
      future_records: [],
      medical_records: [
      {
        "AL": "",
        "SD": "",
        "age": 0,
        "gender": "",
        "patient_id": 0,
        "reflection_S": "",
        plot: ''
      }
      ],
      patient: {
        "birthday": "T",
        "created_at": "",
        "gender": "",
        "parent_id": null,
        "patient_id": 0,
        "pid": null
      },
      warning_level: null
    };
  },
  methods: {
    async submitForm() {
      try {
        console.log('submitForm')
        var data = {
          studentid: this.studentid,
          age: this.age,
          gender: this.gender,
          AL: this.AL,
          SD: this.SD
        };
        console.log("Sending this data: ", data);

        const response = await axios.post('http://localhost:5000/predict', data, {
          headers: {
            'Content-Type': 'application/json'
          }
        });

        const responseData = response.data;

        console.log("Received this response: ", responseData);
        for (var key in responseData) {
          this[key] = responseData[key];
        }
        this.drawPlot(responseData.future_records);
      } catch (error) {
        console.error(error);
      }
    },
    drawPlot(future_records) {
      console.log('drawPlot')
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
    async loadData() {

      try {
        const response = await fetch('sample.json');
        const data = await response.json();

        // Check if the required fields exist in the JSON data
        if (data && data.patient) {
          this.future_records = data.future_records;
          this.medical_records = data.medical_records;
          this.patient = data.patient;
          this.warning_level = data.warning_level;
        } else {
          console.log('Required fields do not exist in the JSON data');
        }
      } catch (error) {
        console.log('Error while fetching and parsing the JSON data:', error);
      }
    },
    loadLocalStorage() {
      console.log('loadLocalStorage')
      for (var key in this.data) {
        var savedValue = localStorage.getItem(key);
        if (savedValue) {
          this.data[key] = savedValue;

        }
      }
    },
    saveToLocalStorage(key, value) {
      console.log('saveToLocalStorage')
      localStorage.setItem(key, value);
    }
  },
  mounted() {
    console.log('loaddata')
    this.loadData();
    this.loadLocalStorage();
  },
  watch: {
    data: {
      handler(newVal) {
        for (var key in newVal) {
          this.saveToLocalStorage(key, newVal[key]);
        }
      },
      deep: true
    }
  }
};
</script>
