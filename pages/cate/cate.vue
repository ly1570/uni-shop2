<template>
      <!-- 右侧滑动区域 -->
  <view>
    <view class="scroll-view-container">
      <!-- 左侧滑动区域 -->
      <scroll-view class="left-scroll-view" scroll-y :style="{height: wh + 'px' }">
        <block v-for="(item, i) in cateList" :key="i">
          <view :class="['left-scroll-view-item', i === active ? 'active' : '']"
            @click="activeChanged(i)">{{item.cat_name}}</view>
        </block>
      </scroll-view>
      <scroll-view class="right-scroll-view" scroll-y :style="{height: wh + 'px' }" 
        :scroll-top="scrollTop">
        <view class="cate-lv2" v-for="(item2, i2) in cateList2" :key="i2">
          <view class="cate-lv2-title">/{{ item2.cat_name }}/</view>
          <!-- 动态渲染三级分类的列表数据 -->
          <view class="cate-lv3-list">
            <!-- 三级分类 Item 项 -->
            <view class="cate-lv3-item" v-for="(item3, i3) in item2.children" :key="i3"
              @click="gotoGoodsList(item3)">
              <!-- 图片 -->
              <image :src="item3.cat_icon.replace('dev','web')"></image>
              <!-- 文本 -->
              <text>{{ item3.cat_name }}</text>
            </view>
          </view>
        </view>
      </scroll-view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        // 窗口的可用高度 = 屏幕高度 - navigationBar高度 - tabBar 高度
        wh: 0,
        // 分类数据列表
        cateList: [],
        // 2级分类数据列表
        cateList2: [],
        // 当前选中项的索引，默认让第一项被选中
        active: 0,
        scrollTop: 0
      };
    },
    methods: {
      async getCateList() {
        const {data: res} = await uni.$http.get('/api/public/v1/categories')
        console.log(res);
        if(res.meta.status !== 200) return uni.$showMsg()
        this.cateList = res.message
        // 为二级分类赋值
        this.cateList2 = res.message[0].children
        uni.$showMsg("数据请求成功！")
      },
      activeChanged(i) {
        this.active= i
        // 为二级分类列表重新赋值
        this.cateList2 = this.cateList[i].children
        this.scrollTop = this.scrollTop === 0 ? 1 : 0
      },
      gotoGoodsList(item3) {
        uni.navigateTo({
          url: '/subpkg/goods_list/goods_list?cid=' + item3.cat_id
        })
      }
    },
    onLoad() {
      this.getCateList()
      // 获取当前系统信息
      const sysInfo = uni.getSystemInfoSync()
      console.log(sysInfo);
      this.wh = sysInfo.windowHeight
    }
  }
</script>

<style lang="scss">
  .scroll-view-container {
    display: flex;
    ::-webkit-scrollbar {
      display: none;
      width: 0 !important;
      height: 0 !important;
      -webkit-appearance: none;
      background: transparent;
      color: transparent;
    }
    .left-scroll-view {
      width: 120px;
      .left-scroll-view-item {
        background-color: #f7f7f7;
        line-height: 60px;
        font-size: 15px;
        text-align: center;
        &.active {
          background-color: #ffffff;
          border-radius: 10px;
          position: relative;
          // 激活项的样式
          &::before {
            content: '';
            display: block;
            width: 3px;
            height: 30px;
            background-color: #C00000;
            position: absolute;
              top: 50%;
              left: 0;
              transform: translateY(-50%);
          }
        }
      }
    }
    .cate-lv2-title {
      text-align: center;
      font-weight: bold;
      font-size: 12px;
      padding: 15px 0;
    }
    .cate-lv3-list {
      display: flex;
      flex-wrap: wrap;
      .cate-lv3-item {
        width: 33.33%;
        margin-bottom: 10px;
        display: flex;
        flex-direction: column;
        align-items: center;
        image {
          width: 60px;
          height: 60px;
        }
        text {
          font-size: 12px;
        }
      }
    }
  }
</style>
