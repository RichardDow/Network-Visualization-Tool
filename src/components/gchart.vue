<template>
  <div>
    <b-col cols='12' class='ml-5 mr-5'>
      <b-row class='p-3'>
        <b-col cols='9'>
          <b-row>
            <GChart
              type="LineChart"
              :data="chartData"
              :options="chartOptions"
              style="width: 900px; height: 500px"
            />
          </b-row>
        </b-col>
        <b-col cols='3'>
          <b-row style='padding-top: 200px; padding-left: 50px;'>
            Difference in MTTSF (Final - Initial)
            <h1>{{ difference }}</h1>
          </b-row>
        </b-col>
      </b-row>
      <b-row>
        <b-table striped :items="tableData"></b-table>
      </b-row>
    </b-col>
  </div>
</template>

<script>
import { GChart } from 'vue-google-charts'
export default {
  name: 'gchart',
  components: {
    GChart,
  },
  props: {
    data: {
      type: Array, 
      default() {
        return [];
      },
    }
  },
  data() {
    return {
      chartData: this.data,
      chartOptions: {
        title: 'MTTSF of Network',
      },
      tableData: []
    }
  },
  watch: {
    data(val) {
      console.log(val)
      this.chartData = val;
      this.formatDataTable(val)
    }
  },
  computed: {
    difference() {
      const initialMTTSF = this.data.length > 2 ? this.data[1][1] : null;
      const finalMTTSF = this.data.length > 3 ? this.data[this.data.length - 1][1] : null;
      let difference = 'N/A';
      if (initialMTTSF && finalMTTSF) {
        difference = (((finalMTTSF - initialMTTSF)/initialMTTSF) * 100).toFixed(2);
        if (difference > 0) {
          difference = `+${difference}%`;
        } else {
          difference = `${difference}%`;
        }
      }
      return difference;
    }
  },
  methods: {
    formatDataTable(data) {
      this.tableData = [];
      data.forEach((d, idx) => {
        if (idx !== 0) {
          this.tableData.push({ time: d[0].toFixed(2), MTTSF: d[1].toFixed(2) })
        }
      })
    },
  },
}
</script>

<style scoped>
</style>
