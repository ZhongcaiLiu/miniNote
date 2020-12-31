<script>
	export default {
		onLaunch: function() {
			console.log('App Launch')
			// 一天未使用后弹出欢迎对话框
			let now = new Date().getTime()
			let lastIn = uni.getStorageSync('lastIn')
			if((now - lastIn)> (1000*3600*24)){
			  uni.showModal({
			  	title:'Wow',
			  	content:'Welcome to miniNote!',
			  	showCancel:false
			  })
			  uni.setStorageSync('lastIn',now)
			}
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
	/*每个页面公共css */
</style>
