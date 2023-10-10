<template>
	<view>
		<view class="head" style="height: 150rpx;">
			<view style="position: fixed;top: 0;width: 100%;z-index: 1;background-color: #fff;">
				<view style="height: 60rpx;">
					
				</view>
				<view class="sou">
					<view class="cu-bar search bg-white">
						<view class="search-form round">
							<text class="cuIcon-search"></text>
							<input type="text" placeholder="搜索电影、电视剧、动漫..." focus v-model="souName"  adjust-position="false" confirm-type="search" @confirm="mysou"></input>
						</view>
						<view class="action">
							<button class="cu-btn shadow-blur round text-white" style="background-color: #512fbb;" @click="mysou">搜索</button>
						</view>
					</view>
				</view>
			</view>
			
		</view>
		<view class="center">
			<block v-if="mvlist.length == 0">
				<view style="text-align: center;width: 100%;color: #512fbb;margin-top: 80rpx;font-size: 20px;">
					{{xx}}
				</view>
			</block>
			<block v-else>
				<view style="margin-top: 30rpx;">
					<mvbox v-for="item in mvlist" :mvdetail="item"></mvbox>
				</view>
			</block>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				souName:'',
				mvlist:[],
				xx:'Hi!等你好久了'
			};
		},
		onPullDownRefresh() {
			this.souName = '';
			this.mvlist = [];
			this.xx = 'Hi!等你好久了';
			setTimeout(function() {
				uni.stopPullDownRefresh();
			},500);
		},
		methods:{
			mysou () {
				this.mvlist = [];
				if (this.souName) {
					uni.showLoading({
						title:'为了你，我拼命搜索'
					})
					uni.request({
						url:getApp().globalData.cms + '/api.php/iimv/getlist/wd/' + this.souName,
						dataType:'json',
						success:res=>{
							uni.hideLoading()
							if (res.data.data.length == 0) {
								this.xx = '没搜到，我会继续努力的，WuWu~~~';
								uni.showModal({
									title:'抱歉',
									content:'没搜到，我会继续努力的，WuWu~~~',
									cancelText:'没关系',
									confirmText:'求片',
									success:r=>{
										if (r.confirm) {
											var data = '求片：' + this.souName;
										    uni.request({
										    	url:getApp().globalData.cms + 'api.php/iimv/liuy/content/' + data,
										    	dataType:'text',
										    	success:res=>{
										    		if (res.data == 1) {
										    			uni.showToast({
										    				icon:'none',
										    				title:'已收到，感谢反馈！马上安排！'
										    			})
										    		} else {
										    			uni.showToast({
										    				icon:'none',
										    				title:'求片失败！'
										    			})
										    		}
										    	}
										    })
								        }
									}
								})
							} else {
								this.mvlist = res.data.data;
								
							}
						}
					})
				} else {
					this.xx = '你好像忘记输入电影名字啦！'
				}
				
			}
		}
	}
</script>

<style lang="scss" scoped>

</style>
