<template>
  <div id="home" class="wrapper">
    <nav-bar class="home-nav"><div slot="center">购物街</div></nav-bar>
    <scroll class="content" ref="scroll" :probe-type="3" @scroll="scroll" :pull-up-load="true" @pullingUp="loadMore">
      <home-swiper :banners="banners"></home-swiper>
      <recommend-view :recommends="recommends"></recommend-view>
      <feature-view/>
      <tab-control class="tab-control" :titles="['流行', '新款', '精选']" @tabClick="tabClick"></tab-control>
      <goods-list :goodsList="goodsList"/>
    </scroll>
    <back-top @click.native="topClick" v-show="isShowTop"/>
  </div>
</template>

<script>

import NavBar from "@/components/common/navbar/NavBar";
import Scroll from "@/components/common/scroll/Scroll";

import TabControl from "@/components/content/tabControl/TabControl";
import GoodsList from "@/components/content/goods/GoodsList";
import BackTop from "@/components/content/backTop/BackTop";

import HomeSwiper from "@/views/home/childComps/HomeSwiper";
import RecommendView from "@/views/home/childComps/RecommendView";
import FeatureView from "@/views/home/childComps/FeatureView";

import {getHomeMultidata, getHomeGoods} from 'network/home';

export default {
  name: "Home",
  components: {
    NavBar,
    HomeSwiper,
    RecommendView,
    FeatureView,
    TabControl,
    GoodsList,
    Scroll,
    BackTop
  },
  data() {
    return {
      banners: [],
      recommends: [],
      currentType: 'pop',
      isShowTop: false,
      goods: {
        'pop' : {
          page: 0,
          list: []
        },

        'new': {
          page: 0,
          list: []
        },

        'sell': {
          page: 0,
          list: []
        }
      }
    }
  },
  computed: {
    goodsList(){
      return this.goods[this.currentType].list;
    }
  },
  created: function () {
    // 1.获取多个数据
    this.getHomeMultidata();

    // 2.获取首页数据
    this.getHomeGoods('pop');
    this.getHomeGoods('new');
    this.getHomeGoods('sell');
  },
  methods: {

    // 上拉加载
    loadMore() {
      // 加载下一页数据
      this.getHomeGoods(this.currentType);

      // 刷新
      this.$refs.scroll.scroll.refresh();
    },

    // 滚动监听
    scroll(position) {
      this.isShowTop = (-position.y) > 1000;
    },

    //回到顶部
    topClick(){
      this.$refs.scroll.scrollTo(0, 0, 500);
    },

    // 切换tab的方法
    tabClick(index){
      switch(index){
        case 1:
          this.currentType = 'pop';
          break;
        case 2:
          this.currentType = 'new';
          break;
        case 3:
          this.currentType = 'sell';
          break;
      }
    },

    // 网络请求的方法
    getHomeMultidata(){
      getHomeMultidata().then(res => {
        this.banners = res.data.banner.list;
        this.recommends = res.data.recommend.list;
      })
    },

    getHomeGoods(type){
      const page = this.goods[type].page + 1;
      getHomeGoods(type, page).then(res => {
        this.goods[type].list.push(...res.data.list);
        this.goods[type].page++;
        this.$refs.scroll.finishPullUp();
      })
    }
  }
}
</script>

<style scoped>

  .content {
    overflow: hidden;

    position: absolute;
    top: 44px;
    bottom: 49px;
    left: 0;
    right: 0;
  }

  #home {
    height: 100vh;
    position: relative;
  }

  .home-nav {
    background-color: var(--color-tint);
    color: #fff;

    position: fixed;
    left: 0;
    top: 0;
    right: 0;
    z-index: 9;
  }

  .tab-control {
    position: sticky;
    top: 44px;
  }
</style>
