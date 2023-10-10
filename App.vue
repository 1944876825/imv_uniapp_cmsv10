<script>
	export default {
		globalData:{
			cms: 'http://www.baidu.com/',// 只需修改这里，加http(s)://和/
			bbh: '',
			showweb: false,
			user: '',
			webbfq: ''
		},
		onLaunch: function() {
			console.log('App Launch')
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style>
	@import "colorui/main.css";
    @import "colorui/icon.css";
	/*每个页面公共css */
	html,
	body {
		background-color: #fff;
	}
</style>
