<template>
  <div id="app">
    <header>
      <h1>Predictive maintaince</h1>
    </header>
    <chart title="Open" :data="items.open" />
    <chart title="Close" :data="items.close" />
  </div>
</template>

<script>
import Chart from './components/Chart.vue'

export default {
  name: 'app',
  components: {
    Chart,
  },
  data() {
    return {
      items: {
      },
    };
  },
  mounted() {
    fetch('http://wb-predictivemaintenance-api.prsp7vkew2.eu-central-1.elasticbeanstalk.com/api/TorqueValues')
      .then(res => res.json()).then(data => {
      this.items = data.sort((a, b) => a.Position - b.Position).reduce((res, item) => {
        if (item.Direction === 'Close') {
          res.close.push(item);
        } else {
          res.open.push(item);
        }

        return res;
      }, {open: [], close: []});
    });
  }
}
</script>

<style>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }
  .chart {
    height: 400px;
  }
</style>
