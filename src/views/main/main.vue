<template>
  <div class="main">
    <!-- <div class="map"></div> -->
    <div class="histogram"></div>
    <div class="line"></div>
  </div>
</template>

<script>
import * as d3 from 'd3'
// import sensorLocations from '../../../static/csvData/Sensor_Locations.csv'
export default {
  name: 'Main',
  data () {
    return {
      msg: '',
      map: '',
      histogram: '',
      line: '',
      width: 500,
      height: 300,
      margin: {
        left: 50,
        top: 30,
        right: 20,
        bottom: 20
      },
      show: 'hidden'
    }
  },
  methods: {
    // 创建svg
    createMapSvg () {
      this.svg = d3.select('.map')
      .append('svg')
      .attr('width', '100%')
      .attr('height', '100%')
      .style('display', 'block')
    },

    // 柱状图
    createHistogram () {
      let data = [1,2,3,4,5]
      let bar_width = 50

      let scale = d3.scaleLinear()
        .domain([0, d3.max(data)])
        .range([0, this.height])

      this.histogram = d3.select('.histogram')
        .append('svg')
        .attr('width', this.width)
        .attr('height', this.height)
      
      let bar = this.histogram
        .selectAll('g')
        .data(data)
        .enter()
        .append('g')
        .attr('transform', (d, i) => `translate(${i * bar_width}, 0)`)

      bar.append('rect')
        .attr('width', bar_width - 10)
        .attr('height', d => scale(d))
        .style('fill', '#fcc')
        .on('mouseover', (d, i) => {
          console.log(i)
          console.log(bar)
          console.log(bar.select('text')[i])
          console.log(d)
          this.show = 'visible'
        })
      bar.append('text')
        .text((d) => d)
        .attr('x', (bar_width - 10) / 2)
        .attr('y', (d) => scale(d))
        .style('visibility', this.show)
    },

    // 折线图
    createLine () {
      let data = [1,5,2,7,4,1,2,3,1,1,1]

      let g_width = this.width - this.margin.left - this.margin.right
      let g_height = this.height - this.margin.top - this.margin.bottom

      let scale_x = d3.scaleLinear()
        .domain([0, data.length - 1]) // 输入范围
        .range([0, g_width]) // 输出范围

      let scale_y = d3.scaleLinear()
      .domain([ 0, d3.max(data) ])
      .range([ g_height, 0 ])

      let line_generator = d3.line()
        .x((d, i) => scale_x(i))
        .y((d, i) => scale_y(d))
        .curve(d3.curveCatmullRom.alpha(0))

      let g = d3.select('.line')
        .append('svg')
        .attr('width', this.width)
        .attr('height', this.height)
        .append('g')
        .attr('transform', `translate(${this.margin.left}, ${this.margin.top})`)
      g
        .append('path')
        .attr('d', line_generator(data))
        .attr('class', 'line-path')

      // let x_axis = d3.axisRight(scale_x)
      let x_axis = d3.axisBottom(scale_x)
      let y_axis = d3.axisLeft(scale_y)

      // x 坐标轴
      g
        .append('g')
        .call(x_axis)
        .attr('transform', `translate(0, ${g_height})`)
      // y 坐标轴
      g
        .append('g')
        .call(y_axis)
        .append('text')
        .text('价格($)')
        .style('stroke', '#f00')
        .style('transform', 'rotate(-90deg)')
        .attr('dy', '1em')

    }
  },
  mounted () {
    // this.createMapSvg()
    this.createHistogram()
    this.createLine()
    // d3.csv('/static/csvData/Sensor_Locations.csv').then(res => {
    //   console.log(res)
    // })
  }
}
</script>
<style lang="less" scoped>
.main {
  display: flex;
}
.map, .histogram, .line {
  width: 50%;
  height: 350px;
  margin: 5px;
  // margin-right: 10px;
  background: #f5f5f5;
}
path {
  stroke: #fcc;
  stroke-width: 2;
  fill: none;
}
</style>
