<template>
  <div class="container incomeStatistics">
    <Vheader name="收入统计"></Vheader>
    <div class="main">
      <div class="top-content">
        <button class="select_yue" @click="toSelectDate">{{selectDate}}</button>
      </div>
      <div class="yuE-content">
        <van-row>
          <van-col span="12">
            <span>收入</span>
            <br />
            <span style="color: #FE5162;">
              <small>¥</small>
              <span v-html="moneyFormat(userFundLog.earning)" style="font-size: 18px;"></span>
            </span>
          </van-col>
          <van-col span="12" style="color: #C9C9C9;">
            <span>支出</span>
            <br />
            <span>
              <small>¥</small>
              <span v-html="moneyFormat(userFundLog.disbursement)" style="font-size: 18px;"></span>
            </span>
          </van-col>
        </van-row>
      </div>
      <div class="main-content">
        <div style="color: #4C4C4C;">收入排行榜</div>
        <ul class="price-list">
          <li v-for="val in userFundLog.userFundLogList" :key="val.id">
            <i class="fontIcon">
              <!-- <img src="../../assets/goods/user.png" width="100%" height="100%" /> -->
              <img :src="val.imageUrl" width="100%" height="100%" v-if="val.imageUrl" />
              <img src="../../assets/user.jpg" width="100%" height="100%" v-else />
            </i>
            <div class="list-item">
              <!-- <span style="color: #888888;">会员1</span>
              <br />-->
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
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import Vheader from "../HeaderV";
export default {
  name: "incomeStatistics",
  components: {
    Vheader
  },
  data() {
    return {
      // 用户资金日志
      userFundLog: {
        earning: 0,
        disbursement: 0,
        userFundLogList: []
      },

      //用户ID
      userId: "",

      dateType: "year-month",
      datePopup: false,
      currentDate: new Date("2019-10"),
      selectDate: "2019年10月"
    };
  },
  created() {
    // 用户ID
    let userId = JSON.parse(window.localStorage.getItem("clickUserId"));
    if (userId) {
      this.userId = userId;
    } else {
      this.$router.back(-1);
    }

    this.listUserFundLog(userId);
  },
  methods: {
    // 获取用户资金日志
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
        return "0";
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

.incomeStatistics {
  height: 100%;
  padding-top: 105px;
  background-color: #f8f8f8;
}
/*.wallet-my .main{
		height: calc(100% - px);
		overflow-y: auto;
	}*/

.fontIcon {
  display: inline-block;
  width: 140px;
  height: 140px;
  float: left;
}

.fontIcon img {
  border-radius: 50%;
}

.top-content {
  padding: 5px 20px;
  height: 80px;
  text-align: center;
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

.select_yue {
  border: 1px solid #fed2d6;
  color: #fe6776;
  background-color: transparent;
  border-radius: 30px;
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
  line-height: 80px;
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
  padding-right: 10px;
}

.list-price-item .moreIcon {
  width: 20px;
  height: 20px;
  display: inline-block;
  position: absolute;
  top: 45px;
  right: 10px;
}
</style>