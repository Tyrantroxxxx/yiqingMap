<template>
  <div class="hello">
    <!-- 初始化echarts 需要一个有宽高的盒子 -->
    <div ref="mapbox" style="height:500px;width:700px"></div>
    <router-view></router-view>
  </div>
</template>

<script>
import echarts from "echarts";
import "echarts/map/js/china.js";
import jsonp from "jsonp";

// 不写data中提高性能 写data会自动监听所有属性
// 使用地图先注册地图
const option = {
  title: {
    // 标题
    text: "Tyrant",
    link: "https://zhujingyu.com",
    // 副标题
    // ***************修改**************
    subtext: "OK",
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
        fontSize: 10
      },
      zoom: 1.25, //控制地图放大缩小系数
      // 全局地图样式
      itemStyle: {
        areaColor: "#fff",
        borderColor: "#000"
      },
      // 鼠标滑过的样式设置 （强调设置
      emphasis: {
        itemStyle: {
          // 鼠标经过区域的各类样式
          areaColor: "skyblue"
        },
        label: {
          color: "#fff",
          fontSize: 12
        }
      },
      // 用来展示后台给的数据 {name:xx,value:XXX}
      data: []
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
        color: ["#ffc0b1", "#9c0505"]
      },
      itemWidth: 15,
      itemHeight: 10
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
              value: item.value
            }));
            option.series[0].data = list;
            this.mychart.setOption(option);
          }
        }
      );
    }
  },
  mounted() {
    this.getData();
    this.mychart = echarts.init(this.$refs.mapbox);
    this.mychart.setOption(option);
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
