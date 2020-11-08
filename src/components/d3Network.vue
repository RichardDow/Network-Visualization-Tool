<template>
  <div>
    <b-col cols='12' class='m-3'>
      <b-row>
        <h1>Network Visualization</h1>
      </b-row>
    </b-col>
    <b-col cols='12' style="background-color: #eaeaea;">
      <b-row class='p-5'>
        <b-col cols='8'>
          <b-form-select v-model="selected" :options="simulationOptions"/>
        </b-col>
        <b-col cols='4'>
          <b-button variant="primary" @click="fetchData()" class='mr-1'>Fetch Data</b-button>
          <b-button variant="primary" @click="runSimulation(true)" class='mr-1'>Run (Decoy)</b-button>
          <b-button variant="primary" @click="runSimulation(false)">Run</b-button>
        </b-col>
      </b-row>
    </b-col>
    <b-col style="background-color: #f7f7f7;">
      <b-row>
        <D3Network :net-nodes="nodes" :net-links="links" :options="options" />
      </b-row>
    </b-col>
    <b-col class="p-5">
      <b-row>
        <lineChart :data="results" />
      </b-row>
    </b-col>
    <svg>
          <defs>
            <marker id="m-end" markerWidth="7" markerHeight="7" refX="9" refY="3" orient="auto" markerUnits="strokeWidth" >
              <path d="M0,0 L0,6 L9,3 z"></path>
            </marker>
            <marker id="m-end-decoy" markerWidth="7" markerHeight="7" refX="9" refY="3" orient="auto" markerUnits="strokeWidth" >
              <path d="M0,0 L0,6 L9,3 z"></path>
            </marker>
          </defs>
        </svg>
  </div>
</template>

<script>
import D3Network from 'vue-d3-network'
import axios from 'axios'
import lineChart from './gchart.vue'
export default {
  name: 'd3Network',
  components: {
    D3Network,
    lineChart
  },
  data() {
    return {
      selected: null,
      simulationOptions: [
        { value: null, text: 'Please select a simulation' },
      ],
      simulations: [],
      nodes: [],
      links: [],
      results: [],
      options:
      {
        force: 2000,
        nodeSize: 10,
        nodeLabels: true,
        linkWidth:2,
        size: {
          w: 1000,
          h: 700
        }
      },
      
    }
  },
  watch: {
    selected: {
      handler(val) {
        if (!val && val !== 0) {
          this.nodes = [];
          this.links = [];
        } else if (val < this.simulations.length) {
          this.nodes = this.simulations[val].nodes;
          this.links = this.simulations[val].links;
        }
      }
    }
  },
  mounted() {
    this.fetchData();
  },
  methods: {
    fetchData() {
      axios.get('http://localhost:5000/')
        .then((res) => {
          if (res && res.data) {
            this.loadData(res.data)
          }
        })
    },
    runSimulation(decoy) {
      this.nodes = [];
      this.links = [];
      this.results = [];
      this.selected = null;
      axios.post(`http://localhost:5000/run?decoy=${decoy}`,)
        .then((res) => {
          if (res && res.data) {
            this.loadData(res.data)
          }
        })
    },
    loadData(data) {
      this.simulations = []
      Object.keys(data).forEach((key) => {
        if (key !== 'results') {
          this.simulations.push(data[key])
        } else {
          this.results = data[key]
        }
      })
      if (this.simulations.length > 0) {
        this.simulationOptions = [this.simulationOptions[0]]
        for (let i = 0; i < this.simulations.length; i++) {
          this.simulationOptions.push({ value: i, text: `Simulation ${i+1}`})
        }
        this.selected = 0;
      }
    },
    directedLink (link) {
      link._svgAttrs = { 'marker-end': 'url(#m-end)' }
      return link
    }
  },
}
</script>

<style scoped>
#m-end path, #m-start{
  fill: blue;
}
#m-end-decoy {
  fill: orange;
}
</style>
