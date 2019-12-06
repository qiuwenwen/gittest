<template>
  <div class="lottery" :style="'height:'+fullHeight+'px;'">
    <canvas id="canvas" width="500" height="500"></canvas>
    <div class="lottery_box">
      <div class="lottery_content">
        <div class="ottery_contentBox">
          <button v-show="startShow" @click="start()" class="ottery_contentbtn">开始</button>
          <button v-show="!startShow" @click="finish()" class="ottery_contentbtn" :disabled="disfinish">结束</button>
        </div>
        <div v-for="(item,index) in titlearr" :key="index" style="float:left;margin-left:30px;margin-top:20px;">
          <div style="width:100px;height:100px;">
            <img style="width:100%;height:100%;" :src="item.url" alt />
          </div>
          <p style="width:100px;text-align:center;">{{item.id}}</p>
        </div>
      </div>
    </div>
    <div class="stop-main">

      <div id="stop-time"></div>
      <div class="back"></div>
    </div>
    <!--弹出名单 -->
    <div class="lottery_mask" v-show="maskshow">
      <audio id="lottery_audio" src="../../static/img/yanhua.mp3" loop></audio>
      <div class="mask_content">
        <button class="maskshowClose" @click="maskshowClose()">关闭</button>
        <h2>{{grade}}</h2>
        <div v-for="(item,index) in titlearr" :key="index" style="float:left;margin-left:30px;margin-top:20px;">
          <div style="width:100px;height:100px;">
            <img style="width:100%;height:100%;" :src="item.url" alt />
          </div>
          <p style="width:100px;text-align:center;">{{item.name}}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Axios from "axios";
