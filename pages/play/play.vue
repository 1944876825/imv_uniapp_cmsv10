<template>
	<view class="content">
		<view class="head">
			<view class="header">
				<view class="video">
					<video id="myVideo" :src="url" :play-strategy="playstrategy" style="width: 100%;margin-top: 10px;" :header="videoHeader" controls autoplay :initial-time="statime" @play="ifplay(true)" @pause="ifplay(false)" @timeupdate="timeupdate" @ended="videoend"></video>
				</view>
			</view>
		</view>
		
		<view class="center innbox" v-show="ifhomeshow">
			<!-- 简介 -->
			<view class="jianjie" @tap="modal('jianjie')">
				<view class="jjtop flex justify-between padding" style="padding-bottom: 15rpx;">
					<text class="mvname" style="font-size: 16px;">{{mvdetail.vod_name}}</text>
					<text style="font-size: 12px;">简介<text class="cuIcon-right"></text></text>
				</view>
				<view class="jjlx" style="font-size: 12px;margin-left: 30rpx;color:#9c9c9c;">
					<text>{{mvdetail.vod_area ? mvdetail.vod_area : ''}}</text>
					<text style="margin-left: 10rpx;">{{mvdetail.vod_year ? mvdetail.vod_year : ''}}</text>
				</view>
			</view>
			
			<!-- 功能 -->
			<view class="gn" style="margin-top: 30rpx;">
				<view class="gnbox" v-for="(gn,index) in gns" @tap="mygn(gn.id)">
					<view>
						<image class="gnimg" :src="gn.img" mode=""></image>
					</view>
					<view class="gntext">
						{{gn.name}}
					</view>
				</view>
			</view>
			
			<block v-if="xll.length > 0">
				<!-- 播放源 -->
				<view class="kz" style="height: 120rpx;margin-top: 20rpx;">
					<view class="kztop">
						<text>播放源</text>
					</view>
					<view class="kzcen" v-if="xll.length > 0">
						<scroll-view class="tab" id="tab" :show-scrollbar="false" :scroll-x="true" style="width: 96%;">
							<view class="tabContent" style="display: flex; flex-direction: column;">
								<view style="display: flex;flex-direction: row;">
									<view class="tabItem" v-for="(tab,tabItemIndex) in xll" :data-current="tabItemIndex">
										<view class="tabItemClass" @tap="qhbfy(tabItemIndex)"
											:class="tabIndex.bfy==tabItemIndex ? 'tabItemClassActive' : ''"
											style="display: inline-block;width: 120rpx;height: 60rpx;line-height: 60rpx;text-align: center;border-radius: 20rpx;font-size: 12px;"
											>
											{{tab.player_info.show}}
										</view>
									</view>
								</view>
							</view>
						</scroll-view>
					</view>
					
				</view>
				<!-- 接口 -->
				<view class="kz" style="height: 120rpx;" v-if="jkss > 0">
					<view class="kztop">
						<text>线路</text>
					</view>
					<view class="kzcen">
						<scroll-view class="tab" id="tab" :show-scrollbar="false" :scroll-x="true" style="width: 96%;">
							<view class="tabContent" style="display: flex; flex-direction: column;">
								<view style="display: flex;flex-direction: row;">
									<view class="tabItem" v-for="(tab,tabItemIndex) in jkss" :data-current="tabItemIndex">
										<view class="tabItemClass" @tap="qhxl(tabItemIndex)"
											:class="tabIndex.xl==tabItemIndex ? 'tabItemClassActive' : ''"
											style="display: inline-block;width: 120rpx;height: 60rpx;line-height: 60rpx;text-align: center;border-radius: 20rpx;font-size: 12px;"
											>
											线路{{tabItemIndex + 1}}
										</view>
									</view>
								</view>
							</view>
						</scroll-view>
					</view>
				</view>
				
				<!-- 选集 -->
				<view class="js">
					<view class="jstop flex justify-between padding" style="padding-top: 10rpx;" @tap="modal('alljs')">
						<text>选集</text>
						<text style="margin-left: 15rpx;" v-if="xll[tabIndex.bfy].urls.length > 5">更新至{{xll[tabIndex.bfy].urls.length}}集<text class="cuIcon-right"></text></text>
					</view>
					<view class="jscen" v-if="xll[tabIndex.bfy] && xll[tabIndex.bfy].urls.length > 0">
						<scroll-view class="tab" id="tab" :show-scrollbar="false" :scroll-x="true" :scroll-into-view="tabScrollInto" style="width: 96%;">
							<view class="tabContent" style="display: flex; flex-direction: column;">
								<view style="display: flex;flex-direction: row;">
									<view class="tabItem" v-for="(tab,tabItemIndex) in xll[tabIndex.bfy].urls" :key="'tab' + tabItemIndex" :id="'tab' + tabItemIndex" :data-current="tabItemIndex" @tap="play(tabItemIndex)">
										<view class="tabItemClass"
											:class="tabIndex.js==tabItemIndex ? 'tabItemClassActive' : ''"
											style="display: inline-block;min-width: 50px;padding:8px 20rpx;text-align: center;border-radius: 20rpx;font-size: 12px;"
											>
											{{tab.name}}
										</view>
									</view>
								</view>
							</view>
						</scroll-view>
					</view>
					<view v-else class="textzts" style="text-align: center;">
						暂无视频源
					</view>
				</view>
			</block>
			
			<block v-else>
				<view class="textzts" style="text-align: center;margin-top: 30px;font-size: 16px;">
					{{zwbfy}}
				</view>
			</block>
			
			<view class="mymodal">
				<!-- 简介 -->
				<view class="cu-modal bottom-modal" :class="modalName=='jianjie'?'show':''" @tap="hideModal" v-if="mvdetail">
					<view class="cu-dialog" style="border-top-left-radius: 20rpx;border-top-right-radius: 20rpx;">
						<view class="cu-bar bg-white">
							<view class="action textzts">简介</view>
							<view class="action text-blue" @tap="hideModal">看完啦！</view>
						</view>
						<view class="jianjiemodal padding-xl" style="text-align: left;">
							<view style="font-size: 20px;text-align: center;">
								{{mvdetail.vod_name}}
							</view>
							<view v-if="mvdetail.vod_director">
								导演：{{mvdetail.vod_director}}
							</view>
							<view v-if="mvdetail.vod_writer">
								编辑：{{mvdetail.vod_writer}}
							</view>
							<view v-if="mvdetail.vod_actor">
								主演：{{mvdetail.vod_actor}}
							</view>
							<view v-if="mvdetail.type_name">
								类型：{{mvdetail.type_name}}
							</view>
							<view v-if="mvdetail.vod_blurb">
								简介：{{mvdetail.vod_blurb}}
							</view>
							<view v-else>
								简介：{{mvdetail.vod_content}}
							</view>
						</view>
					</view>
				</view>
				
				<!-- 链接 -->
				<view class="cu-modal bottom-modal" :class="modalName=='link'?'show':''" @tap="hideModal">
					<view class="cu-dialog" style="border-top-left-radius: 20rpx;border-top-right-radius: 20rpx;">
						<view class="mylink textzts padding-xl">
							<view @tap="mylink">
								复制链接 （=>鲸遇）
							</view>
							<!-- #ifndef H5 -->
							<view @tap="openlink">
								浏览器打开 （=>下载、投屏）
							</view>
							<!-- #endif -->
							<view @tap="hideModal">
								取消
							</view>
						</view>
					</view>
				</view>
				
				
				<!-- 留言 -->
				<view class="cu-modal bottom-modal" :class="modalName=='liuy'?'show':''">
					<view class="cu-dialog" style="border-top-left-radius: 20rpx;border-top-right-radius: 20rpx;">
						<view class="cu-bar bg-white">
							<view class="action textzts">留言</view>
							<view class="action text-blue">
								<view @tap="hideModal">取消</view>
								<view style="margin-left:15rpx;margin-right: 50rpx;" @tap="sentliuy">发送</view>
							</view>
							
						</view>
						<view class="padding-xl">
							<view class="liuytop">
								<text>名字：{{mvdetail.vod_name}}</text>
								<text v-if="jkss.length > 0">线路：{{tabIndex.xl + 1}}</text>
								<block v-if="xll[tabIndex.bfy]">
									<text v-if="xll[tabIndex.bfy.player_info]">播放源：{{xll[tabIndex.bfy].player_info.show}}</text>
									<text v-if="xll[tabIndex.bfy].urls.length > 0">剧集：{{xll[tabIndex.bfy].urls[tabIndex.js].name}}</text>
								</block>
							</view>
							<textarea class="liuybot" v-model="liuy" confirm-type="send" @confirm="sentliuy" placeholder="你可以在这里报错、催更新、求片..." />
						</view>
					</view>
					
				</view>
				
				
				<!-- 全部剧集 -->
				<view class="cu-modal bottom-modal" :class="modalName=='alljs'?'show':''" v-if="xll[tabIndex.bfy]">
					<view class="cu-dialog" style="height: 630rpx;border-top-left-radius: 20rpx;border-top-right-radius: 20rpx;">
						
						<view class="cu-bar bg-white">
							<view class="action textzts">全部剧集</view>
							<view class="action text-blue">
								<view @tap="dxzxf">{{dxzx}}</view>
								<view @tap="hideModal" style="margin: 0 15px;">关闭</view>
							</view>
							
						</view>
						<view class="cu-bar">
							<view style="margin-top: 10rpx;width: 100%;">
								<scroll-view scroll-y="true" style="height: 500rpx;text-align: left;">
									<view class="jsbox" v-for="(js,index) in xll[tabIndex.bfy].urls" @tap="play(index)" :class="tabIndex.js==index ? 'tabItemClassActivejs' : ''">
										<view class="jsbox1">
											{{js.name}}
										</view>
									</view>
								</scroll-view>
							</view>
						</view>
					</view>
				</view>
			</view>
			
			<!-- 倍速 -->
			<view class="cu-modal bottom-modal" :class="modalName=='beisu'?'show':''" @tap="hideModal">
				<view class="cu-dialog" style="border-top-left-radius: 20rpx;border-top-right-radius: 20rpx;">
					<view class="cu-bar bg-white">
						<view class="action textzts">倍速</view>
						<view class="action text-blue" @tap="hideModal">关闭</view>
					</view>
					<view class="padding-xl">
						<view class="cu-list" @tap="playrate(bss)" v-for="bss in bs">
							<text class="text-grey" style="font-size: 16px;">{{bss}}</text>
						</view>
					</view>
				</view>
			</view>
			
		</view>
			
		<view class="bottom">
			<view class="innbox" v-if="mvdetail.rel_vods">
				<view style="display: flex;">
					<view class="flex padding justify-between" style="width: 100%;">
						<view style="font-size: 16px;font-weight: 700;">猜你喜欢</view>
					</view>
				</view>
				<view class="inbom" style="margin-top: 8px;">
					<view class="inbox">
						<mvbox v-for="item in mvdetail.rel_vods" :mvdetail="item"></mvbox>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	import md5 from "../../until/md5.js";
	export default {
		data() {
			return {
				ifhomeshow: false,
				zwbfy:'',
				videoHeader: {},
				vid:'',
				url:'',
				playste:false,
				bfq:[],
				videoUrl:true,
				dqrequest:{},
				// bfys:[],//所有播放源，腾讯...
				xll:[],//所有播放器的播放链接
				modalName:null,
				statime:0,//视频初始播放时间
				liuy:'',//留言内容
				tabIndex:{
					js:0,
					bfy:0,
					xl:0
				},
				playstrategy: 0,
				bs:[0.5,0.8,1.0,1.25,1.5],
				dxzx:'倒序',
				mvdetail:{},
				jkss:[],//json接口
				tabScrollInto:'',
				gns:[
					{
						id:1,
						name:'留言',
						img:'../../static/icon/liuy1.png'
					},
					{
						id:2,
						name:'下一集',
						img:'../../static/icon/next.png'
					},
					{
						id:3,
						name:'倍速',
						img:'../../static/icon/rate.png'
					},
					{
						id:4,
						name:'链接',
						img:'../../static/icon/link.png'
					},
					{
						id:5,
						name:'分享',
						img:'../../static/icon/share.png'
					}
				]
			};
		},
		onLoad(opt) {
			this.vid = opt.vid;
			this.detail();
		},
		onPullDownRefresh() {
			this.detail();
			setTimeout(function() {
				uni.stopPullDownRefresh();
			},500);
		},
		onReady() {
			this.videoContext = uni.createVideoContext('myVideo')
		},
		onShow() {
			if (this.videoContext) {
				this.conplay(true);
			}
		},
		onBackPress() {
			if (this.dqrequest) {
				this.dqrequest.abort()
			}
		},
		onHide() {
			this.conplay(false);
		},
		methods:{
			detail(){
				uni.showLoading({
					title:'正在玩命加载...'
				})
				this.dqrequest = uni.request({
					url: getApp().globalData.cms + 'api.php/iimv/detail/vod_id/' + this.vid,
					dataType:"json",
					success:res=>{
						if (res.data.code == 200) {
							this.mvdetail = res.data.data;
							this.jzxq();//渲染
						} else {
							this.zwbfy = '抱歉，暂无播放源';
							uni.showToast({
								icon:'none',
								title:'加载失败'
							})
						}
					}
				});
			},
			setdhc() {//设置视频线路、播放源、集数
				uni.setStorage({
					key:'mv' + this.vid,
					data:{
						xl:this.tabIndex.xl,
						js:this.tabIndex.js,
						bfy:this.tabIndex.bfy
					}
				})
			},
			settimehc(time){//设置视频播放时间
				uni.setStorage({
					key: md5.hex_md5(this.xll[this.tabIndex.bfy].urls[this.tabIndex.js].url),
					data:time
				})
			},
			gethc(){
				uni.getStorage({
					key:'mv' + this.vid,
					success:res=>{
						this.tabIndex.xl = res.data.xl;
						this.tabIndex.js = res.data.js;
						this.tabIndex.bfy = res.data.bfy;
						
						this.getjk(this.xll[this.tabIndex.bfy]['from']);
						this.gettimehc()
					},
					fail:err=> {
						this.getjk(this.xll[0]['from'])
						this.play()
					}
				})
			},
			getjk(bfq) {
				uni.request({
					url:getApp().globalData.cms + 'api.php/iimv/getjx/bfq/' + bfq,
					dataType:'json',
					success:res=>{
						if (res.data.code == 200) {
							this.jkss = res.data.data;
						} else {
							this.jkss= 0;
						}
					}
				})
			},
			gettimehc(){
				if (this.xll[this.tabIndex.bfy].urls.length > 0) {
					uni.getStorage({
						key: md5.hex_md5(this.xll[this.tabIndex.bfy].urls[this.tabIndex.js].url),
						success:res=>{
							this.statime = res.data;
							this.play()
						},
						fail:err=> {
							this.play()
						}
					})
				}
				
			},
			jzxq () { //加载详情，渲染解析detail里面的数据
				if (this.mvdetail.vod_name) {
					uni.setNavigationBarTitle({
						title:this.mvdetail.vod_name
					});
				}
				this.ifhomeshow = true
				if (this.mvdetail.vod_play_list.length > 0) {
					this.xll = this.mvdetail.vod_play_list;
					this.gethc();
					this.sethistory();
				} else {
					this.zwbfy = '抱歉，暂无播放源';
					uni.showModal({
						title:'抱歉',
						content:'当前视频没有播放源',
						cancelText:'没关系',
						confirmText:'反馈',
						success:r=>{
							if (r.confirm) {
								var data = '无播放源：' + this.mvdetail.vod_name;
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
				}
				uni.hideLoading();
				
			},
			sethistory() {//设置历史播放
				var history = {};
				uni.getStorage({
					key:'history',
					success:res=>{
						var hisarr = res.data;
						for(var i = 0;i < hisarr.length;i++) {
							if (hisarr[i].vid == this.vid) {
								hisarr.splice(i,1);
								break;
							}
						}
						history.vid = this.vid;
						history.img = this.mvdetail.vod_pic;
						history.name = this.mvdetail.vod_name;
						history.js = this.xll[this.tabIndex.bfy].urls.length > 0 ? this.xll[this.tabIndex.bfy].urls[this.tabIndex.js].name : '无';
						hisarr.unshift(history);
						uni.setStorage({
							key:'history',
							data:hisarr
						})
					},
					fail:err=>{
						var hisarr = [];
						history.vid = this.vid;
						history.img = this.mvdetail.vod_pic;
						history.name = this.mvdetail.vod_name;
						history.js = this.xll[this.tabIndex.bfy].urls.length > 0 ? this.xll[this.tabIndex.bfy].urls[this.tabIndex.js].name : '无';
						hisarr.unshift(history);
						uni.setStorage({
							key:'history',
							data:hisarr
						})
					}
				})			
			},
			ifplay(bool) {
				this.playste = bool;
			},
			conplay(bool) {
				if (this.playste) {
					if (bool == false) {//暂停
						this.videoContext.pause()
						this.playste = false;
					}
				} else {
					if (bool == true) {
						this.videoContext.play()
						this.playste = true;
					}
				}
			},
			play (index) { //解析，获取m3u8，观看成功，观看历史将保存至本地缓存
				this.conplay(false)
				uni.showLoading({
					title:'马上就能播放啦...'
				})
				if (index) {
					this.tabIndex.js = index
					this.tabScrollInto = 'tab' + index
					this.statime = 0;
				} else {
					this.tabScrollInto = 'tab' + this.tabIndex.js;
				}
				if (index == 0) this.tabIndex.js = 0;
				if (this.modalName == 'alljs') {
					this.modalName = null;
				}
				this.setdhc()
				var tempurl = ''
				var fankui
				if (this.xll[this.tabIndex.bfy].urls.length > 0) {
					if (this.xll[this.tabIndex.bfy].urls[this.tabIndex.js].url) {
						tempurl = this.xll[this.tabIndex.bfy].urls[this.tabIndex.js].url
						fankui = 0
					} else {
						fankui = 2
					}
				} else {
					fankui = 1
				}
				if (fankui == 0) {
					if (tempurl.search('.mp4') < 0 && tempurl.search('.m3u8') < 0 && tempurl.search('.mkv') < 0 && tempurl.search('.flv') < 0 && tempurl.search('.rtmp') < 0 && tempurl.search('.ts') < 0) {
						if (this.dqrequest) {
							this.dqrequest.abort()
							this.dqrequest = {}
						}
						this.dqrequest = uni.request({
							url:getApp().globalData.cms + 'api.php/iimv/jx/bfq/' + this.xll[this.tabIndex.bfy]['from'] + '/xl/' + this.tabIndex.xl + '/?url=' + tempurl,
							dataType:'json',
							success:res => {
								uni.hideLoading()
								if (res.data.msg == 'success') {
									tempurl = res.data.data.url;
									this.url = tempurl
									// #ifdef APP
										if (res.data.data.header) {
											this.videoHeader = this.data.data.header
										}
									// #endif
								} else {
									uni.showToast({
										icon:'none',
										title:'解析失败，试试切换线路叭'
									})
								}
							},
							fail:err=>{
								uni.showToast({
									icon:'none',
									title:'请求失败，试试切换线路叭'
								})
							}
						})
					} else {
						this.url = tempurl;
						uni.hideLoading()
					}
					if (this.url.search('.m3u8') > 0) {
						this.playstrategy = 2
					}
				} else {
					uni.hideLoading()
					var fknr = '';
					if (fankui == 1) {
						fknr = '无视频源：' + this.mvdetail.vod_name;
					}
					if (fankui == 2) {
						fknr = '无播放链接：' + this.mvdetail.vod_name + '-第' + (this.tabIndex.js + 1)+ '集';
					}
					uni.showModal({
						title:'抱歉',
						content:'当前剧集没有视频源',
						cancelText:'没关系',
						confirmText:'反馈',
						success:r=> {
							if (r.confirm) {
							    uni.request({
							    	url:getApp().globalData.cms + 'api.php/iimv/liuy/content/' + fknr,
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
				}
			},
			playrate(rate) { // 倍速
				this.videoContext.playbackRate(rate);
				this.gns[2].name = rate + '倍';
			},
			qhbfy(index){ // 切换播放源
				this.tabIndex.bfy = index;
				this.tabIndex.xl = 0;
				this.getjk(this.xll[this.tabIndex.bfy]['from']);
				uni.showToast({
					title:'切换成功，请选集',
					icon:'none'
				})
			},
			qhxl(index){ // 切换线路
				this.tabIndex.xl =  index;
				uni.showToast({
					title:'切换成功，马上播放',
					icon:'none'
				})
				this.play()
			},
			dxzxf(){ // 倒序/正序
				this.xll[this.tabIndex.bfy].urls = this.xll[this.tabIndex.bfy].urls.reverse();
				this.tabIndex.js = this.xll[this.tabIndex.bfy].urls.length - 1 - this.tabIndex.js;
				this.tabScrollInto = 'tab' + this.tabIndex.js;
				if (this.dxzx == '倒序') {
					this.dxzx = '正序';
				} else {
					this.dxzx = '倒序';
				}
			},
			modal(index) {
				if (index == 'alljs') {
					if (this.xll[this.tabIndex.bfy].urls.length > 5) {
						this.modalName = index;
					}
				} else {
					this.modalName = index;
				}
				
			},
			hideModal(){ // 隐藏弹窗
				this.conplay(true)
				this.modalName = null;
			},
			mygn(index){
				this.conplay(false);
				if (index == 1) {
					this.modalName = 'liuy';
				}
				
				if (index == 2) {
					this.videoend()
				}
				
				if (index == 3) {
					this.modalName = 'beisu';
				}
				
				if (index == 4) {
					// #ifndef H5
						uni.showToast({
							icon:'none',
							title:'用夸克、QQ浏览器观看效果更佳哦'
						})
					// #endif
					this.modalName = 'link';
				}
				if (index == 5) {
					// #ifdef APP
						plus.share.sendWithSystem({
							type: 'text',
							content: '我正在看《' + this.mvdetail.vod_name + '》，完全免费，画质感人！下载链接：',
							href: getApp().globalData.down
						})
					// #endif
					// #ifndef APP
						uni.setClipboardData({
							data: '我正在看《' + this.mvdetail.vod_name + '》，完全免费，画质感人！下载链接：' + getApp().globalData.down
						})
					// #endif
				}
				
			},
			
			mylink() { //复制视频m3u8链接
				this.modalName = null;
				if (this.url) {
					uni.setClipboardData({
						data: this.url
					});
				} else {
					uni.showToast({
						title:'请先播放视频',
						icon:'none'
					});
				}
			},
			openlink(){
				this.modalName = null;
				this.conplay(false);
				if (this.url){
					plus.runtime.openURL(getApp().globalData.webbfq + this.url)
				} else {
					uni.showToast({
						title:'请先播放视频',
						icon:'none'
					});
				}
			},
			videoend(){
				var js = this.tabIndex.js + 1;
				if (js == this.xll[this.tabIndex.bfy].urls.length) {
					uni.showToast({
						icon:'none',
						title:'视频已经看完啦！'
					})
				} else {
					uni.showToast({
						icon:'none',
						title:'正在播放下一集'
					})
					this.tabIndex.js = js;
					this.play()
				}
			},
			timeupdate(e){
				this.settimehc(e.detail.currentTime);
			},
			sentliuy() {//发送留言
				var data = '报错：视频id：' + this.vid + '-片名：' + this.mvdetail.vod_name;
				if (this.xll.length > 0) {
					var xlid = this.jkss.length > 0 ? this.tabIndex.xl + 1 : 0;
					var bfyt = this.xll[this.tabIndex.bfy].player_info ? this.xll[this.tabIndex.bfy].player_info.show : '无';
					var js = this.xll[this.tabIndex.bfy].urls.length > 0 ? this.xll[this.tabIndex.bfy].urls[this.tabIndex.js].name : '无';
					data = data + '-线路：' + xlid + '-播放源：' + bfyt + '-剧集：' + js + '-留言：' + this.liuy;
				} else {
					data = data + this.liuy;
				}
				
				uni.request({
					url:getApp().globalData.cms + 'api.php/iimv/liuy/content/' + data,
					dataType:'text',
					success:res=>{
						if (res.data == 1) {
							uni.showToast({
								icon:'none',
								title:'发送成功！'
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
	.header {
		height: 230px;
		.video {
			position: fixed;
			
			/* #ifdef H5 */
			top: 30px;
			/* #endif */
			/* #ifndef H5 */
			top: 0;
			/* #endif */
			background-color: #fff;
			width: 100%;
			height: 240px;
			z-index: 1;
		}
	}
	.tab {
		margin-left: 10rpx;
		width: 100vw;
		height: 100rpx;
		display: flex;
		flex-direction: row;
		white-space: nowrap;
		.tabItemClass {
			border: 1px solid #512fbb;
			margin: 0 5rpx;
		}
	}
	.tabItemClassActive {
		background-color: #512fbb;
		color: #fff;
	}
	.tabItemClassActivejs {
		border-bottom: 2px solid $zts;
		color: $zts;
	}
	.center {
		.jianjie {
			.mvname {
				color: $zts;
			}
		}
		.gn {
			color: $zts;
			.gnbox {
				width: 20%;
				display: inline-block;
				text-align: center;
				.gnimg {
					width: 60rpx;
					height: 60rpx;
				}
				.gntext {
					font-size: 12px;
					margin-top: 5rpx;
				}
			}
		}
		.js {
			font-size: 14px;
			view {
				text:nth-child(1) {
					color: $zts;
				}
				text:nth-child(2) {
					font-size: 12px;
					color: #9c9c9c;
				}
			}
			
		}
		.kz {
			font-size: 14px;
			.kztop {
				margin-left: 30rpx;
				color: $zts;
			}
			.kzcen {
				margin-top: 10rpx;
			}
		}
		.jianjiemodal {
			view {
				margin: 15rpx 0;
				color: $zts;
			}
		}
	}
	.textzts {
		color: $zts;
	}
	.mylink {
		view {
			font-size: 14px;
			height: 80rpx;
			line-height: 80rpx;
			text-align: center;
		}
		view:nth-child(1) {
			border-bottom: 1px solid #d6d6d6;
		}
		view:nth-child(2) {
			border-bottom: 1px solid #d6d6d6;
		}
		view:nth-child(3) {
			color: #747474;
		}
	}
	.jsbox {
		display: inline-block;
		width: 20%;
		.jsbox1 {
			height: 30px;
			font-size: 12px;
			line-height: 30px;
			text-align: center;
			overflow: hidden;
			white-space: nowrap;
			text-overflow: ellipsis;
		}
	}
	.liuytop {
		color: $zts;
		margin-bottom: 10rpx;
		text {
			margin-left: 10rpx;
		}
	}
	.liuybot {
		border: 1px solid $zts;
		margin: 0 auto;
		font-size: 12px;
	}
	.innbox {
		width: 96%;
		margin: 10px auto;
		border-radius: 8px;
		box-shadow: 0 2px 2px $zts;
		border: 1px solid $zts;
	}
</style>
