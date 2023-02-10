<template>
	<view class="home">
		<view class="scrollNav">
			<scroll-view scroll-x class="navscroll">
				<view class="item " :class="{active: active == index}" 
				v-for="(item,index) in navArr" :key="item.id"
				@click="clickNav(index,item.id)">{{item.classname}}</view>
			</scroll-view>
		</view>
		<view class="content">
			<view class="row" v-for="item in newsArr" :key="item.id">
				<newsbox :item="item"></newsbox>
			</view>
		</view>
		<view class="nodata" v-if="!newsArr.length">
					<image src="../../static/images/nodata.png" mode="widthFix"></image>
				</view>
				
				<view class="loading" v-if="newsArr.length">			
					<view v-if="loading==1">数据加载中...</view>
					<view v-if="loading==2">没有更多了~~</view>
				</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				title: 'Hello',
				active:0,
				navArr:[],
				newsArr:[],
				id:0,
				currentPage:1,
				loading:0       //0默认  1加载中  2没有更多了
			}
		},
		onLoad() {
			this.getNavData()
		},
		onReachBottom() {
			console.log('到底不')
			this.currentPage++,
			this.getNewsData(this.id)
		},
		
		methods: {
			clickNav(index,id) {
				this.active = index,
				this.id = id, //下拉加载是要用到
				this.newsArr = [], //清空 防止和第一次的数据混入
				this.currentPage=1, //初始页
				this.getNewsData(id)
			},
			getNavData(){
					uni.request({
						url:"https://ku.qingnian8.com/dataApi/news/navlist.php",
						success:res=>{
							console.log(res)
							this.navArr=res.data
							this.id = this.navArr[0].id
							this.getNewsData(this.navArr[0].id)
						}
					})
				},
			
			
			getNewsData(id){
				uni.request({
					url:"https://ku.qingnian8.com/dataApi/news/newslist.php",
					data:{
						cid:id,
						page:this.currentPage
					},
					success: (res) => {
						console.log(res)
						if(res.data.length==0){
							this.loading=2
						}
						// this.newsArr = res.data
						this.newsArr=[...this.newsArr,...res.data]
					}
				})
			},
			
			
		}
	}
</script>

<style lang="scss" scoped>
.navscroll {
	height: 100rpx;
	background-color: #F7F8FA;
	white-space: nowrap;
	position: fixed;
	z-index: 10;
	top: var(--window-top);
	::-webkit-scrollbar{
		  display: none;
	}
	.item {
		font-size: 40rpx;
		display: inline-block;
		line-height: 100rpx;
		padding: 0 30rpx;
		color: #333;
		&.active {
			color: #31c27c;
		}
	}	
}
.content {
	padding: 30rpx;
	padding-top: 130rpx;
	.row {
		border-bottom: 1px dotted #efefef;
		padding: 15rpx 0;
	}
}
.nodata{
	display: flex;
	justify-content: center;
	image{
		width: 360rpx;
	}
}
.loading{
	text-align: center;
	font-size: 26rpx;
	color:#888;
	line-height: 2em;
}
</style>
