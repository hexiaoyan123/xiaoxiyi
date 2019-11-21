<template>
  <div class="container wallet-my">
    <Vheader name="我的钱包"></Vheader>
    <div class="main">
      <div class="top-content">
        <i class="fontIcon">
          <img :src="userFund.imageUrl" width="100%" height="100%" v-if="userFund.imageUrl" />
          <img src="../../assets/user.jpg" width="100%" height="100%" v-else />
        </i>
        <div class="top-item">
          <span style="color: #4C4C4C; font-size: 13px;">{{userFund.nickname}}</span>
          <br />
          <span style="color: #A3A3A3;" v-if="userFund.phone">已绑定手机</span>
        </div>
      </div>
      <div class="yuE-content">
        <van-row>
          <van-col span="12">
            <span>可用余额</span>
            <br />
            <span style="color: #FE5162;">
              <small>¥</small>
              <span v-html="moneyFormat(userFund.residueFund)" style="font-size: 18px;"></span>
            </span>
          </van-col>
          <van-col span="12">
            <span>可提现余额</span>
            <br />
            <span style="color: #FE5162;">
              <small>¥</small>
              <span v-html="moneyFormat(userFund.residueFund)" style="font-size: 18px;"></span>
            </span>
          </van-col>
        </van-row>
      </div>
      <div class="main-content">
        <!-- <button class="now_yue">本月</button> -->
        <button class="select_yue" @click="toSelectDate">{{selectDate}}</button>
        <div class="main-left-content">
          <span>收入：¥{{userFundLog.earning | priceFormat}}</span>
          <br />
          <span>支出：¥{{userFundLog.disbursement | priceFormat}}</span>
          <i class="moreIcon" @click="goIncomeStatistics(userFund.userId)">
            <img src="../../assets/xb/more.png" width="100%" height="100%" />
          </i>
        </div>
        <ul class="price-list" v-if="userFundLog.userFundLogList">
          <li v-for="val in userFundLog.userFundLogList" :key="val.id">
            <i class="fontIcon">
              <img :src="val.imageUrl" width="100%" height="100%" v-if="val.imageUrl" />
              <img src="../../assets/user.jpg" width="100%" height="100%" v-else />
            </i>
            <div class="list-item">
              <span style="color: #888888;" v-if="val.operatorCategory == 1">推广收益</span>
              <span style="color: #888888;" v-if="val.operatorCategory == 2">提现</span>
              <span style="color: #888888;" v-if="val.operatorCategory == 3">消费</span>
              <br />
              <span style="color: #CFCFCF;">{{val.updateTime | dateFormat}}</span>
            </div>
            <div class="list-price-item" style="color: #FE5162;">
              <span>
                +
                <small>¥</small>
                <span v-html="moneyFormat(val.changeFund)" style="font-size: 16px;"></span>
              </span>
              <i class="moreIcon" @click="goIncomeInfo(val)">
                <img src="../../assets/xb/more.png" width="100%" height="100%" />
              </i>
            </div>
          </li>
        </ul>
      </div>
    </div>
    <div class="bottom-button">
      <van-row>
        <!-- <van-col span="12" @click.native="goMyBankCard">绑定银行卡</van-col>
        <van-col span="12">提现</van-col>-->
      </van-row>
    </div>
    <van-actionsheet v-model="datePopup" class="dateModel">
      <div class="dateTitle">选择时间</div>
      <div class="dateType">
        <span :class="dateType=='year' ? 'active': ''" @click="dateType='year'">按年</span>
        <span :class="dateType=='year-month' ? 'active': ''" @click="dateType='year-month'">按月</span>
      </div>
      <van-datetime-picker
        :class="dateType=='year' ? 'typeYear' : ''"
        v-model="currentDate"
        type="year-month"
        @confirm="confirm"
      ></van-datetime-picker>
    </van-actionsheet>
  </div>
</template>

