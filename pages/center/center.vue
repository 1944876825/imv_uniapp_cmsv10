<template>
	<view class="content">
		<view class="head">
			<view class="header">
				
			</view>
			<view class="tximg" @tap="set">
				<image :src="qq.tx ? qq.tx : user.img" mode=""></image>
				<view class="userxx" style="margin-right: 60rpx;">
					<view class="name">
						{{qq.name ? qq.name : user.name}}
					</view>
					<view class="yiyan">
						{{yiyan ? yiyan : ''}}
					</view>
				</view>
			</view>
			
		</view>
		<view class="center">
			<view class="history" v-if="mvlist.length > 0">
				<view class="htop" @click="history">
					观看历史
				</view>
				<view class="hcen">
					<scroll-view style="border-bottom: 1px solid #9c9c9c;padding-bottom: 20rpx;" class="tab" scroll-x="true">
						<view class="hbox" v-for="item in mvlist" @click="play(item.vid)">
							<view class="himg">
								<image :src="item.img" mode="aspectFill"></image>
							</view>
							<view class="hdet">
								<view class="hdett">
									<text class="mvname">{{item.name}} {{item.js}}</text>
								</view>
							</view>
						</view>
					</scroll-view>
				</view>
			</view>
			<view class="gn">
				<view class="gntop">常用功能</view>
				<view>
					<view class="gnbox" @tap="history">
						<view class="gnimg">
							<image src="../../static/icon/history.png" mode=""></image>
						</view>
						<view class="gnname">
							历史
						</view>
					</view>
					<view class="gnbox" @tap="liuy">
						<view class="gnimg">
							<image src="../../static/icon/liuy.png" mode=""></image>
						</view>
						<view class="gnname">
							留言
						</view>
					</view>
					<view class="gnbox" @tap="wyb" v-show="ifshowweb">
						<view class="gnimg">
							<image src="../../static/icon/wyb.png" mode=""></image>
						</view>
						<view class="gnname">
							网页版
						</view>
					</view>
					<view class="gnbox" @tap="set">
						<view class="gnimg">
							<image src="../../static/icon/set.png" mode=""></image>
						</view>
						<view class="gnname">
							设置
						</view>
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
				hcsize: 0,//本地缓存大小
				mvlist: [],
				yiyan: '',//一言
				qq: {},
				ifshowweb: false,
				user: getApp().globalData.user
			}
		},
		onLoad() {
			this.getyy()
			this.getqq()
			if (getApp().globalData.showweb) {
				this.ifshowweb = true
			}
			// #ifdef H5
				this.ifshowweb = false
			// #endif
		},
		onPullDownRefresh() {
			this.gethistory()
			this.getyy()
			this.getqq()
			setTimeout(function() {
				uni.stopPullDownRefresh();
			},500);
		},
		onShow() {
			this.getqq();
			this.gethistory()
		},
		methods: {
			getyy(){
				uni.request({
					url:'https://api.muxiaoguo.cn/api/yiyan?api_key=741e52b91e82e89a',
					dataType:'json',
					success:res=> {
						if (res.data.code == 200) this.yiyan = res.data.data.content;
					}
				})
			},
			gethistory() {
				uni.getStorage({
					key:'history',
					success:res=>{
						if (this.mvlist.length != res.data.length) {
							this.mvlist = res.data;
						}
					},
					fail:err=>{
						this.mvlist = [];
					}
				})
			},
			history(){
				uni.navigateTo({
					url:'../history/history',
					animationType: "slide-in-right",
					animationDuration: 300
				})
			},
			liuy(){
				uni.navigateTo({
					url:'../liuyan/liuyan',
					animationType: "slide-in-right",
					animationDuration: 300
				})
			},
			wyb(){
				plus.runtime.openURL(getApp().globalData.cms);
			},
			play(id) {
				uni.navigateTo({
					url:'../../pages/play/play?vid=' + id,
					animationType: "slide-in-right",
					animationDuration: 300
				})
			},
			set(){
				uni.navigateTo({
					url:'../set/set',
					animationType: "slide-in-right",
					animationDuration: 300
				})
			},
			getqq(){
				uni.getStorage({
					key:'qq',
					success:res=>{
						this.qq = res.data;
					},
					fail:err=>{
						this.qq = {}
					}
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.head {
		.header {
			height: 30px;
		}
		height: 160px;
		width: 96%;
		margin: 0 auto;
		border-radius: 10px;
		.tximg {
			position: relative;
			margin-top: 80rpx;
			image {
				margin-left: 60rpx;
				width: 130rpx;
				height: 130rpx;
				border-radius: 50%;
			}
			.userxx {
				position: absolute;
				left: 220rpx;
				top:0;
			}
			.name {
				margin-top: 20rpx;
				font-size: 16px;
			}
			.yiyan {
				margin-top: 10rpx;
				font-size: 12px;
				overflow: hidden;
				text-overflow: ellipsis;
				display: -webkit-box;
				-webkit-line-clamp:2;
				-webkit-box-orient:vertical;
			}
		}
		border-bottom: 1px solid $zts;
		box-shadow: 0 2px 1px $zts;
	}
	
	.center {
		.history {
			width: 96%;
			margin: 10px auto;
			border: 1px solid $zts;
			box-shadow: 0 2px 1px $zts;
			border-radius: 8px;
			padding-top: 10px;
			.htop {
				margin: 0 15px;
				color: $zts;
				font-size: 16px;
			}
			.tab {
				display: flex;
				flex-direction: row;
				white-space: nowrap;
			}
			.hbox {
				display: inline-block;
				margin: 0 10px;
				margin-top: 15px;
				height: 220rpx;
				.himg {
					width: 300rpx;
					height: 180rpx;
					image {
						width: 100%;
						height: 180rpx;
						border-radius: 20rpx;
					}
				}
				.hdet {
					margin-top: 10rpx;
					width: 300rpx;
					height: 50rpx;
					view {
						font-size: 12px;
					}
					.hdett {
						color: $zts;
						text-align: center;
						overflow: hidden;
						white-space: nowrap;
						text-overflow: ellipsis;
					}
				}
			}
		}
		
		.gn {
			width: 96%;
			margin: 10px auto;
			border: 1px solid $zts;
			box-shadow: 0 2px 1px $zts;
			border-radius: 8px;
			padding-top: 10px;
			.gntop {
				margin: 0 15px;
				padding-bottom: 10rpx;
				color: $zts;
				font-size: 16px;
			}
			.gnbox {
				display: inline-block;
				width: 25%;
				margin: 10px 0;
				.gnimg {
					width: 80rpx;
					margin: 0 auto;
					image {
						width: 80rpx;
						height: 80rpx;
					}
				}
				.gnname {
					text-align: center;
					color: $zts;
				}
			}
		}
	}
</style>
