<template>
	<view class="mvbox" @click="play">
		<view class="mvimg">
			<image :src="detail.vod_pic" mode=""></image>
			<text v-show="ifxx">{{xx}}</text>
		</view>
		<view class="mvname">
			{{detail.vod_name}}
		</view>
	</view>
</template>

<script>
	export default {
		name:"mvbox",
		props:['mvdetail','vid'],
		data() {
			return {
				detail:{},
				ifxx:false,
				xx:''
			};
		},
		mounted() {
			if (this.vid) {
				uni.request({
					url:getApp().globalData.cms + 'api.php/provide/vod/ac/list/ids/' + this.vid,
					dataType:'json',
					success:res=>{
						if (res.data.code == 1) {
							this.detail = res.data.list[0];
							if (this.detail.vod_pic.substr(0,7) == '/upload') {
								this.detail.vod_pic = getApp().globalData.cms + this.detail.vod_pic.replace('/upload','upload')
							}
							if (this.detail.vod_douban_score > 2.0) {
								this.xx = this.detail.vod_douban_score;
								this.ifxx = true;
							} else if (this.detail.vod_remarks && this.detail.vod_remarks.length < 4){
								this.xx = this.detail.vod_remarks;
								this.ifxx = true;
							} else if (this.detail.vod_year){
								this.xx = this.detail.vod_year;
								this.ifxx = true;
							}else {
								this.ifxx = false;
							}
						}
						
					}
				})
			} else {
				this.detail = this.mvdetail;
				if (this.detail.vod_pic.substr(0,7) == '/upload') {
					this.detail.vod_pic = getApp().globalData.cms + this.detail.vod_pic.replace('/upload','upload')
				}
				if (this.detail.vod_douban_score > 2.0) {
					this.xx = this.detail.vod_douban_score;
					this.ifxx = true;
				} else if (this.detail.vod_year){
					this.xx = this.detail.vod_year;
					this.ifxx = true;
				} else {
					this.ifxx = false;
				}
			}
		},
		methods:{
			play() {
				uni.navigateTo({
					url:'../../pages/play/play?vid=' + this.detail.vod_id,
					animationType: "slide-in-right",
					animationDuration: 300
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.mvbox {
		width: 33.33%;
		height: 380rpx;
		display: inline-block;
		.mvimg {
			width: 90%;
			margin: 0 auto;
			position: relative;
			image {
				width: 100%;
				height: 320rpx;
				border-radius: 16rpx;
			}
			text {
				position: absolute;
				right: 0;
				top: 0;
				font-size: 16rpx;
				background-color: $zts;
				width: 80rpx;
				height: 40rpx;
				color: #fff;
				line-height: 40rpx;
				text-align: center;
				border-bottom-left-radius: 16rpx;
				border-top-right-radius: 16rpx;
				display: inline-block;
			}
		}
		.mvname {
			width: 90%;
			margin: 0 auto;
			font-size: 24rpx;
			text-align: center;
			overflow: hidden;
			white-space: nowrap;
			text-overflow: ellipsis;
		}
		
	}
</style>
