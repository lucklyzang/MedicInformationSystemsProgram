<template>
	<view class="content-box">
		<u-transition :show="showLoadingHint" mode="fade-down">
			<view class="loading-box" v-if="showLoadingHint">
				<u-loading-icon :show="showLoadingHint" :text="infoText" size="18" textSize="16"></u-loading-icon>
			</view>
		</u-transition>
		<view class="top-background-area" :style="{ 'height': statusBarHeight + navigationBarHeight + 5 + 'px' }"></view>
		<u-toast ref="uToast" />
		<!-- 取消订单原因弹框 -->
		<view class="transport-rice-box" v-if="showCancelReason">
			<ScrollSelection buttonLocation='top' v-model="showCancelReason" :pickerValues="canCelReasonDefaultIndex" :isShowSearch="false" :columns="CancelReasonOption" @sure="cancelReasonSureEvent" @cancel="cancelReasonCancelEvent" @close="cancelReasonCloseEvent" />
		</view>
		<view class="nav">
			<nav-bar :home="false" :isShowBackText="true" backState='3000' fontColor="#FFF" bgColor="none" title="保洁管理" @backClick="backTo">
			</nav-bar> 
		</view>
		<view class="content">
			<view class="empty-info" v-show="noDataShow">
				<u-empty text="数据为空" mode="list"></u-empty>
			</view>
			<view class="task-tail-title">
				<u-tabs 
				  :list="list"
					:scrollable="false" 
					lineColor="#fff"
					:activeStyle="{
						color: '#2c9af1',
						fontSize: '14px'
					}"
				 :inactiveStyle="{
						color: '#606060',
						fontSize: '14px'
				 }"
					lineWidth="0" 
					lineHeight="0"
					:current="current" 
					@change="tabChange"
				>
				</u-tabs>
				<view class="tab-line" :class="{'tab-left':current == 0,'tab-right':current == 1}"></view>
			</view>
			<view class="task-tail-content" v-show="current == 0">
				<view class="task-tail-content-item" v-for="(item,index) in stateCompleteList" :key="index" @click="enterTaskMessage(item)">
					<view class="item-title">
						<view class="item-top-one">
							<view class="number">
								<text>编号: {{item.number}}</text>
								<text>{{item.createTime}}</text>
							</view>
						  <view class="priority"
							:class="{
								'noStartStyle ' : item.state == 1 || item.state == 2, 
								'underwayStyle' : item.state == 3,
								'waitReviewStyle' : item.state == 4,
								'completeStyle' : item.state == 5,
								'haveReviewStyle' : item.state == 6,
								'cancelStyle' : item.state == 7,
								'reviewStyle' : item.state == 8
								}"
							>
						  	<text>{{stateTransfer(item.state)}}</text>
						  </view>
						</view>
					</view>
					<view class="item-top">
						<view class="item-top-two">
						 <text>优先级:</text>
						 <text>{{priorityTransfer(item.priority)}}</text>
						</view>
						<view class="item-top-three">
						 <text>位置:</text>
						 <text>{{!item.taskDesc ? '无' : item.taskDesc}}</text>
						</view>
						<view class="item-top-four">
						 <text>问题描述:</text>
						 <text>{{!item.taskDesc ? '无' : item.taskDesc}}</text>
						</view>
					</view>
					<view class="item-bottom">
						<view class="item-bottom-right">
							<view class="left">
								<text @click.stop="reminder(item)">催单</text>
							</view>
							<view class="right" v-show="item.state !== 3">
								<text @click.stop="cancel(item)">取消订单</text>
							</view>
						</view>
					</view>
				</view>
			</view>
			<view class="task-tail-content task-tail-content-going" v-show="current == 1">
				<view class="task-tail-content-item" v-for="(item,index) in stateCompleteList" @click="enterTaskMessage(item)" :key="index">
					<view class="item-title">
						<view class="item-top-one">
							<view class="number">
								<text>编号: {{item.number}}</text>
								<text>{{item.createTime}}</text>
							</view>
						  <view class="priority"
						  :class="{
						  	'noStartStyle ' : item.state == 1 || item.state == 2, 
						  	'underwayStyle' : item.state == 3,
						  	'waitReviewStyle' : item.state == 4,
						  	'completeStyle' : item.state == 5,
						  	'haveReviewStyle' : item.state == 6,
						  	'cancelStyle' : item.state == 7,
						  	'reviewStyle' : item.state == 8
						  	}"
						  >
						  	<text>{{stateTransfer(item.state)}}</text>
						  </view>
						</view>
					</view>
					<view class="item-top">
						<view class="item-top-two">
						 <text>优先级:</text>
						 <text>{{priorityTransfer(item.priority)}}</text>
						</view>
						<view class="item-top-three">
						 <text>位置:</text>
						 <text>{{!item.taskDesc ? '无' : item.taskDesc}}</text>
						</view>
						<view class="item-top-four">
						 <text>问题描述:</text>
						 <text>{{!item.taskDesc ? '无' : item.taskDesc}}</text>
						</view>
					</view>
					<view class="item-bottom">
						<view class="item-bottom-right">
							<view class="left">
								<text @click.stop="reminder(item)">催单</text>
							</view>
							<view class="right" v-show="item.state !== 3">
								<text @click.stop="cancel(item)">取消订单</text>
							</view>
						</view>
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
			  	  class="u-page__item__slot-icon"
			  		style="width:19px;height:18px"
			      slot="active-icon"
			      src="/static/img/call-active-icon.png"
			    ></image>
			    <image
			  	  class="u-page__item__slot-icon"
			      slot="inactive-icon"
			  		style="width:19px;height:18px"
			      src="/static/img/call-inactive-icon.png"
			    ></image>
			  </u-tabbar-item>
			  <u-tabbar-item text="实时任务">
			    <image
			  	  class="u-page__item__slot-icon"
			  		style="width:19px;height:18px"
			      slot="active-icon"
			      src="/static/img/real-timetask-active-icon.png"
			    ></image>
			    <image
			  	  class="u-page__item__slot-icon"
			  		style="width:19px;height:18px"
			      slot="inactive-icon"
			      src="/static/img/real-timetask-inactive-icon.png"
			    ></image>
			  </u-tabbar-item>
			  <u-tabbar-item text="历史任务">
			    <image
			  	  class="u-page__item__slot-icon"
			  		style="width:19px;height:18px"
			      slot="active-icon"
			      src="/static/img/historical-task-active-icon.png"
			    ></image>
			    <image
			  	  class="u-page__item__slot-icon"
			  		style="width:19px;height:18px"
			      slot="inactive-icon"
			      src="/static/img/historical-task-inactive-icon.png"
			    ></image>
			  </u-tabbar-item>
			</u-tabbar>
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
		removeAllLocalStorage
	} from '@/common/js/utils'
	import { queryCleaningManageTaskList, cancelTask, cancelTaskReason } from "@/api/environment.js";
	import navBar from "@/components/zhouWei-navBar"
	import ScrollSelection from "@/components/scrollSelection/scrollSelection";
	export default {
		components: {
			navBar,
			ScrollSelection
		},
		data() {
			return {
				infoText: '开启中···',
				showLoadingHint: false,
				valueName: 1,
				list: [{name: '待办任务'}, {name: '进行中'}],
				current: 0,
				contactIsolationPng: require("@/static/img/contact-isolation.png"),
				cancelReasonDefaultIndex: [0],
				cancelReasonOption: [],
				showCancelReason: false,
				currentCancelReason: '请选择',
				stateCompleteList: [
					{
						createTime: '2025-05-15　22：11',
						planUseTime: '2025-05-15　22：11',
						planStartTime: '2025-05-15　22：11',
						patientInfoList: [],
						state: 2,
						setOutPlaceName: 'hi的撒旦',
						destinationName: '既生克',
						taskTypeName: 'Djakarta',
						toolName: '平板车',
						priority: 1,
						number: 'd12',
						quarantine: 1,
						distName: '的急啊卡的',
						destinations: '的急啊卡的',
						patientName: '的急啊卡的',
						bedNumber: 'b12',
						workerName: '飒飒'
					}
				]
			}
		},
		computed: {
			...mapGetters([
				'userInfo',
				'userBasicInfo',
				'statusBarHeight',
				'navigationBarHeight',
				'templateType'
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
		mounted() {
			// this.queryCompleteDispatchTask(
			// 	{
			// 	  	proId : this.proId, // 所属项目id
							// queryDate: '', // 查询时间
							// managerId: this.workerId,// 保洁主管id 
							// taskType: 0 // 0-即时，1-专项
			// 	},'待办'
			// )
		},
		methods: {
			...mapMutations([
				'changeEnvironmentTaskMessage'
			]),
			
			// 顶部导航返回事件
			backTo () {
				uni.redirectTo({
					url: '/cleanManagementPackage/pages/callTask/callTask'
				})
			},
			
			// tab切换改变事件
			tabChange (index) {
				this.current = index['index'];
				if (this.current == 0) {
				  this.queryCompleteDispatchTask(
					{
					   proId : this.proId, // 所属项目id
					   queryDate: '', // 查询时间
					   managerId: this.workerId,// 保洁主管id 
					   taskType: 0 // 0-即时，1-专项
					},'待办'
				  )
				} else {
				  this.queryCompleteDispatchTask(
					{
					  proId : this.proId, // 所属项目id
					  queryDate: '', // 查询时间
					  managerId: this.workerId,// 保洁主管id 
					  taskType: 0 // 0-即时，1-专项
					},'进行中'
				  )
				}
			},
			
			// 查询环境订单取消原因
			getEnvironmentOrderCancelReason () {
				cancelTaskReason({proId: this.proId})
				.then((res) => {
					if (res && res.data.code == 200) {
						for (let i = 0, len = res.data.data.length; i < len; i++) {
							this.CancelReasonOption.push({
								text: res.data.data[i]['cancelName'],
								value: res.data.data[i]['id'],
								id: i
							})
						};
					} else {
						this.showLoadingHint = false;
						this.$refs.uToast.show({
							message: res.data.msg,
							type: 'error',
						})
					}
				})
				.catch((err) => {
					reject({message:err})
				})
			},
			
			// 取消原因下拉选择框确认事件
			cancelReasonSureEvent (val,value,id) {
				if (val) {
					this.cancelReasonDefaultIndex = [id]
					this.currentCancelReason=  val;
					this.cancelEnvironmentWorkerOrderMessageTask({
						id: this.taskId, //当前任务ID
						state: 7, //取消后的状态
						cancelReason: this.this.currentCancelReason//取消原因
					})
				} else {
					this.currentCancelReason = '请选择'
				};
				this.showCancelReason = false
			},
			
			// 取消原因下拉选择框取消事件
			cancelReasonCancelEvent () {
				this.showCancelReason = false
			},
			
			// 取消原因下拉选择框关闭事件
			cancelReasonCloseEvent () {
				this.showCancelReason = false
			},
			
			// 任务优先级转换
			  priorityTransfer (index) {
					switch(index) {
						case 1 :
						return '正常'
						break;
						case 2 :
						return '重要'
						break;
						case 3 :
						return '紧急'
						break;
						case 4 :
						return '紧急重要'
						break;
					}
			  },
				
			//任务状态转换
			stateTransfer (num) {
				switch(num) {
						case 1:
								return '未开始'
								break;
						case 2:
								return '未开始'
								break;
						case 3:
								return '进行中'
								break;
						case 4:
								return '待复核'
								break;
						case 5:
								return '已完成'
								break;
						case 6:
								return '已复核'
								break;
						case 7:
								return '已取消'
								break
						case 8:
								return '复核中'
								break
				} 
			},
				
			// 进入订单详情事件	
			enterTaskMessage (item) {
				this.changeEnvironmentTaskMessage(item);
				uni.navigateTo({
					url: '/cleanManagementPackage/pages/cleanWorkerOrderMessage/cleanWorkerOrderMessage'
				})
			},
			
			// 查询保洁任务
			queryCompleteDispatchTask (data,text) {
			  this.noDataShow = false;
			  this.showLoadingHint = true;
			  queryCleaningManageTaskList(data).then((res) => {
				this.showLoadingHint = false;
				if (res && res.data.code == 200) {
				  this.stateCompleteList = [];
					let temporaryDataList = [];
				  if (res.data.data.length > 0) {
						if (text == '待办') {
							temporaryDataList = res.data.data.filter((item) => { return item.state != 7 && item.state != 0});
						} else {
							temporaryDataList = res.data.data
						};
						if (temporaryDataList.length > 0) {
							this.noDataShow = false;
						} else {
							this.noDataShow = true;
						};
						for (let item of temporaryDataList) {
							this.stateCompleteList.push({
								createTime: item.createTime,
								startTime: item.startTime,
								finalFinishTime: item.finalFinishTime,
								finishTime: item.finishTime,
								state: item.state,
								destinationName: item.depName,
								taskTypeName: item.typeName,
								priority: item.priority,
								number: item.taskNumber,
								id: item.id,
								taskDesc: item.taskDesc,
								workerName: item.workerName
							})
						}
				  } else {
						this.noDataShow = true
				  }
				}
			  })
			  .catch((err) => {
				this.$refs.uToast.show({
					title: `${err.message}`,
					type: 'error'
				});
				this.showLoadingHint = false;
				this.noDataShow = true;
				if (this.isFresh) {
					uni.stopPullDownRefresh();
					this.isFresh = false
				}
			  })
			},
			 
			// 取消事件
			cancel (item) {
				this.sureCancelShow = true;
				this.cancelIndex = null;
				this.taskCancelReason = '';
				this.getEnvironmentOrderCancelReason();
				this.taskId = item.id
			},
			
			// 环境订单的取消
			cancelEnvironmentWorkerOrderMessageTask (data) {
				this.showLoadingHint = true;
				this.infoText = '取消中···';
			  cancelTask(data)
			  .then((res) => {
					this.showLoadingHint = false;
					if (res && res.data.code == 200) {
						this.$refs.uToast.show({
							message: `${res.data.msg}`,
							type: 'success'
						});
						this.queryCompleteDispatchTask({
							proId : this.proId, // 所属项目id
							queryDate: '', // 查询时间
							managerId: this.workerId,// 保洁主管id 
							taskType: 0 // 0-即时，1-专项
						})
					} else {
					 this.$refs.uToast.show({
						message: `${res.data.msg}`,
						type: 'error'
					 })
					}
			  })
			  .catch((err) => {
					this.showLoadingHint = false;
					this.$refs['environmentCancelOption'].clearSelectValue();
					this.$refs.uToast.show({
						message: `${err}`,
						type: 'error'
					})
			  })
			},
			  
			// 保洁任务催单
			reminder(item) {
			  projectTaskReminder(this.proId,item.id).then((res) => {
			    if (res && res.data.code == 200) {
			      this.$refs.uToast.show({
			        title: `${res.data.data}`,
			        type: 'success'
			      })
			    } else {
			      this.$refs.uToast.show({
			        title: `${res.data.msg}`,
			        type: 'warning'
			      })
			    }
			  })
			  .catch((err) => {
			    this.$refs.uToast.show({
			      title: `${err.message}`,
			      type: 'error'
			    })
			  })
			},
			
			// tabBar点击事件
			tabBarEvent (index) {
				this.valueName = index;
				if (this.valueName == 0) {
					 uni.redirectTo({
						url: '/cleanManagementPackage/pages/callTask/callTask'
					 })
				} else if (this.valueName == 1) {
					 uni.redirectTo({
						url: '/cleanManagementPackage/pages/realtimeTask/realtimeTask'
					 })
				} else if (this.valueName == 2) {
					 uni.redirectTo({
						url: '/cleanManagementPackage/pages/historicalTask/historicalTask'
					 })
				}
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
		.tab-bar {
			height: 85px;
			::v-deep {
				.u-tabbar {
					height: 100%;
					.u-tabbar__content {
						background: #F8F8F8;
						.u-tabbar-item {
							transition: none;
						};
						.u-tabbar-item.active {
						  transform: scale(1.1); /* 轻微放大 */
						  transition: transform 0.2s ease; /* 平滑过渡 */
						}
					}
				}
			}
		};
		.content {
			 flex: 1;
			 padding: 6px 4px;
			 box-sizing: border-box;
			 position: relative;
			 background: #F8F8F8;
			 display: flex;
			 flex-direction: column;
			 .empty-info {
					position: absolute;
					top: 0;
					left: 0;
					bottom: 0;
					right: 0;
					margin: auto
			 };
			 .task-tail-title {
				 width: 80%;
				 margin: 0 auto;
				 position: relative;
				 .tab-line {
					 width: 96px;
					 height: 2px;
					 background: #2c9af1;
					 position: absolute;
					 bottom: -2px;
				 };
				 .tab-left {
						left: 0
				 };
				 .tab-right {
					 right: 0
				 };
				 border-bottom: 1px solid #bbbbbb;
				 ::v-deep .u-tabs {
					 .u-tabs__wrapper {
						 .u-tabs__wrapper__nav {
								.u-tabs__wrapper__nav__item {
									padding: 0 20px;
									box-sizing: border-box;
								 &:nth-child(1) {
										justify-content: flex-start !important;
								 };
								 &:nth-child(2) {
										justify-content: flex-end !important;
								 }
								};
								.u-tabs__wrapper__nav__line {
									margin-bottom: -3px;
								}
						 }
					 }
				 }
			 };
			 .task-tail-content {
			 	flex: 1;
			 	overflow: auto;
			 	-webkit-overflow-scrolling: touch;
			 	background: #f7f7f7;
				margin-top: 10px;
			 	.task-tail-content-item {
			 		width: 98%;
			 		margin: 0 auto;
			 		margin-top: 6px;
			 		border-radius: 4px;
			 		background: #FFFFFF;
			 		&:last-child {
			 			margin-bottom: 6px
			 		};
					.item-title {
						box-sizing: border-box;
						padding: 0 12px;
						.item-top-one {
							height: 40px;
							display: flex;
							align-items: center;
							border-bottom: 1px solid #BBBBBB;
						  > view {
						    word-break: break-all;
						    font-size: 12px;
						    text {
						      color: #ACADAF;
						    };
						    &:first-child {
						      flex: 1;
						      display: flex;
						      align-items: center;
						      >text {
						      	display: inline-block;
						      	&:last-child {
						      		margin-left: 4px;
						      		flex: 1
						      	}
						      }
						    };
								&:nth-child(2) {
									width: 60px;
									display: flex;
									align-items: center;
									>image {
										width: 22px;
										height: 22px
									}
								};
						    &:last-child {
						      padding: 0 6px;
									box-sizing: border-box;
						      display: flex;
						      justify-content: flex-end;
						      align-items: center;
									height: 21px;
									background: #E86F50;
									border-radius: 3px;
									>text {
										color: #fff;
										font-size: 14px;
									}
						    }
						  };
							.noStartStyle {
								background: #BBBBBB !important
							};
							.underwayStyle {
								background: #289E8E !important
							};
							.completeStyle {
								background: #242424 !important
							};
							.reviewStyle {
								background: #F2A15F !important
							};
							.haveReviewStyle {
								background: #9B7D31 !important
							};
							.waitReviewStyle {
								background: orange !important
							};
							.cancelStyle {
								background: #E8CB51 !important
							};
							.completeStyle {
								background: #101010 !important
							}
						}
					};
			 		.item-top {
			 			width: 100%;
			 		  box-sizing: border-box;
			 		  padding: 10px 12px;
			 		  > view {
			 		  	display: flex;
							align-items: center;
							>text {
								display: inline-block;
								font-size: 12px;
								color: #101010;
								&:first-child {
									margin-right: 4px;
								};
								&:last-child {
									flex: 1;
									word-break: break-all;
								}
							}
			 		  };
			 			.item-top-two {
			 			};
			 		  .item-top-three {
							margin: 10px 0;
			 		  };
			 		  .item-top-four {
			 		  }
			 		};
			 		.item-bottom {
			 			width: 100%;
						display: flex;
						align-items: center;
						justify-content: flex-end;
			 			.item-bottom-right {
			 				width: 55%;
			 				height: 60px;
			 				display: flex;
			 				align-items: center;
			 				justify-content: space-between;
			 				> view {
								width: 45%;
								height: 30px;
								border-radius: 4px;
								text-align: center;
								line-height: 30px;
								>text {
									font-size: 14px;
								}
			 				};
			 				.left  {
			 					background: #E8CB51;
								color: #fff;
			 				};
			 				.right  {
								color: #E86F50;
								background: #fff;
								border: 1px solid #E86F50;
								box-sizing: border-box;
			 				}
			 			}
			 		}
			 	}
			 }
		}
	}
</style>
