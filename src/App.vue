<template>
  <div id="app">
    <div class="titleV">
      <a href="https://zhujingyu.com">TyrantMap</a>
    </div>
    <div class="card">
      <div class="card-item">
        <div class="itemTitle">现存确诊</div>
        <div class="num list1">{{list.econNum}}</div>
      </div>
      <div class="card-item">
        <div class="itemTitle">现存确诊重症</div>
        <div class="num list2">{{list.heconNum}}</div>
      </div>
      <div class="card-item">
        <div class="itemTitle">现存疑似</div>
        <div class="num list3">{{list.sustotal}}</div>
      </div>
      <div class="card-item">
        <div class="itemTitle">累计确诊</div>
        <div class="num list4">{{list.gntotal}}</div>
      </div>
      <div class="card-item">
        <div class="itemTitle">累计死亡</div>
        <div class="num list5">{{list.deathtotal}}</div>
      </div>
      <div class="card-item">
        <div class="itemTitle">累计治愈</div>
        <div class="num list6">{{list.curetotal}}</div>
      </div>
    </div>
    <div class="timeBox">{{list.times}}</div>
    <div id="nav">
      <router-link to="/">累计</router-link>|
      <router-link to="/about">现存</router-link>
    </div>
    <router-view />
  </div>
</template>

<script>
import jsonp from "jsonp";

export default {
  data() {
    return {
      list: []
    };
  },
  methods: {
    getData() {
      jsonp(
        "https://interface.sina.cn/news/wap/fymap2020_data.d.json",
        {},
        (err, data) => {
          if (!err) {
            // err不存在 代表请求数据成功
            console.log(data);
            this.list = data.data;
          }
        }
      );
      console.log(this.list);
    }
  },
  mounted() {
    this.getData();
  }
};
</script>



<style >
html body {
  margin: 0;
  padding: 0;
  background-color: black;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  background: url(./assets/bg.jpg) center;
  background-size: 100%;
  width: 100%;
}

#nav {
  padding: 30px;
}
a {
  text-decoration: none;
}

.titleV {
  font-weight: 600;
  font-size: 26px;
  width: 100%;
  text-align: center;
  height: 50px;
  line-height: 50px;
}

#nav a {
  font-weight: bold;
  color: #2c3e50;
}

#nav a.router-link-exact-active {
  color: #ff677d;
}

* {
  padding: 0;
  margin: 0;
}

.card {
  width: 90%;
  height: 200px;
  margin: 0 auto;
  border-radius: 15px;
  z-index: 999;
  background: rgba(0, 0, 0, 0.155);
}

.card-item {
  width: 33.333333333333%;
  float: left;
  height: 100px;
}
.itemTitle {
  height: 40%;
  width: 100%;
  text-align: center;
  padding-top: 20px;
}
.num {
  font-size: 26px;
}
.list1 {
  color: #ff3535;
}
.list2 {
  color: #8a121c;
}
.list3 {
  color: #434e52;
}
.list4 {
  color: #b10000;
}
.list5 {
  color: #4b4b4b;
}
.list6 {
  color: #13b593;
}
.timeBox {
  margin-top: 10px;
  font-weight: 600;
  font-size: 16px;
}
</style>
