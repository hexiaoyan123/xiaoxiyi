<template>
	<div class="container order-pay">
		<Vheader name="支付"></Vheader>
		<!-- 支付提示开始 -->
		<div class="pay-tips" v-if="ticket.dataStatus == 1">
			<!-- <van-icon name="underway" /> -->
			支付剩余时间：{{timer}}
		</div>
		<div class="pay-tips" v-if="ticket.dataStatus != 1">
			{{ticket.dataStatus | statusToStr}}
		</div>
		<!-- 支付提示结束 -->

		<!-- 票信息开始 -->
		<div class="ticket-cont">
			<h3 class="ticket-name">{{ticket.activityName}}</h3>
			<div class="ticket-cinema-name">{{ticket.activitySkuName}}</div>
			<div class="ticket-cinema-name">
				{{ticket.activityStartTime | dateFormat}} - {{ticket.activityEndTime | dateFormat}}
			</div>
			<div class="ticket-cinema-name">
				{{ticket.activityAreaName}} {{ticket.activityAddress}}
			</div>
			<!-- 选择支付方式开始 -->
			<div class="ticket-cont" v-if="ticket.dataStatus == 1">
				<h3>选择支付方式</h3>
				<van-cell-group>
					<!-- <van-cell @click="goOnceCard" :title="payChannelObj.p3.name"  :value="checkPayCard.payType == 3 ? '抵扣:'+ deductible : ''" icon="card" v-if="payChannel.indexOf(3) >= 0" is-link />
          <van-cell @click="goCashCard" :title="payChannelObj.p1.name"  :value="checkPayCard.payType == 1 ? '抵扣:'+ deductible : ''" icon="debit-pay" v-if="payChannel.indexOf(1) >= 0" is-link />

          <van-cell :title="payChannelObj.p8.name" icon="wechat" v-if="payChannel.indexOf(8) >= 0" @click="checkPayChannel = 8">
            <van-icon slot="right-icon" name="checked" class="van-cell__right-icon" v-show="checkPayChannel == 8"/>
            <van-icon slot="right-icon" name="check" class="van-cell__right-icon" v-show="checkPayChannel != 8"/>
          </van-cell>
          <van-cell :title="payChannelObj.p9.name" icon="alipay"  v-if="payChannel.indexOf(9) >= 0"  @click="checkPayChannel = 9">
            <van-icon slot="right-icon" name="checked" class="van-cell__right-icon" v-show="checkPayChannel == 9"/>
            <van-icon slot="right-icon" name="check" class="van-cell__right-icon" v-show="checkPayChannel != 9"/>
          </van-cell> -->
					<template v-for="item in listPayChannel">
						<!-- <van-cell v-if="item.payChannelCategory == 3" @click="goOnceCard(item)" :title="item.name" :value="checkPayCard.payChannel == item.id ? '抵扣:'+ deductible : ''" icon="card" is-link />
            <van-cell v-if="item.payChannelCategory == 1" @click="goCashCard(item)" :title="item.name" :value="checkPayCard.payChannel == item.id ? '抵扣:'+ deductible : ''" icon="debit-pay" is-link /> -->

						<div class="van-cell van-hairline" v-if="item.payChannelCategory == 3" @click="goOnceCard(item)">
							<figure class="van-yinlian-logo">
								<img src="@/assets/icon/2.png">
							</figure>
							<div class="van-cell__title">{{item.name}}</div>
							<div v-show="checkPayCard.payChannel == item.id">抵扣: {{deductible}}</div>
							<i class="van-icon van-icon-arrow van-cell__right-icon"></i>
						</div>
						<div class="van-cell van-hairline" v-if="item.payChannelCategory == 1" @click="goCashCard(item)">
							<figure class="van-yinlian-logo">
								<img src="@/assets/icon/1.png">
							</figure>
							<div class="van-cell__title">{{item.name}}</div>
							<div v-show="checkPayCard.payChannel == item.id">抵扣: {{deductible}}</div>
							<i class="van-icon van-icon-arrow van-cell__right-icon"></i>
						</div>
						<van-cell v-if="item.payChannelCategory == 8" :title="item.name" icon="wechat" @click="checkPayChannel = item.id">
							<van-icon slot="right-icon" name="checked" class="van-cell__right-icon" v-show="checkPayChannel == item.id" />
							<van-icon slot="right-icon" name="check" class="van-cell__right-icon" v-show="checkPayChannel != item.id" />
						</van-cell>
					</template>

					<!-- 其他支付方式开始 -->
					<!-- <div class="van-cell van-hairline">
            <figure class="van-yinlian-logo" style="padding: 5px 0;">
              <img src="@/assets/yinlian.png">
            </figure>
            <div class="van-cell__title">银联积分</div>
            <i class="van-icon van-icon-arrow van-cell__right-icon"></i>
          </div>
          <div class="van-cell van-hairline">
            <figure class="van-yinlian-logo">
              <img src="@/assets/icon/3.png">
            </figure>
            <div class="van-cell__title">银行卡</div>
            <i class="van-icon van-icon-arrow van-cell__right-icon"></i>
          </div>
          <div class="van-cell van-hairline">
            <figure class="van-yinlian-logo">
              <img src="@/assets/icon/4.png">
            </figure>
            <div class="van-cell__title">行业积分</div>
            <i class="van-icon van-icon-arrow van-cell__right-icon"></i>
          </div>
          <div class="van-cell van-hairline">
            <figure class="van-yinlian-logo">
              <img src="@/assets/icon/5.png">
            </figure>
            <div class="van-cell__title">积分转换</div>
            <i class="van-icon van-icon-arrow van-cell__right-icon"></i>
          </div>
          <div class="van-cell van-hairline">
            <figure class="van-yinlian-logo">
              <img src="@/assets/icon/6.png">
            </figure>
            <div class="van-cell__title">第三方支付卡</div>
            <i class="van-icon van-icon-arrow van-cell__right-icon"></i>
          </div>
          <div class="van-cell van-hairline">
            <figure class="van-yinlian-logo">
              <img src="@/assets/icon/7.png">
            </figure>
            <div class="van-cell__title">单用途卡</div>
            <i class="van-icon van-icon-arrow van-cell__right-icon"></i>
          </div> -->
					<!-- 其他支付方式结束 -->

				</van-cell-group>
			</div>
			<!-- 选择支付方式结束 -->
		</div>
		<!-- 票信息结束 -->

		<!-- 支付按钮开始 -->
		<div class="ticket-ctrl">
			<div class="ticket-ctrl-left">
				<em>待支付：</em>￥{{toBePaid}}
			</div>
			<div class="ticket-ctrl-right" @click="goWechat" v-if="ticket.dataStatus == 1 && toBePaid > 0 && payChannel.indexOf(8) >= 0 && (! $route.query.wechat)" v-show="timer != '00:00'">获取微信授权</div>
			<div class="ticket-ctrl-right" @click="payOk" v-if="ticket.dataStatus == 1 && (toBePaid == 0 || payChannel.indexOf(8) < 0 || $route.query.wechat)" v-show="timer != '00:00'">确认支付</div>
		</div>
		<!-- 支付按钮结束 -->

	</div>