import pc_draw from "../../static/js/pc_draw.js";
export default {
  name: "cecountUp",
  data() {
    return {
      num: 1, //点击一次出现几个中奖的人
      codedata: [
        {
          name: "张三",
          id: 1,
          img:
            "https://ss1.bdstatic.com/70cFuXSh_Q1YnxGkpoWK1HF6hhy/it/u=1334953102,2491427455&fm=26&gp=0.jpg"
        },
        {
          name: "李四",
          id: 2,
          img:
            "https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=3136075639,3338708347&fm=26&gp=0.jpg"
        },
        {
          name: "小名",
          id: 3,
          img:
            "https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/u=4090061760,3566002114&fm=26&gp=0.jpg"
        },
        {
          name: "小红",
          id: 4,
          img:
            "https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=1358126537,2470243223&fm=26&gp=0.jpg"
        },
        {
          name: "王二",
          id: 5,
          img:
            "https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=1897589137,2261370756&fm=26&gp=0.jpg"
        },
        {
          name: "小红",
          id: 6,
          img:
            "https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=3962805517,413692801&fm=26&gp=0.jpg"
        },
        {
          name: "王二",
          id: 7,
          img:
            "https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/u=2954407842,3286872892&fm=26&gp=0.jpg"
        },
        {
          name: "王二",
          id: 8,
          img:
            "https://ss2.bdstatic.com/70cFvnSh_Q1YnxGkpoWK1HF6hhy/it/u=1226778478,3432630272&fm=26&gp=0.jpg"
        },
        {
          name: "小红",
          id: 9,
          img:
            "https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=4100987808,2324741924&fm=26&gp=0.jpg"
        },
        {
          name: "王二",
          id: 10,
          img:
            "https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=4100987808,2324741924&fm=26&gp=0.jpg"
        },
        {
          name: "王二",
          id: 11,
          img:
            "https://ss1.bdstatic.com/70cFuXSh_Q1YnxGkpoWK1HF6hhy/it/u=2009840232,3610865157&fm=26&gp=0.jpg"
        },
        {
          name: "小红",
          id: 12,
          img:
            "http://img3.imgtn.bdimg.com/it/u=3603761103,54769728&fm=26&gp=0.jpg"
        },
        {
          name: "王二",
          id: 13,
          img:
            "http://img4.imgtn.bdimg.com/it/u=3666495020,1800109572&fm=26&gp=0.jpg"
        },
        {
          name: "王五",
          id: 14,
          img:
            "http://img2.imgtn.bdimg.com/it/u=2289198615,2271927219&fm=26&gp=0.jpg"
        }
      ], //所有的数据
      arr: [], //随机数组
      titlearr: [], //用来展示当前的信息
      titleData_arr: [], //所有中奖名单放一起
      myNumber: 0, //定时器
      startShow: true, //开始停止的按钮显示
      overallArr: [], //中奖级别的数组
      maskshow: false, //中奖名单弹框
      grade: "三", //显示现在是几等奖
      threeArr: [],
      twoArr: [],
      oneArr: [],
      startNum: 0,
      awardsPerson: 7, //奖项人数
      myAudio: "",
      disfinish: true,
      fullHeight: document.documentElement.clientHeight
    };
  },
  watch: {},
  methods: {
    get() {
      console.log(1);
      Axios({
        //此处不写默认为get方法
        // url: "http://localhost:8080/getdata"
        url: "http://172.16.0.36:3000/user/getuser_list",
        params: {
          type: 0,
          level: 0
        }
      })
        .then(res => {
          console.log(res.data.data);
          this.codedata = res.data.data;
        })
        .catch(error => {
          if (error) throw error;
        });
    },
    //抽奖逻辑
    showRandomNum() {
      for (var i = 0; i < this.codedata.length; i++) {
        this.arr[i] = i;
      }
      this.arr.sort(() => {
        return 0.5 - Math.random();
      });
      this.titlearr = [];
      //点击一次吧中奖的人数添加到一个数组里
      let numRandom = this.num;
      if (this.startNum == Math.ceil(this.awardsPerson / this.num)) {
        if (this.awardsPerson % this.num === 0) {
          numRandom = this.num;
        } else {
          numRandom = this.awardsPerson % this.num;
        }
      }
      for (var i = 0; i < numRandom; i++) {
        var index = this.arr[i];
        if (this.codedata.length > 0) {
          this.titlearr.push(this.codedata[index]);
        }
      }
    },
    //开始
    start() {
      console.log(this.codedata);
      if (this.codedata.length >= this.num) {
        this.startShow = false;
        this.myNumber = setInterval(() => {
          this.showRandomNum();
        }, 30);
      } else {
        alert("不够" + this.num + "人");
      }
      var onloadDelayTime = setTimeout(() => {
        clearTimeout(onloadDelayTime); //清除定时器
        this.disfinish = false;
      }, 3000);
    },
    //结束
    finish() {
      clearInterval(this.myNumber);
      this.disfinish = true;
      this.countdown();
      // pc_draw();
      this.myAudio = document.getElementById("lottery_audio");
      var canvas = document.getElementById("canvas");
      // canvas.style.visibility = "visible";
      // console.log(canvas.style.visibility);
      // this.myAudio.play(); //音乐
      //把中奖的放到一个数组里
      this.titlearr.map((item, index) => {
        this.overallArr.push(item);
      });
      // console.log(this.overallArr)
      var temp = [];
      // 把抽过的从原数组中删除
      for (var i = 0; i < this.codedata.length; i++) {
        for (var k = 0; k < this.titlearr.length; k++) {
          if (this.codedata[i].id == this.titlearr[k].id) {
            temp.unshift(i);
          }
        }
      }
      temp.forEach(item => {
        this.codedata.splice(item, 1);
      });
      this.arr.length = this.codedata.length;
      console.log(this.codedata);
      this.startShow = true;
      // this.maskshow = true; //中奖结束后弹框出现
    },
    //弹框关闭
    maskshowClose() {
      canvas.style.visibility = "hidden";
      console.log(canvas.style.visibility);
      this.maskshow = false;
      this.myAudio.pause();
      console.log(this.overallArr);
      if (this.overallArr.length == 7) {
        // this.startNum = 0;
        this.awardsPerson = 5;
        this.grade = "三";
        this.overallArr = [];
        this.num = 2;
      } else if (this.overallArr.length == 5) {
        // this.startNum = 0;
        this.awardsPerson = 2;
        this.grade = "二";
        this.overallArr = [];
        this.num = 1;
      }
    },

    countdown() {
      console.log("11");
      // 最后的倒计时
      // canvas.style.visibility = "hidden";
      $(".stop-main").show();
      var stop_time = setTimeout(() => {
        $("#stop-time").fadeIn();
        $("#stop-time").text("叁");
        $("#stop-time").fadeOut();
      }, 10);
      //        $('#stop-time').fadeIn();
      var stop_time = setTimeout(() => {
        $("#stop-time").fadeIn();
        $("#stop-time").text("贰");
        $("#stop-time").fadeOut();
      }, 1000);
      stop_time = setTimeout(() => {
        $("#stop-time").fadeIn();
        $("#stop-time").text("壹");
      }, 2000);
      stop_time = setTimeout(() => {
        $("#stop-time").fadeOut();
        clearTimeout(stop_time);
        $(".stop-main").hide();
        pc_draw(); //烟花
        this.maskshow = true; //中奖弹框显示
        this.myAudio.play(); //音乐
        // var canvas = document.getElementById("canvas");
        canvas.style.visibility = "visible";
        // console.log(canvas.style.visibility);
      }, 2500);
    },
    get_bodyHeight() {
      //动态获取浏览器高度
      const that = this;
      window.onresize = () => {
        return (() => {
          window.fullHeight = document.documentElement.clientHeight;
          that.fullHeight = window.fullHeight;
        })();
      };
    }
  },
  mounted() {
    this.get_bodyHeight();
  },
  created() {
    this.get();
  },
  computed: {}
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
#container {
  width: 800px;
  height: 300px;
  background: #000;
  .container_box {
    position: relative;
    left: 50%;
    top: 50%;
    -webkit-transform: translate(-50%, -50%);
    -ms-transform: translate(-50%, -50%);
    transform: translate(-50%, -50%);
  }
}
.video-js .vjs-icon-placeholder {
  width: 100%;
  height: 100%;
  display: block;
}
.lottery {
  width: 100%;
  height: 100%;
  background: url("../../static/img/bg.jpg");
  background-repeat: no-repeat;
  background-size: 100% 100%;
  .top_head {
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: 60px;
    z-index: 2;
    // background: url("../../static/img/top_border.png") repeat-x;
  }
  .top_left {
    position: absolute;
    left: 0;
    top: 0;
    width: 85px;
    height: 215px;
    z-index: 2;
    img {
      width: 100%;
      height: 100%;
    }
  }
  .top_right {
    position: absolute;
    right: 0;
    top: 0;
    width: 85px;
    height: 215px;
    z-index: 2;
    img {
      width: 100%;
      height: 100%;
    }
  }
  .lottery_content {
    // width: 80%;
    // margin-left: 10%;
    // margin-top: 100px;
    // position: relative;
    // background: #fff;
    padding: 20px;
    // background: orange;
    color: #fff;
    position: absolute;
    top: 50%;
    left: 50%;
    border-radius: 5px;
    -webkit-transform: translate(-50%, -50%);
    -moz-transform: translate(-50%, -50%);
    transform: translate(-50%, -50%);
    .ottery_contentBox {
      width: 90px;
      margin: 0 auto;
      .ottery_contentbtn {
        width: 90px;
        height: 90px;
        outline: none;
        border: none;
        border: 1px solid yellow;
        background: #fff;
        border-radius: 50%;
        font-size: 18px;
        cursor: pointer;
        margin: 0 auto;
      }
    }
  }

  .lottery_mask {
    position: fixed;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    z-index: 999;
  }
  #canvas {
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    background: rgba(0, 0, 0, 0.7);
    z-index: 9;
    width: 100%;
    height: 100%;
    visibility: hidden;
    // opacity: 0;
  }
  .mask_content {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    -webkit-transform: translate(-50%, -50%);
    -moz-transform: translate(-50%, -50%);
    width: 618px;
    height: 400px;
    background: #fff;
    z-index: 999;
  }
}
.stop-main {
  text-align: center;
  display: none;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 9;
  background: rgba(0, 0, 0, 0.7);
}

#stop-time {
  text-align: center;
  line-height: 180px;
  display: inline-block;
  width: 200px;
  height: 200px;
  margin-top: 280px;
  font-size: 160px;
  color: #fff;
  // border-radius: 100%;
  // z-index: 999;
}

.stop-main .back:after {
  content: "";
  width: 100%;
}

.stop-main .back {
  top: 0;
  position: absolute;
  width: 100%;
  height: 100%;
  // background: rgba(0, 0, 0, 0.7);
}
</style>