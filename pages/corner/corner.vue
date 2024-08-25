<template>
  <view class="container">
    <!-- 搜索栏 -->
	<!-- 能根据关键词“专业” “同乡”搜索感兴趣的内容
	还能进一步搜索“专业->计科”  “同乡->安徽”
	这个搜索的地方包含所有数据，其他地方可能只有自己板块数据 -->
    <view class="search-container">
      <u-search placeholder="搜索" v-model="searchText" border-color=#dddddd  @search="search"></u-search>
    </view>

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
   <view class="icons" style="margin-top: 70px;padding: 60px;">
      <!-- 专业 -->
      <view class="icon1" @click="toggleCategory('专业')" style="background-color: #f2b2c3; display: flex; flex-direction: column; align-items: center; margin: 10px;">
        <image class="icon2" src="/static/speciality.png" />
        <text class="icon-text">专业</text>
      </view>
      <!-- 同乡 -->
      <view class="icon1" @click="toggleCategory('同乡')" style="background-color: #fed2e0; display: flex; flex-direction: column; align-items: center; margin: 10px;">
        <image class="icon2" src="/static/homie.png" />
        <text class="icon-text">同乡</text>
      </view>
      <!-- MBTI -->
      <view class="icon1" @click="toggleCategory('MBTI')" style="background-color: #f9e4e5; display: flex; flex-direction: column; align-items: center; margin: 10px;">
        <image class="icon2" src="/static/mbti.png" />
        <text class="icon-text">MBTI</text>
      </view>
      <!-- 社团 -->
      <view class="icon1" @click="toggleCategory('社团')" style="background-color: #ffdfe6; display: flex; flex-direction: column; align-items: center; margin: 10px;">
        <image class="icon2" src="/static/society.png" />
        <text class="icon-text">社团</text>
      </view>
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
		searchText:"搜索你想要的",
		info: [
				{
					colorClass: 'uni-bg-blue',
					url: 'https://qiniu-web-assets.dcloud.net.cn/unidoc/zh/shuijiao.jpg',
					//轮播图图片，不知要不要改成数据库图片
				}
			],
			//-------------------------
			list:'',
			current: 4,
			//-------------------------
			
			//轮播图
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
    };
  },
  
  //--------------------------------------------
  onLoad() {
  	this.list = [{
  			iconPath: "/static/newhomeg.png",
  			selectedIconPath: "/static/newhomep.png",
  			text: '家',
  			isDot: true,
  			customIcon: false,
  			pagePath:'/pages/home/homepage'
  		},
  		{
  			iconPath:  "/static/happygrey.png",
  			selectedIconPath:"/static/happierp.png",
  			text: '聚',
  			isDot: true,
  			customIcon: false,
  			pagePath:'/pages/corner/corner'
  		},
  		{
  			iconPath: "/static/yanblack.png",
  			selectedIconPath: "/static/yanpink.png",
  			text: '言',
  			midButton: true,
  			customIcon: false,
  			pagePath:'/pages/tabbar3'
  		},
  		{
  			iconPath:  "/static/messagegrey.png",
  			selectedIconPath:"/static/messagep.png",
  			text: '讯',
  			customIcon: false,
  			pagePath:'/pages/info/infopage'
  		},
  		{
  			iconPath:  "/static/megrey.png",
  			selectedIconPath:"/static/mep.png",
  			text: '我',
  			isDot: false,
  			customIcon: false,
  			pagePath:'/pages/me/mypage'
  		},
  	]
  },
  
  
  
  
  //--------------------------------------------
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
	  //-------------------------------------------
    toggleCategory(category) {
      // 假设这是点击某个图标后触发的逻辑
	  //跳转到对应的超话推荐与关注页面
      console.log(`点击了 ${category}`);
	   uni.navigateTo({
	          url: '/pages/corner/Superwordname',
	        });
    },
	
	//---------------------------------------------
	//重点！！！之后要和后端连接
    search() {
      // 搜索逻辑
      console.log(`搜索了: ${this.searchText}`);
    }
	//--------------------------------------------
	
  }
};
</script>

//轮播图
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
		border-color: #fff5ff;
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


<style>
	//四个框框
.container {
  display: flex;
  flex-direction: column;
  
}

.icon2 {
  width: 50%; /* 设置图标的宽度 */
  height: 50%; /* 设置图标的高度 */
  margin-top: 10px; /* 设置图标与顶部的距离 */
}

.icon-text {
  color: white; /* 设置文字颜色为黑色 */
  margin-top: 5px; /* 设置文字与图标的距离 */
  font-size: 14px; /* 设置文字大小 */
}

.icon1 {
  width: 100px;
  height: 100px;
  background-color: #fed6e3;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 24px;
  color: #fff;
  cursor: pointer;
}

.slider {
  margin-top: 20px;
}

.icons {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 0px;/* 调整图标之间的间距 */
 /* margin-top: 40px;
    padding: 40px; */
}
//搜索框
.search-container {
  display: flex;
  justify-content: center;
    bg-color:#007bff;
	 border-color:#007bff;
	/* 使搜索框水平居中 */
}



</style>