</template>

<script>
	import Vheader from '../HeaderV'
	export default {
		name: 'MoviePay',
		components: {
			Vheader
		},
		data() {
			return {
				ticket: {

				},
				seats: [], // 座位
				timer: '00:00', // 倒计时时间
				listPayChannel: [], // 支付渠道列表
				checkPayCard: {
					money: 0
				}, // 选中的卡券
				orderAmount: 0, // 订单总额

				payChannelObj: {}, // 支付对象
				payChannel: [], // 支付类型集合
				checkPayChannel: '', // 选中补差支付方式
				// 支付类型判断
				paymentMethodList: [{
					// 现金卡支付
					channel: '1',
					str: '1'
				}, {
					// 次卡支付
					channel: '3',
					str: '2'
				}, {
					// 微信支付
					channel: '8',
					str: '3'
				}, {
					// 现金卡微信补差支付
					channel: '1,8',
					str: '4'
				}, {
					// 次卡微信补差支付
					channel: '3,8',
					str: '5'
				}],
				deductible: 0, // 抵扣金额(元)
				toBePaid: 0, // 待支付金额(元)
				wxData: {}, // 微信支付信息
				wechatOk: this.$route.query.wechat ? true : false
			}
		},
		created() {
			// 获取选中卡券信息
			if(this.$cookies.get('checkPayCard')) {
				let checkPayCard = JSON.parse(this.$cookies.get('checkPayCard'));
				if(checkPayCard.order == this.$route.params.id) {
					this.checkPayCard = checkPayCard;
				}
			}
			// console.log(this.checkPayCard)

			this.getListPayChannel();
			this.getOrder();

			// 验证微信支付
			// if(! this.$route.query.wechat){
			//   this.goWechat()
			// }
		},
		methods: {
			// 计算金额
			amountCalculation() {
				if(this.checkPayCard.money) {
					this.deductible = (this.checkPayCard.money / 100).toFixed(2)
					this.toBePaid = ((this.ticket.orderAmount - this.checkPayCard.money) / 100).toFixed(2);
				} else {
					this.deductible = 0;
					this.toBePaid = (this.ticket.orderAmount / 100).toFixed(2);
				}
			},
			// 0元支付
			pay0() {
				let vm = this;
				// 上传参数
				let postData = {
					"paymentMethod": '6',
					"orderId": this.$route.params.id,
					"voucher": '', // 卡号
					"wechatPayPrice": 0
				}
				this.$http.post('/panda-activity-api/v1/paymentOrder', postData).then(function(response) {
						if("1000" == response.data.code) {
							vm.$router.push('/success')
						} else {
							vm.$toast(response.data.msg)
						}
					})
					.catch(function(error) {
						vm.$toast('请求超时，请稍后再试！')
					});
			},
			// 支付
			payOk() {
				let vm = this;

				let paymentMethod = ''; // 支付类型

				if(this.orderAmount == 0) {
					this.pay0();
					return
				}

				// console.log(vm.payChannel.indexOf(8) >= 0)
				// console.log(vm.checkPayCard.money)

				if(this.ticket.dataStatus != 1) {
					vm.$toast('订单不可支付')
					return
				}

				let nextStater = true;

				// 验证微信补差
				if(vm.payChannel.indexOf(8) >= 0 && (vm.checkPayCard.money - 0) < (vm.orderAmount - 0) * 100) {
					if(!vm.$route.query.wechat) {
						nextStater = false;
						vm.goWechat()
						return;
					}
					if(!vm.checkPayCard.order) {
						// 单一微信支付
						vm.paymentMethodList.forEach((e, i) => {
							if(e.channel == '8') {
								paymentMethod = e.str;
							}
						})
					} else {
						// 微信补差支付
						vm.paymentMethodList.forEach((e, i) => {
							if(e.channel.indexOf(vm.checkPayCard.payType + '') >= 0 && e.channel.indexOf('8') >= 0) {
								paymentMethod = e.str;
							}
						})
					}
				} else {
					// 不补差情况
					if(!vm.checkPayCard.order) {
						vm.$toast('请选择支付方式')
						return;
					}
					// 支付类型赋值
					vm.paymentMethodList.forEach((e, i) => {
						if(e.channel == vm.checkPayCard.payType) {
							paymentMethod = e.str;
						}
					})
				}

				// 下一步
				if(!nextStater) {
					return
				}
				// 乘法函数
				function numMulti(num1, num2) {
					var baseNum = 0;
					try {
						baseNum += num1.toString().split(".")[1].length;
					} catch(e) {}
					try {
						baseNum += num2.toString().split(".")[1].length;
					} catch(e) {}
					return Number(num1.toString().replace(".", "")) * Number(num2.toString().replace(".", "")) / Math.pow(10, baseNum);
				};
				// 上传参数
				let postData = {
					"paymentMethod": paymentMethod,
					"orderId": this.$route.params.id,
					"voucher": vm.checkPayCard.voucher ? vm.checkPayCard.voucher : '', // 卡号
					"wechatPayPrice": numMulti(Number(vm.toBePaid), 100)
				}
				this.$http.post('/panda-activity-api/v1/paymentOrder', postData).then(function(response) {
						if("1000" == response.data.code) {
							if(response.data.result) {
								vm.wxData = response.data.result;
								vm.wxPlay()
							} else {
								vm.$router.push('/success')
							}
						} else {
							vm.$toast(response.data.msg)
						}
					})
					.catch(function(error) {
						vm.$toast('请求超时，请稍后再试！')
					});

				// if(! this.checkPayCard.order){
				//   vm.$toast('请选择支付方式')
				//   return;
				// }
				// if(this.checkPayCard.payType == 3){
				//   let postData = {
				//     "paymentMethod":"time_card_pay",
				//     "orderId": this.$route.params.id, 
				//     "voucher": this.checkPayCard.voucher
				//   }
				//   this.$http.post('/panda-cinema-api/v1/payment', postData).then(function (response) {
				//     if("1000" == response.data.code){
				//       vm.$router.push('/success')
				//     }else{
				//       vm.$toast(response.data.msg)
				//     }
				//   })
				//   .catch(function (error) {
				//     vm.$toast('请求超时，请稍后再试！')
				//   });
				// }else if(this.checkPayCard.payType == 1){
				//   let postData = {
				//     "paymentMethod": "cash_card_pay",
				//     "orderId": this.$route.params.id, 
				//     "voucher": this.checkPayCard.voucher + '_' + this.ticket.orderAmount
				//   }
				//   this.$http.post('/panda-cinema-api/v1/payment', postData).then(function (response) {
				//     if("1000" == response.data.code){
				//       vm.$router.push('/success')
				//     }else{
				//       vm.$toast(response.data.msg)
				//     }
				//   })
				//   .catch(function (error) {
				//     vm.$toast('请求超时，请稍后再试！')
				//   });
				// }
			},
			// 次卡支付
			goOnceCard(payChannel) {
				this.$cookies.set('payChannel', JSON.stringify({
					id: payChannel.id,
					params: payChannel.params
				}))
				this.$router.push({
					path: '/orderPayOnceCard/' + this.$route.params.id,
					query: {
						payChannel: payChannel.id,
						money: this.ticket.orderAmount,
						nums: this.ticket.quantity,
						full: this.payChannel.indexOf(8) >= 0 ? 1 : 0
					}
				})
			},
			// 现金卡支付
			goCashCard(payChannel) {
				this.$cookies.set('payChannel', JSON.stringify({
					id: payChannel.id,
					params: payChannel.params
				}))
				this.$router.push({
					path: '/orderPayCashCard/' + this.$route.params.id,
					query: {
						payChannel: payChannel.id,
						money: this.ticket.orderAmount,
						full: this.payChannel.indexOf(8) >= 0 ? 1 : 0
					}
				})
			},
			// 获取微信授权
			goWechat() {
				let urlStr = window.location.href;
				let backUrl = encodeURIComponent(urlStr + '?wechat=true');
				let nowToken = this.$init.accessToken;
				this.$cookies.set('payBackData', JSON.stringify({
					user: this.$init.user,
					accessToken: this.$init.accessToken,
					refreshToken: this.$init.refreshToken
				}))
				// 获取权限链接
				window.location.href = this.$init.ip + '/panda-payment-api/wap/wechat/authorize?accessToken=' + nowToken + '&state=' + backUrl;
			},
			wxPlay() {
				let vm = this;
				WeixinJSBridge.invoke(
					'getBrandWCPayRequest', {
						"appId": vm.wxData.appId, //公众号名称，由商户传入     
						"timeStamp": vm.wxData.timeStamp, //时间戳，自1970年以来的秒数     
						"nonceStr": vm.wxData.nonceStr, //随机串     
						"package": vm.wxData.package,
						"signType": "MD5", //微信签名方式：     
						"paySign": vm.wxData.paySign //微信签名 
					},
					function(res) {
						if(res.err_msg == "get_brand_wcpay_request:ok") {
							vm.$router.push('/success')
						} else if(res.err_msg == "get_brand_wcpay_request:fail") {
							vm.$router.push('/fail')
						} else if(res.err_msg == "get_brand_wcpay_request:cancel") {
							vm.$router.push('/fail')
						}
					}
				);
			},
			// 返回上一页
			goBack() {
				this.$router.back(-1);
			},
			// 获取订单信息
			getOrder() {
				let vm = this;
				this.$http.get('/panda-activity-api/v1/getOrder/' + this.$route.params.id).then(function(response) {
						if("1000" == response.data.code) {
							vm.ticket = response.data.result;
							vm.resetTime(response.data.result.overTime)
							vm.orderAmount = (vm.ticket.orderAmount / 100).toFixed(2)
							vm.amountCalculation(); // 计算金额
						} else {
							vm.$toast(response.data.msg)
						}
					})
					.catch(function(error) {
						vm.$toast('请求超时，请稍后再试！')
					});
			},
			// 获取支付渠道
			getListPayChannel() {
				let vm = this;
				this.$http.get('/panda-activity-api/v1/listPayChannel').then(function(response) {
						if("1000" == response.data.code) {
							vm.listPayChannel = response.data.result;
							if(response.data.result) {
								let payChannelObj = {};
								response.data.result.forEach((e, i) => {
									vm.payChannel.push(e.payChannelCategory);
									if(vm.checkPayChannel == '' && e.payChannelCategory == 8) {
										vm.checkPayChannel = e.id
									}
									payChannelObj['p' + e.payChannelCategory] = e;
								})
								vm.payChannelObj = payChannelObj;
							}
							// vm.getOrder() 
						} else {
							vm.$toast(response.data.msg)
						}
					})
					.catch(function(error) {
						vm.$toast('请求超时，请稍后再试！')
					});
			},
			// 倒计时插件
			resetTime(time) {
				let vm = this;
				//获取当前时间
				var date = new Date();
				var now = date.getTime();
				//设置截止时间
				var end = time * 1000;
				if(end <= now) {
					return false;
				}
				//时间差
				var leftTime = end - now;
				var timer = null;
				// 倒计时函数
				function countDown() {
					leftTime -= 1000;
					//定义变量 d,h,m,s保存倒计时的时间
					var d, h, m, s;
					if(leftTime >= 0) {
						d = Math.floor(leftTime / 1000 / 60 / 60 / 24);
						h = Math.floor(leftTime / 1000 / 60 / 60 % 24);
						m = Math.floor(leftTime / 1000 / 60 % 60);
						s = Math.floor(leftTime / 1000 % 60);

						if(m < 10) {
							m = '0' + m
						}
						if(s < 10) {
							s = '0' + s
						}

						vm.timer = m + ":" + s;
					} else {
						vm.timer = "00:00";
						clearInterval(timer);
					}
				}

				timer = setInterval(countDown, 1000);
			},
		},
		filters: {
			// 金钱格式化
			moneyFormat(value) {
				if(value) {
					return(value / 100).toFixed(2)
				} else if(value === 0) {
					return 0
				} else {
					return ''
				}
			},
			// 时间格式化
			dateFormat(shijianchuo) {
				function add0(m) {
					return m < 10 ? '0' + m : m
				}
				var time = new Date(shijianchuo * 1000);
				var y = time.getFullYear();
				var m = time.getMonth() + 1;
				var d = time.getDate();
				var h = time.getHours();
				var mm = time.getMinutes();
				var s = time.getSeconds();
				return y + '-' + add0(m) + '-' + add0(d) + ' ' + add0(h) + ':' + add0(mm);
			},
			// 状态转字符串
			statusToStr(value) {
				if(value == 1) {
					return '待付款'
				} else if(value == 3) {
					return '已报名'
				} else if(value == 4) {
					return '已取消'
				} else if(value == 5) {
					return '已失败'
				} else {
					return ''
				}
			}
		}
	}
