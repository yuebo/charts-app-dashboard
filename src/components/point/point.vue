<!-- 柱状图 -->
<style lang="stylus" scoped>
  .point
    background url('../../assets/bg.jpg') no-repeat
    background-size 100% 100%
    .main
      height calc(100% - 120px)
      width 100%
      transition all 0.5s linear
</style>

<template>
  <div class="point">
    <v-header :name="name" :legendArr="legendArr" :myChart="myChart"></v-header>
    <v-filter :myChart="myChart" v-if="myChart._dom" @change="onChange" :reverse="true"></v-filter>
    <div class="main"></div>
  </div>

</template>

<script>
  import echarts from 'echarts'
  import header from 'components/header/header'
  import filter from 'components/filter/filter'

  export default {
    data() {
      return {
        legendArr: [],
        color: this.$store.state.color,
        myChart: {},
        name: '住房保障案件来源',
        startDate: '',
        endDate: '',
        data: []
      }
    },
    methods: {
      initChart() {
        // debugger
        // 基于准备好的dom，初始化echarts实例
        this.myChart = echarts.init(document.querySelector('.point .main'));
        this.myChart.setOption({
          tooltip: {
            trigger: 'axis',
            axisPointer: {
              type: 'shadow'
            }
          },
          legend: {
            data: ['2019年'],
            textStyle: {
              color: 'white'
            }
          },
          grid: {
            left: '3%',
            right: '4%',
            bottom: '3%',
            containLabel: true
          },
          xAxis: {
            type: 'value',
            boundaryGap: [0, 0.01],
            axisLabel: {
              textStyle: {
                color: 'white'
              }
            }
          },
          yAxis: {
            type: 'category',
            data: this.data.axis,
            splitLine: {
              lineStyle: {
                color: ['rgba(230, 230, 230, 0.2)']
              }
            },
            axisLabel: {
              textStyle: {
                color: 'white',
                fontSize: 14
              }
            }
          },
          series: [
            {
              name: '2019年',
              type: 'bar',
              barWidth: 16,
              data: this.data.data,
              color: ['rgba(0, 230, 0, 0.8)'],
              label: {
                normal: {
                  show: true,
                  position: 'right'
                }
              }
            }
          ]
        });
      },
      onChange(event) {
        this.startDate = event.startDate;
        this.endDate = event.endDate;
        this.request()
      },
      request() {
        let promise = this.$axios.get("/charts", {
          params: {
            startDate: this.startDate,
            endDate: this.endDate,
            groupBy: 'origin',
            level1: '住房保障'
          }
        })
        promise.then(data => {
          // alert(JSON.stringify(data.data))
          this.data = data.data
          this.initChart();
          // debugger
          this.legendArr = this.myChart.getOption().series
          this.legendArr.forEach((data) => {
            data.selected = true;
          })
        })
      },
      init() {
        this.request()
        this.$root.charts.push(this.myChart)
        window.addEventListener('resize', function () {
          if (this.myChart) {
            this.myChart.resize()
          }
        }.bind(this))
      }
    },
    components: {
      'v-header': header,
      'v-filter': filter
    },
    mounted() {
      this.init()
    }
  }

</script>
