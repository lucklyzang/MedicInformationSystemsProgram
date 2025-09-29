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
			<nav-bar :home="false" backState='2000' fontColor="#FFF" bgColor="none" title="个人中心">
			</nav-bar> 
		</view>
		<view class="content">
			<view class="content-top">
				<view class="account-area account-area-bottom-border">
					<view class="account-area-left">
						账号
					</view>
					<view class="account-area-right">
						LIJI
					</view>
				</view>
				<view class="account-area">
					<view class="account-area-left">
						姓名
					</view>
					<view class="account-area-right">
						LIJI
					</view>
				</view>
				<view class="account-area">
					<view class="account-area-left">
						所属科室
					</view>
					<view class="account-area-right">
						LIJI
					</view>
				</view>
				<view class="account-area" @click="modificationPasswordEvent">
					<view class="account-area-left">
						修改密码
					</view>
					<view class="account-area-right">
						<u-icon name="arrow-right" color="#101010" size="24"></u-icon>
					</view>
				</view>
			</view>
		</view>
		<view class="bottom-area">
			<view class="quit-area">退出登录</view>
			<view class="version-area">当前版本 1.0.13</view>
		</view>
	</view>
</template>

<script>
	import {
		mapGetters,
		mapMutations
	} from 'vuex'
	import _ from 'lodash'
	
	import {
		setCache,
		removeAllLocalStorage,
		fenToYuan
	} from '@/common/js/utils'
	import { } from '@/api/user.js'
	import navBar from "@/components/zhouWei-navBar"
	export default {
		components: {
			navBar
		},
		data() {
			return {
				infoText: '开启中···',
				showLoadingHint: false,
			}
		},
		computed: {
			...mapGetters([
				'userInfo',
				'userBasicInfo',
				'statusBarHeight',
				'navigationBarHeight'
			]),
			// userName() {
			// 	return this.userInfo.userName
			// },
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
			accountName() {
			}
		},
		onShow() {
		},
		methods: {
			...mapMutations([
				'changeUserBasicInfo'
			]),
			
			// 修改密码事件
			modificationPasswordEvent () {
				uni.navigateTo({
					url: '/pages/modificationPassword/modificationPassword'
				})
			},
			
			// 获取用户基本信息
			queryUserBasicMessage (flag) {
				if (flag) {
					this.showLoadingHint = true;
					this.infoText = '加载中...';
				};
				getUserMessage().then((res) => {
					if ( res && res.data.code == 0) {
						this.changeUserBasicInfo(res.data.data);
						this.personPhotoSource = !this.userBasicInfo.avatar ? this.defaultPersonPhotoIconPng :  this.userBasicInfo.avatar;
						this.niceNameValue = !this.userBasicInfo.nickname ? this.niceNameValue : this.userBasicInfo.nickname;
						this.isSendOrdersValue = this.userBasicInfo.receive;
						this.isAuth = this.userBasicInfo.auth;
						this.cashOut = this.userBasicInfo.cashOut;
					} else {
						this.$refs.uToast.show({
							message: res.data.msg,
							type: 'error',
							position: 'bottom'
						})
					};
					if (flag) {
						this.showLoadingHint = false
					}
				})
				.catch((err) => {
					if (flag) {
						this.showLoadingHint = false
					};
					this.$refs.uToast.show({
						message: err.message,
						type: 'error',
						position: 'bottom'
					})
				})
			}
		}
	}
</script>

<style lang="scss">
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
			 padding: 6px 0;
			 box-sizing: border-box;
			 position: relative;
			 background: #F8F8F8;
			 .content-top {
				 background: #fff;
				 .account-area-bottom-border {
					 border-bottom: 1px solid #F8F8F8;
				 };
				 .account-area {
					 padding: 0 10px;
					 box-sizing: border-box;
					 height: 44px;
					 display: flex;
					 align-items: center;
					 justify-content: space-between;
					 .account-area-left {
						 font-size: 14px;
						 color: #9C9C9C;
					 };
					 .account-area-right {
						 font-size: 14px;
						 color: #000000;
					 }
				 }
			 }
		};
		.bottom-area {
			height: 120px;
			display: flex;
			flex-direction: column;
			justify-content: space-between;
			align-items: center;
			padding-bottom: 10px;
			box-sizing: border-box;
			.quit-area {
				display: block;
				height: 44px;
				background: #E86F50;
				border-radius: 4px;
				font-size: 14px;
				color: #fff;
				line-height: 44px;
				text-align: center;
				width: 70%;
			};
			.version-area {
				font-size: 14px;
				color: #9C9C9C;
			}
		}
	}
</style>
