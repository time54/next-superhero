<template>
	<view>
		<view class="movie-score-wapper">
			<image v-for="yellow in yelloScore" src="../static/icos/star-yellow.png" class="star-ico"></image>
			<image v-for="grey in greyScore" src="../static/icos/star-gray.png" class="star-ico"></image>
			<view class="movice-score" v-if="showNum">
				{{innerScore}}
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		name: "trailerStars",
		data() {
			return {
				yelloScore: 0,
				greyScore: 5
			};
		},
		
		props: {
			innerScore: 0, // 评分
			showNum: 0     // 是否显示具体的分数 1 显示 0 不显示
		},
		
		created() {
			let tempScore = 0;
			if(this.innerScore) {
				tempScore = this.innerScore;
			}
			
			this.yelloScore = parseInt(tempScore / 2);
			this.greyScore = 5 - this.yelloScore;
		}
	}
</script>

<style>
	.movie-score-wapper {
		display: flex;
		flex-direction: row;
	}
	.star-ico {
		width: 20upx;
		height: 20upx;
		margin-top: 6upx;
	}
	
	.movice-score {
		font-size: 22upx;
		color: grey;
		margin-left: 8upx;
	}
</style>
