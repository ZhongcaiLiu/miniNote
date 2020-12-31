<template>
	<view class="container">
		<input placeholder="请输入待办..." type="text" v-model="thing" @confirm="addThing" maxlength="18"/>
		<uni-swipe-action>
			<uni-swipe-action-item :right-options="item.finished ? options1 : options" @click="changeItem($event,index)" v-for="(item,index) in list" :key="index">
				<view class="item" :style="item.finished ? 'color:#00ff7f':''">{{item.name}}</view>
			</uni-swipe-action-item>
		</uni-swipe-action>
		<view class="tips" v-if="list.length==0">
			<text>您还没有待办事项...</text>
			<text>tips:输入待办后按键盘右下角回车键即可添加待办事项，左滑待办事项列表可进行标记完成、删除操作。</text>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				thing:'',
				list:[],
				options:[
				        {
				            text: '完成',
				            style: {
				                backgroundColor: '#00ff7f',
				            }
				        },
						{
						    text: '删除',
						    style: {
						        backgroundColor: '#f9cce2'
						    }
						}
				      ],
			    options1:[{
					text:'删除',
					style:{
						backgroundColor:'#f9cce2'
					}
				}],
			}
		},
		mounted(){
			 this.list = uni.getStorageSync('list') || []
		},
		methods: {
          addThing(){
			 if(this.thing.trim()==''){
				 uni.showModal({
				 	title:'添加失败',
					content:'待办事项不能为空！',
					showCancel:false
				 })
				 return;
			 }
			 // #ifdef MP-WEIXIN
			 uni.showLoading({
			 	title:'加载中...'
			 })
			 wx.serviceMarket.invokeService({
				service: 'wxee446d7507c68b11',
				api: 'msgSecCheck',
				data: {
				  "Action": "TextApproval",
				  "Text": this.thing,
				},
			  }).then(res=>{
				  // console.log(res)
				  uni.hideLoading()
				  if(res.data.Response.EvilTokens.length>0){
					  uni.showModal({
					  	title:'添加失败',
						content:'请勿发表敏感内容',
						showCancel:false,
					  })
					  return;
				  }
				  this.list.unshift({name:this.thing,finished:false})
				  uni.setStorageSync('list',this.list)
				  this.thing = '';
			  })
			  // #endif
			  // #ifndef MP-WEIXIN
			  this.list.unshift({name:this.thing,finished:false})
			  uni.setStorageSync('list',this.list)
			  this.thing = '';
			  // #endif
		  },
		  changeItem(e,index){
			  if(e.content.text == '完成'){  // 点击完成
				 this.list[index].finished = true;
				 this.list.sort((a,b)=>a.finished-b.finished)
				 uni.setStorageSync('list',this.list)
			  }else{           // 点击删除
				 let that = this;
				 uni.showModal({
				 	title:'删除'+this.list[index].name,
					content:'您确定要删除这个事项吗？',
					success:function(res){
						if(res.confirm){
							that.list.splice(index,1)
							uni.setStorageSync('list',that.list)
						}else if(res.cancel){
							console.log('取消')
						}
					}
				 })
			  }
		  },
		}
	}
</script>

<style>
	.container {
		padding: 20px;
		font-size: 14px;
		line-height: 24px;
	}
	input{
		height: 88rpx;
		border-radius: 20px;
		border: 1px solid #ffaa00;
		padding-left: 28rpx;
		margin-bottom: 18rpx;
	}
	.item{
		width: 100%;
		height: 88rpx;
		line-height: 88rpx;
		padding-left: 20rpx;
		border-bottom: 1px solid #ddd;
	}
	.tips{
		width: 80%;
		display: flex;
		flex-direction: column;
		position: absolute;
		left: 70rpx;
		top: 188rpx;
		color: #999;
	}
	/* .uni-swipe_button-group{
		top:auto !important;
	}
	.uni-swipe_button{
		height: 80rpx;
	} */
</style>