</script>

<style scoped>
	.container {
		background-color: #fff;
		padding-top: 84px;
	}
	
	.top img {
		display: block;
		width: 100%;
	}
	
	header {
		height: 85px;
		line-height: 85px;
		/* position: absolute; */
		position: fixed;
		width: 100%;
		top: 0;
		left: 0;
		background-color: #ff5162;
		color: #fff;
		text-align: center;
		display: flex;
		font-size: 28px;
		z-index: 101;
	}
	
	.header-back {
		width: 70px;
		text-align: center;
	}
	
	.header-cont {
		flex: 10;
	}
	
	.header-more {
		width: 75px;
		text-align: center;
	}
	
	header .iconfont {
		font-size: 28px;
	}
	/* 支付提示 */
	
	.pay-tips {
		font-size: 24px;
		color: #fff;
		text-align: center;
		background-color: #f1cf64;
		line-height: 70px;
	}
	/* 票信息 */
	
	.ticket-cont {
		color: #666;
		padding: 20px 0;
	}
	
	.ticket-name {
		font-size: 48px;
		font-weight: 700;
		padding: 0 20px;
		color: #666;
		line-height: 80px;
	}
	
	.ticket-cinema-name {
		font-size: 27px;
		padding: 0 20px;
		line-height: 44px;
	}
	
	.ticket-time {
		font-size: 24px;
		line-height: 40px;
		padding: 0 20px 20px 20px;
	}
	
	.ticket-seats {
		padding: 40px 20px;
		position: relative;
		border-top: 1px solid #eee;
		border-bottom: 1px solid #eee;
	}
	
	.ticket-seat {
		display: inline-block;
		line-height: 50px;
		font-size: 24px;
		padding: 0 6px;
		border-radius: 6px;
		border: 1px solid #ff5162;
		color: #ff5162;
		margin-right: 20px;
	}
	
	.ticket-seats-many {
		color: #ff9000;
		font-size: 36px;
		position: absolute;
		right: 20px;
		line-height: 52px;
		font-weight: 700;
	}
	
	.newmaney {
		position: absolute;
		right: 0;
		top: 0;
		padding: 0 20px;
		text-align: right;
		color: #ff9000;
		font-size: 36px;
		font-weight: 700;
		line-height: 96px;
	}
	
	.newmaney em {
		font-size: 26px;
	}
	
	.ticket-phone {
		font-size: 28px;
		padding: 0 20px;
		line-height: 100px;
		border-bottom: 1px solid #eee;
		border-top: 1px solid #eee;
		position: relative;
	}
	
	.ticket-select {
		padding: 20px 0;
	}
	
	.ticket-select li {
		line-height: 66px;
		padding: 0 20px;
		font-size: 28px;
		position: relative;
	}
	
	.ticket-btn {
		margin: 0 20px;
		line-height: 85px;
		border-radius: 6px;
		color: #fff;
		background-color: #ff5162;
		text-align: center;
		font-size: 32px;
	}
	
	.ticket-select-check {
		position: absolute;
		line-height: 66px;
		right: 20px;
		top: 0
	}
	
	.ticket-select-checkd {
		position: absolute;
		line-height: 66px;
		right: 20px;
		top: 0;
		display: none;
		color: #ff5162;
	}
	
	.ticket-select-on {
		background-color: #f6f6f6;
	}
	
	.ticket-select-on .ticket-select-check {
		display: none;
	}
	
	.ticket-select-on .ticket-select-checkd {
		display: block;
	}
	
	.ticket-cont h3 {
		font-size: 36px;
		line-height: 60px;
		padding: 10px 20px;
		background-color: #fff;
	}
	
	.ticket-ctrl {
		width: 100%;
		display: flex;
		line-height: 85px;
		font-size: 36px;
		overflow: hidden;
		border-top: 1px solid #eee;
		background-color: #fff;
		position: fixed;
		bottom: 0;
		left: 0;
	}
	
	.ticket-ctrl-left {
		flex: 10;
		color: #ff5162;
		font-weight: 700;
		padding: 0 20px;
	}
	
	.ticket-ctrl-left em {
		font-size: 27px;
		color: #898989;
	}
	
	.ticket-ctrl-right {
		width: 300px;
		text-align: center;
		color: #fff;
		background-color: #ff5162;
		font-size: 27px;
	}
	
	.ticket-magin {
		margin-right: 20px;
	}
	
	.van-yinlian-logo {
		height: 24px;
		box-sizing: border-box;
		padding: 3px 0;
		margin-right: 4px;
		width: 26px;
	}
	
	.van-yinlian-logo img {
		display: block;
		height: 100%;
		margin: 0 auto;
	}
</style>

<style>
	.van-popup--right {
		height: 100%;
		width: 100%;
		background-color: #fff;
	}
	
	.van-icon-alipay {
		color: #00bbee;
	}
	
	.van-icon-wechat {
		color: #609700;
	}
	
	.van-icon-card {
		color: #ff5162;
	}
	
	.van-icon-debit-pay {
		color: #ff5162;
	}
	
	.van-cell__right-icon {
		font-size: 14px;
	}
	
	.van-icon-checked {
		color: #ff5162;
	}
	
	.order-pay .van-icon-wechat {
		width: 26px;
		text-align: center;
	}
</style>