<template>
	<view class="content" :style="{ 'padding-top': statusBarHeight + 'px' }">
		<view class="top-background-area" :style="{ 'height': statusBarHeight + navigationBarHeight + 'px' }"></view>
		<u-toast ref="uToast" />
    <!-- 顶部标题 -->
    <view class="topTabbar" :style="{ 'height': navigationBarHeight + 'px', 'lineHeight': navigationBarHeight + 'px', 'paddingRight': capsuleMessage.width + 10 + 'px' }">
			<view class="title-left">
				<image src="/static/img/home-icon.png" mode="widthFix"></image>
				<text>新茂医信</text>
			</view>
			<view class="title-center">
				智慧后勤服务平台
			</view>
    </view>
		<view class="home-banner-area">
			<image src="/static/img/home-banner.png" mode="widthFix"></image>
		</view>
		<view class="content-box">
			<view class="service-management">
				<view class="service-management-title">
					服务管理
				</view>
				<view class="service-management-content">
					<view class="service-list" v-for="(item,index) in serviceList" :key="index" @click="serviceManagementEvent(item,index)">
						<view class="list-top">
							<image :src="item.url" mode="widthFix"></image>
						</view>
						<view class="list-bottom">{{ item.text }}</view>
					</view>
				</view>
			</view>
			<view class="department-box">
				急症科-JZK
			</view>
		</view>
		<u-transition :show="showLoadingHint" mode="fade-down">
			<view class="loading-box" v-if="showLoadingHint">
				<u-loading-icon :show="showLoadingHint" text="加载中···" size="18" textSize="16"></u-loading-icon>
			</view>
		</u-transition>
	</view>
</template>
<script>
	import {
		mapGetters,
		mapMutations
	} from 'vuex'
	// import { getUserChatPage, getUserMessage, chatMessageRead } from '@/api/user.js'
	// import { getTradeOrderUserCareInfo } from '@/api/orderForm.js'
	import store from '@/store'
	export default{
		data() {
			return {
				showLoadingHint: false,
				triangleRectListInfoShow: false,
				infoText: '加载中···',
				loadingText: '加载中···',
				serviceList: [
					{
						text: '中央运送',
						url: '/static/img/trans-icon.png'
					},
					{
						text: '工程维修',
						url: '/static/img/project-icon.png'
					},
					{
						text: '保洁管理',
						url: '/static/img/clean-icon.png'
					}
				]
			}
		},
		updated() {},
		computed: {
			...mapGetters([
				'userInfo',
				'socketOpen',
				'statusBarHeight',
				'navigationBarHeight',
				'capsuleMessage'
			]),
			userName() {
				return this.userInfo.userName
			},
			// proName () {
			//   return this.userInfo.worker['hospitalList'][0]['hospitalName']
			// },
			// proId() {
			// 	return this.userInfo.worker['hospitalList'][0]['hospitalId']
			// },
			// workerId() {
			// 	return this.userInfo.worker.id
			// },
			// depId() {
			// 	return this.userInfo.worker['departments'][0]['id']
			// },
			// windowHeight() {
			//   return this.rpxTopx(uni.getSystemInfoSync().windowHeight)
			// },
			// 键盘弹起来的高度+发送框高度
			inputHeight(){
				return this.bottomHeight+this.keyboardHeight
			}
		},
		
		onShow() {
			// 获取用户基本信息
			// getUserMessage().then((res) => {
			// 	if ( res && res.data.code == 0) {
			// 		this.changeUserBasicInfo(res.data.data);
			// 		this.personPhotoSource = !this.userBasicInfo.avatar ? this.defaultPersonPhotoIconPng :  this.userBasicInfo.avatar;
			// 	} else {
			// 		this.$refs.uToast.show({
			// 			title: res.data.msg,
			// 			type: 'error',
			// 			position: 'bottom'
			// 		})
			// 	}
			// })
			// .catch((err) => {
			// 	this.$refs.uToast.show({
			// 		title: err.message,
			// 		type: 'error',
			// 		position: 'bottom'
			// 	})
			// })
		},
		
		onLoad(options) {
			uni.onKeyboardHeightChange(res => {
				//这里正常来讲代码直接写
				//this.keyboardHeight=this.rpxTopx(res.height)就行了
				//但是之前界面ui设计聊天框的高度有点高,为了不让键盘和聊天输入框之间距离差太大所以我改动了一下。
				this.keyboardHeight = this.rpxTopx(res.height)
				if(this.keyboardHeight<0)this.keyboardHeight = 0
			})
		},
		
		onUnload() {
		},
		
		methods: {
			...mapMutations([
				'changeSocketOpen',
				'storeCurrentIndex',
				'storeLocationMessage'
			]),
			
			// 格式化时间
			getNowFormatDate(currentDate,type) {
				// type:1(只显示小时分钟秒),2(只显示年月日)3(只显示年月)4(显示年月日小时分钟秒)5(显示月日)
				let currentdate;
				let strDate = currentDate.getDate();
				let seperator1 = "-";
				let seperator2 = ":";
				let seperator3 = " ";
				let month = currentDate.getMonth() + 1;
				let hour = currentDate.getHours();
				let minutes = currentDate.getMinutes();
				let seconds = currentDate.getSeconds();
				if (month >= 1 && month <= 9) {
					month = "0" + month;
				};
				if (hour >= 0 && hour <= 9) {
					hour = "0" + hour;
				};
				if (minutes >= 0 && minutes <= 9) {
					minutes = "0" + minutes;
				};
				if (seconds >= 0 && seconds <= 9) {
					seconds = "0" + seconds;
				};
				if (strDate >= 0 && strDate <= 9) {
					strDate = "0" + strDate;
				};
				if (type == 1) {
					currentdate = hour + seperator2 + minutes + seperator2 + seconds
				};
				if (type == 2) {
					currentdate = currentDate.getFullYear() + seperator1 + month + seperator1 + strDate
				};
				if (type == 3) {
					currentdate = currentDate.getFullYear() + seperator1 + month
				};
				if (type == 4) {
					currentdate = currentDate.getFullYear() + seperator1 + month + seperator1 + strDate + seperator3 + hour + seperator2 + minutes + seperator2 + seconds
				};
				if (type == 5) {
					currentdate = month + seperator1 + strDate
				};
				return currentdate
			},
			
			// px转换成rpx
			rpxTopx(px){
				let deviceWidth = uni.getSystemInfoSync().windowWidth;
				let rpx = ( 750 / deviceWidth ) * Number(px);
				return Math.floor(rpx)
			},
			
			// 服务管理项点击事件
			serviceManagementEvent (item,index) {
				if (item.text == '中央运送') {
					uni.navigateTo({
						url: '/transManagementPackage/pages/index/index'
					})
				} else if (item.text == '工程维修') {
					uni.navigateTo({
						url: '/projectManagementPackage/pages/callTask/callTask'
					})
				} else if (item.text == '保洁管理') {
					uni.navigateTo({
						url: '/cleanManagementPackage/pages/callTask/callTask'
					})
				}
			}
		}
	}
