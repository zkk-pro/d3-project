<template>
  <div class="main">
    <!-- <div class="map"></div> -->
    <div class="bg"></div>
    <div class="main-page">
      <h1>Hey, Welcome to Melbourne CBD pedestrian counts Exploration</h1>
      <p>In this exploration, there are 11 sensors in Melbourne CBD has be choosen to visualization , and the record from 2009 to 2015. In the visualization, four seasons is the measurement for pedestrian counts to make comparison in different sensor location and year. Hope this visualization help you make great decision.</p>
    </div>
    <div class="chart" v-loading="loading">
      <div class="histogram">
        <h2>The pedestrian counts in each season from 2009 to 2015</h2>
        <div class="line-chart">
          <div class="spring"></div>
          <div class="summer"></div>
        </div>
        <div class="line-chart" style="margin-top: 20px;">
          <div class="autumn"></div>
          <div class="winter"></div>
        </div>
      </div>
      <div class="select-box">
          <h3>Choose a sensor here：</h3>
          <div class="sensor">
            <el-select v-model="sensor" placeholder="Sensor" @change="sensorChange">
              <el-option
                v-for="item in sensorArr"
                :key="item.Sensor_Name"
                :label="item.Sensor_Name"
                :value="item.Sensor_Name">
              </el-option>
            </el-select>
          </div>
        </div>
      <div class="pie">
        <div class="date">
          <span class="date-des">data in different year：</span>
          <el-select v-model="date" style="width: 100px" placeholder="Year" @change="dateChange">
            <el-option
              v-for="item in dateArr"
              :key="item"
              :label="item"
              :value="item">
            </el-option>
          </el-select>
        </div>      
        
        <div class="chart-box">
          <div class="pie-chart">
            <h3>the percentage of counts in each season in {{date}}</h3>
          </div>
          <!-- 趋势 -->
          <div class="trend">
            <h3>The trend of pedestrian counts in each season in {{date}}</h3>
            <div class="trend-chart"></div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import * as d3 from 'd3'

