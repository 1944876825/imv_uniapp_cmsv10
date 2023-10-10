<template>
	<view>
		<view class="head">
			
		</view>
		<view class="center">
			<view class="cgn">
				<view class="gnbox" @click="modal('setqq')">
					<text>{{qq ? '您的QQ：' : '设置QQ'}}</text>
					<text class="fr" style="margin-right: 20rpx;">{{qq ? qq : ''}}</text>
				</view>
				<!-- #ifndef H5 -->
					<view class="gnbox">
						<text>当前版本：</text>
						<text class="fr" style="margin-right: 20rpx;">V{{bbh}}</text>
					</view>
				<!-- #endif -->
				<view class="gnbox" @tap="qchc">
					<text>清除缓存</text>
					<text class="fr" style="margin-right: 20rpx;">{{hcsize}} KB</text>
				</view>
				<view class="gnbox" @tap="modal('mzsm')">
					<text>免责声明</text>
				</view>
			</view>
		</view>
		<view class="cu-modal bottom-modal" :class="modalName=='mzsm'?'show':''" @tap="hideModal">
			<view class="cu-dialog" style="border-top-left-radius: 20rpx;border-top-right-radius: 20rpx;">
				<view class="mylink textzts padding-xl">
					本站所有内容均来自互联网分享站点所提供的公开引用资源，未提供资源上传、存储服务。如有侵权，请发邮件至jwml1229@52ig.cn联系我们，我们会在3个工作日内删除下架处理。
				</view>
			</view>
		</view>
		<view class="cu-modal" :class="modalName=='setqq'?'show':''">
			<view class="cu-dialog">
				<view class="cu-bar bg-white justify-end">
					<view class="content">设置QQ</view>
					<view class="action" @tap="hideModal">
						<text class="cuIcon-close text-red"></text>
					</view>
				</view>
				<view class="padding-xl">
					<input class="setqqinput" type="text" placeholder="QQ号" v-model="qq" />
				</view>
				<view class="cu-bar bg-white justify-end">
					<view class="action">
						<view class="setqqbtn margin-left" @tap="setqq">确定</view>
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
				bbh:'',
				hcsize:0,
				modalName:null,
				qq:''
			};
		},
		onLoad() {
			this.bbh = getApp().globalData.bbh;
			this.gethc()
			this.gethcqq()
		},
		methods:{
			gethcqq(){
				uni.getStorage({
					key:'qq',
					success:res=>{
						this.qq = res.data.qqh;
					}
				})
			},
			gethc(){//获取缓存大小
				try {
					this.hcsize = 0;
				    const ress = uni.getStorageInfoSync();
					this.hcsize = ress.currentSize;
				} catch (e) {
				    uni.showToast({
				    	icon:'error',
						title:'计算缓存大小失败，请重试'
				    })
				}
			},
			qchc() {
				uni.showModal({
					title:'你真的要删除所有缓存吗？',
					content:'缓存大小：' + this.hcsize + 'KB,如果删除缓存，观看历史也会被删除',
					confirmText:'删',
					success:res=> {
						if (res.confirm) {
						    uni.clearStorage();
							this.hcsize = 0;
							this.qq = '';
							getApp().globalData.qq = '';
							uni.showToast({
								title:'好了，全都删了'
							});
						}
					}
				})
			},
			modal(type){
				this.modalName = type;
			},
			hideModal(){
				this.modalName = null;
			},
			setqq(){
				if (this.qq) {
					uni.request({
						url:'https://api.muxiaoguo.cn/api/QqInfo?api_key=fb4500a34a0504a1&qq=' + this.qq,
						dataType:'json',
						success:res=>{
							this.modalName = null;
							if (res.data.code == 200) {
								uni.setStorage({
									key:'qq',
									data:{
										qqh:this.qq,
										tx: res.data.data.imgurl,
										name: res.data.data.name
									}
								})
								uni.showToast({
									icon:'none',
									title:'设置成功'
								})
							} else {
								uni.showToast({
									icon:'none',
									title:'获取QQ信息失败'
								})
							}
						},
						fail:err=>{
							this.modalName = null;
							uni.showToast({
								icon:'none',
								title:'获取QQ信息失败'
							})
						}
					})
					
					
				}
			}
		}
	}
</script>

<style lang="scss" scoped>
	.center {
		.cgn {
			width: 90%;
			margin: 0 auto;
			padding-top: 30rpx;
			
			.gnbox {
				height: 100rpx;
				margin: 10rpx 0;
				border-bottom: 1px solid $zts;
				text {
					margin-left: 50rpx;
					font-size: 14px;
					line-height: 100rpx;
				}
			}
			.gnbox:nth-child(1){
				border-top: 1px solid $zts;
			}
		}
	}
	.setqqinput {
		color: $zts;
		border-bottom: 1px solid $zts;
		
	}
	.setqqbtn {
		width: 80rpx;
		height: 50rpx;
		border-radius: 10rpx;
		background-color: $zts;
		font-size: 12px;
		text-align: center;
		line-height: 50rpx;
		color: #fff;
	}
</style>
