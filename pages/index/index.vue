<template>
	<view class="content">
		<view class="head" v-show="ifhomeshow">
			<view class="header" style="height: 30px;">
				
			</view>
			<view class="sou">
				<view class="cu-bar search bg-white">
					<view class="search-form round">
						<text class="cuIcon-search"></text>
						<input @tap="Inputtap" disabled type="text" placeholder="搜索电影、电视剧、动漫..." confirm-type="search"></input>
					</view>
					<view class="action">
						<view class="action" v-if="tabIndex != 0">
							<button class="cu-btn shadow-blur round text-white" style="background-color: #512fbb;" @click="shaixuan">筛选</button>
						</view>
					</view>
				</view>
			</view>
		</view>
		<view class="center" v-show="ifhomeshow">
			<scroll-view class="tab1" id="tab" :show-scrollbar="false" :scroll-x="true" :scroll-into-view="tabScrollInto">
				<view class="tabContent" style="display: flex; flex-direction: column;">
					<view style="display: flex;flex-direction: row;">
						<view class="tabItem" v-for="(tab,tabItemIndex) in myclass" :key="'tabb' + tabItemIndex" :id="'tabb' + tabItemIndex" :data-id="tabItemIndex"
							:data-current="tabItemIndex" @click="pressScrollViewItem(tabItemIndex)">
							<text class="tabItemTitle"
								:class="tabIndex==tabItemIndex ? 'tabItemTitleActive' : ''"
								style="width: 60px"
								>{{tab.type_name}}</text>
						</view>
					</view>
					<view class="tabLineView">
						<view class="tabLine tabLineActive"
							:style="{left: tabItemLineLeft + 'px', width: tabItemLineWidth + 'px'}"></view>
					</view>
				</view>
			</scroll-view>
			
			<swiper class="childPageView" :current="tabIndex" :duration="100" @change="swiperChange" @transition="transition"
				@animationfinish="swiperChangeEnd" @onAnimationEnd="swiperChangeEnd" :style="{height: swiperHeight +'px'}">
				<swiper-item class="childPageViewItem" v-for="(page,childPageViewItemIndex) in myclass" :key="childPageViewItemIndex">
					
					<scroll-view scroll-y="true" :style="{height: swiperHeight +'px'}" lower-threshold="300">
						<view v-if="childPageViewItemIndex == 0">
							<swiper class="square-dot" :indicator-dots="true" :circular="true"
							 :autoplay="true" interval="5000" duration="500" style="width:96%;margin: 0 auto;height: 150px;border-radius: 8px;box-shadow: 0 2px 1px #512fbb;">
								<swiper-item v-for="item in swips" @click="swiptz(item.vod_id)">
									<image :src="item.vod_pic_slide" mode="" style="border-radius: 8px;height: 150px;width: 100%;"></image>
								</swiper-item>
							</swiper>
							<view v-if="ggnr" class="notice padding">
								<view>
									{{ggnr}}
								</view>
							</view>
							<view class="inxbox">
								<view v-for="item in catlist" class="inxxbox">
									<view style="display: flex;">
										<view class="flex padding justify-between" style="width: 100%;">
											<view style="font-size: 16px;font-weight: 700;">
												<image v-if="item.logo" :src="item.logo" mode="aspectFit" style="vertical-align: middle;width: 36px;height: 36px;margin-right: 10px;border-radius: 50%;box-shadow: 1px 1px 2px #512fbb;"></image>
												{{item.name}}
											</view>
											<view style="color: #9e9e9e;font-size: 14px;margin-top: 8px;" @click="More(item.bfq, item.name)">More</view>
										</view>
									</view>
									<view class="inbom">
										<view class="inbox">
											<mvbox v-for="item2 in item.list" :mvdetail="item2"></mvbox>
										</view>
									</view>
								</view>
							</view>
						</view>

						<view v-else-if="page.classes" >
							<view v-for="item in page.classes" v-if="item.vods.length > 0" class="inxxbox">
								<view style="display: flex;">
									<view class="flex padding justify-between" style="width: 100%;">
										<view style="font-size: 16px;font-weight: 700;">{{item.name}}</view>
										<view style="color: #bfbfbf;margin-top: 8px;font-size: 14px;" @click="ckgd(item.name)">More</view>
									</view>
								</view>
								<view class="inbom">
									<view class="inbox">
										<mvbox v-for="item2 in item.vods" :mvdetail="item2"></mvbox>
									</view>
								</view>
							</view>
						</view>
						<view v-else class="text-center">
							正在加载...
						</view>
					</scroll-view>
				</swiper-item>
			</swiper>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				ifhomeshow: false,
				tabIndex:0,
				canshu:{//请求参数
					t:1
				},
				tabListSize: {},
				myclass:[
					{
						type_name: '推荐'
					}
				],
				tabScrollInto:'',
				tabItemLineLeft: 0,
				tabItemLineWidth: 0,
				windowHeight:0,
				windowWidth:0,
				catlist:[],
				swips:[],
				swiperHeight: uni.getSystemInfoSync().windowHeight - 120,
				ggnr: ''
			}
		},
		onLoad() {
			uni.showLoading({
				title:'正在拼命加载',
				mask:true
			})
			this.getVodPhbAll()
			let system = uni.getSystemInfoSync();
			this.windowHeight = system.windowHeight;
			this.windowWidth = system.windowWidth;
			this.gethistory()
		},
		onReady() {
			this.gxjc();
		},
		methods: {
			shaixuan(){
				var tempobj = this.myclass[this.tabIndex].type_extend;
				uni.navigateTo({
					url:'../class/class?tid=' + this.myclass[this.tabIndex].type_id + '&tname=' + this.myclass[this.tabIndex].type_name + '&lx=' + tempobj.class + '&area=' + tempobj.area + '&year=' + tempobj.year
				})
			},
			swiptz(id){//点击swiper事件
				uni.navigateTo({
					url:'../play/play?vid=' + id,
					animationType: "slide-in-right",
					animationDuration: 300
				})
			},
			getVodPhbAll(){
				uni.request({
					url:getApp().globalData.cms + 'api.php/iimv/VodPhbAll',
					dataType:'json',
					success:res=>{
						if (res.data.code == 200) {
							var info = res.data.data
							this.catlist = info['hot']
							this.swips = info['swiper']
							this.myclass = [...this.myclass,...res.data.data.typelist]
						}
						this.ifhomeshow = true
						this.jcsetTabItemDistance()
						uni.hideLoading()
					}
				})
			},
			More(bfq, name) {
				var tempobj = this.myclass[1].type_extend
				uni.navigateTo({
					url:'../class/class?bfq=' + bfq + '&tname=' + name + '&area=' + tempobj.area + '&year=' + tempobj.year
				})
			},
			ckgd(type) {//查看更多
				var tempobj = this.myclass[this.tabIndex].type_extend
				uni.navigateTo({
					url:'../class/class?tid=' + this.myclass[this.tabIndex].type_id + '&tname=' + type + '&xlx=' + type + '&lx=' + tempobj.class + '&area=' + tempobj.area + '&year=' + tempobj.year
				})
			},
			gethistory(){//获取历史播放
				uni.getStorage({
					key:'history',
					success:res=>{
						getApp().globalData.history = res.data;
					}
				})
			},
			gxjc() {//更新检测
				var obj = {};
				var iftc = true;
				uni.request({
					url: getApp().globalData.cms + 'api.php/iimv/getapp',
					dataType: "json",
					success:res=> {
						obj = res.data
						getApp().globalData.user = res.data.user
						getApp().globalData.webbfq = res.data.webbfq
						
						// #ifdef APP
							plus.runtime.getProperty(plus.runtime.appid,(wgtinfo)=>{
								getApp().globalData.bbh = wgtinfo.version
							})
							getApp().globalData.showweb = res.data.showweb
							if (obj.ifgx == true) {
								if (obj.zxbb != getApp().globalData.bbh) {
									var url = obj.url;
									iftc = false;
									uni.showModal({
										title:'是否更新到最新版v' + obj.zxbb,
										content:'更新内容：' + obj.gxnr,
										confirmText:'下载',
										success: function (res) {
											if (res.confirm) {
												plus.runtime.openURL(url);
											}
										}
									})
								}
							}
						// #endif
						this.ggnr = obj.gg_nr
						if (obj.iftc == true && iftc == true) {
							uni.showModal({
								title: obj.gg_title,
								content: obj.gg_nr
							})
						}
					}
				});
			},
			transition(e){
				var dx = e.detail.dx;
				if (dx > 0 && this.tabIndex != this.myclass.length - 1) {
					var sha = this.tabListSize[this.tabIndex + 1].left - this.tabListSize[this.tabIndex].left;
					var bili = e.detail.dx / this.windowWidth;
					var left = sha * bili + this.tabListSize[this.tabIndex].left;
					this.setTabItemLinePosition(left,this.tabListSize[this.tabIndex].width);
				} else if (dx < 0 && this.tabIndex != 0) {
					var sha = this.tabListSize[this.tabIndex].left - this.tabListSize[this.tabIndex - 1].left;
					var bili = e.detail.dx / this.windowWidth;
					var left = sha * bili + this.tabListSize[this.tabIndex].left;
					this.setTabItemLinePosition(left,this.tabListSize[this.tabIndex].width);
				}
			},
			Inputtap() {//搜索框获得焦点
				uni.navigateTo({
					url:'../search/search',
					animationType: "slide-in-right",
					animationDuration: 300
				})
			},
			pressScrollViewItem(index) {
				this.setTabSelect(index);
			},
			swiperChange(e) {
				let index = e.target.current || e.detail.current;
				this.setTabSelect(index);
			},
			jcsetTabItemDistance() {
				var queryTabSize = uni.createSelectorQuery().in(this);
				var timer = setInterval(res=>{
					queryTabSize.select('#tabb' + (this.myclass.length-1)).boundingClientRect(data => {
						if (data) {
							clearInterval(timer);
							this.setTabItemDistance()
						}
					}).exec()
				},500)
			},
			setTabItemDistance() {
				var queryTabSize = uni.createSelectorQuery().in(this);
				for (var i = 0; i < this.myclass.length; i++) {
					queryTabSize.select('#tabb' + [i]).boundingClientRect()
				}
				
				queryTabSize.exec(rects => {
					rects.forEach((rect) => {
						this.tabListSize[rect.dataset.id] = rect;
					})
					this.setTabItemLinePosition(this.tabListSize[this.tabIndex].left, this.tabListSize[this.tabIndex].width);
				});
			},
			setTabItemLinePosition(left, width) {
				this.tabItemLineLeft = left;
				this.tabItemLineWidth = width;
			},
			swiperChangeEnd(e) {
				this.setTabItemLinePosition(this.tabListSize[this.tabIndex].left, this.tabListSize[this.tabIndex].width);
			},
			setTabSelect(index) {
				this.tabScrollInto = 'tabb' + index;
				this.tabIndex = index;
			}
		}
	}
