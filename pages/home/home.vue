<template>
  <view>
    <!-- 使用自定义的搜索组件 -->
    <view class="search-box">
      <my-search @click="gotoSearch"></my-search>
    </view>
    <!-- 轮播区域 -->
    <swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
      <swiper-item v-for="(item, i) in swiperList" :key="i">
        <navigator class="swiper-item" 
          :url="'/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id">
          <!-- 动态绑定图片的 src 属性 -->
          <image :src="item.image_src"></image>
        </navigator>
      </swiper-item>
    </swiper>
    <!-- 分类导航区域 -->
    <view class="nav_list">
      <view class="nva_item" v-for="(item, i) in navList" :key="i" @click="navClickHandler(item)">
        <image :src="item.image_src" class="nav_img"></image>
      </view>
    </view>
    <!-- 楼层区域 -->
    <!-- 楼层的容器 -->
    <view class="floor_list">
      <!-- 每个楼层的 item 项 -->
      <view class="floor_item" v-for="(item, i) in floorList" :key="i">
        <!-- 楼层的标题 -->
        <image :src="item.floor_title.image_src" class="floor_title"></image>
        <!-- 楼层图片区域 -->
        <view class="floor_img_box">
          <!-- 左侧大图片的盒子 -->
          <navigator class="left_img_box" :url="item.product_list[0].url">
            <image :src="item.product_list[0].image_src" mode="widthFix"
              :style="{width: item.product_list[0].image_width + 'rpx'}" ></image>
          </navigator>
          <!-- 右侧小图片的盒子 --> 
          <view class="right_img_box">
            <navigator 
              class="right_img_item" v-for="(item2, i2) in item.product_list" 
              :key="i2" :url="item2.url" v-if="i2 !== 0">
              <image :src="item2.image_src" mode="widthFix" 
                :style="{width: item.product_list[0].image_width + 'rpx'}"></image>
            </navigator>
          </view> 
      </view>   
      </view>
    </view>
  </view>
</template>

<script>
  // 导入自己封装的 mixin 模块
  import badgeMix from '../../mixins/tabbar-badge.js'
  
  export default {
    // 将 badgeMix 混入到当前的页面中进行使用
    mixins: [badgeMix],
    
    data() {
      return {
        // 轮播图的数据列表
        swiperList: [],
        // 分类导航的数据列表
        navList: [],
        // 楼层的数据列表
        floorList: []
      };
    },
    onLoad() {
      // 调用方法，获得轮播图的数据
      this.getSwiperList()
      // 获得分类导航数据
      this.getNavList()
      // 获得楼层的数据
      this.getFloorList()
    },
    methods: {
      async getSwiperList() {
        const { data: res } = await uni.$http.get('/api/public/v1/home/swiperdata')
        // console.log(res)
        // 请求失败
        if( res.meta.status !== 200) return uni.$showMsg()
        this.swiperList = res.message
        uni.$showMsg('数据请求成功！')
      },
      async getNavList() {
        const { data: res} = await uni.$http.get('/api/public/v1/home/catitems')
        // console.log(res);
        if( res.meta.status !== 200) return uni.$showMsg()
        this.navList = res.message
      },
      async getFloorList() {
        const { data: res } =  await uni.$http.get('/api/public/v1/home/floordata')
        console.log(res);
        if(res.meta.status !== 200) return uni.$showMsg()
        // 对数据进行处理
        res.message.forEach(floor => {
          floor.product_list.forEach(prod => {
            prod.url= '/subpkg/goods_list/goods_list?' + prod.navigator_url.split('?')[1]
          })
        })
        this.floorList = res.message
      },
      // nav-item 项被点击时候的事件处理函数
      navClickHandler(item) {
        // 判断点击的是哪个 nav
        if(item.name === '分类') {
          uni.switchTab({
            url: '/pages/cate/cate'
          })
        }
      },
      gotoSearch() {
        uni.navigateTo({
          url: '/subpkg/search/search'
        })
      }
    },
  }
</script>

<style lang="scss">
  .search-box {
    // 设置定位效果为“吸顶”
    position: sticky;
    // 吸顶的“位置”
    top: 0;
    // 提高层级，防止被轮播图覆盖
    z-index: 999;
  }
  swiper {
    height: 330rpx;
    .swiper-item,
    image {
      width: 100%;
      // height: 100%;
    }
  }
  .nav_list {
    display: flex;
    justify-content: space-around;
    margin: 15rpx 0;
    .nav_img {
      width: 128rpx;
      height: 140rpx;
    }
  }
  .floor_title {
    height: 60rpx;
    width: 100%;
    // display: flex;
  }
  .floor_img_box {
    display: flex;
    padding-left: 15rpx;
    .right_img_box {
      display: flex;
      flex-wrap: wrap;
      justify-content: space-around;
    }
  }

</style>
