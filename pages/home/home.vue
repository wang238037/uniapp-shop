<template>
  <view>
    <!-- 轮播图区域 -->
    <swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
      <!-- 循环渲染轮播图的 item 项 -->
      <swiper-item v-for="(item, i) in swiperList" :key="i">
        <navigator class="swiper-item" :url="'/subpkg/goods_details/goods_details?goods_id=' + item.goods_id">
          <!-- 动态绑定图片的 src 属性 -->
          <image :src="item.image_src"></image>
        </navigator>
      </swiper-item>
    </swiper>
    <!-- 分类导航列表区域 -->
    <view class="nav-list">
      <view class="nav-item" v-for="(item, i) in navList" :key="i" @click="navClickHandler(item)">
        <image :src="item.image_src" class="nav-img"></image>
      </view>
    </view>
    <!-- 楼层区域 -->
    <view class="floor-list">
      <!-- 楼层 item 项 -->
      <view class="floor-item" v-for="(item, i) in floorList" :key="i">
        <!-- 楼层标题 -->
        <image :src="item.floor_title.image_src" class="floor-title"></image>
        <!-- 楼层图片区域 -->
        <view class="floor-img-box">
          <!-- 左侧大图片的盒子 -->
          <navigator class="left-img-box" :url="item.product_list[0].url">
            <image :src="item.product_list[0].image_src" :style="{width: item.product_list[0].image_width + 'rpx'}"
              mode="widthFix"></image>
          </navigator>
          <!-- 右侧 4 个小图片的盒子 -->
          <view class="right-img-box">
            <navigator :url="item2.url" class="right-img-item" v-for="(item2, i2) in item.product_list" :key="i2" v-if="i2 !== 0">
              <image :src="item2.image_src" mode="widthFix" :style="{width: item2.image_width + 'rpx'}"></image>
            </navigator>
          </view>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        swiperList: [], // 轮播图的数据列表
        navList: [], // 分类导航的数据列表
        floorList: [], // 楼层的数据列表
      };
    },
    methods: {
      // 获取轮播图数据的方法
      async getSwiperList() {
        const {
          data: res
        } = await uni.$http.get("/api/public/v1/home/swiperdata")
        if (res.meta.status !== 200) {
          return uni.$showMsg()
        } else {
          this.swiperList = res.message
        }
      },
      // 获取分类导航的数据
      async getNavList() {
        const {
          data: res
        } = await uni.$http.get("/api/public/v1/home/catitems")
        if (res.meta.status !== 200) {
          return uni.$showMsg()
        } else {
          this.navList = res.message
        }
      },
      // 获取楼层的数据列表
      async getFloorList() {
        const {
          data: res
        } = await uni.$http.get("/api/public/v1/home/floordata")
        if (res.meta.status !== 200) {
          return uni.$showMsg()
        } else {
          // 通过双层 forEach 循环，处理 URL 地址
          res.message.forEach(floor => {
            floor.product_list.forEach(prod => {
              prod.url = '/subpkg/goods_list/goods_list?' + prod.navigator_url.split('?')[1]
            })
          })

          this.floorList = res.message
        }
      },
      // 点击分类导航图标的事件处理函数
      navClickHandler(item) {
        if (item.name == '分类') {
          uni.switchTab({
            url: "/pages/cate/cate"
          })
        }
      }
    },
    onLoad() {
      // 在小程序页面刚加载的时候，调用获取轮播图数据的方法
      this.getSwiperList()
      this.getNavList()
      this.getFloorList()
    },
  }
</script>

<style lang="scss">
  swiper {
    height: 330rpx;

    .swiper-item,
    image {
      width: 100%;
      height: 100%;
    }
  }

  .nav-list {
    display: flex;
    justify-content: space-around;
    margin: 15px 0;

    .nav-img {
      width: 128rpx;
      height: 140rpx;
    }
  }

  .floor-title {
    height: 60rpx;
    width: 100%;
    display: flex;
  }

  .right-img-box {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-around;
  }

  .floor-img-box {
    display: flex;
    padding-left: 10rpx;
  }
</style>