</script>
<style lang="scss" scoped>
	@import "~@/common/stylus/variable.scss";
	page {
		width: 100%;
		height: 100%;
	};
	$chatContentbgc: #1E86FD;
	$sendBtnbgc: #4F7DF5;
	view,button,text,input,textarea {
		margin: 0;
		padding: 0;
		box-sizing: border-box;
	};
	.content {
		@include content-wrapper;
		height: 100vh !important;
		padding: 0 2px;
		box-sizing: border-box;
		position: relative;
		background: #F8F8F8;
		::v-deep .u-popup {
			flex: none !important
		};
		.top-background-area {
			width: 100%;
			position: absolute;
			top: 0;
			left: 0
		};
    .topTabbar {
			width: 100%;
			display: flex;
			box-sizing: border-box;
			align-items: center;
			position: relative;
			.title-left {
				padding-left: 8px;
				box-sizing: border-box;
				>image {
					width: 23px;
					margin-right: 2px;
					vertical-align: middle;
				};
				>text {
					font-size: 12px;
					color: #3370FF;
					vertical-align: middle;
				}
			};
			.title-center {
				flex: 1;
				text-align: center;
				font-size: 14px;
				color: #101010;
			}
		};
		.home-banner-area {
			margin-top: 10px;
			>image {
				width: 100%;
			}
		};
		.content-box {
			position: relative;
			flex: 1;
			margin-top: 10px;
			.department-box {
				position: absolute;
				bottom: 10px;
				right: 10px;
				font-size: 16px;
				color: #ACADAF;
			};
			.service-management {
				padding: 10px 10px 20px 10px;
				box-sizing: border-box;
				width: 98%;
				margin: 0 auto;
				background: #fff;
				border-radius: 10px;
				.service-management-title {
					font-size: 16px;
					color: #242424;
					font-weight: bold;
					margin-bottom: 16px;
				};
				.service-management-content {
					display: flex;
					flex-wrap: wrap;
					.service-list {
						width: 25%;
						display: flex;
						flex-direction: column;
						.list-top {
							width: 50px;
							height: 50px;
							display: flex;
							align-items: center;
							justify-content: center;
							background: #3370FF;
							border-radius: 12px;
							>image {
								width: 32px;
							}
						};
						.list-bottom {
							margin-top: 10px;
							font-size: 12px;
							color: #101010;
						}
					};
					>view {
						&:nth-child(2) {
							.list-top {
								background: #FC8F66 !important;
							} 
						};
						&:nth-child(3) {
							.list-top {
								background: #4CC9E4 !important;
							} 
						}
					}
				}
			}
		};
		.loading-box {
			height: 35px;
			display: flex;
			align-items: center;
			justify-content: center
		};
	}
</style>