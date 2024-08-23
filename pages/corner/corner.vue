<template>
  <view class="container">
    

    <!-- 搜索栏 -->
    <u-search placeholder="搜索" v-model="searchText" @search="search"></u-search>

    <view class="container">
      <!-- 轮播图 -->
      <view class="swiper-container">
        <uni-swiper-dot class="uni-swiper-dot-box" @clickItem=clickItem :info="info" :current="current" :mode="mode" :dots-styles="dotsStyles" field="content">
          <swiper class="swiper-box" @change="change" :current="swiperDotIndex">
            <swiper-item v-for="(item, index) in 3" :key="index">
              <view class="swiper-item" :class="'swiper-item' + index">
                <text style="color: #fff; font-size: 32px;">{{index+1}}</text>
              </view>
            </swiper-item>
          </swiper>
        </uni-swiper-dot>
      </view>
      
    </view>

    <!-- 2x2排列的四个小圆形图标 -->
    <view class="icons" style="margin-top: 70px;padding: 80px;">
      <view class="icon" @click="toggleCategory('专业')" style="background-color:#f2b2c3;">专业</view>
      <view class="icon" @click="toggleCategory('同乡')" style="background-color:#fed6e3;">同乡</view>
	  
	  <view class="icon" @click="toggleCategory('MBTI')" style="background-color: #f9e9ea;">MBTI</view>
      <view class="icon" @click="toggleCategory('社团')" style="background-color: #ffe1e7 ;">社团</view>
     
    </view>

<view>
		<!-- 与包裹页面所有内容的元素u-page同级，且在它的下方 -->
		<u-tabbar v-model="current" :list="list" :mid-button="true"></u-tabbar>
	</view>
  </view>
</template>

<script>
export default {
	components: {},
  data() {
	  
    return {
		info: [
				{
					colorClass: 'uni-bg-blue',
					url: 'https://qiniu-web-assets.dcloud.net.cn/unidoc/zh/shuijiao.jpg',
					content: '内容 C'
				}
			],
			
			list: [{
					iconPath: "/static/newhomeg.png",
					selectedIconPath: "/static/newhomep.png",
					text: '家',
					customIcon: false,
				},
				{
					iconPath:  "/static/happygrey.png",
					selectedIconPath:"/static/happierp.png",
					text: '聚',
					customIcon: false,
				},
				{
					iconPath: "/static/yanblack.png",
					selectedIconPath: "/static/yanpink.png",
					text: '言',
					midButton: true,
					customIcon: false,
				},
				{
					iconPath:  "/static/messagegrey.png",
					selectedIconPath:"/static/messagep.png",
					text: '讯',
					customIcon: false,
				},
				{
					iconPath:  "/static/megrey.png",
					selectedIconPath:"/static/mep.png",
					text: '我',
					
					isDot: false,
					customIcon: false,
				},
			],
			current: 0,
			
			
			
			dotStyle: [
				
				{
					backgroundColor: 'rgba(83, 200, 249,0.3)',
					border: '1px rgba(83, 200, 249,0.3) solid',
					color: '#fff',
					selectedBackgroundColor: 'rgba(83, 200, 249,0.9)',
					selectedBorder: '1px rgba(83, 200, 249,0.9) solid'
				}
			],
			modeIndex: -1,
			styleIndex: -1,
			current: 0,
			mode: 'default',
			dotsStyles: {},
			swiperDotIndex: 0,
		
      searchText: ''
    };
  },
  methods: {
	  change(e) {
	  	this.current = e.detail.current
	  },
	  selectStyle(index) {
	  	this.dotsStyles = this.dotStyle[index]
	  	this.styleIndex = index
	  },
	  selectMode(mode, index) {
	  	this.mode = mode
	  	this.modeIndex = index
	  	this.styleIndex = -1
	  	this.dotsStyles = this.dotStyle[0]
	  },
	  clickItem(e) {
	  	this.swiperDotIndex = e
	  },
	  onBanner(index) {
	  	console.log(22222, index);
	  },
    toggleCategory(category) {
      // 假设这是点击某个图标后触发的逻辑
      console.log(`点击了 ${category}`);
    },
    search() {
      // 搜索逻辑
      console.log(`搜索了: ${this.searchText}`);
    }
  }
};
</script>

<style>
.container {
  display: flex;
  flex-direction: column;
  
}

.icon-text {
  position: absolute;
  top: 10px;
  left: 10px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 24px;
  color: #aa675f;
  cursor: pointer;
}

.icon {
  width: 100px;
  height: 100px;
  background-color: #fea49d;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 24px;
  color: #fff;
  cursor: pointer;
}

.icon-text text {
  position: absolute;
  top: 100%;
  left: 200%;
  transform: translate(-50%, -50%);
  font-size: 20px;
}

.slider {
  margin-top: 20px;
}

.icons {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 0px;
  margin-top: 0px;
    padding: 20px; /* 调整图标之间的间距 */
  
}


</style>
<style lang="scss">
	
	.container {
	  display: flex;
	  flex-direction: column;
	  
	 
	}
	
	.swiper-container {
	  // 设置轮播图容器的高度和边距
	  height: 100px; /* 设置轮播图容器的高度 */
	  margin-top: 10px; /* 设置轮播图容器距顶部的高度 */
	}

	.swiper-box {
		height: 200px;
	}
	

	.swiper-item {
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		flex-direction: column;
		justify-content: center;
		align-items: center;
		height: 200px;
		color: #fff;
	}

	.swiper-item0 {
		background-color: #cee1fd;
	}

	.swiper-item1 {
		background-color: #b2cef7;
	}

	.swiper-item2 {
		background-color: #cee1fd;
	}

	.image {
		width: 500rpx;
	}

	/* #ifndef APP-NVUE */
	::v-deep .image img {
		-webkit-user-drag: none;
		-khtml-user-drag: none;
		-moz-user-drag: none;
		-o-user-drag: none;
		user-drag: none;
	}

	/* #endif */

	@media screen and (min-width: 200px) {
		.uni-swiper-dot-box {
			width: 400px;
			margin: 0 auto;
			margin-top: 8px;
		}

		.image {
			width: 10%;
		}
	}

	
	.example-body {
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		flex-direction: row;
		padding: 20rpx;
	}

	.example-body-item {

		flex-direction: row;
		justify-content: center;
		align-items: center;
		margin: 15rpx;
		padding: 15rpx;
		height: 60rpx;
		/* #ifndef APP-NVUE */
		display: flex;
		padding: 0 15rpx;
		/* #endif */
		flex: 1;
		border-color: #e5e5e5;
		border-style: solid;
		border-width: 1px;
		border-radius: 5px;
	}



	.active {
		border-style: solid;
		border-color: #007aff;
		border-width: 1px;
	}
</style>

