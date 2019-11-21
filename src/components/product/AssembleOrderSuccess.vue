<template>
	<div class="goods-list-out-cont produce-cont order-goods-list">
		<Vheader name="订单支付"></Vheader>
		<div class="content">
			<i class="iconFont"><img src="../../assets/goods/xinxi.png" width="100%"></i>
			<p class="successText">支付成功</p>
			<p style="margin-top: 30px; color: #A3A3A3;">还差3人,赶快邀请好友来拼单吧！</p>
			<div class="fenxiangContent">
				<van-row>
					<van-col span="8">
						<i class="fontIcon"><img src="../../assets/goods/pengyouquan.png" width="100%" height="100%"></i>
						<p class="fenxiangP">分享到朋友圈</p>
					</van-col>
					<van-col span="8">
						<i class="fontIcon"><img src="../../assets/goods/haoyou.png" width="100%" height="100%"></i>
						<p class="fenxiangP">分享给微信好友</p>
					</van-col>
					<van-col span="8">
						<i class="fontIcon"><img src="../../assets/goods/xiazai.png" width="100%" height="100%"></i>
						<p class="fenxiangP">保存至手机</p>
					</van-col>
				</van-row>
			</div>
			<span class="btn" @click="$router.push('/AssembleSuccess')">返回首页</span>
		</div>
	</div>
</template>

<script>
	import Vheader from '../HeaderV'
	export default {
		name: 'AssembleSuccess',
		components: {
			Vheader
		},
		data() {
			return {

			}
		},
		created() {},
		methods: {

		}
	}
</script>

<style scoped>
	.fenxiangP{
		color: #808080;
		margin-top: 10px;
	}
	.fenxiangContent{
		margin-top: 100px;
	}
	.fenxiangContent .fontIcon{
		display: inline-block;
		width: 80px;
		height: 80px;
	}
	span.btn {
		margin-top: 200px;
		display: inline-block;
		border-radius: 30px;
		padding: 10px 25px;
		background-color: #ffffff;
		font-size: 30px;
		border: 1px solid #FF5162;
		color: #FF5162;
	}
	
	.content {
		padding: 0px 40px;
		text-align: center;
	}
	.fenxiangContent{
		padding: 20px;
	}
	.iconFont {
		margin-top: 200px;
		width: 200px;
		height: 200px;
		display: inline-block;
	}
	
	.successText {
		margin-top: 10px;
		color: #808080;
	}
	
</style>