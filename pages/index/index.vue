<template>
	<view class="content">
		<!--轮播图-->
		<swiper :indicator-dots="true" :autoplay="true" class="carousel">
			<swiper-item v-for="carousel in carouselList">
				<image :src="carousel.image" 
				class="carousel"></image>
			</swiper-item>
<!-- 			<swiper-item>
				<image src="../../static/carousel/spiderman.png" class="carousel"></image>
			</swiper-item> -->
		</swiper>
		<!-- 轮播图 end -->
		
		<!-- 热门超英 start -->
		<view class="page-block super-hot">
			<view class="hot-title-wapper">
				<image src="../../static/icos/hot.png" class="hot-ico"></image>
				<view class="hot-title">
					热门超英
				</view>
			</view>
		</view>
		<scroll-view scroll-x="true" class="page-block hot">
			<view class="single-poster" v-for="superhero in hotSuperheroList">
				<view class="poster-wapper">
					<image :src="superhero.cover" class="poster"></image>
					<view class="movie-name">
						{{superhero.name}}
					</view>
					<trailerStars :innerScore="superhero.score" :showNum="1"></trailerStars>
				</view>
			</view>
		</scroll-view>
		<!-- 热门超英 end -->
		
		<!-- 热门预告 start -->
		<view class="page-block super-hot">
			<view class="hot-title-wapper">
				<image src="../../static/icos/interest.png" class="hot-ico"></image>
				<view class="hot-title">
					热门预告
				</view>
			</view>
		</view>
		<view class="hote-movies page-block">
			<video v-for=" trailer in hotTrailerList" 
			       :src="trailer.trailer" 
				   :poster="trailer.poster"
				   class="hote-movies-single"
				   controls></video>
		</view>
		<!-- 热门预告  end -->
		
		<!-- 猜你喜欢 start -->
		<view class="page-block super-hot">
			<view class="hot-title-wapper">
				<image src="../../static/icos/guess-u-like.png" class="hot-ico"></image>
				<view class="hot-title">
					猜你喜欢
				</view>
			</view>
		</view>
		<view class="page-block guess-u-like">
			<view class="single-like-movie" v-for="(guess,gIndex) in guessULikeList">
				<image :src="guess.cover" class="like-movie"></image>
				<view class="movie-desc">
					<view class="movie-title">
						{{guess.name}}
					</view>
					<trailerStars :innerScore="9.1" :showNum="0"></trailerStars>
					<view class="movie-info">
						{{guess.basicInfo}}
					</view>
					<view class="movie-info">
						{{guess.releaseDate}}
					</view>
				</view>
				<view class="movie-oper" :data-gIndex="gIndex" @click="praiseMe">
					<image src="../../static/icos/praise.png" class="praise-ico"></image>
					<view class="praise-me">
						点赞
					</view>
					<view :animation="animationDataArr[gIndex]" class="praise-me animation-opacity">
						+1
					</view>
				</view>
			</view>
		</view>
		<!-- 猜你喜欢 end -->
	</view>
</template>

<script>
	import trailerStars from "../../components/trailerStars.vue";
	
	export default {
		data() {
			return {
				qq: "lee93071066",
				carouselList: [],
				hotSuperheroList: [],
				hotTrailerList: [],
				animationData: {},
				animationDataArr: [
					{},{},{},{},{}
				],
				guessULikeList: []
			}
		},
		onLoad() {
			// let serverUrl = common.serverUrl;
			// 通过挂载到 main.js 中获取服务器的地址，作为全局变量
			let serverUrl = this.serverUrl;
			// 请求轮播图数据
			uni.request({
				url: serverUrl + '/index/carousel/list',
				method: "POST",
				header:{
					'content-type':'application/x-www-form-urlencoded'
				},
				data:{
					qq: this.qq
				},
				success: (res) => {

					// 获取真实数据之前，务必判断状态是否为200
					if (res.data.status == 200) {
						this.carouselList = res.data.data;
					}

				}
			});
			
			// 查询热门超英
			uni.request({
				url: serverUrl + '/index/movie/hot?type=superhero',
				method: "POST",
				header:{
					'content-type':'application/x-www-form-urlencoded'
				},
				data:{
					qq:this.qq
				},
				success: (res) => {
					// 获取真实数据之前，务必判断状态是否为200
					if (res.data.status == 200) {
						this.hotSuperheroList = res.data.data;
					}
			
				}
			});
			
			// 查询热门预告
			uni.request({
				url: serverUrl + '/index/movie/hot?type=trailer',
				method: "POST",
				header:{
					'content-type':'application/x-www-form-urlencoded'
				},
				data:{
					qq:this.qq
				},
				success: (res) => {
					// 获取真实数据之前，务必判断状态是否为200
					if (res.data.status == 200) {
						this.hotTrailerList = res.data.data;
					}
			
				}
			});
			
			this.refresh();
			
			// #ifdef APP-PLUS || MP-WEIXIN
			// 页面创建时，创建一个临时动画对象
			this.animation = uni.createAnimation();
			// #endif
		},
		onUnload() {
			// 页面卸载时，清除动画数据
			this.animationData = {};
			this.animationDataArr = [{},{},{},{},{}];
		},
		onPullDownRefresh() {
			this.refresh();
		},
		methods: {
			refresh() {
				let serverUrl = this.serverUrl;
				
				uni.showLoading({ // 中间 Loading 
					mask: true // 防止用户点击穿透
				})

				
				// 查询猜你喜欢数据列表
				uni.request({
					url: serverUrl + '/index/guessULike',
					method: "POST",
					header:{
						'content-type':'application/x-www-form-urlencoded'
					},
					data:{
						qq:this.qq
					},
					success: (res) => {
						// 获取真实数据之前，务必判断状态是否为200
						if (res.data.status == 200) {
							this.guessULikeList = res.data.data;
						}
				
					},
					complete: () => {
						uni.stopPullDownRefresh(); // 隐藏 下拉 loading
						uni.hideLoading();
					}
				});
			},
			// 实现点赞动画效果
			praiseMe(e) {
				// #ifdef APP-PLUS || MP-WEIXIN
				let gIndex = e.currentTarget.dataset.gindex;
				// 构建动画数据，并且通过step来表示这组动画的完成
				this.animation.translateY(-60).opacity(1).step({duration: 400});
				
				// 导出动画数据到 view 组件，实现组件的动画效果
				// this.animationData = this.animation.export();
				this.animationData =  this.animation;
				this.animationDataArr[gIndex] = this.animationData.export();
				// 还原动画
				setTimeout(()=>{
					this.animation.translateY(0).opacity(0).step({duration: 0});
					// 导出动画数据到 view 组件，实现组件的动画效果
					// this.animationData = this.animation.export();
					this.animationData =  this.animation;
					this.animationDataArr[gIndex] = this.animationData.export();
				},500);
				// #endif
			}
		},
		components: {
			trailerStars
		},
		
	}
</script>

<style>
	@import url("index.css");
</style>
