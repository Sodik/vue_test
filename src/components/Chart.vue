<template>
  <div ref="root" class="chart" :class="{loading: !data.length}"></div>
</template>

<script>
  import echarts from 'echarts';

  export default {
    name: "Chart",
    props: {
      data: {
        type: Array,
        default: () => [],
        required: true,
      },
      title: {
        type: String,
        required: true,
      }
    },
    methods: {
      onResize() {
        if (this.chart) {
          this.chart.resize();
        }
      },
      initChart(data = this.data) {
        // it doesn't react on setting option with new series data so here is a workaround
        if (this.chart) {
          this.chart.dispose();
        }
        this.chart = echarts.init(this.$refs.root);

        this.chart.setOption({
          title: {
            text: this.title,
          },
          xAxis: {
            name: 'Valve position',
            nameLocation: 'center',
            nameGap: 35,
            data: data.map((item) => item.Position),
            axisTick: {
              show: false,
            },
            axisLine: {
              show: false
            },
            axisLabel: {
              showMaxLabel: true,
              interval: 640,
            },
            z: 10
          },
          yAxis: {
            name: 'Required tongue (%)',
            nameLocation: 'middle',
            nameGap: 35,
            axisLine: {
              show: false
            },
            axisTick: {
              show: false
            },
          },
          legend: {
            align: 'left',
            right: 80,
            itemHeight: 20,
            itemWidth: 20,
            data: ['Average open torque', 'Last open torque', 'Forecast open torque'],
            itemGap: 5,
          },
          dataZoom: [
            {
              type: 'inside'
            }
          ],
          series: [
            {
              name: 'Average open torque',
              type: 'bar',
              itemStyle: {
                color: '#188df0',
              },
              data: data.map(item => item.AverageTorque),
            },
            {
              name: 'Last open torque',
              type: 'bar',
              itemStyle: {
                color: '#83bff6',
              },
              data: data.map(item => item.LastTorque),
            },
            {
              name: 'Forecast open torque',
              type: 'bar',
              itemStyle: {
                color: '#f4ac41',
              },
              data: data.map((item, ind) => item.LastTorque + (item.AverageTorque - item.LastTorque + 5)),
            },
          ]
        });
      },
    },
    watch: {
      data(data) {
        this.initChart(data);
      },
    },
    mounted() {
      this.initChart();

      window.addEventListener('resize', this.onResize, false);
    },
    beforeDestroy() {
      window.removeEventListener('resize', this.onResize);
      this.chart.dispose();
      this.chart = null;
    }
  }
</script>

<style scoped>
  .chart {
    position: relative;
  }
  .loading::before {
    content: 'Loading...';
    position: absolute;
    top: 50%;
    left: 50%;
  }
</style>
