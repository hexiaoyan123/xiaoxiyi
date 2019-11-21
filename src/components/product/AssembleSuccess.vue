<template>
	<div class="goods-list-out-cont produce-cont order-goods-list">
		<Vheader name="订单支付"></Vheader>
		<div class="content">
			<i class="iconFont"><img src="../../assets/goods/xinxi.png" width="100%"></i>
			<p class="successText">订单成功</p>
			<ul class="assemblUl">
				<li class="assemblUl-info">
					<van-row>
						<van-col span="6" class="pinzhuCol"><i class="fontIcon"><img src="../../assets/goods/user.png" width="85%" height="85%" style="margin-top: 6%;"></i><span class="pinzhu">拼主</span></van-col>
						<van-col span="6" class="pinzhuCol"><i class="fontIcon"><img src="../../assets/goods/user.png" width="85%" height="85%" style="margin-top: 6%;"></i></van-col>
						<van-col span="6" class="pinzhuCol"><i class="fontIcon"><img src="../../assets/goods/user.png" width="85%" height="85%" style="margin-top: 6%;"></i></van-col>
						<van-col span="6" class="pinzhuCol"><i class="fontIcon"><img src="../../assets/goods/user.png" width="85%" height="85%" style="margin-top: 6%;"></i></van-col>
					</van-row>
				</li>
			</ul>
			<span class="btn" @click="$router.push('/AssembleOrderSuccess')">返回首页</span>
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
	
	.assemblUl {
		margin-top: 100px;
	}
	
	.assemblUl-info .fontIcon {
		display: inline-block;
		width: 100%;
		height: 100%;
	}
	
	.assemblUl-info .fontIcon img {
		border-radius: 50%;
	}
	
	.assemblUl-info .pinzhu {
		padding: 2px 10px;
		display: inline-block;
		border-radius: 5px;
		color: #ffffff;
		position: absolute;
		background: -webkit-gradient(linear, left top, right top, from(#FF7F62), to(#ff3666));
		background: linear-gradient(90deg, #FF7F62, #ff3666);
		top: 4px;
		right: 0px;
	}
	
	.assemblUl-info .pinzhuCol {
		position: relative;
	}
</style>