<template>
  <view>
    <view class="my-search-container" :style="{'background-color': bgcolor}"
      @click="searchBoxHandler">
      <!-- 使用 view 组件，模拟 input 输入框样式 -->
      <view class="my-search-box" :style="{'border-radius': radius + 'px'}">
        <uni-icons type="search" size="17"></uni-icons>
        <text class="placeholder">搜索</text>
      </view>
    </view>
  </view>
</template>

<script>
  export default {
    name:"my-search",
    props: {
      // 背景颜色
      bgcolor: {
        type: String,
        default: '#C00000'
      },
      // 圆角尺寸
      radius: {
        type: Number,
        default: 18
      }
    },
    data() {
      return {
        
      };
    },
    methods: {
      searchBoxHandler() {
        // console.log("~~~");
        this.$emit('click')
      }
    }
  }
</script>

<style lang="scss">
  .my-search-container {
    // background-color: #c00000;
    height: 50px;
    padding: 0 10px;
    display: flex;
    align-items: center;
    .my-search-box {
      background-color: #fff;
      height: 36px;
      width: 100%;
      // border-radius: 18px;
      display: flex;
      justify-content: center;
      align-items: center;
      .placeholder {
        margin-left: 20px;
        font-size: 15px;
      }
    }
  }
</style>