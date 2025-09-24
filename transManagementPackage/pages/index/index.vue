<template>
  <view class="content-box">
		<u-transition :show="showLoadingHint" mode="fade-down">
			<view class="loading-box" v-if="showLoadingHint">
				<u-loading-icon :show="showLoadingHint" :text="infoText" size="18" textSize="16"></u-loading-icon>
			</view>
		</u-transition>
		<view class="top-background-area" :style="{ 'height': statusBarHeight + navigationBarHeight + 5 + 'px' }"></view>
		<u-toast ref="uToast" />
		<view class="nav">
			<nav-bar :home="false" :isShowBackText="true" :isHomeText="true" backState='3000' fontColor="#FFF" bgColor="none" title="任务呼叫" @backClick="backTo">
			</nav-bar> 
		</view>
    <view class="content">
			<view class="trans-type">
				<view class="trans-type-title">
					运送类型
				</view>
				<view class="trans-type-content">
					<view class="trans-list" v-for="(item,index) in transList" :key="index" @click="transTypeEvent(item,index)">
						<view class="list-top">
							<image src="/static/img/circle-background.png" mode="widthFix"></image>
							<image :src="item.url" mode="widthFix"></image>
						</view>
						<view class="list-bottom">{{ item.text }}</view>
					</view>
				</view>
			</view>
    </view>
		<view class="tab-bar">
			<u-tabbar
			  :value="valueName"
			  @change="tabBarEvent"
			  :placeholder="false"
				activeColor="#3890EE"
				:fixed="true"
			  :safeAreaInsetBottom="true"
			>
			  <u-tabbar-item text="呼叫">
			    <image
						style="width:15px"
						mode="widthFix"
			      slot="active-icon"
			      src="/static/img/call-active-icon.png"
			    ></image>
			    <image
			      slot="inactive-icon"
						style="width:15px"
						mode="widthFix"
			      src="/static/img/call-inactive-icon.png"
			    ></image>
			  </u-tabbar-item>
				<u-tabbar-item text="实时任务">
				  <image
						style="width:15px"
						mode="widthFix"
				    slot="active-icon"
				    src="/static/img/real-timetask-active-icon.png"
				  ></image>
				  <image
						style="width:15px"
						mode="widthFix"
				    slot="inactive-icon"
				    src="/static/img/real-timetask-inactive-icon.png"
				  ></image>
				</u-tabbar-item>
				<u-tabbar-item text="历史任务">
				  <image
						style="width:15px"
						mode="widthFix"
				    slot="active-icon"
				    src="/static/img/historical-task-active-icon.png"
				  ></image>
				  <image
						style="width:15px"
						mode="widthFix"
				    slot="inactive-icon"
				    src="/static/img/historical-task-inactive-icon.png"
				  ></image>
				</u-tabbar-item>
			</u-tabbar>
		</view>
  </view>
</template>
<script>
import { mapGetters, mapMutations } from "vuex";
import { setCache, removeAllLocalStorage } from '@/common/js/utils'
import navBar from "@/components/zhouWei-navBar"
export default {
  components: {
		navBar
  },
  data() {
    return {
			infoText: '加载中···',
			tierNum: 0,
			valueName: 0,
			showLoadingHint: false,
			transList: [
				{
					text: '标本',
					url: '/static/img/sample-icon.png'
				},
				{
					text: '检查',
					url: '/static/img/examine-icon.png'
				},
				{
					text: '药物文书',
					url: '/static/img/medicine-icon.png'
				}
			]
    }
  },

  onShow() {
		const pages = getCurrentPages(); //获取当前页面栈的实例数组
		if (pages.length == 1) {
			this.tierNum = 1
		} else {
			this.tierNum = pages.length;
		}
  },

  watch: {},

  computed: {
    ...mapGetters(["userInfo",'statusBarHeight','navigationBarHeight']),
		// userName() {
		// 	return this.userInfo.worker.name
		// },
		// userAccount() {
		// 	return this.userInfo.username
		// },
		// proName () {
		// 	return this.userInfo.worker['hospitalList'][0]['hospitalName']
		// },
		// proId() {
		// 	return this.userInfo.worker['hospitalList'][0]['hospitalId']
		// },
		// workerId() {
		// 	return this.userInfo.worker.id
		// },
		// depId() {
		// 	return this.userInfo.worker['departments'][0]['id']
		// }
  },

  methods: {
    ...mapMutations([
		]),

     // 顶部导航返回事件
     backTo () {
     	uni.navigateBack()
     },
		 
		 
		 // 运送类型点击事件
		 transTypeEvent (item,index) {
			uni.navigateTo({
				url: `/transManagementPackage/pages/callTask/callTask?msg=${item.text}`
			})
		 },
		 
		 // tabBar点击事件
		 tabBarEvent (index) {
			 this.valueName = index
		 }
  }
};
</script>
<style lang='scss' scoped>
@import "~@/common/stylus/variable.scss";
page {
	width: 100%;
	height: 100%;
};
.content-box {
  @include content-wrapper;
	height: 100vh !important;
  box-sizing: border-box;
  background: #f6f6f6;
	::v-deep .u-popup {
		flex: none !important
	};
  ::v-deep .u-loading-icon {
  	position: absolute;
  	top: 50%;
  	left: 50%;
  	transform: translate(-50%,-50%);
  	z-index: 200000;
  };
  ::v-deep .u-transition {
  	z-index: 100000 !important;
  };
  .top-background-area {
  	width: 100%;
  	background: #3890EE;
  	position: absolute;
  	top: 0;
  	left: 0;
  	z-index: 10
  };
  .nav {
		width: 100%;
  };
  .content {
		 flex: 1;
		 overflow: auto;
		 padding: 8px 4px;
		 box-sizing: border-box;
		 position: relative;
		 background: #F8F8F8;
		 .trans-type {
		 	padding: 10px 10px 20px 10px;
		 	box-sizing: border-box;
		 	width: 98%;
		 	margin: 0 auto;
		 	background: #fff;
		 	border-radius: 10px;
		 	.trans-type-title {
		 		font-size: 16px;
		 		color: #242424;
		 		font-weight: bold;
		 		margin-bottom: 20px;
		 	};
		 	.trans-type-content {
		 		display: flex;
		 		flex-wrap: wrap;
		 		.trans-list {
		 			width: 25%;
		 			display: flex;
		 			flex-direction: column;
		 			.list-top {
		 				width: 50px;
		 				height: 50px;
						position: relative;
		 				>image {
							&:nth-child(1) {
								width: 50px;
								position: absolute;
								top: 0;
								left: 0
							};
							&:nth-child(2) {
								width: 24px;
								position: absolute;
								top: 50%;
								left: 50%;
								transform: translate(-50%,-50%)
							};
		 				}
		 			};
		 			.list-bottom {
						width: 50px;
						text-align: center;
		 				margin-top: 10px;
		 				font-size: 16px;
		 				color: #101010;
		 			};
					&:nth-child(2) {
						width: 35% !important;
						align-items: center;
					};
					&:nth-child(3) {
						width: 40% !important;
						align-items: center;
						.list-bottom {
							width: 70px !important
						}
					}
		 		}
		 	}
		 }
  }
}
</style>