<template>
  <div id="app">
    <input type="range" min="0" max="200" v-model="value">
    <div class="value">{{ value }}</div>
    <svg class="graph">
      <!-- <line x1="100" y1="500" x2="100" y2="0" stroke="#666"></line> -->
      <line v-for="i in 25" :x1="movement(i)" :y1="graphSize" :x2="movement(i)" :y2="0" stroke="#666"></line>
      <polyline fill="none" stroke="orangered" :points="points" stroke-width="3"></polyline>
    </svg>
  </div>
</template>

<script>
import ValueSlider from './components/ValueSlider.vue'
import moment from 'moment'

export default {
  name: 'app',
  data () {
    return {
      value: 50,
      graphSize: 500,
      pts: [],
      timer: {
        timeElapsed: 0,
        startTime: null,
        clock: null,
        start() {
          this.startTime = moment()
          this.clock = setInterval(() => {
            this.timeElapsed = moment().diff(this.startTime, 'milliseconds')
          }, 1)
        },
        stop() {
          clearInterval(this.clock)
        }
      }
    }
  },
  computed: {
    points() {
      let pts = []

      if (this.timer.timeElapsed) {
        this.pts.push(this.value)
        pts = this.pts.slice(-500)
      }

      let p = ''
      pts.map((v, i) => {
        p += `${i},${500 - v / 200 * 500} `
      })
      // if (this.timer.timeElapsed >= 5000) {
        // this.timer.stop()
        // console.log(this.pts)
      // }
      return p
    }
  },
  created() {
    this.timer.start()
  },
  methods: {
    movement(i) {
      const slowDown = 10
      const spacing = 20
      const divisions = 25

      // 500 (width) / divisions = spacing

      let t = this.timer.timeElapsed / slowDown % spacing

      return this.graphSize / divisions * i - t
    }
  }
}
</script>

<style lang="scss">
body {
  background: black;
  color: white;
}

#app {
  input[type="range"] {
    transform: rotate(-90deg);
    margin-top: 100px;
    float: left;
  }

  .graph {
    width: 500px;
    height: 500px;
    border: 1px solid whitesmoke;
  }
}
</style>
