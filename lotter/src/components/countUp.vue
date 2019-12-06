<template>
      <div>
         <slot></slot>
        </div>
</template>


<script>
export default {
  name: "countUp",
  props: {
    lastSymbol: {
      type: String,
      default: " 位"
    },
    value: {
      //滚动结束最终显示数字
      type: [String, Number],
      default: 120
    },
    from: {
      // 开始时的数字
      type: [String, Number],
      default: 0
    },
    speed: {
      // 总时间
      type: [String, Number],
      default: 5000
    },
    refreshInterval: {
      // 刷新一次的时间
      type: [String, Number],
      default: 10
    },
    beforeSize: {
      //小数点前最小显示位数，不足的话用0代替
      type: [String, Number],
      default: 0
    },
    decimals: {
      // 小数点后的位数，小数做四舍五入
      type: [String, Number],
      default: 2
    },
    isstart: {
      //是否开始滚动
      type: Boolean,
      default: true
    }
  },
  data() {
    return {
      loops: "", //刷新次数
      increment: "", //刷新一次增加的数值
      loopCount: "", //记录刷新的次数
      CurrentValue: "", //开始时候的数字
      interval: "", //定时器
      sizeNum: "", //当前数字的长度
      sizeNumBefore: "" //当前数字小数点前的位数
    };
  },
  watch: {
    isstart(val) {
      if (val) {
        this.start();
      } else {
        clearInterval(this.interval);
      }
    }
  },
  methods: {
    start() {
      this.loops = Math.ceil(this.speed / this.refreshInterval); //刷新次数

      this.increment = (this.value - this.from) / this.loops; //（结束的数字-开始的数字）/刷新次数 ，刷新一次增加的数值
      this.loopCount = 0; //记录刷新的次数
      this.CurrentValue = this.from; //开始时候的数字

      this.interval = setInterval(this.updateTimer, this.refreshInterval); //设置定时器，没个一段时间就执行
    },
    updateTimer() {
      //刷新一次数值叠加
      this.CurrentValue += this.increment; //当前展示的值
      this.loopCount++; //刷新次数+1
      var tim = this.CurrentValue.toFixed(this.decimals); //对当前值进行四舍五入 ,tim四射物质之后的当前数值

      this.sizeNum = String(tim).length;
      this.sizeNumBefore = this.sizeNum - this.decimals - 1;
      if (this.sizeNumBefore >= this.beforeSize) {
        //当前数字的小数点位数》=要求的小数点前位数
        this.$emit("sendValue", tim + this.lastSymbol);
      } else {
        tim = Array(this.beforeSize - this.sizeNumBefore + 1).join("0") + tim;
        this.$emit("sendValue", tim + this.lastSymbol);
      }

      if (Number(this.loopCount) >= Number(this.loops)) {
        //清楚定时器

        clearInterval(this.interval);
      }
    }
  }
};
</script>
<style scoped>
</style>