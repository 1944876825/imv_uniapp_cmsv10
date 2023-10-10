<template>
	<view>
		<view class="head" v-if="mvlist.length == 0">
			啥也没有...
		</view>
		<view class="center" v-else>
			<view class="hbox" v-for="item in mvlist" @click="play(item.vid)">
				<view class="himg fl">
					<image :src="item.img" mode="aspectFill"></image>
				</view>
				<view class="hdet fr">
					<view class="hdett">
						<text>{{item.name}}</text>
						<text>{{item.js}}</text>
					</view>
					<view class="hdetb">
						点击播放
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
				mvlist:[]
			};
		},
		onLoad() {
			this.gethistory()
		},
		onPullDownRefresh() {
			this.mvlist = [];
			this.gethistory()
			setTimeout(function() {
				uni.stopPullDownRefresh();
			},500);
		},
		methods:{
			gethistory() {
				uni.getStorage({
					key:'history',
					success:res=>{
						this.mvlist = res.data;
					}
				})
			},
			play(id) {
				uni.navigateTo({
					url:'../../pages/play/play?vid=' + id,
					animationType: "slide-in-right",
					animationDuration: 300
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.head {
		margin-top: 80rpx;
		text-align: center;
		color: $zts;
		font-size: 16px;
	}
	.center {
		width: 96%;
		margin: 10rpx auto;
		.hbox {
			margin: 20rpx 0;
			height: 160rpx;
			.himg {
				width: 40%;
				height: 160rpx;
				image {
					width: 100%;
					height: 160rpx;
					border-radius: 20rpx;
				}
			}
			.hdet {
				width: 60%;
				height: 160rpx;
				position: relative;
				view {
					margin-left: 20rpx;
					font-size: 14px;
				}
				.hdett {
					color: $zts;
					text:nth-child(2){
						margin-left: 10rpx;
					}
				}
				.hdetb {
					color: #9c9c9c;
					position: absolute;
					font-size: 12px;
					bottom: 0;
				}
			}
		}
	}
</style>