export default {
  name: 'Main',
  data () {
    return {
      loading: true, 
      width: 500, // svg 宽度
      height: 300, // svg 高度
      dateArr: [2009, 2010, 2011, 2012, 2013, 2014,2015], // 所有年份
      date: 2009, // 当前选择的年份
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

      // 折线图 使用的margin
      margin: {
        left: 70,
        top: 50,
        right: 20,
        bottom: 20
      },

      // 饼状图
      pieTotalCount: 0,
      arg_generator: '', // arg生成器
      angle_data: '', // 角度数据

      // 趋势图
      trend: ''
      
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
      this.updatePie()
      this.updateTrend()
    },
    updateSpring () {
      let data = this.springData
      let g_width = this.width - this.margin.left - this.margin.right
      let g_height = this.height - this.margin.top - this.margin.bottom

      // 缩放函数
      let scale_x = d3.scaleLinear().domain([0, data.length - 1]).range([0, g_width])
      let scale_y = d3.scaleLinear().domain([d3.max(data, d => d.Hourly_Counts) || 1, 0]).range([0, g_height])

      // 折线生成
      let line_generator = d3.line()
        .x((d,i) => {
          return scale_x(i)
        })
        .y((d,i) => {
          return scale_y(d.Hourly_Counts)
        })
      let scale_yz = d3.scaleLinear()
        .domain([d3.max(data, d => d.Hourly_Counts) || i + 10, 0 ])
        .range([0, g_height])

      d3.selectAll('path.spring').attr('d', line_generator(data))
      d3.selectAll('.name-spring').text(this.sensor)
      d3.selectAll('g.spring-y').call(d3.axisLeft(scale_yz))
    },
    updateSummer () {
      let data = this.summerData
      let g_width = this.width - this.margin.left - this.margin.right
      let g_height = this.height - this.margin.top - this.margin.bottom

      // 缩放函数
      let scale_x = d3.scaleLinear().domain([0, data.length - 1]).range([0, g_width])
      let scale_y = d3.scaleLinear().domain([d3.max(data, d => d.Hourly_Counts) || 1,0 ]).range([0, g_height])

      let scale_yz = d3.scaleLinear()
        .domain([d3.max(data, d => d.Hourly_Counts) || i + 10, 0 ])
        .range([0, g_height])

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
      d3.selectAll('g.summer-y').call(d3.axisLeft(scale_yz))
    },
    updateAutumn () {
      let data = this.autumnData
      let g_width = this.width - this.margin.left - this.margin.right
      let g_height = this.height - this.margin.top - this.margin.bottom

      // 缩放函数
      let scale_x = d3.scaleLinear().domain([0, data.length - 1]).range([0, g_width])
      let scale_y = d3.scaleLinear().domain([d3.max(data, d => d.Hourly_Counts) || 1,0 ]).range([0, g_height])

      let scale_yz = d3.scaleLinear()
        .domain([d3.max(data, d => d.Hourly_Counts) || i + 10, 0 ])
        .range([0, g_height])

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
      d3.selectAll('g.autumn-y').call(d3.axisLeft(scale_yz))
    },
    updateWinter() {
      let data = this.winterData
      let g_width = this.width - this.margin.left - this.margin.right
      let g_height = this.height - this.margin.top - this.margin.bottom

      // 缩放函数
      let scale_x = d3.scaleLinear().domain([0, data.length - 1]).range([0, g_width])
      let scale_y = d3.scaleLinear().domain([d3.max(data, d => d.Hourly_Counts) || 1,0 ]).range([0, g_height])

      let scale_yz = d3.scaleLinear()
        .domain([d3.max(data, d => d.Hourly_Counts) || i + 10, 0 ])
        .range([0, g_height])

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
      d3.selectAll('g.winter-y').call(d3.axisLeft(scale_yz))
    },

    // 时间选择事件
    dateChange(value) {
      this.date = value
      console.log(this.date)
      this.updatePie()
      this.updateTrend()
    },
    // 更新饼状图
    updatePie () {
      let result = this.filterPieData()
      let color = d3.scaleOrdinal(d3.schemeSet2)

      console.log('angle:', this.angle_data(result))
      d3.selectAll('.pie-path')
        .data(this.angle_data(result))
        .attr('d', this.arg_generator)
        .style('fill', (d,i) => color(i))
      
      d3.selectAll('.pie-text')
        .data(this.angle_data(result))
        .text(d => {
          if (d.data.Hourly_Counts) 
            return (d.data.Hourly_Counts / this.pieTotalCount * 100).toFixed(2) + '%'
          else
            return ''
        })
        .attr('transform', d => `translate(${this.arg_generator.centroid(d)})`)
        .attr('text-anchor', 'middle')
    },
    // 更新趋势图
    updateTrend () {
      let data = this.filterTrendData()
      let g_width = this.width - this.margin.left - this.margin.right
      let g_height = this.height - this.margin.top - this.margin.bottom

      // 缩放函数
      let scale_x = d3.scaleLinear().domain([0, data.length - 1]).range([60, g_width -60])
      let scale_y = d3.scaleLinear().domain([d3.max(data, d => d.Hourly_Counts) || 1,0 ]).range([0, g_height])

      // 折线生成
      let line_generator = d3.line()
        .x((d,i) => {
          return scale_x(i)
        })
        .y((d,i) => {
          return scale_y(d.Hourly_Counts)
        })
      let scale_yz = d3.scaleLinear()
        .domain([d3.max(data, (d, i) => d.Hourly_Counts || i + 10), 0])
        .range([0, g_height])

      d3.selectAll('path.trend').attr('d', line_generator(data))
      d3.selectAll('g.trend-y').call(d3.axisLeft(scale_yz))
    },

// ----------- 折线图---------------
    // 读取csv数据
    async readCsv() {
      try {
        let csvData = await d3.json('/static/jsonData/Pedestrian_Counting_System_perhour.json')
        this.csvData = csvData
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
            let spring = item
            spring.Year = parseInt(spring.Year)
            spring.Hourly_Counts = parseInt(spring.Hourly_Counts)
            this.sensorClassify.spring.push(spring)
            break
          case 'April':
          case 'May':
          case 'June':
            let summer = item
            summer.Year = parseInt(summer.Year)
            summer.Hourly_Counts = parseInt(summer.Hourly_Counts)
            this.sensorClassify.summer.push(summer)
            break
          case 'July':
          case 'August':
          case 'September':
            let autumn = item
            autumn.Year = parseInt(autumn.Year)
            autumn.Hourly_Counts = parseInt(autumn.Hourly_Counts)
            this.sensorClassify.autumn.push(autumn)
            break
          case 'October':
          case 'November':
          case 'December':
            let winter = item
            winter.Year = parseInt(winter.Year)
            winter.Hourly_Counts = parseInt(winter.Hourly_Counts)
            this.sensorClassify.winter.push(winter)
            break
        }
      })
      
      this.filtered()

      this.createLine(this.springData, '.spring')
      this.createLine(this.summerData, '.summer')
      this.createLine(this.autumnData, '.autumn')
      this.createLine(this.winterData, '.winter')
    },
    // 过滤好的数据
    filtered () {
      this.springData = this.filterYearData(this.sensorClassify.spring)

      this.summerData = this.filterYearData(this.sensorClassify.summer)

      this.autumnData = this.filterYearData(this.sensorClassify.autumn)

      this.winterData = this.filterYearData(this.sensorClassify.winter)
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
    createLine (data, className) {
      let g_width = this.width - this.margin.left - this.margin.right
      let g_height = this.height - this.margin.top - this.margin.bottom

      // 缩放函数
      let scale_x = d3.scaleLinear().domain([0, data.length - 1]).range([0, g_width])
      let scale_y = d3.scaleLinear().domain([d3.max(data, d => d.Hourly_Counts) || 1,0]).range([0, g_height])

      // 折线生成
      let line_generator = d3.line()
        .x((d,i) => {
          return scale_x(i)
        })
        .y((d,i) => {
          return scale_y(d.Hourly_Counts)
        })

      let svg = d3.select(className)
        .append('svg')
        .attr('width', this.width)
        .attr('height', this.height)
      
      svg
        .append('text')
        .text(className.replace('.', ''))
        .attr('dy', '1em')
        .attr('dx', '1em')
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
        .style('stroke-width', '2')

      // x轴坐标比例尺
      let scale_xz = d3.scaleTime()
        .range([0, g_width])
        .domain([new Date(2009, 0), new Date(2015, 1)])
        // y轴坐标比例尺
      let scale_yz = d3.scaleLinear()
        .domain([d3.max(data, d => d.Hourly_Counts) || i + 10, 0 ])
        .range([0, g_height])

      // x 坐标轴
      g
        .append('g')
        .call(d3.axisBottom(scale_xz))
        .attr('transform', `translate(0, ${g_height})`)
      // y 坐标轴
      g
        .append('g')
        .attr('class', `${className.replace('.', '')}-y`)
        .call(d3.axisLeft(scale_yz))
        .append('text')
        .text('counts')
        .style('stroke', '#606266')
        .style('stroke-width', '1')
        .style('font-weight', '300')
        .style('text-anchor', 'middle')
        .attr('dy', '-1em')
      this.loading = false
    },
    // ----------- 折线图End---------------

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
        .attr('transform', `translate(210, 180)`)

      let color = d3.scaleOrdinal(d3.schemeSet2)
      let textArr = ['spring', 'summer', 'autumn', 'winter']

      textArr.forEach((item, index) => {
        textG
          .append('text')
          .text(item)
          .attr('dy', `${index}em`)
          .style('fill', color(index))
          .attr('text-anchor', 'middle')
      })
      textArr.forEach((item, index) => {
        textG
          .append('circle')
          .attr('cx', -40)
          .attr('cy', `${index}em`)
          .attr('r', 7)
          .style('fill', color(index))
          .style('transform', 'translate(0, -4px)')
      })
      
      let g = svg.append('g')
        .attr('transform', `translate(200, 200)`)

      this.arg_generator = d3.arc()
        .innerRadius(100)
        .outerRadius(200)

      let result = this.filterPieData()
      this.pieTotalCount = result.reduce((total, current) => {
        return total += current.Hourly_Counts
      }, 0)
      console.log('totalCount', this.pieTotalCount)

      this.angle_data = d3.pie()
        .value(d => d.Hourly_Counts || 1)

      console.log('angle_data', this.angle_data(result))
      g
        .selectAll('path')
        .data(this.angle_data(result))
        .enter()
        .append('path')
        .attr('class', 'pie-path')
        .attr('d', this.arg_generator)
        .style('fill', (d,i) => color(i))

      // 饼图上的百分比
      g
        .selectAll('text')
        .data(this.angle_data(result))
        .enter()
        .append('text')
        .attr('class', 'pie-text')
        .text(d => {
          if (d.data.Hourly_Counts) 
            return (d.data.Hourly_Counts / this.pieTotalCount * 100).toFixed(2) + '%'
          else
            return ''
        })
        .attr('transform', d => `translate(${this.arg_generator.centroid(d)})`)
        .attr('text-anchor', 'middle')
      
    },
    // 过滤出饼状数据
    filterPieData () {
      let spring = {
        Year: this.date,
        Hourly_Counts: 0,
        Sensor_Name: this.sensor,
      }
      this.sensorClassify.spring.forEach(item => {
        if (item.Year === this.date && item.Sensor_Name === this.sensor) {
          spring.Hourly_Counts += item.Hourly_Counts
        }
      })

      let summer = {
        Year: this.date,
        Hourly_Counts: 0,
        Sensor_Name: this.sensor,
      }
      this.sensorClassify.summer.forEach(item => {
        if (item.Year === this.date && item.Sensor_Name === this.sensor) {
          summer.Hourly_Counts += item.Hourly_Counts
        }
      })

      let autumn = {
        Year: this.date,
        Hourly_Counts: 0,
        Sensor_Name: this.sensor,
      }
      this.sensorClassify.autumn.forEach(item => {
        if (item.Year === this.date && item.Sensor_Name === this.sensor) {
          autumn.Hourly_Counts += item.Hourly_Counts
        }
      })

      let winter = {
        Year: this.date,
        Hourly_Counts: 0,
        Sensor_Name: this.sensor,
      }
      this.sensorClassify.winter.forEach(item => {
        if (item.Year === this.date && item.Sensor_Name === this.sensor) {
          winter.Hourly_Counts += item.Hourly_Counts
        }
      })
      let result = [spring, summer, autumn, winter]
      console.log('result', result)
      return result
    },

    // 趋势图
    createTrendChart () {
      let data = this.filterTrendData()
      
      let g_width = this.width - this.margin.left - this.margin.right
      let g_height = this.height - this.margin.top - this.margin.bottom

      // 缩放函数
      let scale_x = d3.scaleLinear().domain([0, data.length - 1]).range([60, g_width -60])
      let scale_y = d3.scaleLinear().domain([d3.max(data, d => d.Hourly_Counts) || 1,0]).range([0, g_height])

      // 折线生成
      let line_generator = d3.line()
        .x((d,i) => {
          return scale_x(i)
        })
        .y((d,i) => {
          return scale_y(d.Hourly_Counts)
        })

      let svg = d3.select('.trend-chart')
        .append('svg')
        .attr('width', this.width)
        .attr('height', this.height)

      let g = svg
        .append('g')
        .attr('transform', `translate(${this.margin.left}, ${this.margin.top})`)
      
      g
        .append('path')
        .attr('d', line_generator(data))
        .attr('class', 'trend')
        .style('fill', 'none')
        .style('stroke', '#3a8ee6')
        .style('stroke-width', '2')

      // x轴坐标比例尺
      let scale_xz = d3.scaleBand()
        .range([0, g_width])
        .domain(['spring', 'summer', 'autumu', 'winter'])

        // y轴坐标比例尺
      let scale_yz = d3.scaleLinear()
        .domain([d3.max(data, (d, i) => d.Hourly_Counts || i + 10), 0])
        .range([0, g_height])

      // x 坐标轴
      g
        .append('g')
        .call(d3.axisBottom(scale_xz))
        .attr('transform', `translate(0, ${g_height})`)
      // y 坐标轴
      g
        .append('g')
        .attr('class', 'trend-y')
        .call(d3.axisLeft(scale_yz))
        .append('text')
        .text('counts')
        .style('stroke', '#606266')
        .style('stroke-width', '1')
        .style('font-weight', '300')
        .style('text-anchor', 'middle')
        .attr('dy', '-1em')
      
    },
    // 过滤趋势数据
    filterTrendData () {
      let tempFilter = [
        {season: 'spring', Hourly_Counts: 0},
        {season: 'summer', Hourly_Counts: 0},
        {season: 'autumn', Hourly_Counts: 0},
        {season: 'winter', Hourly_Counts: 0}
      ]
      let filterData = this.csvData.filter(item => {
        return item.Sensor_Name === this.sensor && item.Year === this.date
      })
      
      filterData.forEach(item => {
        switch (item.Month) {
          case 'January':
          case 'February':
          case 'March':
            tempFilter[0].Hourly_Counts += item.Hourly_Counts
            break
          case 'April':
          case 'May':
          case 'June':
            tempFilter[1].Hourly_Counts += item.Hourly_Counts
            break
          case 'July':
          case 'August':
          case 'September':
            tempFilter[2].Hourly_Counts += item.Hourly_Counts
            break
          case 'October':
          case 'November':
          case 'December':
            tempFilter[3].Hourly_Counts += item.Hourly_Counts
            break
        }
      })
      console.log('trend-data-->', tempFilter)
      return tempFilter
    },

    // 感应器数据
    async sensorData () {
      let sensor = await d3.json('/static/jsonData/location.json')
      this.sensorArr = sensor
      this.sensor = sensor[0].Sensor_Name
    }
  },
  async mounted () {

    this.sensorData()

    await this.readCsv()

    this.createPieSvg()
    this.createTrendChart()
  }
}
</script>
<style lang="less" scoped>
.main {
  min-width: 100%;
  background: rgba(0, 0, 0, .6);
  color: #fff;
}
.bg::before {
  content: '';
  position: fixed;
  top: 0;
  left:0;
  right: 0;
  bottom: 0;
  background: url('../../assets/img/meb_cbd.jpg');
  background-size: cover;
  background-repeat: no-repeat;
  z-index: -10;
}
.main-page {
  width: 100%;
  height: 100vh;
  text-align: center;
  position: relative;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  h1 {
    padding: 100px;
  }
  p {
    width: 80%;
    margin: 0 auto;
    margin-bottom: 10px;
    word-wrap: break-word;
    font-size: 22px;
  }
  i {
    position: absolute;
    bottom: 20px;
    font-size: 60px;
    background: rgba(255, 255, 255, .3);
    border-radius: 50%;
  }
}
.map, .histogram, .pie {
  width: 100%;
}
path {
  stroke: #fcc;
  stroke-width: 2;
  fill: none;
}
.histogram {
  padding: 20px;
  box-sizing: border-box;
  h2 {
    text-align: center;
    padding: 10px 0;
  }
  .line-chart {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    .spring, .summer, .autumn, .winter {
      height: 330px;
      background: #fdfdfd;
      margin: 5px;
    }
  }
}
.select-box {
    display: flex;
    justify-content: center;
    align-items: center;
    margin: 30px 0;
  }
  .date, .sensor {
    margin: 0 5px;
  }
.pie {
  background: rgba(255, 255, 255, .8);
  padding: 20px;
  box-sizing: border-box;
  text-align: center;
  .date {
    margin: 30px;
    .date-des {
      color: #333;
      font-weight: 500;
    }
  }
  h3 {
    color: #333;
    text-align: center;
    margin-bottom: 30px;
  }
  .chart-box {
    display: flex;
    justify-content: center;
    flex-wrap: wrap;
    .trend, .pie-chart {
      flex: 1;
    }
    .pie-chart {
      width: 400px;
    }
    .trend {
      justify-self: center;
      .trend-chart {
        width: 500px;
        margin: 0 auto;
        background: #fff;
      }
    }
  }
}
</style>
