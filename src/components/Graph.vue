<template lang="html">
    <div id="graph">
        <svg class="graph" :width="width" :height="height">
            <polyline :points="plot" fill="none" stroke="black"></polyline>
        </svg>
    </div>
</template>

<script>
export default {
    name: 'graph',
    props: ['points'],
    data() {
        return {
            width: 700,
            height: 500,
            maxY: 0
        }
    },
    computed: {
        plot() {
            const length = this.points.length
            const strPlot = this.points.reduce((strPlot, val, i) => {
                this.maxY = val.y > this.maxY ? val.y : this.maxY


                const x = i ? this.width / (length - 1) * i : 0
                const y = this.height - this.height * (val.y / this.maxY)

                strPlot += ` ${x},${y}`

                return strPlot
            }, `0,${this.height}`)

            //
            // this.points.map((v, i) => {
            //     const xInterval = Math.floor(this.width / (length - 1))
            //
            //     this.max.x = v.x
            //     this.maxY = v.y > this.maxY ? v.y : this.maxY
            //
            //     let x = xInterval * i
            //     strPlot += `${x},${v.y} `
            //
            //     if (x > this.width) {
            //         console.log(x, length)
            //     }
            // })
            //
            return strPlot
            // console.log(this.max.x, this.maxY)
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