</script>

<style scoped lang="scss">
	.head {
		height: 140rpx;
	}
	.center {
		/* flex: 1; */
		display: flex;
		flex-direction: column;
		overflow: hidden;
		.inxxbox {
			width: 96%;
			margin: 20rpx auto;
			border-radius: 20rpx;
			border: 1px solid $zts;
			box-shadow: 0 2px 1px $zts;
		}
		.inbom {
			margin-top: 16rpx;
		}
		.notice {
			width: 96%;
			margin: 20rpx auto;
			border-radius: 16rpx;
			box-shadow: 0 4rpx 4rpx $zts;
			border: 2rpx solid $zts;
			font-size: 28rpx;
		}
	}
	.tab1 {
		/* #ifdef APP-PLUS */
		width: 100vw;
		/* #endif */
		height: 44px;
		display: flex;
		flex-direction: row;
		/* #ifndef APP-PLUS */
		white-space: nowrap;
		/* #endif */
	}
	.tab {
		margin-top: 10rpx;
		margin-left: 10rpx;
		/* #ifdef APP-PLUS */
		width: 100vw;
		/* #endif */
		height: 55rpx;
		display: flex;
		flex-direction: row;
		/* #ifndef APP-PLUS */
		white-space: nowrap;
		/* #endif */
	}

	/* 隐藏滚动条 */

	.tab ::-webkit-scrollbar {
		display: none;
		width: 0 !important;
		height: 0 !important;
		-webkit-appearance: none;
		background: transparent;
	}

	.tabLineView {
		position: relative;
		height: 2px;
		background-color: transparent;
	}

	.tabLine {
		position: absolute;
		top: 0;
		bottom: 0;
		width: 0;
		background-color: #512fbb;
	}

	.tabLineActive {
		transition-duration: 0.1s;
		transition-property: left;
	}

	.childPageView {
		display: flex;
		flex-direction: column;
	}

	.tabItem {
		/* #ifndef APP-PLUS */
		/* display: inline-block; */
		/* #endif */
		display: flex;
		
		
		/* flex-wrap: nowrap; */
		
		/* 
		padding-left: 20px;
		padding-right: 20px; */
		
	}

	.tabItemTitle {
		color: #333333;
		font-size: 16px;
		height: 42px;
		line-height: 40px;
		display: flex;
		flex-wrap: nowrap;
		align-items: center;
		justify-content: center;
	}

	.tabItemTitleActive {
		color: #512fbb;
	}
	
	.tabItemClass {
		color: #333333;
		font-size: 12px;
		height: 50rpx;
		line-height: 50rpx;
		display: flex;
		flex-wrap: nowrap;
		align-items: center;
		justify-content: center;
		border: #fff 1px solid;
	}
	
	.tabItemClassActive {
		border: #512fbb 1px solid;
		border-radius: 25rpx;
		color: #512fbb;
	}

	.childPageViewItem {
		flex: 1;
		flex-direction: column;
		margin-top: 20rpx;
	}
</style>
