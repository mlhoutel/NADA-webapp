<template>
  <apexchart
    ref="areaChart"
    width="100%"
    height="180px"
    type="area"
    :options="areaChartOptions"
    :series="filteredSeries"
    @mounted="areaChartMounted"
  ></apexchart>
</template>
<script lang="ts">
  import { defineComponent } from 'vue'
  import { mapGetters, mapMutations } from 'vuex'

  export default defineComponent({
    name: 'AreaChart',
    props: {
      data: { type: Array, default: [] },
      color: { type: String, default: '' },
      title: { type: String, default: '' },
      legend: { type: String, default: '' },
    },
    data() {
      return {
        areaChart: null,
        areaChartOptions: {
          colors: [this.color],
          chart: {
            id: 'chart1',
            toolbar: {
              show: false,
              autoSelected: 'pan',
            },
          },
          dataLabels: {
            enabled: false,
          },
          xaxis: {
            type: 'datetime',
          },
          yaxis: {
            tickAmount: 3,
            decimalsInFloat: 2,
          },
        },
      }
    },
    computed: {
      series(): any {
        return [{ data: this.data }]
      },
      filteredSeries(): any {
        if (!this.$store.state.analysis.selection.length) return this.series
        if (this.$store.state.analysis.selection[0] == null) return this.series
        if (this.$store.state.analysis.selection[1] == null) return this.series

        const lower = this.$store.state.analysis.selection[0]
        const upper = this.$store.state.analysis.selection[1]

        return [
          {
            data: this.data.filter((e: any) => {
              return e[0] >= lower && e[0] <= upper
            }),
          },
        ]
      },
    },
    watch: {
      '$store.state.common.theme'(): void {
        this.setPopupTheme(this.areaChart)
      },
      '$store.state.analysis.sensor'(): void {
        this.areaChart.updateOptions({}) // forceUpdate
        this.setMinMaxValue(this.areaChart)
      },
    },
    methods: {
      ...mapGetters('analysis', ['getTimeline']),
      ...mapMutations('analysis', ['setSelection']),
      areaChartMounted(): void {
        this.areaChart = this.$refs.areaChart
        this.setPopupTheme(this.areaChart)
        this.setLabel(this.areaChart)
      },
      setPopupTheme(chart: any): void {
        console.log(chart)
        chart.updateOptions({
          tooltip: {
            theme: this.$store.state.common.theme == null ? 'light' : 'dark',
          },
        })
      },
      setLabel(chart: any): void {
        chart.updateOptions({
          xaxis: {
            title: {
              text: this.title,
            },
          },
          yaxis: {
            title: {
              text: this.legend,
            },
          },
        })
      },
      setMinMaxValue(chart: any): void {
        chart.updateOptions({
          yaxis: {
            max: Math.max(
              ...this.data.map((e: any) => {
                return e[1]
              })
            ),
            min: Math.min(
              ...this.data.map((e: any) => {
                return e[1]
              })
            ),
          },
        })
      },
    },
  })
</script>
