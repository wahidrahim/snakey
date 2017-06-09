<template>
    <div id="app">
        <input type="range" min="0" max="200" v-model.number="value">
        <div class="value">{{ value }}</div>
        <button @click="toggleTimer">Timer</button>
        <p>{{ timer.timeElapsed | formatTime }}</p>
        <graph :points="points" :time="timer.timeElapsed"></graph>
    </div>
</template>

<script>
import Graph from './components/Graph.vue'
import moment from 'moment'

export default {
    name: 'app',
    components: {
        Graph
    },
    data () {
        return {
            value: 50,
            points: [],
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
                    this.clock = null
                }
            }
        }
    },
    watch: {
        timer: {
            deep: true,
            handler(t) {
                this.points.push({
                    x: t.timeElapsed,
                    y: this.value
                })
            }
        }
    },
    methods: {
        toggleTimer() {
            if (this.timer.clock) {
                this.timer.stop()
            } else {
                this.timer.start()
            }
        }
    },
    filters: {
        formatTime: function(ms) {
            const zeroTime = moment('2016-06-12 00:00:00')
            if (ms > 3600000) {
                return zeroTime.add(ms, 'milliseconds').format('HH:mm:ss:SSS')
            } else if (ms > 60000) {
                return zeroTime.add(ms, 'milliseconds').format('mm:ss:SSS')
            } else if (ms > 1000) {
                return zeroTime.add(ms, 'milliseconds').format('ss:SSS')
            } else {
                return zeroTime.add(ms, 'milliseconds').format('SSS')
            }
        }
    }
}
</script>

<style lang="scss">
</style>
