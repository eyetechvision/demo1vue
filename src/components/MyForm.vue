<template>
 
  <div class="container">
    <h2>Future Records</h2>
    <ul class="column-list">
      <li v-for="(record, index) in future_records" :key="index">
        <input v-model="future_records[index]" type="text">
      </li>
    </ul>
    <div class="warning-level">
      Warning level
      <input v-model="warning_level" type="text">
    </div>
    <form id="myForm" @submit.prevent="submitForm">
    <div>
      <label for="AL">AL:</label>
      <input v-model="AL" type="text" id="AL">
    </div>
    <div>
      <label for="SD">SD:</label>
      <input v-model="SD" type="text" id="SD">
    </div>
    <div>
      <label for="age">Age:</label>
      <input v-model="age" type="text" id="age">
    </div>
    <div>
      <label for="gender">Gender:</label>
      <input v-model="gender" type="text" id="gender">
    </div>
    <div>
      <label for="studentid">Student ID:</label>
      <input v-model="studentid" type="text" id="studentid">
    </div>
    <button type="submit" id="myButton">Get API Result</button>
  </form>

  </div>

  

</template>

<script>
import axios from "axios";

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

        this.future_records = responseData.future_records;

        this.$emit('updateData', responseData);
      } catch (error) {
        console.error(error);
      }
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
