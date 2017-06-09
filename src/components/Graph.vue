<template lang="html">
    <div id="graph">
        <svg class="graph" :width="width" :height="height">
            <defs>
                <linearGradient id="gradient" x1="0%" y1="0%" x2="0%" y2="100%">
                    <stop offset="0%" style="stop-color:rgba(255,54,102,0.5);stop-opacity:1" />
                    <stop offset="100%" style="stop-color:rgba(42,174,255,0.5);stop-opacity:1" />
                </linearGradient>
            </defs>
            <!-- NOTE: 60 normally means 60 seconds, after 1 minute 1 line represents 2 seconds so it will show 30 lines and then more as time progress -->
            <!-- <line v-for="i in 60" :x1="verticalGrid(i)" :y1="height" :x2="verticalGrid(i)" stroke="darkgray" fill="none"></line> -->
            <!-- only 30 lines will show at max (30 seconds before spacing out again)-->
            <line v-for="i in 30" :x1="verticalGrid(i)" :y1="height" :x2="verticalGrid(i)" :y2="0" stroke="darkgray" fill="none"></line>
            <text class="time-label" v-for="i in 60" :x="verticalGrid(i)" :y="height - 100">{{ lineSecond(i) }}</text>
            <!-- <line class="speeds" v-for="i in 60" :x1="0" :y1="horizontalGrid(i)" :x2="width" :y2="horizontalGrid(i)" fill="none" stroke="darkgray"></line> -->
            <line class="average" :x1="0" :y1="averageLineHeight" :x2="width" :y2="averageLineHeight" fill="none" stroke="orange"></line>
            <polyline :points="plot" fill="url(#gradient)" stroke="black"></polyline>
            <!-- <polyline :points="plot" fill="none" stroke="red"></polyline> -->
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

                const x = this.width * (val.x / this.max.x || 0)
                const y = this.height - this.height * (val.y / this.max.y)

                strPlot += ` ${x},${y}`

                return strPlot
            }, `0,${this.height}`)

            strPlot += ` ${this.width},${this.height}`
            return strPlot
        },
        averageLineHeight() {
            const sum = this.points.reduce((sum, val) => {
                return sum + val.y
            }, 0)
            const average = sum / this.points.length

            const height = this.height - this.height * (average / this.max.y) || this.height

            console.log(height)

            return height
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
                return this.width + 1
            }

            return interval * i
        },
        horizontalGrid(i) {
            return this.height / (this.max.y / 10) * i
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
