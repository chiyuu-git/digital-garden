---
{"aliases":[],"tags":[],"review-dates":[],"dg-publish":true,"date-created":"2022-04-19-Tue, 10:06:27 pm","date-modified":"2023-04-29-Sat, 8:14:53 pm","permalink":"/programming/front-end/field/echarts-basic/","dgPassFrontmatter":true}
---


精读 《 echarts-for-react 源码 》: https://zhuanlan.zhihu.com/p/260474675

D3 教程成本太高，有待观察 https://www.youtube.com/watch?v=2LhoCfjm8R4

# ~~echarts-for-react~~

## 周边库定位

1. 封装就要有自己的明确定位，不要过度封装
2. 透传概念，不新增数据结构和技术概念
3. 符合技术栈的习惯（按照 React 的使用性质，按需增加一些开发优化的内容）

## 特性

### className + Style 属性

React 组件本质还是 ui 组件，我的意识中，每一个 React 组件应该有 className 和 style 属性，为其做样式的自定义

### echarts.init 参数作为顶层 Props

顶层 API 参数作为顶层 props，概念层级对等，开发者易于理解。这些包含有：

1. notMerge
2. showLoading
3. loadingOption
4. opts.renderer
5. ...

### Echarts Option 完全透传

echarts 使用 option 方式来构件图表，结果完善的文档、丰富的官网 DEMO，让开发者开发一个图形非常容易，几乎直接 copy 即可。

所以 echarts-for-react 对于 option 也是完全透传，不做任何修改，甚至都没有默认值的处理，达到的就是官网代码中的 option copy 到这里一样可用。

这大大降低了我自己的维护成本，也降低了开发者同学的调试成本

> react 报错了，先去 echarts 官网试试看，官网上可以，这边一定可以

### 按需决定 Update 还是 New

针对顶层 props，还是 option 变化，包内决定是否新建实例，还是在旧实例上进行更新。让开发者只需要关注在 props，它包里处理好不同 props 要做不同操作

### 自动适配容器大小

