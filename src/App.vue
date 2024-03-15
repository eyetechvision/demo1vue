<template>
  <div :class="layoutClass">
    <MyInfo :patient="patient" :medical_records="medical_records" />
    <MyChart :futureRecords="future_records" />
    <MyForm @updateData="updateData" />
  </div>
</template>

<script>
import MyInfo from './components/MyInfo.vue';
import MyChart from './components/MyChart.vue';
import MyForm from './components/MyForm.vue';

export default {
  data() {
    return {
      future_records: [],
      medical_records: [],
      patient: {}
    };
  },
  components: {
    MyInfo,
    MyChart,
    MyForm
  },
  computed: {
    layoutClass() {
      return window.innerWidth > 600 ? 'desktop-layout' : 'mobile-layout';
    },
  },
  mounted() {
    window.addEventListener('resize', this.handleResize);
    this.handleResize();
  },
  beforeUnmount() {
    window.removeEventListener('resize', this.handleResize);
  },
  methods: {
    updateData(newData) {
      this.future_records = newData.future_records;
      this.patient = newData.patient;
      this.medical_records = newData.medical_records;
    },
    handleResize() {
      this.layoutClass = window.innerWidth > 600 ? 'desktop-layout' : 'mobile-layout';
    }
  }
};
</script>

<style>
.desktop-layout {
  display: flex;
}

.mobile-layout {
  display: block;
}
</style>