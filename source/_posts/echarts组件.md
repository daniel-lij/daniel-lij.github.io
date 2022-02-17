---
title: P4 中文件上传下载
date: 2021-10-21 10:11:16
tags:
  - 工作记录
categories: [工作记录]
---

# echarts5.21之后的echarts组件

### 组件自适应,自动读取父元素高度,简单不到3kb

```javascript
<script>
import { use } from 'echarts/core'
import * as echarts from 'echarts/core'
import 'echarts-liquidfill'
import $ from 'jquery'
import {
  BarChart,
  LineChart,
  PieChart,
  MapChart,
  RadarChart,
  ScatterChart,
  EffectScatterChart,
  LinesChart,
} from 'echarts/charts'
import {
  GridComponent,
  PolarComponent,
  GeoComponent,
  TooltipComponent,
  LegendComponent,
  TitleComponent,
  VisualMapComponent,
  DatasetComponent,
  ToolboxComponent,
  DataZoomComponent,
} from 'echarts/components'
import { CanvasRenderer, SVGRenderer } from 'echarts/renderers'
import { throttle } from 'lodash'
use([
  BarChart,
  LineChart,
  PieChart,
  MapChart,
  RadarChart,
  ScatterChart,
  EffectScatterChart,
  LinesChart,
  GridComponent,
  PolarComponent,
  GeoComponent,
  TooltipComponent,
  LegendComponent,
  TitleComponent,
  VisualMapComponent,
  DatasetComponent,
  CanvasRenderer,
  SVGRenderer,
  ToolboxComponent,
  DataZoomComponent,
])
export default {
  props: {
    option: {
      type: Object,
      default: () => {},
    },
    theme: {
      type: Object,
      default: null,
    },
  },

  data() {
    return {
      chart: null,
      widthAndHeight: {},
    }
  },

  watch: {
    option: {
      handler(newValue, oldValue) {
        var self = this
        self.$nextTick(() => {
          self.setOption(newValue)
        })
      },
      deep: true,
    },
  },

  mounted() {
    var self = this
    self.$nextTick(() => {
      console.log($(self.$refs.chartYT).width())
      self.getHiddenStyle(self.$refs.chartYT)
      self.chart = echarts.init(self.$refs.chartYT, this.theme, {
        width: `${self.widthAndHeight.width}px`,
        height: `${self.widthAndHeight.height}px`,
      })
      if (self.option) {
        self.setOption(self.option)
      }
      self.chart._resize = throttle(() => {
        self.chart.resize({
          width: `${self.$refs.chartYT.clientWidth}px`,
          height: `${self.$refs.chartYT.clientHeight}px`,
        })
      }, 200)
      window.addEventListener('resize', self.chart._resize)
    })
  },

  beforeDestroy() {
    window.removeEventListener('resize', this.chart._resize)
    clearTimeout(this.timeout)
  },

  methods: {
    setOption(option) {
      this.chart && this.chart.setOption(option)
    },

    getHiddenStyle(elem) {
      var properties, previous
      // elem元素可获取尺寸样式的属性
      properties = {
        position: 'absolute',
        visibility: 'hidden',
        display: 'block',
      }
      previous = {}
      // 对elme设置可获取宽高的样式
      for (var key in properties) {
        previous[key] = elem.style[key]
        elem.style[key] = properties[key]
      }
      // 获取宽高
      if (elem.offsetHeight > 0 || elem.offsetWidth > 0) {
        this.widthAndHeight = {
          width: this.$refs.chartYT.offsetWidth,
          height: this.$refs.chartYT.offsetHeight,
        }
        // 恢复原来的样式
        for (var akey in properties) {
          elem.style[akey] = previous[akey]
        }
      } else {
        for (var bkey in properties) {
          elem.style[bkey] = previous[bkey]
        }
        this.getHiddenStyle(elem.parentNode)
      }
    },
  },
}
</script>

<template>
  <div ref="chartYT" :class="$style.chart" />
</template>

<style lang="scss" module>
@import '@design';

.chart {
  position: relative;
  width: 100%;
  height: 100%;
  overflow: hidden;
}
</style>



```
