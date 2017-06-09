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
            <!-- <line v-for="i in 60" :x1="verticalGrid(i)" :y1="height" :x2="verticalGrid(i)" stroke="darkgray" fill="none"></line> -->
            <!-- only 30 lines will show at max (30 seconds before spacing out again)-->
            <line v-for="i in 30" :x1="verticalGrid(i)" :y1="height" :x2="verticalGrid(i)" stroke="darkgray" fill="none"></line>
            <text class="time-label" v-for="i in 60" :x="verticalGrid(i)" :y="height - 100">{{ lineSecond(i) }}</text>
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
            max: { x: 0, y: 0},
            perSec: 1
        }
    },
    computed: {
        plot() {
            const length = this.points.length
            let strPlot = this.points.reduce((strPlot, val, i) => {
                this.max.x = val.x > this.max.x ? val.x : this.max.x
                this.max.y = val.y > this.max.y ? val.y : this.max.y



                // NOTE: this is not right
                // const x = i ? this.width / (length - 1) * i : 0
                 // TODO: right now it is being spaced as if the x value is
                 // is always equally spaced out, but that is not necessarily the case
                 // assuming: 0,0 350,80 700,90
                 // reality: 0,0, 620,80 700,90
                const x = this.width * (val.x / this.max.x || 0)
                const y = this.height - this.height * (val.y / this.max.y)

                strPlot += ` ${x},${y}`

                return strPlot
            }, `0,${this.height}`)

            strPlot += ` ${this.width},${this.height}`
            // console.log(strPlot)
            return strPlot
        }
    },
    methods: {
        verticalGrid(i) {
            const minute = 60000
            const spaceOutEveryTime = Math.floor((this.time / (minute / 2))) // every 30 sec

            // TODO sec needs to increase preiodically
            // 1000, 2000, 3000

            if (spaceOutEveryTime > 0) {
                this.perSec = 2 * spaceOutEveryTime
            }

            let sec = 1000 * this.perSec


            let interval = this.width / (this.time / sec)

            if (interval === Infinity) {
                // hide
                return -1
            }

            return interval * i
        },
        lineSecond(i) {
            return this.perSec * i
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
