<template>
	<view class="detail">
		<view class="title">{{detail.title}}</view>
		<view class="info">
			<view class="author">编辑：{{detail.author}}</view>
			<view class="time">发布日期：{{detail.posttime}}</view>
		</view>
		<view class="content">
			<!-- rich-text 解析富文本 -->
			<rich-text :nodes="detail.content"></rich-text>
		</view>
		<view class="description">
		声明：本站的内容均采集与腾讯新闻，如果侵权请联系管理（1844211826@qq.com）进行整改删除，本站进行了内容采集不代表本站及作者观点，若有侵犯请及时联系管理员，谢谢您的支持。
		</view>
	</view>
</template>

<script>
	import {parseTime} from '../../utils/tool.js'
	export default {
		data() {
			return {
				option: null,
				detail:{}
			};
		},
		onLoad(e) {
			this.option = e
			this.getDetail()
		},
		methods: {
			getDetail() {
				uni.request({
					url:"https://ku.qingnian8.com/dataApi/news/detail.php",
					data: this.option,
					success: (res) => {
						console.log(res.data)
						res.data.posttime=parseTime(res.data.posttime) //时间搓转化成时间
						res.data.content=res.data.content.replace(/<img/gi,'<img style="max-width:100%"')						
						this.detail = res.data
						
						this.saveHistory()
						
						uni.setNavigationBarTitle({
							title: this.detail.title
						});
					},
					fail: (err) => {
						console.log(err)
					}
				})
			},
			saveHistory() {
				let historyArr = uni.getStorageSync('historyArr') || []
				
				let item = {
					id: this.detail.id,
					classid: this.detail.classid,
					picurl: this.detail.picurl,
					title: this.detail.title,
					looktime: parseTime(Date.now()) 
				}
				let index =historyArr.findIndex(i=> {
					return i.id == this.detail.id
				})
				if(index>=0) {
					historyArr.splice(index,1)
				}
				historyArr.unshift(item)
				historyArr=historyArr.slice(0,10)	
				uni.setStorageSync('historyArr',historyArr)
			}
		}
	}
</script>

<style lang="scss" scoped>
 .detail {
	 padding: 30rpx;
	 .title {
		 font-size: 46rpx;
		 color: #333;
	 }
	 .info {
		 background-color: #f6f6f6;
		 padding: 20rpx 10rpx;
		 font-size: 25rpx;
		 color: #666;
		 display: flex;
		 justify-content: space-between;
		 margin: 30rpx 0;
	 }
	 .content {
		 padding-bottom: 50rpx;
		 // 穿透对组件内的样式修改 微信小程序不支持
		 // /deep/ img {  					
			//  max-width: 100%;
		 // }
	 }
	 .description {
		 background: #FEF0F0;
		 font-size: 26rpx;
		 padding: 20rpx;
		 color: #f89898;
		 line-height: 1.8em;
	 }
 }
</style>