<script>
import Vheader from "../HeaderV";
export default {
  name: "walletMy",
  components: {
    Vheader
  },
  data() {
    return {
      // 用户资金
      userFund: {},
      // 用户资金日志
      userFundLog: {
        earning: 0,
        disbursement: 0,
        userFundLogList: []
      },

      dateType: "year-month",
      datePopup: false,
      currentDate: new Date("2019-10"),
      selectDate: "2019年10月"
    };
  },
  created() {
    this.getUserFund();
  },
  watch: {},
  methods: {
    // 获取用户资金
    getUserFund() {
      var vm = this;
      vm.$http
        .get("/panda-payment-api/v1/getUserFund")
        .then(function(response) {
          if ("1000" == response.data.code) {
            vm.userFund = response.data.result;

            vm.listUserFundLog(vm.userFund.userId);
          } else {
            vm.$toast(response.data.msg);
          }
        })
        .catch(function(error) {
          vm.$toast("请求超时，请稍后再试！");
        });
    },

    // 获取用户资金日志
    //  pageNo 当前页码
    //  pageSize 分页大小
    //  userFundId 用户资金ID
    //  startCreateTime 开始创建时间
    //  endCreateTime 结束创建时间
    // /panda-payment-api/v1/pageUserFundLog/{pageNo}/{pageSize}/{userFundId}/{startCreateTime}/{endCreateTime}
    listUserFundLog(val) {
      var vm = this;
      vm.$http
        .get(
          "/panda-payment-api/v1/pageUserFundLog/1/20/" +
            val +
            "/1572537600/1575129599"
        )
        .then(function(response) {
          if ("1000" == response.data.code) {
            vm.userFundLog = response.data.result;
          } else {
            vm.$toast(response.data.msg);
          }
        })
        .catch(function(error) {
          vm.$toast("请求超时，请稍后再试！");
        });
    },

    // 收入详情
    goIncomeInfo(val) {
      window.localStorage.setItem("clickUserFundLog", JSON.stringify(val));
      this.$router.push("/wallet/incomeInfo");
    },

    goIncomeStatistics(val) {
      window.localStorage.setItem("clickUserId", JSON.stringify(val));
      this.$router.push("/wallet/incomeStatistics");
    },

    goMyBankCard() {
      this.$router.push("/wallet/myBankCard");
    },

    confirm() {
      this.selectDate = this.currentDate.getFullYear() + "年";
      if (this.dateType == "year-month") {
        this.selectDate += this.currentDate.getMonth() + "月";
      }
      this.datePopup = false;
    },
    toSelectDate() {
      this.datePopup = true;
    },
    moneyFormat(value) {
      if (value) {
        var moneyValue = (value / 100).toFixed(2).split(".");
        return "<big>" + moneyValue[0] + "</big>." + moneyValue[1];
      } else if (value === 0) {
        return "0";
      } else {
        return "";
      }
    }
  },
  filters: {
    // 金钱格式化
    priceFormat(val) {
      if (val || val === 0) {
        return (val / 100).toFixed(2);
      } else {
        return "0.00";
      }
    },

    // 时间格式化
    dateFormat(val) {
      if (val) {
        let add0 = m => {
          return m < 10 ? "0" + m : m;
        };
        var time = new Date(val * 1000);
        var y = time.getFullYear();
        var m = time.getMonth() + 1;
        var d = time.getDate();
        var h = time.getHours();
        var mm = time.getMinutes();
        var s = time.getSeconds();
        return (
          y +
          "-" +
          add0(m) +
          "-" +
          add0(d) +
          " " +
          add0(h) +
          ":" +
          add0(mm) +
          ":" +
          add0(s)
        );
      } else {
        return "";
      }
    }
  }
};
</script>

<style scoped>
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

.wallet-my {
  height: 100%;
  padding-top: 105px;
  padding-bottom: 100px;
  background-color: #f8f8f8;
}

.top-content {
  padding: 5px 20px;
  height: 180px;
}

.fontIcon {
  display: inline-block;
  width: 140px;
  height: 140px;
  float: left;
}

.fontIcon img {
  border-radius: 50%;
}

.top-content .top-item {
  display: inline-block;
  height: 116px;
  line-height: 40px;
  padding-top: 24px;
  padding-left: 30px;
  vertical-align: middle;
  float: left;
}

