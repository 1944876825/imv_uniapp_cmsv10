<template>
	<view>
		<view class="head">
			
		</view>
		<view class="center">
			<view class="ctop">
				<view class="cbox" :class="lxindex==index ? 'actcbox' : ''" v-for="(item,index) in list" @click="liulx(index)">
					{{item}}
				</view>
			</view>
			<view class="area">
				<textarea v-model="liuy" placeholder="内容" />
			</view>
			<view class="btn">
				<button @click="send">发送</button>
			</view>
		</view>
		<view class="bottom" style="margin-top: 20rpx;">
			<view class="search">
				<view class="cu-bar search bg-white">
					<view class="search-form round">
						<text class="cuIcon-search"></text>
						<input v-model="souName" focus :adjust-position="false" type="text" placeholder="搜索留言..." confirm-type="search" @confirm="sou"></input>
					</view>
					<view class="action">
						<button class="cu-btn shadow-blur round text-white" style="background-color: #512fbb;" @click="sou">搜索</button>
					</view>
				</view>
			</view>
			<view class="btop text-center padding">
				留言板没有就是已经修复了
			</view>
			<view class="bcen">
				<view class="bbox" v-for="item in lylist">
					<view class="bid">
						问题id:{{item.id}}
					</view>
					<view class="bcon">
						{{item.content}}
					</view>
					<view class="bcon">
						时间：{{item.stime}}
					</view>
					<view class="bhf">
						回复：<text v-if="item.status == 0">已修复-</text> {{item.hf ? item.hf : '暂未回复'}}
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
				list:['求片','报错','建议'],
				lxindex:0,
				liuy:'',
				lylist:[],
				souName:''
			}
		},
		onLoad() {
			this.getly()
		},
		onPullDownRefresh() {
			this.liuy = '';
			this.lxindex = 0;
			this.souName = '';
			this.getly()
			setTimeout(function() {
				uni.stopPullDownRefresh();
			},500);
		},
		methods: {
			sou () {
				this.lylist = [];
				if (this.souName) {
					uni.request({
						url:getApp().globalData.cms + 'api.php/iimv/souly',
						data:{
							word:this.souName
						},
						dataType:'json',
						success:res=>{
							if (res.data.code == 200) {
								this.lylist = res.data.list;
							}
							if (res.data.code == 201) {
								uni.showToast({
									icon:'none',
									title:'搜不到呀！'
								})
							}
						}
					})
				} else {
					uni.showToast({
						icon:'none',
						title:'你忘了输入留言关键词啦！'
					})
				}
				
			},
			getly() {
				this.lylist = [];
				uni.request({
					url:getApp().globalData.cms + 'api.php/iimv/liui',
					dataType:'json',
					success:res=>{
						if (res.data != '[]') this.lylist = res.data;
					}
				})
			},
			liulx(index){
				this.lxindex = index;
			},
			send(){
				var data = this.list[this.lxindex] + '：' + this.liuy;
				uni.request({
					url:getApp().globalData.cms + 'api.php/iimv/liuy/content/' + data,
					dataType:'text',
					success:res=>{
						if (res.data == 1) {
							uni.showToast({
								icon:'none',
								title:'发送成功！感谢留言'
							})
							this.liuy = '';
						} else {
							uni.showToast({
								icon:'none',
								title:'发送失败！'
							})
						}
						this.modalName = null;
					}
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	
	.center {
		padding-bottom: 30rpx;
		border-bottom: 1px solid $zts;
		.ctop {
			padding-left: 50rpx;
			margin-top: 30rpx;
			.cbox {
				display: inline-block;
				width: 20%;
				height: 60rpx;
				border: 1px solid $zts;
				margin: 10rpx 5rpx;
				line-height: 60rpx;
				text-align: center;
				font-size: 14px;
				border-radius: 30rpx;
				color: $zts;
			}
			.actcbox {
				background-color: $zts;
				color: #fff;
			}
		}
		.area {
			textarea {
				width: 90%;
				height: 360rpx;
				margin: 20rpx auto;
				border: 1px solid $zts;
				border-radius: 20rpx;
				padding: 20rpx;
				font-size: 12px;
				color: $zts;
			}
		}
		.btn {
			button {
				width: 200rpx;
				height: 80rpx;
				border-radius: 40rpx;
				line-height: 80rpx;
				font-size: 12px;
				color: #fff;
				background-color: $zts;
			}
		}
	}
	.bottom {
		.btop {
			color: $zts;
			font-size: 14px;
		}
		.bcen {
			width: 90%;
			margin: 0 auto;
			.bbox {
				border: 1px solid $zts;
				border-radius: 20rpx;
				padding: 10rpx;
				color: $zts;
				margin-top: 10rpx;
				font-size: 12px;
			}
		}
	}
	
</style>
