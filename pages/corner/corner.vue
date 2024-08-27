<template>
  <view class="container">
	  <u-navbar :is-back="false" title="聚" :background="background" :customBack="backtoinfo" height="50" title-size=40 title-color=#3e3e3e>
	  </u-navbar>
	  
   <view class="search-container">
       <u-search 
         placeholder="搜索" 
         v-model="searchText" 
         border-color="#dddddd"  
         @search="search" 
         margin="15px"
       ></u-search>
       <view v-if="filteredTopics.length === 0">没有找到相关的超话</view>
       <ul>
         <li v-for="topic in filteredTopics" :key="topic.id">{{ topic.title }}</li>
       </ul>
     </view>

    <view class="container">
      <!-- 轮播图 -->
      <view class="swiper-container">
        <uni-swiper-dot class="uni-swiper-dot-box" @clickItem=clickItem :info="info" :current="current" :mode="mode" :dots-styles="dotsStyles" field="content">
          <swiper class="swiper-box" @change="change" :current="swiperDotIndex">
            <swiper-item v-for="(item, index) in swiperImages" :key="index">
              <view class="swiper-item" :class="'swiper-item' + index">
                <image class="swiper-image" :src="item" mode="aspectFill" />
              </view>
            </swiper-item>
          </swiper>
        </uni-swiper-dot>
      </view>
    </view>
	
  <!-- 2x2排列的四个小圆形图标 -->
  <view class="icons" style="margin-top: 70px;padding: 60px;display: grid;grid-template-columns: repeat(2, 1fr);">
     
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
  data() {
    return {
      searchText: '',  // 搜索输入框的内容
      allTopics: [],   // 存储从API获取的所有超话
      filteredTopics: [],  // 存储过滤后的超话
      background: {
        backgroundColor: '#fed6dc'
      },
      info: [
        {
          colorClass: 'uni-bg-blue',
          url: 'https://qiniu-web-assets.dcloud.net.cn/unidoc/zh/shuijiao.jpg',
        }
      ],
      user: { // 传给后台的信息
        flag: ''
      },
      matchuser1: { // 超话信息
        topicId: '',
        name: '',
        cover: '',
        description: '',
        postCount: '',
        followerCount: '',
        flag: ''
      },
      list: '',
      current: 0,
      modeIndex: -1,
      styleIndex: -1,
      mode: 'default',
      dotsStyles: {},
      swiperDotIndex: 0,
      swiperImages: [
        '/static/MBTIb.png',
        '/static/chinas.png',
        '/static/joinUss.png',
        '/static/study.png'
      ],
    };
  },

  onLoad() {
    this.list = [
      {
        iconPath: "/static/newhomeg.png",
        selectedIconPath: "/static/newhomep.png",
        text: '家',
        isDot: true,
        customIcon: false,
        pagePath: '/pages/home/homepage'
      },
      {
        iconPath: "/static/happygrey.png",
        selectedIconPath: "/static/happierp.png",
        text: '聚',
        isDot: true,
        customIcon: false,
        pagePath: '/pages/corner/corner'
      },
      {
        iconPath: "/static/yanblack.png",
        selectedIconPath: "/static/yanpink.png",
        text: '言',
        midButton: true,
        customIcon: false,
        pagePath: '/pages/post/postpage'
      },
      {
        iconPath: "/static/messagegrey.png",
        selectedIconPath: "/static/messagep.png",
        text: '讯',
        customIcon: false,
        pagePath: '/pages/info/infopage'
      },
      {
        iconPath: "/static/megrey.png",
        selectedIconPath: "/static/mep.png",
        text: '我',
        isDot: false,
        customIcon: false,
        pagePath: '/pages/me/mypage'
      },
    ]
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

    toggleCategory(flag) {
      this.user.flag = flag
      uni.request({
        url: "http://localhost:8080/corner/getTopicsByFlag", // API
        data: this.user, // 传递信息
        method: 'GET',
        success: (res) => {
          console.log(res);
          if (res.statusCode == 200) {
            this.matchuser1 = res.data.data; 
            uni.setStorage({
              key: 'matchuser1',
              data: this.matchuser1,
              success: function() {
                uni.navigateTo({
                  url: '/pages/corner/superWordName',
                })
              }
            });
          } else {
            uni.showToast({
              title: '获取数据失败',
              icon: 'none'
            });
          }
        },
        fail: () => {
          console.log(1111)
        }
      })
    },

    async fetchTopics() {
      try {
        const response = await fetch('http://localhost:8080/corner/topics');
        this.allTopics = await response.json();
        this.filteredTopics = this.allTopics; // 初始化显示所有超话
      } catch (error) {
        console.error('获取超话失败:', error);
      }
    },

    search() {
      if (this.searchText) {
        this.filteredTopics = this.allTopics.filter(topic => 
          topic.title.includes(this.searchText) 
        );
      } else {
        this.filteredTopics = this.allTopics; // 如果搜索框为空，显示所有超话
      }
    }
  },

  mounted() {
    this.fetchTopics(); // 组件加载时获取超话
  }
};
</script>

//轮播图
<style lang="scss">
	
	.swiper-image {
	  width: 100%; /* 使图片宽度充满整个轮播项 */
	  height: 100%; /* 使图片高度充满整个轮播项 */
	  display: block; /* 防止图片下方出现空隙 */
	  margin: auto; /* 水平居中图片 */
	}
	/* 可能需要调整轮播图容器的高度 */
	.swiper-box {
	  height: 300px; /* 根据您的需求调整这个高度 */
	}
	.container {
	  display: flex;
	  flex-direction: column;
	  background-color:#fcfcfa;
	 
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
  gap: 0px;/* 调整图标之间的间距 */
 /* margin-top: 40px;
    padding: 40px; */
}

</style>
<style scoped>
.search-container {
  padding: 16px;
}
</style>