<template>
	<div class="container card-my">
		<Vheader name="我的钱包"></Vheader>
		<div class="main-content" :style="{height: (540 + 80 * list.length) + 'px'}">
			<div class="card-content bank-card" :class="initCard(item.bankType)" :ref="'card' + index" @click="selectCard(index)" v-for="(item, index) in list" :style="{top: (80 * (index + 1) - 60) + 'px'}">
				<div class="card-main">
					<i class="iconFont"><img src="../../assets/card/zgyh.png" width="60%" height="60%"/></i>
					<div class="card-item">
						<span style="font-size: 13px;">{{item.bankType}}</span><br>
						<span>{{item.cardType}}</span>
					</div>
					<div class="card-num">**** 6566</div>
				</div>
			</div>
			<div class="card-content add-card" :style="{top: (list.length > 0 ? 100 + 80 * list.length : 20) + 'px'}">
				<div class="card-main">
					<span class="add-card-warn">支持储蓄卡</span>
					<i class="iconFont"><img src="../../assets/goods/jiahao.png" width="100%" height="100%"/></i>
					<span class="card-text">添加银行卡</span>
				</div>
			</div>
			<p class="warn-content" :style="{marginTop: (440 + 80 * list.length) + 'px'}">
				<i>
				<img src="../../assets/goods/warn.png" width="100%" height="100%"/>
			</i> 绑定银行卡须知
			</p>
		</div>
	</div>
</template>

<script>
	import Vheader from '../HeaderV'
	export default {
		name: 'CardMy',
		components: {
			Vheader
		},
		data() {
			return {
				list: [{
					cardNo: '**** 6566',
					cardType: '储蓄卡',
					bankType: '中国银行'
				}, {
					cardNo: '**** 6566',
					cardType: '储蓄卡',
					bankType: '中国建设银行'
				}],
				activeCardIndex: -1
			}
		},
		created() {

		},
		methods: {
			initCard(type) {
				if(type == '中国银行') {
					return 'zgyh-card';
				} else if(type == '中国建设银行') {
					return 'zgjsyh-card';
				}
			},
			selectCard(index) {
				debugger
				if(this.activeCardIndex > -1) {
					while(this.$refs['card' + (this.activeCardIndex + 1)]) {
						this.$refs['card' + (this.activeCardIndex + 1)][0].style.top = (Number(this.$refs['card' + (this.activeCardIndex + 1)][0].style.top.replace('px', '')) - 80) + 'px';
						this.activeCardIndex++;
					}
				}
				var nowIndex = index;
				debugger
				while(this.$refs['card' + (nowIndex + 1)]) {
					this.$refs['card' + (nowIndex + 1)][0].style.top = (Number(this.$refs['card' + (nowIndex + 1)][0].style.top.replace('px', '')) + 80) + 'px';
					nowIndex++;
				}
				this.activeCardIndex = index;
			}
		}
	}
</script>

<style scoped>
	/* 头 */
	
	header {
		/* position: absolute; */
		position: fixed;
		width: 100%;
		top: 0;
		left: 0;
		font-size: 28px;
		color: #fff;
		line-height: 84px;
		text-align: center;
		border-bottom: 1px solid #eee;
		background-color: #ff5162;
		z-index: 101;
	}
	
	.iconFont {
		display: inline-block;
		width: 100px;
		height: 100px;
	}
	
	.card-my {
		padding-top: 105px;
	}
	
	.card-my .main-content {
		padding: 0px 10%;
		background-color: #f8f8f8;
		position: relative;
	}
	
	.card-content {
		width: 80%;
		position: absolute;
		border-radius: 10px;
	}
	
	.card-content .card-main {
		position: relative;
	}
	
	.add-card {
		padding: 120px 0px;
		background-color: #F4F4F4;
		color: #919191;
		text-align: center;
		z-index: 999;
	}
	
	.add-card .card-text {
		position: absolute;
		top: calc(50% - 17px);
		font-size: 26px;
	}
	
	.add-card .iconFont {
		margin-left: -160px;
	}
	
	.add-card .add-card-warn {
		position: absolute;
		right: 20px;
		top: -100px;
		color: #C4C4C4;
	}
	
	.bank-card {
		padding: 50px 0px 190px;
		color: #FFFFFF;
	}
	
	.zgyh-card {
		background-color: #B81C22;
	}
	
	.bank-card .iconFont {
		background-color: #ffffff;
		border-radius: 50%;
		text-align: center;
	}
	
	.bank-card img {
		margin-top: 20%;
	}
	
	.bank-card .iconFont {
		margin-left: 6%;
	}
	
	.card-item {
		display: inline-block;
		line-height: 40px;
		padding-left: 30px;
		vertical-align: middle;
		position: absolute;
		top: 10px;
		color: #F6E6E7;
	}
	
	.card-num {
		display: inline-block;
		position: absolute;
		right: 30px;
		top: 30%;
		font-size: 26px;
	}
	
	.zgjsyh-card {
		background-color: #003B8F;
	}
	
	.warn-content {
		color: #FE8103;
		position: relative;
		padding-left: 30px;
		display: inline-block;
		width: 100%;
		text-align: center;
	}
	
	.warn-content i {
		position: absolute;
		left: calc(50% - 100px);
		top: 2px;
		width: 24px;
		height: 23px;
		display: inline-block;
	}
</style>