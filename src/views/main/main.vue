<template>
  <div class="main">
    <!-- <div class="map"></div> -->
    <div class="histogram">
      <div class="select-box">
        <div class="date">
          <!-- <el-select v-model="date" placeholder="请选择年份">
            <el-option
              v-for="item in dateArr"
              :key="item"
              :label="item"
              :value="item">
            </el-option>
          </el-select> -->
        </div>
        <div class="sensor">
          <el-select v-model="sensor" placeholder="请选择感应点" @change="sensorChange">
            <el-option
              v-for="item in sensorArr"
              :key="item.Sensor_Name"
              :label="item.Sensor_Name"
              :value="item.Sensor_Name">
            </el-option>
          </el-select>
        </div>
      </div>
      
      <div class="line-chart">
        <div class="spring"></div>
        <div class="summer"></div>
        <div class="autumn"></div>
        <div class="winter"></div>
      </div>
    </div>
    <div class="pie">
      <div class="pie-chart"></div>
    </div>
  </div>
</template>

<script>
import * as d3 from 'd3'

export default {
  name: 'Main',
  data () {
    return {
      width: 500, // svg 宽度
      height: 300, // svg 高度
      dateArr: [2009, 2010, 1012, 2013], // 所有年份
      date: 2014, // 当前选择的年份
      sensorArr: [], // 所有感应器
      sensor: '', // 当前选择的感应器
      csvData: [], // 行人数据

      // 曲线图
      sensorClassify: {
        spring: [], // 春
        summer: [], // 夏
        autumn: [], // 秋
        winter: [],  // 冬
      },
      springData: [], // 春
      summerData: [], // 夏
      autumnData: [], // 秋
      winterData: [],  // 冬

      mapSvg: '', //地图svg
      histogram: '', // 柱状图

      // 折线图 使用的margin
      margin: {
        left: 70,
        top: 50,
        right: 20,
        bottom: 20
      },
      show: 'hidden',
      projection: '', // 
      
    }
  },
  methods: {
    // 感应器改变
    sensorChange(value) {
      this.sensor = value
      this.filtered()
      this.updateSpring()
      this.updateSummer()
      this.updateAutumn()
      this.updateWinter()
      // this.divideSeason()
    },
    updateSpring () {
      let data = this.springData
      let g_width = this.width - this.margin.left - this.margin.right
      let g_height = this.height - this.margin.top - this.margin.bottom

      // 缩放函数
      let scale_x = d3.scaleLinear().domain([0, data.length - 1]).range([0, g_width])
      // let scale_y = d3.scaleLinear().domain([0, d3.max(data.Hourly_Counts)]).range([0, g_height])
      let scale_y = d3.scaleLinear().domain([d3.max(data, d => d.Hourly_Counts) || 1, 0]).range([0, g_height])
      // let scale_y = d3.scaleLinear().domain([1500000,0]).range([0, g_height])

      // 折线生成
      let line_generator = d3.line()
        .x((d,i) => {
          return scale_x(i)
        })
        .y((d,i) => {
          return scale_y(d.Hourly_Counts)
        })
      d3.selectAll('path.spring').attr('d', line_generator(data))
      d3.selectAll('.name-spring').text(this.sensor)
    },
    updateSummer () {
      let data = this.summerData
      let g_width = this.width - this.margin.left - this.margin.right
      let g_height = this.height - this.margin.top - this.margin.bottom

      // 缩放函数
      let scale_x = d3.scaleLinear().domain([0, data.length - 1]).range([0, g_width])
      // let scale_y = d3.scaleLinear().domain([0, d3.max(data.Hourly_Counts)]).range([0, g_height])
      let scale_y = d3.scaleLinear().domain([d3.max(data, d => d.Hourly_Counts) || 1,0 ]).range([0, g_height])
      // let scale_y = d3.scaleLinear().domain([1500000,0]).range([0, g_height])

      // 折线生成
      let line_generator = d3.line()
        .x((d,i) => {
          return scale_x(i)
        })
        .y((d,i) => {
          return scale_y(d.Hourly_Counts)
        })
      d3.selectAll('path.summer').attr('d', line_generator(data))
      d3.selectAll('.name-summer').text(this.sensor)
    },
    updateAutumn () {
      let data = this.autumnData
      let g_width = this.width - this.margin.left - this.margin.right
      let g_height = this.height - this.margin.top - this.margin.bottom

      // 缩放函数
      let scale_x = d3.scaleLinear().domain([0, data.length - 1]).range([0, g_width])
      // let scale_y = d3.scaleLinear().domain([0, d3.max(data.Hourly_Counts)]).range([0, g_height])
      let scale_y = d3.scaleLinear().domain([d3.max(data, d => d.Hourly_Counts) || 1,0 ]).range([0, g_height])
      // let scale_y = d3.scaleLinear().domain([1500000,0]).range([0, g_height])

      // 折线生成
      let line_generator = d3.line()
        .x((d,i) => {
          return scale_x(i)
        })
        .y((d,i) => {
          return scale_y(d.Hourly_Counts)
        })
      d3.selectAll('path.autumn').attr('d', line_generator(data))
      d3.selectAll('.name-autumn').text(this.sensor)
    },
    updateWinter() {
      let data = this.winterData
      let g_width = this.width - this.margin.left - this.margin.right
      let g_height = this.height - this.margin.top - this.margin.bottom

      // 缩放函数
      let scale_x = d3.scaleLinear().domain([0, data.length - 1]).range([0, g_width])
      // let scale_y = d3.scaleLinear().domain([0, d3.max(data.Hourly_Counts)]).range([0, g_height])
      let scale_y = d3.scaleLinear().domain([d3.max(data, d => d.Hourly_Counts) || 1,0 ]).range([0, g_height])
      // let scale_y = d3.scaleLinear().domain([1500000,0]).range([0, g_height])

      // 折线生成
      let line_generator = d3.line()
        .x((d,i) => {
          return scale_x(i)
        })
        .y((d,i) => {
          return scale_y(d.Hourly_Counts)
        })
      d3.selectAll('path.winter').attr('d', line_generator(data))
      d3.selectAll('.name-winter').text(this.sensor)
    },

    // 创建svg
    createMapSvg () {
      let width = window.innerHeight
      let height = window.innerHeight
      this.mapSvg = d3.select('.map')
        .append('svg')
        .attr('width', '100%')
        // .attr('height', this.height + 50)
        .attr('height', '100%')
        .style('display', 'block')

      this.createMap()

    },

    // 创建地图
    async createMap () {
      // let projection = d3.geoMercator().scale(d3.geoMercator().scale() * 1.3).translate([this.width / 2, this.height / 1.4])
      // let projection = d3.geoMercator().scale(d3.geoMercator().scale()*500).translate([-0000, d3.geoMercator().scale()*500])

      // this.projection = d3.geoMercator().scale(d3.geoMercator().scale() * 10).translate([-this.width * 2.4, -this.height / 2.4])

      // this.projection = d3.geoMercator()
      let projection = d3.geoMercator(d3.geoMercator().scale())

      let width = window.innerHeight
      let height = window.innerHeight
      console.log('width: ', width)
      // this.projection = d3.geoMercator().scale(500).translate([width/2, height/2])
      // this.projection = d3.geoMercator().scale(1).translate([1,-0])

      let path = d3.geoPath().projection(projection)

      let data = await d3.json('/static/jsonData/new.json')
      // let data = await d3.json('/static/jsonData/louisville.json')
      // let data = await d3.json('/static/jsonData/world.json')
      console.log('data:', data)
      
      // return
      // let features = data.features.filter(function (item, index) {
      //   return item.properties.name === 'Australia'
      // })

      // ==》new
      // let features = data.features
      // console.log(features)
      let features = data
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


// ----------- 折线图
    // 读取csv数据
    async readCsv() {
      try {
        this.csvData = await d3.csv('/static/csvData/Pedestrian_Counting_System___2009_to_Present__counts_per_hour.csv', (d) => {
          d.Hourly_Counts = +d.Hourly_Counts
          d.Year = +d.Year
          return d
        })
        this.divideSeason(this.csvData)
      } catch (error) {
        console.log('error: ', error)
      }
    },
    // 划分季节
    divideSeason (csvData) {
      // 分季节
      csvData.forEach(item => {
        switch (item.Month) {
          case 'January':
          case 'February':
          case 'March':
            this.sensorClassify.spring.push(item)
            break
          case 'April':
          case 'May':
          case 'June':
            this.sensorClassify.summer.push(item)
            break
          case 'July':
          case 'August':
          case 'September':
            this.sensorClassify.autumn.push(item)
            break
          case 'October':
          case 'November':
          case 'December':
            this.sensorClassify.winter.push(item)
            break
        }
      })
      // 感应器过滤数据
      console.log(this.sensor)
      console.log(this.date)
      
      // this.filtered()

      // this.createLineNew(this.springData, '.spring')
      // this.createLineNew(this.summerData, '.summer')
      // this.createLineNew(this.autumnData, '.autumn')
      // this.createLineNew(this.winterData, '.winter')
    },
    // 过滤好的数据
    filtered () {
      this.springData = this.filterYearData(this.sensorClassify.spring)
      // console.log('springData:', this.springData)

      this.summerData = this.filterYearData(this.sensorClassify.summer)
      // console.log('summerData:', this.summerData)

      this.autumnData = this.filterYearData(this.sensorClassify.autumn)
      // console.log('autumnData:', this.autumnData)

      this.winterData = this.filterYearData(this.sensorClassify.winter)
      // console.log('winterData:', this.winterData)
    },
    // 过滤出每个年份的数据
    filterYearData(data) {
      let tempFilter = [
        {Year: 2009, Hourly_Counts: 0},
        {Year: 2010, Hourly_Counts: 0},
        {Year: 2011, Hourly_Counts: 0},
        {Year: 2012, Hourly_Counts: 0},
        {Year: 2013, Hourly_Counts: 0},
        {Year: 2014, Hourly_Counts: 0},
        {Year: 2015, Hourly_Counts: 0},
      ]
      let filterData = data.filter(item => {
        return item.Sensor_Name === this.sensor
      })
      filterData.forEach(item => {
        switch(item.Year) {
          case 2009:
            tempFilter[0].Hourly_Counts += item.Hourly_Counts
            break
          case 2010:
            tempFilter[1].Hourly_Counts += item.Hourly_Counts
            break
          case 2011:
            tempFilter[2].Hourly_Counts += item.Hourly_Counts
            break
          case 2012:
            tempFilter[3].Hourly_Counts += item.Hourly_Counts
            break
          case 2013:
            tempFilter[4].Hourly_Counts += item.Hourly_Counts
            break
          case 2014:
            tempFilter[5].Hourly_Counts += item.Hourly_Counts
            break
          case 2015:
            tempFilter[6].Hourly_Counts += item.Hourly_Counts
            break
        }
      })
      return tempFilter
    },
    // 创建曲线图表
    createLineNew (data, className) {
      let g_width = this.width - this.margin.left - this.margin.right
      let g_height = this.height - this.margin.top - this.margin.bottom

      // 缩放函数
      let scale_x = d3.scaleLinear().domain([0, data.length - 1]).range([0, g_width])
      // let scale_y = d3.scaleLinear().domain([0, d3.max(data.Hourly_Counts)]).range([0, g_height])
      let scale_y = d3.scaleLinear().domain([d3.max(data, d => d.Hourly_Counts) || 1,0]).range([0, g_height])
      // let scale_y = d3.scaleLinear().domain([1500000, 0]).range([0, g_height])

      // 折线生成
      let line_generator = d3.line()
        .x((d,i) => {
          return scale_x(i)
        })
        .y((d,i) => {
          return scale_y(d.Hourly_Counts)
        })

      // let svg = d3.select('.spring')
      let svg = d3.select(className)
        .append('svg')
        .attr('width', this.width)
        .attr('height', this.height)
      
      svg
        .append('text')
        .text(className.replace('.', ''))
        .attr('dy', '1em')
        .style('fill', '#f77c0c')

      svg
        .append('text')
        .text(this.sensor)
        .attr('class', `name-${className.replace('.', '')}`)
        .attr('dx', g_width / 2)
        .attr('dy', '2.5em')
        .style('fill', '#3a8ee6')
        .style('font-size', '12px')
        .style('text-anchor', 'middle')

      let g = svg
        .append('g')
        .attr('transform', `translate(${this.margin.left}, ${this.margin.top})`)
      
      g
        .append('path')
        .attr('d', line_generator(data))
        .attr('class', className.replace('.', ''))
        .style('fill', 'none')
        .style('stroke', '#3a8ee6')
        .style('stroke-width', '1')

      // x轴坐标比例尺
      let scale_xz = d3.scaleTime()
        .range([0, g_width])
        .domain([new Date(2009, 0), new Date(2015, 1)])
        // y轴坐标比例尺
      let scale_yz = d3.scaleLinear()
        .domain([d3.max(data, d => d.Hourly_Counts) || 1, 0 ])
        .range([0, g_height])

      // x 坐标轴
      g
        .append('g')
        .call(d3.axisBottom(scale_xz))
        .attr('transform', `translate(0, ${g_height})`)
      // y 坐标轴
      g
        .append('g')
        .call(d3.axisLeft(scale_yz))
        .append('text')
        .text('人数')
        .style('stroke', '#606266')
        .style('stroke-width', '1')
        .style('font-weight', '300')
        .style('text-anchor', 'middle')
        // .style('transform', 'rotate(-90deg)')
        // .attr('dy', '1em')
        .attr('dy', '-1em')
        // .attr('dx', '2em')

    },
    // ----------- 折线图End

    // 创建饼状svg
    createPieSvg () {
      let width = 400
      let height = 400
      let svg = d3.select('.pie-chart')
        .append('svg')
        .attr('width', width)
        .attr('height', height)

      let textG = svg
        .append('g')
      
      let g = svg.append('g')
        .attr('transform', `translate(200, 200)`)

      let arg_generator = d3.arc()
        .innerRadius(100)
        .outerRadius(200)
        // .startAngle(0)
        // // .endAngle(Math.PI / 2);
        // .endAngle(120 * Math.PI / 180);

      let result = this.filterPieData()

      let angle_data = d3.pie()
        .value(d => d.Hourly_Counts)

      let color = d3.scaleOrdinal(d3.schemeCategory10)
      g
        .selectAll('path')
        .data(angle_data(result))
        .enter()
        .append('path')
        .attr('d', arg_generator)
        .style('fill', (d,i) => color(i))

      g
        .selectAll('text')
        .data(angle_data(result))
        .enter()
        .append('text')
        .text((d) => d.data.Hourly_Counts)
        .attr('transform', d => `translate(${arg_generator.centroid(d)})`)
        .attr('text-anchor', 'middle')
      
    },
    // 过滤出饼状数据
    filterPieData () {
      let spring = this.sensorClassify.spring[0]
      this.sensorClassify.spring.forEach(item => {
        if (item.Year === this.date && item.Sensor_Name === this.sensor) {
          spring.Hourly_Counts += item.Hourly_Counts
        }
      })
      let summer = this.sensorClassify.summer[0]
      this.sensorClassify.summer.forEach(item => {
        if (item.Year === this.date && item.Sensor_Name === this.sensor) {
          summer.Hourly_Counts += item.Hourly_Counts
        }
      })
      let autumn = this.sensorClassify.autumn[0]
      this.sensorClassify.autumn.forEach(item => {
        if (item.Year === this.date && item.Sensor_Name === this.sensor) {
          autumn.Hourly_Counts += item.Hourly_Counts
        }
      })
      let winter = this.sensorClassify.winter[0]
      this.sensorClassify.winter.forEach(item => {
        if (item.Year === this.date && item.Sensor_Name === this.sensor) {
          winter.Hourly_Counts += item.Hourly_Counts
        }
      })
      let result = [spring, summer, autumn, winter]
      console.log('result', result)
      return result
    },


    // 折线图
    createLine (d) {
      // let data = [1,5,2,7,4,1,2,3,1,1,1]
      let data = d

      let g_width = this.width - this.margin.left - this.margin.right
      let g_height = this.height - this.margin.top - this.margin.bottom

      let scale_x = d3.scaleLinear()
        .domain([0, data.length - 1]) // 输入范围
        .range([0, g_width]) // 输出范围

      let scale_y = d3.scaleLinear()
      .domain([ 0, d3.max(data) ])
      .range([ g_height, 0 ])

      // let line_generator = d3.line()
      //   .x((d, i) => scale_x(i))
      //   .y((d, i) => scale_y(d))
      //   .curve(d3.curveCatmullRom.alpha(0))
      let line_generator = d3.line()
        .x((d, i) => {
          console.log(d)
          scale_x(d.Year)
        })
        .y((d, i) => scale_y(d.Hourly_Counts))
        .curve(d3.curveCatmullRom.alpha(0))

      let g = d3.select('.spring')
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
      let scale_xz = d3.scaleOrdinal().domain([2009,2010,2012]).range([0, 100])
      let x_axis = d3.axisBottom(scale_xz)
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
        .text('人数')
        .style('stroke', '#f00')
        .style('transform', 'rotate(-90deg)')
        .attr('dy', '1em')

    },

    async sensorData () {
      let sensor = await d3.csv('/static/csvData/Sensor_Locations.csv')
      this.sensorArr = sensor
      this.sensor = sensor[1].Sensor_Name
    }
  },
  async mounted () {

    // this.createMapSvg()
    // this.createHistogram()
    // this.createLine()

    this.sensorData()

    await this.readCsv()

    this.createPieSvg()
    // await this.divideSeason()
  },
  watch: {

  }
}
</script>
<style lang="less" scoped>
.main {
  // display: flex;
}
.map, .histogram, .pie {
  width: 100%;
  height: 100vh;
  border-bottom: 1px solid #ccc;
}
path {
  stroke: #fcc;
  stroke-width: 2;
  fill: none;
}
.histogram {
  padding: 20px;
  box-sizing: border-box;
  .select-box {
    display: flex;
  }
  .date, .sensor {
    width: 300px;
  }
  .line-chart {
    display: flex;
    flex-wrap: wrap;
    .spring, .summer, .autumn, .winter {
      width: 500px;
      height: 350px;
      background: #fdfdfd;
      margin: 5px;
    }
  }
}
.pie {
  .pie-chart {

  }
}
</style>
