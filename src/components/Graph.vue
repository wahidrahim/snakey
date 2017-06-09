<template lang="html">
    <div id="graph">
        <svg class="graph" :width="width" :height="height">
            <!-- <defs>
                <linearGradient id="gradient" x1="0%" y1="0%" x2="0%" y2="100%">
                    <stop offset="0%" style="stop-color:rgb(255,255,0);stop-opacity:1" />
                    <stop offset="100%" style="stop-color:rgb(255,0,0);stop-opacity:1" />
                </linearGradient>
            </defs> -->
            <!-- NOTE: 60 normally means 60 seconds, after 1 minute 1 line represents 2 seconds so it will show 30 lines and then more as time progress -->
            <line v-for="i in 60" :x1="verticalGrid(i)" :y1="height" :x2="verticalGrid(i)" stroke="darkgray" fill="none"></line>
            <!-- <polyline :points="plot" fill="url(#gradient)" stroke="black"></polyline> -->
            <polyline :points="plot" fill="none" stroke="black"></polyline>
        </svg>
    </div>
</template>

<script>
export default {
    name: 'graph',
    props: ['points', 'time'],
    data() {
        return {
            width: 700,
            height: 500,
            maxY: 0
        }
    },
    computed: {
        plot() {
            // const length = this.points.length
            // let strPlot = this.points.reduce((strPlot, val, i) => {
            //     this.maxY = val.y > this.maxY ? val.y : this.maxY
            //
            //
            //     // NOTE: this is not right
            //     // const x = i ? this.width / (length - 1) * i : 0
            //      // TODO: right now it is being spaced as if the x value is
            //      // is always equally spaced out, but that is not necessarily the case
            //      // assuming: 0,0 350,80 700,90
            //      // reality: 0,0, 620,80 700,90
            //     const x = (this.width / this.time) * 1000 * i
            //     const y = this.height - this.height * (val.y / this.maxY)
            //
            //     strPlot += ` ${x},${y}`
            //
            //     return strPlot
            // }, `0,${this.height}`)
            //
            // strPlot += ` ${this.width},${this.height}`
            // return strPlot
        }
    },
    methods: {
        verticalGrid(i) {
            const mins = Math.floor((this.time / 60000))

            // TODO sec needs to increase preiodically
            // 1000, 2000, 3000
            let sec = mins > 1 ? 1000 * (mins + 1) : 1000

            let interval = this.width / (this.time / sec)

            if (interval === Infinity) {
                // hide
                return -1
            }

            return interval * i
        }
    }
}
</script>

<style lang="scss">
#graph {
    .graph {
        border: 1px solid black;
    }
}
</style>
