<template>
  <div
    class="custom-list"
    ref="scrollBody"
    @mouseenter="mouseenterFunc"
    @mouseleave="mouseleaveFunc"
    @mousewheel="mousewheelFunc"
  >
    <div
      class="list-body"
      :class="{
        'list-body2': scrollDirection === 'left' || scrollDirection === 'right'
      }"
      ref="listBody"
      :style="{ transform: getScrollDistance() }"
    >
      <slot></slot>
    </div>
    <div
      class="list-body"
      :class="{
        'list-body2': scrollDirection === 'left' || scrollDirection === 'right'
      }"
      ref="tBody"
      v-if="isCanScroll"
      :style="{ transform: getScrollDistance() }"
    >
      <slot></slot>
    </div>

    <!-- <button @click="stop">stop</button>
    <button @click="start">start</button>
    <button @click="initData">result</button> -->
  </div>
</template>

<script>
export default {
  name: "vue-j-scroll",
  props: {
    steep: {
      //滚动速度
      type: Number,
      default: 1
    },
    scrollDirection: {
      //滚动方向
      type: String,
      default: "top"
    },
    isRoller: {
      //是否可以滑轮滚动
      type: Boolean,
      default: true
    },
    rollerScrollDistance: {
      //滑轮滚动的距离
      type: Number,
      default: 20
    },
    data: Array
  },
  data() {
    return {
      timer: null, //滚动定时器
      scrollDistance: 0, //滚动距离
      tDom: "", //复制的容器
      bodyHeight: 0, //滚动容器高度
      bodyWidth: 0, //滚动容器宽度
      listHeight: 0, //列表高度
      listWidth: 0, //列表宽度
      isStop: !1,
      animationFrame: null,
      isCanScroll: true
    };
  },
  methods: {
    start() {
      let that = this;
      //滚动函数
      that.isStop = !1;
      //判断是否可以滚动
      if (that.scrollDirection === "top" || that.scrollDirection === "bottom") {
        if (that.bodyHeight > that.listHeight) {
          this.scrollDistance = 0;
          this.isCanScroll = !1;
          return;
        }
      } else if (
        that.scrollDirection === "left" ||
        that.scrollDirection === "right"
      ) {
        if (that.bodyWidth > that.listWidth) {
          this.scrollDistance = 0;
          this.isCanScroll = !1;
          return;
        }
      }
      this.run();
    },
    run() {
      let that = this;

      //清空动画
      that.clearAnimation();

      that.animationFrame = window.requestAnimationFrame(() => {
        let scrollDistance = Math.abs(that.scrollDistance);
        //根据滚动方向判断使用height或宽度控制效果
        if (
          that.scrollDirection === "top" ||
          that.scrollDirection === "bottom"
        ) {
          if (that.scrollDistance < 0) {
            let cc = 2 * that.listHeight - that.bodyHeight;
            if (scrollDistance > cc) {
              that.scrollDistance = -(that.listHeight - that.bodyHeight);
            }
          } else {
            that.scrollDistance = -that.listHeight;
          }
        } else if (
          that.scrollDirection === "left" ||
          that.scrollDirection === "right"
        ) {
          if (that.scrollDistance < 0) {
            let cc = 2 * that.listWidth - that.bodyWidth;
            if (scrollDistance > cc) {
              that.scrollDistance = -(that.listWidth - that.bodyWidth);
            }
          } else {
            that.scrollDistance = -that.listWidth;
          }
        }
        //判断滚动值
        if (!that.isStop) {
          if (
            that.scrollDirection === "top" ||
            that.scrollDirection === "left"
          ) {
            that.scrollDistance -= that.steep;
          } else if (
            that.scrollDirection === "bottom" ||
            that.scrollDirection === "right"
          ) {
            that.scrollDistance += that.steep;
          }
          that.run();
        }
      });
    },
    //停止滚动
    stop() {
      this.isStop = !0;
      this.clearAnimation();
    },
    //初始化数值
    initData() {
      this.$nextTick(() => {
        this.scrollDistance = 0;
        this.isCanScroll = !0;
        this.bodyHeight = this.$refs.scrollBody.clientHeight;
        this.bodyWidth = this.$refs.scrollBody.clientWidth;
        this.listHeight = this.$refs.listBody.clientHeight;
        this.listWidth = this.$refs.listBody.clientWidth;

        // let vNode = this.$slots.default;

        // let itemWidth = vNode[0].elm.offsetWidth;

        // this.listWidth = vNode.length * itemWidth;

        // console.log(this.listWidth);

        if (
          (this.bodyHeight !== 0 &&
            this.listHeight !== 0 &&
            this.listHeight >= this.bodyHeight) ||
          (this.bodyWidth !== 0 &&
            this.listWidth !== 0 &&
            this.listWidth >= this.bodyWidth)
        ) {
          this.isCanScroll = true;
          this.start();
        } else {
          this.isCanScroll = false;
        }
      });
    },
    //获取滚动样式
    getScrollDistance() {
      let c;
      if (this.scrollDirection === "top" || this.scrollDirection === "bottom") {
        c = "translate(0px, " + this.scrollDistance + "px)";
      } else {
        c = "translate(" + this.scrollDistance + "px,0px)";
      }
      return c;
    },

    clearAnimation() {
      if (this.animationFrame) {
        cancelAnimationFrame(this.animationFrame);
        this.animationFrame = null;
      }
    },
    mouseenterFunc() {
      this.stop();
    },
    mouseleaveFunc() {
      this.start();
    },
    mousewheelFunc(e) {
      if (!this.isCanScroll || !this.isRoller) {
        return false;
      }
      // //滚动方向为横向时禁用鼠标滚轮滚动
      // if (this.scrollDirection === "left" || this.scrollDirection === "right") {
      //   return;
      // }
      let dis = e.deltaY;
      if (dis > 0) {
        this.scrollDistance -= this.rollerScrollDistance;
      } else {
        this.scrollDistance += this.rollerScrollDistance;
      }

      this.run();
    }
  },
  watch: {
    data(newval, oldval) {
      this.initData();
    }
  },

  beforeMount() {},
  mounted() {
    this.$nextTick(() => {
      //初始化页面数据
      this.initData();
    });
  },
  // beforeUpdate() {
  //   console.log("bupdate");
  // },
  // updated() {
  //   console.log("update");
  // },
  beforeDestroy() {
    clearInterval(this.timer);
    this.timer = null;
  },
  created() {},
  beforeCreate() {}
};
</script>

<style scoped>
.custom-list {
  white-space: nowrap;
  font-size: 0;
}
.list-body {
  overflow: hidden;
  white-space: nowrap;
  font-size: 0;
}
.list-body2 {
  display: inline-block;
}
</style>