.yuE-content {
  border-top: 1px solid #ececec;
  border-bottom: 1px solid #ececec;
}

.yuE-content .van-col {
  text-align: center;
  height: 100px;
  padding-top: 30px;
}

.yuE-content .van-col:first-child {
  border-right: 1px solid #ececec;
}

.now_yue,
.select_yue {
  border: 1px solid #fed2d6;
  color: #fe6776;
  background-color: transparent;
  border-radius: 30px;
}

.now_yue {
  padding: 10px 50px 12px;
}

.select_yue {
  margin-left: 10px;
  padding: 10px 20px 12px;
}

.main-content {
  padding: 40px 15px;
}

.main-left-content {
  position: relative;
  display: inline-block;
  width: calc(100% - 450px);
  float: right;
  text-align: right;
  color: #ababab;
  padding-right: 50px;
}

.moreIcon {
  width: 20px;
  height: 20px;
  display: inline-block;
  position: absolute;
  top: 15px;
  right: 10px;
}

.price-list {
  margin-top: 20px;
}

.price-list li {
  padding-top: 20px;
  height: 140px;
}

.list-item {
  display: inline-block;
  height: 116px;
  width: calc(100% - 420px);
  line-height: 40px;
  padding-top: 24px;
  padding-left: 30px;
  vertical-align: middle;
}

.list-price-item {
  float: right;
  position: relative;
  display: inline-block;
  width: 200px;
  padding-top: 30px;
  text-align: right;
  color: #ababab;
  padding-right: 50px;
}

.list-price-item .moreIcon {
  width: 20px;
  height: 20px;
  display: inline-block;
  position: absolute;
  top: 45px;
  right: 10px;
}

.bottom-button {
  width: 100%;
  text-align: center;
  position: fixed;
  bottom: 0px;
}

.bottom-button .van-col {
  padding: 15px 0px;
}

.bottom-button .van-col:first-child {
  color: #a4a4a4;
  background-color: #ffffff;
}

.bottom-button .van-col:last-child {
  color: #ffffff;
  background: -webkit-linear-gradient(left, #ffac62, #ff3666);
  /* Safari 5.1 - 6.0 */
  background: -o-linear-gradient(right, #ffac62, #ff3666);
  /* Opera 11.1 - 12.0 */
  background: -moz-linear-gradient(right, #ffac62, #ff3666);
  /* Firefox 3.6 - 15 */
  background: linear-gradient(to right, #ffac62, #ff3666);
}

.dateModel .van-actionsheet__content {
  position: relative;
}

.dateModel .dateType {
  width: 100%;
  position: absolute;
  top: 120px;
  z-index: 1;
  text-align: center;
}

.dateModel .dateType span {
  display: inline-block;
  border: 1px solid #ff9aa5;
  color: #ff9aa5;
  padding: 15px 40px;
}

.dateModel .dateType span.active {
  background-color: #ff5162;
  color: #ffffff;
}

.dateModel .dateTitle {
  position: absolute;
  z-index: 1;
  width: 100%;
  text-align: center;
  top: 20px;
  font-size: 28px;
}
</style>
<style>
.dateModel .van-picker-column:nth-child(1) {
  position: absolute;
  width: 50%;
  bottom: 0px;
  height: calc(100% - 60px) !important;
}

.dateModel .van-picker-column:nth-child(2) {
  position: absolute;
  width: 50%;
  margin-left: 50%;
  bottom: 0px;
  height: calc(100% - 60px) !important;
}
.dateModel .van-picker-column .van-picker-column__item {
  line-height: 44px;
}


.dateModel .typeYear .van-picker-column:nth-child(1) {
  left: 25%;
}
.dateModel .van-hairline--top-bottom{
  top: 76%;
}

.dateModel .typeYear .van-picker-column:nth-child(2) {
  display: none;
}

.dateModel .van-picker__cancel {
  color: #cdcdcd;
}

.dateModel .van-picker__confirm,
.dateModel .van-picker-column__item--selected {
  color: #ff5062;
}
</style>