<!-- 柱状图 -->
<style lang="stylus" scoped>
  .multipleColumn
    height 1000px
    background url('../../assets/bg.jpg') no-repeat
    background-size 100% 100%
    .main
      width 100%
      height calc(100% - 100px)
      margin-top -15px
</style>

<template>
  <div class="multipleColumn">
    <v-header :name="name" :legendArr="legendArr" :myChart="myChart"></v-header>
    <v-filter :myChart="myChart" v-if="myChart._dom" @change="onChange" :reverse="true" :picker="false"></v-filter>
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
        name: '2018与2019住房统计比较',
        startDate: '',
        endDate: '',
        data: []
      }
    },
    methods: {
      initChart() {
        // debugger
        // 基于准备好的dom，初始化echarts实例
        this.myChart = echarts.init(document.querySelector('.multipleColumn .main'));
        this.myChart.setOption({
          tooltip: {
            trigger: 'axis',
            axisPointer: {
              type: 'shadow'
            }
          },
          legend: {
            data: ['2018年', '2019年'],
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
              name: '2018年',
              type: 'bar',
              barWidth: 16,
              data: this.data.data2,
              label: {
                normal: {
                  show: true,
                  position: 'right'
                }
              },
              color: ['rgba(230, 230, 230, 0.8)']
            },
            {
              name: '2019年',
              type: 'bar',
              barWidth: 16,
              color: ['rgba(0, 230, 0, 0.8)'],
              label: {
                normal: {
                  show: true,
                  position: 'right'
                }
              },
              data: this.data.data
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
            lastYear: true
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
