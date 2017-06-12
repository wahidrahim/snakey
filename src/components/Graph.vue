<template lang="html">
  <div id="graph">
    <svg class="graph" :width="width" :height="height">
      <defs>
        <linearGradient id="gradient" x1="0%" y1="0%" x2="0%" y2="100%">
          <stop offset="0%" style="stop-color:rgba(255,54,102,0.5);stop-opacity:1" />
          <stop offset="100%" style="stop-color:rgba(42,174,255,0.5);stop-opacity:1" />
        </linearGradient>
      </defs>
      <!-- x interval lines -->
      <line class="x-interval-lines" v-for="i in maxXintervals"
      :x1="xInterval(i)"
      :y1="height"
      :x2="xInterval(i)"
      :y2="0" stroke="darkgray"
      fill="none"></line>
      <!-- x interval labels -->
      <text class="time-label" v-for="i in maxXintervals"
      :x="xInterval(i)"
      :y="height - 100">{{ secPerInterval * i }}</text>
      <!-- plotted line -->
      <polyline :points="plot" fill="url(#gradient)" stroke="black"></polyline>
    </svg>
  </div>
</template>

<script>
export default {
  name: 'graph',
  props: ['value', 'time'],
  data() {
    return {
      points: [], // contains point objects - { x: 12, y: 34 }, { x: 56, y: 78 } ...
      width: 700, // svg width
      height: 500, // svg height
      maxValue: 0, // highest y-value in the graph
      maxXintervals: 30, // graph shows 30 vertical lines at max
      secPerInterval: 1, // increases by 2 every 30 seconds: 1, 2, 4, 6, 8, 10...
    }
  },
  computed: {
    plot() {
      // reset when time is 0
      if (this.time === 0) {
        this.points = []
        this.maxValue = 0

        return ''
      }

      const startPoint = `0,${this.height}`
      const endPoint = ` ${this.width},${this.height}`

      let strPlot = this.points.reduce((strPlotAcc, value, i) => {
        this.maxValue = value.y > this.maxValue ? value.y : this.maxValue

        const x = this.width * (value.x / this.time)
        const y = this.height - this.height * (value.y / this.maxValue)

        strPlotAcc += ` ${x},${y}`

        return strPlotAcc
      }, startPoint)

      strPlot += endPoint

      return strPlot
    },
    averageLineHeight() {
      const sum = this.points.reduce((sum, val) => {
        return sum + val.y
      }, 0)
      const average = sum / this.points.length

      const height = this.height - this.height * (average / this.maxValue) || this.height

      return height
    }
  },
  watch: {
    time(t) {
      this.points.push({
        x: t,
        y: this.value
      })
    }
  },
  methods: {
    xInterval(i) {
      const minute = 60000
      const spacingInterval = Math.floor((this.time / minute * 2)) // space out every 30 seconds
      const seconds = 1000 * this.secPerInterval
      const interval = this.width / this.time * seconds

      this.secPerInterval = spacingInterval > 0 ? 2 * spacingInterval : 1

      // hide lines
      if (this.time === 0) {
        return this.width + 1
      }

      return interval * i
    },
    horizontalGrid(i) {
      return this.height / (this.maxValue / 10) * i
    },
    intervalSecond(i) {
      return this.secPerInterval * i
    }
  }
}
</script>

<style lang="scss">
#graph {
  .graph {
    border: 1px solid black;

    .time-label {
      font-size: 10px;
      font-family: monospace;
    }
  }
}
</style>
