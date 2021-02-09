<template>
  <div class="wrapper" ref="wrapper">
    <div class="content">
      <slot></slot>
    </div>
  </div>
</template>

<script>

  import BScroll from 'better-scroll';

  export default {
    name: "Scroll",
    data() {
      return {
        scroll: null,
      }
    },
    props: {
      probeType: {
        type: Number,
        default: 0
      },
      pullUpLoad: {
        type: Boolean,
        default: false
      }
    },
    mounted() {

      // 1.创建BScroll实例
      this.scroll = new BScroll(this.$refs.wrapper, {
        click: true,
        probeType: this.probeType,
        pullUpLoad: this.pullUpLoad,
        momentum: true,
        useTransition: false,
      })

      // 发送滚动监听事件到父组件
      if(this.probeType == 2 || this.probeType == 3) {
        this.scroll.on('scroll', position => {
          this.$emit('scroll', position);
        })
      }

      // 发送上拉加载事件到父组件
      if(this.pullUpLoad) {
        this.scroll.on('pullingUp', () => {
          this.$emit('pullingUp');
        });
      }
    },
    methods: {
      scrollTo(x, y, time) {
        this.scroll.scrollTo(x, y, time);
      },

      finishPullUp() {
        this.scroll.finishPullUp();
      },

      refresh() {
        this.scroll.refresh();
      },

      getScrollY() {
        return this.scroll ? this.scroll.y : 0;
      }
    }
  }

</script>

<style scoped>

</style>
