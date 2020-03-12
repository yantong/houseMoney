<template>
  <div>
    <Input v-model="money" placeholder="本金" style="width: 100px;margin: 10px" />
    <Input v-model="monRate" placeholder="年利率" style="width: 100px;margin: 10px" />
    <Input v-model="mon" placeholder="还款期数" style="width: 100px;margin: 10px" />
    <Button @click="showGraphData" type="primary" style="width: 80px;margin: 10px">查看</Button>

    <div ref="showTable" style="height:800px;width: 100%"></div>
  </div>
</template>

<script>
import echarts from 'echarts'

export default {
  name: 'HelloWorld',
  data() {
    return {
      money: 490000,
      monRate: 0.1 || 0.049 * 1.15,
      mon: 360
    }
  },
  mounted() {
    this.showGraphData()
  },
  methods: {
    getPrice(money,monRate,mon) {
        return (money*monRate* Math.pow(1+monRate, mon)) / (Math.pow(1+monRate, mon) - 1.0 )
    },
    showGraphData () {
      const _this = this



      if(isNaN(+this.money) || isNaN(+this.monRate) || isNaN(+this.mon) || (!this.money || !this.monRate || !this.mon))
        this.$Message.error('只能输入数字')

      let rate = []

      for (let index = 0.001; index < this.monRate; ) {
        rate.push(index)

        index += 0.001
      }

      let option = {
        title: {
          text: ''
        },
        xAxis: [
          {
            type: 'category',
            boundaryGap: false,
            data: rate.map(item => {
              return (item - 0.049 * 1.15).toFixed(5) * 1.0
            })
          }
        ],
        tooltip: {
            trigger: 'axis',
            formatter(params) {
              let x = params[0].axisValue
              let y = params[0].value

              return `利率差为： ${(+x).toFixed(3)} 还款差为：${y}`
            },
        },
        yAxis: [
          {
            type: 'value'
          }
        ],
        series: [
          {
            name: '',
            type: 'line',
            label: {
              normal: {
                // show: true,
                // position: 'top'
              }
            },
            data: rate.map((item) => {
              return ((_this.getPrice(+_this.money,+item / 12,+_this.mon) - _this.getPrice(490000,0.049 * 1.15 / 12,360)) * 360).toFixed(2)
            })
          }
        ]
      }        

      if (!_this.dom) 
        _this.dom = echarts.init(_this.$refs.showTable) 

      _this.dom.setOption(option)
    },
  }
}
</script>


<style scoped>

</style>
