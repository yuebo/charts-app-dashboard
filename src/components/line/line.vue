<!-- 柱状图 -->
<style lang="stylus" scoped>
  .line
    height 1000px
    background url('../../assets/bg.jpg') no-repeat
    background-size 100% 100%

    .main
      width 100%
      height calc(100% - 100px)
      margin-top -15px
</style>

<template>
  <div class="line">
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
        name: '2019案件类型统计',
        startDate: '',
        endDate: '',
        data: []
      }
    },
    methods: {
      initChart() {
        // debugger
        // 基于准备好的dom，初始化echarts实例
        this.myChart = echarts.init(document.querySelector('.line .main'));
        this.myChart.setOption({
          tooltip: {
            trigger: 'item',
            formatter: "{a} <br/>{b} : {c} ({d}%)"
          },
          legend: {
            type: 'scroll',
            orient: 'vertical',
            right: 10,
            top: 20,
            bottom: 20,
            data: this.data.axis,
            textStyle: {
              color: ['rgba(230, 230, 230, 0.8)']
            }
          },
          series: [
            {
              name: '类型',
              type: 'pie',
              radius: '55%',
              center: ['40%', '50%'],
              data: this.data.axis.map(name => {
                return {
                  name: name,
                  value: this.data.data[this.data.axis.indexOf(name)]
                }
              }),
              itemStyle: {
                emphasis: {
                  shadowBlur: 10,
                  shadowOffsetX: 0,
                  shadowColor: 'rgba(0, 0, 0, 0.5)'
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
            level1: '住房保障',
            total: false
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
