<template>
	<view class="content">
		<view class="contop" :style="{height: contopheight + 'rpx'}">
			<view class="head">
				<view v-if="ifshowsx == false">
					<scroll-view v-if="iflx && tabClass.lxClass.length > 0" class="tab" :show-scrollbar="false" :scroll-x="true" :scroll-into-view="tabScrollInto.lxClass">
						<view class="tabContent">
							<view class="tabContent2">
								<view class="tabItem" v-for="(tab,tabItemIndex) in tabClass.lxClass" :key="'tab' + tabItemIndex" :id="'tab' + tabItemIndex" :data-id="tabItemIndex"
									:data-current="tabItemIndex" @click="pressScrollViewItem(0,tabItemIndex)">
									<text class="tabItemClass"
										:class="tabIndex.lxClass==tabItemIndex ? 'tabItemClassActive' : ''"
										>{{tab}}</text>
								</view>
							</view>
						</view>
					</scroll-view>
					
					<scroll-view v-if="tabClass.areaClass.length > 0" class="tab" :show-scrollbar="false" :scroll-x="true" :scroll-into-view="tabScrollInto.areaClass">
						<view class="tabContent">
							<view class="tabContent2">
								<view class="tabItem" v-for="(tab,tabItemIndex) in tabClass.areaClass" :key="'tab' + tabItemIndex" :id="'tab' + tabItemIndex" :data-id="tabItemIndex"
									:data-current="tabItemIndex" @click="pressScrollViewItem(1,tabItemIndex)">
									<text class="tabItemClass"
										:class="tabIndex.areaClass==tabItemIndex ? 'tabItemClassActive' : ''"
										>{{tab}}</text>
								</view>
							</view>
						</view>
					</scroll-view>
					
					<scroll-view v-if="tabClass.yearClass.length > 0" class="tab" :show-scrollbar="false" :scroll-x="true" :scroll-into-view="tabScrollInto.yearClass">
						<view class="tabContent">
							<view class="tabContent2">
								<view class="tabItem" v-for="(tab,tabItemIndex) in tabClass.yearClass" :key="'tab' + tabItemIndex" :id="'tab' + tabItemIndex" :data-id="tabItemIndex"
									:data-current="tabItemIndex" @click="pressScrollViewItem(2,tabItemIndex)">
									<text class="tabItemClass"
										:class="tabIndex.yearClass==tabItemIndex ? 'tabItemClassActive' : ''"
										>{{tab}}</text>
								</view>
							</view>
						</view>
					</scroll-view>
					
				</view>
				<view v-else style="text-align: center;color: #512fbb;height: 40rpx;line-height:40rpx;margin-top: 10rpx;" @click="showsx">
					筛选
				</view>
			</view>
		</view>
		
		<view class="center">
			<mvbox v-for="item in mvlist" :mvdetail="item"></mvbox>
		</view>
		<view style="text-align: center;font-size: 12px;height: 30px;line-height: 30px;">
			正在拼命加载...
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				contopheight: 70,
				ifshowsx: false,
				iflx: true,
				tabIndex:{
					areaClass:0,
					yearClass:0,
					lxClass:0
				},
				tabScrollInto:{
					areaClass:'',
					yearClass:'',
					lxClass:''
				},
				tabClass:{
					areaClass:[],
					yearClass:[],
					lxClass:[]
				},
				mvlist:[],
				canshu:{
					t: 1,
					pg: 0
				}
			};
		},
		onLoad(option) {
			if (this.canshu.bfq) {
				this.canshu.vod_play_from = option.bfq;
			}
			if (this.canshu.t) {
				this.canshu.t = option.tid;
			}
			this.getmvlist()
			
			if (option.xlx) {
				this.iflx = false;
				this.canshu.lxClass = option.xlx;
				uni.setNavigationBarTitle({
					title:option.xlx
				})
			} else {
				uni.setNavigationBarTitle({
					title:option.tname
				})
				if (option.lx) {
					this.contopheight += 40;
					this.tabClass.lxClass = this.deal(option.lx)
				}
			}
			if (option.area) {
				this.contopheight += 40;
				this.tabClass.areaClass = this.deal(option.area)
			}
			if (option.year) {
				this.contopheight += 40;
				this.tabClass.yearClass = this.deal(option.year)
			}
		},
		onReachBottom() {
			this.getmvlist()
		},
		methods:{
			pressScrollViewItem(type,index){
				this.canshu.pg = 0;
				this.mvlist = [];
				if (type == 0 ){// 类型
					if (index == 0) {
						this.canshu.class = '';
					} else {
						this.canshu.class = this.tabClass.lxClass[index];
					}
					this.tabScrollInto.lxClass = 'tab' + index
					this.tabIndex.lxClass = index
				} else if (type == 1) {// 地区
					if (index == 0) {
						this.canshu.area = '';
					} else {
						this.canshu.area = this.tabClass.areaClass[index];
					}
					this.tabScrollInto.areaClass = 'tab' + index
					this.tabIndex.areaClass = index
				} else if (type == 2) {// 年代
					if (index == 0) {
						this.canshu.year = '';
					} else {
						this.canshu.year = this.tabClass.yearClass[index];
					}
					this.tabScrollInto.yearClass = 'tab' + index
					this.tabIndex.yearClass = index
				}
				
				this.getmvlist()
				
			},
			deal(stext){
				var arr = []
				arr = stext.split(',');
				arr.unshift('全部')
				return arr;
			},
			showsx(){
				this.ifshowsx = false;
			},
			getmvlist(){
				this.canshu.pg += 1;
				uni.request({
					url:getApp().globalData.cms + 'api.php/iimv/getlist',
					method:'GET',
					data:this.canshu,
					dataType:'json',
					success:res=>{
						if (res.data.code == 200) {
							this.mvlist = [...this.mvlist,...res.data.data];
						} else {
							uni.showToast({
								icon:'none',
								title:'加载失败'
							})
						}
					}
				})
			},
			scroll(e){
				console.log(1)
				if (e.detail.scrollTop > 200) {
					if (this.ifshowsx == false) this.ifshowsx = true;
				} else {
					if (this.ifshowsx == true) this.ifshowsx = false;
				}	
				
			}
		}
	}
</script>

<style lang="scss" scoped>
	.head {
		/* flex: 1; */
		display: flex;
		flex-direction: column;
		overflow: hidden;
		position: fixed;
		/* #ifdef H5 */
		top: 80rpx;
		/* #endif */
		/* #ifndef H5 */
		top: 0;
		/* #endif */
		
		background-color: #fff;
		z-index: 1;
	}
	.tab {
		margin-top: 10rpx;
		margin-left: 10rpx;
		// /* #ifdef APP-PLUS */
		// width: 100vw;
		// /* #endif */
		height: 55rpx;
		display: flex;
		flex-direction: row;
		/* #ifndef APP-PLUS */
		white-space: nowrap;
		/* #endif */
	}
	
	.tabContent {
		display: flex;
		flex-direction: column;
	}
	.tabContent2 {
		display: flex;
		flex-direction: row;
	}
	
	.tabItem {
		display: flex;
	}
	
	.tabItemClass {
		color: #333333;
		font-size: 24rpx;
		width: 72rpx;
		height: 40rpx;
		line-height: 40rpx;
		display: flex;
		flex-wrap: nowrap;
		align-items: center;
		justify-content: center;
		border: #fff 2rpx solid;
	}
	
	.tabItemClassActive {
		border: #512fbb 2rpx solid;
		border-radius: 25rpx;
		color: #512fbb;
	}
</style>
