<template>
  <div id="details">
    <details-nav-bar class="details-nav"></details-nav-bar>
    <scroll class="details-scroll" ref="scroll">
      <details-swiper :top-images="topImages"></details-swiper>
      <details-base-info :goods="goods"></details-base-info>
      <details-shop-info :shop="shop"></details-shop-info>
      <details-goods-info :detail-info="detailsInfo" @imageLoad="imageLoad"></details-goods-info>
      <details-param-info :param-info="paramInfo"></details-param-info>
    </scroll>
  </div>
</template>

<script>

import Scroll from "@/components/common/scroll/Scroll";

import DetailsNavBar from "@/views/details/childComps/DetailsNavBar";
import DetailsSwiper from "@/views/details/childComps/DetailsSwiper";
import DetailsBaseInfo from "@/views/details/childComps/DetailsBaseInfo";
import DetailsShopInfo from "@/views/details/childComps/DetailsShopInfo";
import DetailsGoodsInfo from "@/views/details/childComps/DetailsGoodsInfo";
import DetailsParamInfo from "@/views/details/childComps/DetailsParamInfo";

import {getGoodsDetails, Goods, Shop, GoodsParam} from "@/network/details";

export default {
  name: "Details",
  components: {
    DetailsNavBar,
    DetailsSwiper,
    DetailsBaseInfo,
    DetailsShopInfo,
    DetailsGoodsInfo,
    DetailsParamInfo,
    Scroll
  },
  data() {
    return {
      iid: null,
      topImages: [],
      goods: {},
      shop: {},
      detailsInfo: {},
      paramInfo: {},
      commentInfo: {}
    }
  },
  created() {
    // 获取动态路由传入的商品iid
    this.iid = this.$route.params.iid;

    // 发送网络请求 获取商品的相关数据
    getGoodsDetails(this.iid).then(res => {
      this.topImages = res.result.itemInfo.topImages;

      const data = res.result;

      // 封装商品信息
      this.goods = new Goods(data.itemInfo, data.columns, data.shopInfo.services);

      // 封装店铺信息
      this.shop = new Shop(data.shopInfo);

      // 封装商品详情信息
      this.detailsInfo = data.detailInfo;

      // 获取参数信息
      this.paramInfo = new GoodsParam(data.itemParams.info, data.itemParams.rule);

      // 封装评论信息
      if(data.rate.cRate != 0){
        this.commentInfo = data.rate.list[0];
      }

    })
  },
  methods: {
    imageLoad() {
      this.$refs.scroll.refresh();
    }
  }
}
</script>

<style scoped>

  #details {
    position: relative;
    z-index: 9;
    background-color: #fff;
    height: 100vh;
  }

  .details-nav {
    position: relative;
    z-index: 9;
    background-color: #fff;
  }

  .details-scroll {
    height: calc(100% - 44px);
  }

</style>
