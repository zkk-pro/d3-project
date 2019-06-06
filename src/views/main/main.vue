<template>
  <div class="main">
    <div class="map"></div>
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
      mapSvg: '', //地图svg
      histogram: '', // 柱状图
      width: 500, // svg 宽度
      height: 300, // svg 高度
      // 折线图 使用的margin
      margin: {
        left: 50,
        top: 30,
        right: 20,
        bottom: 20
      },
      show: 'hidden',
      projection: '', // 
    }
  },
  methods: {
    // 创建svg
    createMapSvg () {
      this.mapSvg = d3.select('.map')
        .append('svg')
        .attr('width', this.width)
        .attr('height', this.height + 50)
        .style('display', 'block')

      this.createMap()
      

    },

    // 创建地图
    async createMap () {
      // let projection = d3.geoMercator().scale(d3.geoMercator().scale() * 1.3).translate([this.width / 2, this.height / 1.4])
      // let projection = d3.geoMercator().scale(d3.geoMercator().scale()).translate([0, 0])

      // this.projection = d3.geoMercator().scale(d3.geoMercator().scale() * 10).translate([-this.width * 2.4, -this.height / 2.4])

      this.projection = d3.geoMercator().fitExtent([
          [0, 0],
          [500, 300]
      ]);
      let path = d3.geoPath().projection(this.projection)

      let data = await d3.json('/static/jsonData/melbourne.json')
      console.log('data:', data)
      // return
      // let features = data.features.filter(function (item, index) {
      //   return item.properties.name === 'Australia'
      // })
      let features = data.features
      console.log(features)
      
      let g = this.mapSvg.selectAll('g')
        .data(features)
        .enter()
        .append('g')
        
      g
        .append('path')
        .attr('stroke-width', 1)
        .attr('stroke', '#fff')
        .attr('fill', function (d, i) {
          return '#aaa'
        })
        .attr('d', path)
      
      // this.createMapPoint()
    },

    // 创建地图上的感应器
    async createMapPoint() {
      let res = await d3.csv('/static/csvData/Sensor_Locations.csv')
      console.log(res)
      res.forEach(item => {
        var myLocation = this.projection([item.longitude, item.latitude]);
        this.mapSvg
          .append('circle')
          .attr('r', 5)
          .attr('fill', '#f00')
          .attr('fill-opacity', '0.7')
          .attr('transform', 'translate(' + myLocation[0] + ', ' + myLocation[1] + ')')
      })
    },

    // 柱状图
    createHistogram () {
      let data = [1,2,3,4,5]
      let bar_width = 50
      let bar_margin = 10
      let svg_width = data.length * (bar_width + bar_margin)

      let scale = d3.scaleLinear()
        .domain([0, d3.max(data)])
        .range([this.height, 0])

      this.histogram = d3.select('.histogram')
        .append('svg')
        .attr('width', this.width)
        .attr('height', this.height)
      
      let bar = this.histogram
        .selectAll('g')
        .data(data)
        .enter()
        .append('g')
        .attr('transform', (d, i) => `translate(${i * (bar_width + bar_margin)}, 0)`)

      bar.append('rect')
        .attr('y', d => scale(d))
        .attr('width', bar_width)
        .attr('height', d => this.height - scale(d))
        .style('fill', '#fcc')
        .on('mouseover', (d, i) => {
          console.log(this)
          this.show = 'visible'
        })
      bar.append('text')
        .text((d) => d)
        .attr('x', (bar_width - 10) / 2)
        .attr('y', (d) => scale(d))
        .attr('dy', 15)
        .style('visibility', 'hidden')
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
    this.createMapSvg()
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
  // display: flex;
}
.map, .histogram, .line {
  width: 500px;
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