这个特性可以说是这个 200 行代码的封装库中，最核心的特性，图表自动根据容器的大小做 resize。为了这个功能， 我还特意增加了一个模块 [size-sensor](https://link.zhihu.com/?target=https%3A//github.com/hustcc/size-sensor)（为什么不用开源？之前用的开源各种问题，拖延不解决，所以自己实现一个简单一些的。）

自动适配容器大小，在目前 low-code、搭建产品中，非常非常必须，几乎可以说是必备功能。这一点在目前我负责的 G2、G2Plot 中同样有大量 issue。

### 按需加载

echarts 包本身还是很大的，混淆后接近 1M。所以按需加载是 echarts 的一个特性，本模块也通过一些代码架构，分拆除 core，让开发者决定如何进行分包和按需引入。

### 全新官网

之前的官网是自己初学 React 的时候，完全自己搭建的，没有 lint、ci，代码凌乱，样式也不好看。所以这次直接使用 [dumi](https://link.zhihu.com/?target=https%3A//github.com/umijs/dumi) 这个库自动生成，网站全部 markdown 开发，也方便大家遇到官网 typo，直接一键提交 PR。

## 基本使用

### Install

```
$ npm install --save echarts-for-react

# `echarts` is the peerDependence of `echarts-for-react`, you can install echarts with your own version.
$ npm install --save echarts
```

### 全量引入

```tsx
import ReactECharts from 'echarts-for-react';

// render echarts option.

<ReactECharts
  option={this.getOption()}
  notMerge={true}
  lazyUpdate={true}
  theme={"theme_name"}
  onChartReady={this.onChartReadyCallback}
  onEvents={EventsDict}
  opts={}
/>
```

### 按需引入

```tsx
import React from 'react';
// import the core library.
import ReactEChartsCore from 'echarts-for-react/lib/core';
// Import the echarts core module, which provides the necessary interfaces for using echarts.
import * as echarts from 'echarts/core';
// Import charts, all with Chart suffix
import {
  // LineChart,
  BarChart,
  // PieChart,
  // ScatterChart,
  // RadarChart,
  // MapChart,
  // TreeChart,
  // TreemapChart,
  // GraphChart,
  // GaugeChart,
  // FunnelChart,
  // ParallelChart,
  // SankeyChart,
  // BoxplotChart,
  // CandlestickChart,
  // EffectScatterChart,
  // LinesChart,
  // HeatmapChart,
  // PictorialBarChart,
  // ThemeRiverChart,
  // SunburstChart,
  // CustomChart,
} from 'echarts/charts';
// import components, all suffixed with Component
import {
  // GridSimpleComponent,
  GridComponent,
  // PolarComponent,
  // RadarComponent,
  // GeoComponent,
  // SingleAxisComponent,
  // ParallelComponent,
  // CalendarComponent,
  // GraphicComponent,
  // ToolboxComponent,
  TooltipComponent,
  // AxisPointerComponent,
  // BrushComponent,
  TitleComponent,
  // TimelineComponent,
  // MarkPointComponent,
  // MarkLineComponent,
  // MarkAreaComponent,
  // LegendComponent,
  // LegendScrollComponent,
  // LegendPlainComponent,
  // DataZoomComponent,
  // DataZoomInsideComponent,
  // DataZoomSliderComponent,
  // VisualMapComponent,
  // VisualMapContinuousComponent,
  // VisualMapPiecewiseComponent,
  // AriaComponent,
  // TransformComponent,
  DatasetComponent,
} from 'echarts/components';
// Import renderer, note that introducing the CanvasRenderer or SVGRenderer is a required step
import {
  CanvasRenderer,
  // SVGRenderer,
} from 'echarts/renderers';

// Register the required components
echarts.use(
  [TitleComponent, TooltipComponent, GridComponent, BarChart, CanvasRenderer]
);

// The usage of ReactEChartsCore are same with above.
<ReactEChartsCore
  echarts={echarts}
  option={this.getOption()}
  notMerge={true}
  lazyUpdate={true}
  theme={"theme_name"}
  onChartReady={this.onChartReadyCallback}
  onEvents={EventsDict}
  opts={}
/>
```

## [Props for Component](https://github.com/hustcc/echarts-for-react#props-of-component)

### **`option`** (required, object)

the echarts option config, can see https://echarts.apache.org/option.html#title.

### **`style`** (optional, object)

the `style` of echarts div. `object`, default is {height: '300px'}.

### **`className`** (optional, string)

the `class` of echarts div. you can setting the css style of charts by class name.

## [Component API & Echarts API](https://github.com/hustcc/echarts-for-react#component-api--echarts-api)

# 动态化

line race 的本质： 其实支持数据生成的特别慢而已

本身 line 就具有延伸的动画，只要数据的粒度够细，然后拉长渲染时间，自然就可以形成类似的动画了

然后只要让 label 做到 focus，自然而然的就形成了 race

所以 line race 并没有像 bar race 一样配置专门的文档。

因为本质上 line race 的效果早就可以实现了

不像是 bar race 还需要实现排名的动态变化

# 样式

## 调色盘

调色盘，可以在 option 中设置。它给定了一组颜色，图形、系列会自动从其中选择颜色。 可以设置全局的调色盘，也可以设置系列自己专属的调色盘。

## 通过 visualMap 组件设定样式

[visualMap 组件](https://echarts.apache.org/option.html#visualMap) 能指定数据到颜色、图形尺寸的映射规则，详见 [数据的视觉映射](https://echarts.apache.org/handbook/zh/concepts/visual-map)。

# DataSet

[Handbook - Apache ECharts](https://echarts.apache.org/handbook/zh/concepts/dataset)

更推荐使用 **数据集** 来管理数据。因为这样，数据可以被多个组件复用，也方便进行 “数据和其他配置” 分离的配置风格。毕竟，在运行时，数据是最常改变的，而其他配置大多并不会改变。
