<template>
  <div id="home">
    <nav-bar class="home-nav-bar">
      <div slot="center">购物街</div>
    </nav-bar>
<!-- ref 是用于定位元素和取到组件的内容，:是绑定属性 @是绑定方法-->
    <scroller class="home-scroller" ref="scroller" :probe-type="3" @scroll="getPostion" :pull-up-load="true" @pullingUp="loadMore">
      <home-swiper :cbanners="banners"/>
      <home-recommend :crecommends="recommends"/>
      <home-feature-view/>
      <tab-control class="home-tab-control" :ctitles="['流行', '新款', '精选']" @tabClick="pTabClick"/>
      <goods-list :cgoods="showGoods"/>
    </scroller>

    <back-top @click.native="backTopClick" v-show="isShowBackTop" />

  </div>
</template>

<script>
    import HomeSwiper from "./childComps/HomeSwiper";
    import HomeRecommend from "./childComps/HomeRecommend";
    import HomeFeatureView from "./childComps/HomeFeatureView";

    import NavBar from "components/common/navbar/NavBar";
    import TabControl from "components/contents/tabControl/TabControl";
    import GoodsList from "components/contents/good/GoodsList";
    import Scroller from "components/common/scroller/Scroller";
    import BackTop from "components/contents/backTop/BackTop";

    import {getHomeMultiData, getGoodsData} from "network/home";

    export default {
        name: "Home",
        components:{
            HomeSwiper,
            HomeRecommend,
            HomeFeatureView,
            NavBar,
            TabControl,
            GoodsList,
            Scroller,
            BackTop
        },
        data() {
          return {
            banners: [],
            recommends:[],
            products: [],
            goods:{
              'pop':{page:0, list:[]},
              'new':{page:0, list:[]},
              'sell':{page:0, list:[]}
            },
            currentType:'pop',
            isShowBackTop: false
          }
        },
        computed: {
            showGoods() {
                return this.goods[this.currentType].list
            }
        },
        created() {
            this.getHomeData()
            this.getHomeGoodsData('pop')
            this.getHomeGoodsData('new')
            this.getHomeGoodsData('sell')
        },
        mounted(){
          //监听item图片加载完成
          this.$bus.$on('imageLoad',()=>{
            this.$refs.scroller.refresh()
          })
        },
        methods:{
            pTabClick(index) {
              console.log(index)
                switch (index) {
                  case 0:
                      this.currentType = 'pop'
                      break
                  case 1:
                      this.currentType = 'new'
                      break
                  case 2:
                      this.currentType = 'sell'
                      break
              }
            },
            getHomeData() {
                getHomeMultiData().then(res => {
                    //轮播图
                    this.banners = res.data.banner.list
                    //推荐
                    this.recommends = res.data.recommend.list

                   //this.products = res.data.products
                })
            },
            getHomeGoodsData(type) {
                let page = this.goods[type].page + 1
                getGoodsData(type, page).then(res => {
                    this.goods[type].list.push(...res.data.list)
                    this.goods[type].page = res.page
                    //完成上拉加载更多
                    this.$refs.scroller.finishPullUp();
                })
            },
            backTopClick() {
                // 通过$refs拿到组件中的对象
                this.$refs.scroller.scrollTo(0, 0, 500)
            },
            getPostion(postion) {
                this.isShowBackTop = -postion.y > 300
            },
            loadMore(){
                console.log(3444444)
               this.getHomeGoodsData(this.currentType)
               //this.$refs.scroller.scroll.refresh()
            }
        }
    }
</script>

<style scoped>
  #home {
    padding-top: 44px;
    height: 100vh;
    position: relative;
  }

  .home-nav-bar {
    background-color: var(--color-tint);
    color:#fff;

    position: fixed;
    left: 0;
    right:0;
    top: 0;
    z-index: 9;

  }

  .home-tab-control{
    /*两个要混合使用*/
    position: sticky;
    top: 43px;/*顶部navbar的高度*/
    z-index: 9;
  }

  .home-scroller{
    /*height:300px;*/
    overflow: hidden;
    position: absolute;
    top: 44px;
    bottom: 49px;
    right: 0;
    left: 0;
  }



</style>
