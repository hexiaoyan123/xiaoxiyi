<template>
  <div class="goods-list-out-cont produce-cont order-goods-list">
    <Vheader name="订单支付"></Vheader>
    <div class="content">
      <i class="iconFont">
        <img src="../../assets/goods/xinxi.png" width="100%" />
      </i>
      <p class="successText">获得收益</p>
      <p class="shouyi-money">￥{{userFundLog.changeFund | priceFormat}}</p>
      <!-- <p class="money-status">已到账</p> -->
      <van-row>
        <van-col span="10">消费人：</van-col>
        <van-col span="14">{{userFundLog.nickname}}</van-col>
        <van-col span="10">消费时间：</van-col>
        <van-col span="14">{{userFundLog.updateTime | dateFormat}}</van-col>
        <van-col span="10">消费金额：</van-col>
        <van-col span="14">￥{{userFundLog.changeFund | priceFormat}}</van-col>
        <van-col span="10">消费类型：</van-col>
        <van-col span="14">{{userFundLog.operatorCategory | operatorCategoryFormat}}</van-col>
      </van-row>
      <p class="warn-content">
        <i>
          <img src="../../assets/goods/warn.png" width="100%" height="100%" />
        </i> 会员消费成功未到账 ？
      </p>
    </div>
  </div>
</template>

<script>
import Vheader from "../HeaderV";
export default {
  name: "incomeInfo",
  components: {
    Vheader
  },
  data() {
    return {
      // 用户资金日志
      userFundLog: {}
    };
  },
  created() {
    // 用户资金日志
    let userFundLog = JSON.parse(
      window.localStorage.getItem("clickUserFundLog")
    );
    if (userFundLog) {
      this.userFundLog = userFundLog;
    } else {
      this.$router.back(-1);
    }
  },
  methods: {},
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
    },

    // 操作类型格式化
    operatorCategoryFormat(val) {
      if (val == 1) {
        return "推广收益";
      } else if (val == 2) {
        return "提现";
      } else if (val == 3) {
        return "消费";
      } else {
        return "";
      }
    }
  }
};
</script>

<style scoped>
.money-status {
  color: #d3d3d3;
}

.shouyi-money {
  color: #fe5062;
  font-size: 52px;
  margin-top: 20px;
}

.fenxiangP {
  color: #808080;
}

.content {
  padding: 0px 40px;
  text-align: center;
}

.iconFont {
  margin-top: 200px;
  width: 140px;
  height: 140px;
  display: inline-block;
}

.successText {
  color: #808080;
}

.van-row {
  margin-top: 30px;
}

.van-col--10 {
  text-align: right;
  line-height: 30px;
}

.van-col--14 {
  text-align: left;
  line-height: 30px;
  color: #d3d3d3;
}

.warn-content {
  color: #fe8103;
  position: relative;
  padding-left: 30px;
  display: inline-block;
  margin-top: 200px;
}

.warn-content i {
  position: absolute;
  left: 0px;
  top: 2px;
  width: 24px;
  height: 23px;
  display: inline-block;
}
</style>