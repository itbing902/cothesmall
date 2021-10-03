<template>
	<div id="detail">
    <detail-nav-bar class="detail-nav" @titleClick="titleClick" ref="nav"/>
    <scroll class="content"
            ref="scroll"
            :probe-type="3"
            @scroll="contentScroll">
      <detail-swiper :top-images="topImages"/>
      <detail-base-info :goods="goods"/>
      <detail-shop-info :shop="shop"/>
      <detail-goods-info :detail-info="detailInfo" @imageLoad="imageLoad"/>
      <detail-param-info :param-info="paramInfo" ref="params"/>
      <detail-comment-info :comment-info="commentInfo" ref="comment"/>
      <goods-list :goods="recommends" ref="recommend"/>

    </scroll>
    <detail-bottom-bar @addCart="addToCart"/>

    <back-top @click.native="backClick" v-show="isShowBackTop"/>
<!--    <toast :message="message" :show="show"/>-->
  </div>
</template>

<script>
  import DetailNavBar from "./childComps/DetailNavBar";
  import DetailSwiper from "./childComps/DetailSwiper";
  import DetailBaseInfo from "./childComps/DetailBaseInfo";
  import DetailShopInfo from "./childComps/DetailShopInfo";
  import DetailGoodsInfo from "./childComps/DetailGoodsInfo";
  import DetailParamInfo from "./childComps/DetailParamInfo";
  import DetailCommentInfo from "./childComps/DetailCommentInfo";
  import DetailBottomBar from "./childComps/DetailBottomBar";

  import Scroll from "components/common/scroll/Scroll";
  import GoodsList from "components/content/goods/GoodsList";
  // import Toast from "components/common/toast/Toast";

  import {getDetail, Goods, Shop, GoodsParam, getRecommend} from "network/detail"
  import {debounce} from "common/utils";
  import {itemListenerMixin, backTopMixin} from "common/mixin";

  import { mapActions } from 'vuex'

  export default {
		name: "Detail",
    components: {
      DetailNavBar,
      DetailSwiper,
      DetailBaseInfo,
      DetailShopInfo,
      DetailGoodsInfo,
      DetailParamInfo,
      DetailCommentInfo,
      DetailBottomBar,
      Scroll,
      GoodsList,
      // Toast,
    },
    mixins: [itemListenerMixin, backTopMixin],
    data() {
		  return {
		    iid: null,
        topImages: [],
        goods: {},
        shop: {},
        detailInfo: {},
        paramInfo: {},
        commentInfo: {},
        recommends: [],
        themeTopYs: [],
        getThemeTopY: null,
        currentIndex: 0,
        // message: '',
        // show: false
      }
    },
    created() {
		  //1.保存传入的iid
		  this.iid = this.$route.params.iid

      //2.根据iid请求详情数据
      getDetail(this.iid).then(res => {
        //1.获取顶部的图片轮播数据
        console.log(res);
        const data = res.result
        this.topImages = data.itemInfo.topImages

        //2.获取商品信息
        this.goods = new Goods(data.itemInfo, data.columns, data.shopInfo.services)

        //3.创建店铺信息的对象
        this.shop = new Shop(data.shopInfo)

        //4.保存商品的详情数据
        this.detailInfo = data.detailInfo

        //5.获取参数信息
        this.paramInfo = new GoodsParam(data.itemParams.info, data.itemParams.rule)

        //6.取出评论信息
        if (data.rate.cRate !== 0) {
          this.commentInfo = data.rate.list[0]
        }

        /*
        //1.第一次获取，值不对
        //原因： this.$refs.params.$el压根没用渲染
        this.themeTopYs = []

        this.themeTopYs.push(0)
        this.themeTopYs.push(this.$refs.params.$el.offsetTop)
        this.themeTopYs.push(this.$refs.comment.$el.offsetTop)
        this.themeTopYs.push(this.$refs.recommend.$el.offsetTop)

        console.log(this.themeTopYs);
        this.$nextTick(() => {
          //2.第二次获取，值不对
          //原因： 图片没有计算在内
          this.themeTopYs = []

          this.themeTopYs.push(0)
          this.themeTopYs.push(this.$refs.params.$el.offsetTop)
          this.themeTopYs.push(this.$refs.comment.$el.offsetTop)
          this.themeTopYs.push(this.$refs.recommend.$el.offsetTop)

          console.log(this.themeTopYs);
        })
         */
      })

      //3.请求推荐数据
      getRecommend().then(res => {
        // console.log(res);
        this.recommends = res.data.list
      })

      //4.给getThemeTopY赋值(对给this.themeTopYs赋值的操作进行防抖)
      this.getThemeTopY = debounce(() => {
        this.themeTopYs = []
        this.themeTopYs.push(0)
        this.themeTopYs.push(this.$refs.params.$el.offsetTop)
        this.themeTopYs.push(this.$refs.comment.$el.offsetTop)
        this.themeTopYs.push(this.$refs.recommend.$el.offsetTop)
        this.themeTopYs.push(Number.MAX_VALUE)

        // console.log(this.themeTopYs);
      }, 100)
    },
    destroyed() {
      this.$bus.$off('itemImgLoad', this.itemImgListener)
    },
    methods: {
      // 将actions中的addCart映射到这
      ...mapActions({
        add: 'addCart'
      }),

      // 监听图片加载之后执行程序
      imageLoad() {
        // console.log('-----');
        this.refresh()
        // this.$refs.scroll.refresh();
        // this.getThemeTopY();
        this.getThemeTopY()
      },
      titleClick(index) {
        console.log(index);
        this.$refs.scroll.scrollTo(0, -this.themeTopYs[index], 200)
      },
      contentScroll(position) {
        //1.获取y值
        const positionY = -position.y

        //2.positionY和主题中值进行对比
        // [0, 5554, 6358, 6574]
        // console.log(Number.MAX_VALUE);

        // positionY 在 0 和 5554 之间， index = 0
        // positionY 在 5554 和 6358 之间， index = 1
        // positionY 在 6358 和 6574 之间， index = 2
        // positionY 大于等于 6574 时， index = 3

        let length = this.themeTopYs.length
        for (let i = 0; i < length-1; i++) {
          // if (positionY > this.themeTopYs[i] && positionY < this.themeTopYs[i+1]) {
          //   console.log(i);
          // }

          if (this.currentIndex !== i && (positionY >=
            this.themeTopYs[i] && positionY < this.themeTopYs[i+1])){
              this.currentIndex = i
              this.$refs.nav.currentIndex = this.currentIndex
          }

          // if (this.currentIndex !== i && (i < length - 1 && positionY >= this.themeTopYs[i] && positionY <
          //   this.themeTopYs[i+1]) || (i === length -1 && positionY >= this.themeTopYs[i])) {
          //   this.currentIndex = i
          //   // console.log(this.currentIndex);
          //   this.$refs.nav.currentIndex = this.currentIndex
          // }
        }


        this.isShowBackTop = (-position.y) > 1000
      },
      addToCart() {
        //1.获取购物车需要展示的信息
        const product = {}
        product.image = this.topImages[0];
        product.title = this.goods.title;
        product.desc = this.goods.desc;
        product.price = this.goods.realPrice;
        product.iid = this.iid;

        //2.将商品添加到购物车(1.Promise 2.mapActions)
        // this.$store.commit('addCart', product)

        this.add(product).then(res => {
          // this.show = true;
          // this.message = res;
          // setTimeout(() => {
          //   this.show = false;
          //   this.message = '';
          // },1500)
          this.$toast.show(res)

          console.log(this.$toast);
        })

        // this.$store.dispatch('addCart', product).then(res => {
        //   console.log(res);
        // })
      }

    }
  }
</script>

<style scoped>
  #detail {
    position: relative;
    z-index: 9;
    background-color: #fff;
    overflow: hidden;
    height: 100vh;
  }

  .detail-nav {
    position: relative;
    z-index: 9;
    background-color: #fff;
  }

  .content {
    height: calc( 100% - 44px - 49px);
  }
</style>
