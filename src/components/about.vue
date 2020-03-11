<template>
  <div class="hello">
    <!-- 初始化echarts 需要一个有宽高的盒子 -->
    <div ref="mapbox" style="height:650px;width:1000px" class="box1"></div>
    <div ref="linebox" style="height:650px;width:1000px" class="box2"></div>
    <router-view></router-view>
  </div>
</template>

<script>
import echarts from "echarts";
import "echarts/map/js/china.js";
import jsonp from "jsonp";

// 不写data中提高性能 写data会自动监听所有属性
// 使用地图先注册地图
// 折线图颜色
const colors = ["#5793f3", "#d14a61", "#675bba"];

const option = {
  title: {
    // 标题
    text: "TyrantMap",
    link: "https://zhujingyu.com",
    // 副标题
    // ***************修改**************
    subtext: "全国疫情图-数据源于新浪",
    sublink: "https://zhujingyu.com/tyrantOld"
  },
  // 样式设定
  series: [
    {
      type: "map", // 设定echarts渲染的是地图
      map: "china", //渲染中国地图
      label: {
        //控制对应地区的汉字
        show: true,
        color: "black", //地区名字体颜色
        fontSize: 15
      },
      zoom: 1.25, //控制地图放大缩小系数
      // 全局地图样式
      itemStyle: {
        areaColor: "#fff",
        borderColor: "#000",
        shadowColor: "rgba(0, 0, 0, 0.5)",
        shadowBlur: 10,
        borderWidth: 2
      },
      // 鼠标滑过的样式设置 （强调设置
      emphasis: {
        itemStyle: {
          // 鼠标经过区域的各类样式
          areaColor: "skyblue"
        },
        label: {
          color: "#fff",
          fontSize: 16
        }
      },
      // 用来展示后台给的数据 {name:xx,value:XXX}
      data: [],
      name: "累计确诊人数",
      // 鼠标滚轮能否放大缩小
      roam: false
    }
  ],
  // 控制对应区域显示颜色 以及分段
  visualMap: [
    {
      type: "piecewise",
      show: true,
      splitNumber: 5, //分成x段
      pieces: [
        // 分段数值
        { min: 10000 },
        { min: 1000, max: 9999 },
        { min: 100, max: 999 },
        { min: 10, max: 99 },
        { min: 0, max: 9 }
      ],
      // 改变分段颜色方块位置 默认为left
      align: "",
      // 分段总体横竖显示控制 horizontal水平 vertical竖直 默认竖直
      orient: "vertical",
      // left right top bottom 控制分段所在位置
      // textStyle:{} 分段数据字体样式

      inRange: {
        // 图标样式
        symbol: "",
        // 写两个颜色 自动过度 如果是特定颜色 几个分段写几个颜色
        color: ["#ECFCFF", "#B2FCFF", "#5EDFFF", "#3E64FF", "#121B74"]
      },
      itemWidth: 15,
      itemHeight: 10,
      // tag样式
      textStyle: {
        fontSize: 12,
        fontWeight: 600
      }
    }
  ],
  // 位置
  // bottom: 0,
  // 鼠标经过显示提示
  tooltip: {
    trigger: "item"
  },
  // 右侧工具栏
  toolbox: {
    show: true,
    orient: "vertical",
    left: "right",
    top: "center",
    feature: {
      // dataView: { readOnly: false },
      // restore: {},
      saveAsImage: {}
    }
  }
};
const option2 = {
  color: colors,

  tooltip: {
    trigger: "none",
    axisPointer: {
      type: "cross"
    }
  },
  legend: {
    data: ["现存确诊", "累计确诊", "现存疑似"]
  },
  grid: {
    top: 70,
    bottom: 50
  },
  xAxis: [
    {
      type: "category",
      axisTick: {
        alignWithLabel: true
      },
      axisLine: {
        onZero: false,
        lineStyle: {
          color: colors[1]
        }
      },
      axisPointer: {
        label: {}
      },
      data: [],
      inverse: true
    },
    {
      type: "category",
      axisTick: {
        alignWithLabel: true
      },
      axisLine: {
        onZero: false,
        lineStyle: {
          color: colors[0]
        }
      },
      axisPointer: {
        label: {}
      },
      data: [],
      inverse: true
    },
    // 疑似
    {
      type: "category",
      axisTick: {
        alignWithLabel: true
      },
      axisLine: {
        onZero: false,
        lineStyle: {
          color: colors[2]
        }
      },
      axisPointer: {
        label: {}
      },
      data: [],
      inverse: true
    }
  ],
  yAxis: [
    {
      type: "value"
    }
  ],
  series: [
    {
      name: "现存确诊",
      type: "line",
      xAxisIndex: 0,
      smooth: true,
      data: [],
      inverse: true
    },
    {
      name: "累计确诊",
      type: "line",
      xAxisIndex: 1,
      smooth: true,
      data: [],
      inverse: true
    },
    // 疑似
    {
      name: "现存疑似",
      type: "line",
      xAxisIndex: 1,
      smooth: true,
      data: [],
      inverse: true
    }
  ]
};

export default {
  name: "HelloWorld",
  methods: {
    // 累计数据获取
    getData() {
      jsonp(
        "https://interface.sina.cn/news/wap/fymap2020_data.d.json",
        {},
        (err, data) => {
          if (!err) {
            // err不存在 代表请求数据成功
            console.log(data);
            let list = data.data.list.map(item => ({
              name: item.name,
              value: item.econNum
            }));
            option.series[0].data = list;
            this.mychart.setOption(option);
          }
        }
      );
    },
    // linebox data获取
    getSumDate() {
      jsonp(
        "https://interface.sina.cn/news/wap/fymap2020_data.d.json",
        {},
        (err, data) => {
          if (!err) {
            // 累计确诊数据获取
            // date
            let list = data.data.historylist.map(item => ({
              value: item.date
            }));
            option2.xAxis[0].data = list;

            // 累计确诊数量
            let list2 = data.data.historylist.map(item => item.cn_conNum * 1);
            option2.series[1].data = list2;

            // 现存确诊数据获取
            // date
            let list3 = data.data.historylist.map(item => ({
              value: item.date
            }));
            option2.xAxis[1].data = list3;

            // 现存确诊数量
            let list4 = data.data.historylist.map(item => item.cn_econNum * 1);
            option2.series[0].data = list4;

            // 现存疑似
            // date
            let list5 = data.data.historylist.map(item => ({
              value: item.date
            }));
            option2.xAxis[2].data = list5;
            // this.mychart2.setOption(option2);
            // 现存疑似
            let list6 = data.data.historylist.map(item => item.cn_susNum * 1);
            option2.series[2].data = list6;
            this.mychart2.setOption(option2);
          }
        }
      );
    }
  },
  mounted() {
    this.getData();
    this.mychart = echarts.init(this.$refs.mapbox);
    this.mychart.setOption(option);
    // 折线图渲染
    this.getSumDate();
    this.mychart2 = echarts.init(this.$refs.linebox);
    this.mychart2.setOption(option2);
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
* {
  margin: 0;
  padding: 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}

.box2 {
  margin-top: 50px;
  margin-left: 50%;
  transform: translateX(-50%);
}
#app {
  margin: 0;
  padding: 0;
}
.box1 {
  margin-bottom: 50px;
  margin-left: 50%;
  transform: translateX(-50%);
}
</style>
