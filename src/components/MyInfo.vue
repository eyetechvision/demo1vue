<template>
  <div>
    <h2>Patient</h2>
    <table>
      <tr v-for="(value, key) in patient" :key="key">
        <td>{{ key }}</td>
        <td>{{ value }}</td>
      </tr>
    </table>

    <h2>Medical Records</h2>
    <table>
      <tr v-for="(record, index) in medical_records" :key="index">
        <td>
          <table>
            <tr v-for="(value, key) in record" :key="key">
              <td>{{ key }}: {{ value }}</td>
            </tr>
          </table>
        </td>
      </tr>
    </table>
  </div>
</template>

<script>
export default {
  props: ['patient', 'medical_records']
};
</script>