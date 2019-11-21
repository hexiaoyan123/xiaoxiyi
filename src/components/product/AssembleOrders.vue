<template>
  <div class="goods-list-out-cont produce-cont order-goods-list assembleOrders">
    <Vheader name="商品订单"></Vheader>
    <ul class="list">
      <li class="new-item">
        <div class="new-item-in clearfix">
          <figure class="new-item-img">
            <div class="hot"></div>
            <img :src="goods.imageUrl.split(',')[0] | imgSpecilize" />
          </figure>
          <div class="new-item-info">
            <div class="new-item-info-title">{{goods.name}}</div>
            <div>
              <span class="ptzx">拼团专享</span>
            </div>
            <div class="new-item-info-many">
              <small>￥</small>
              <span class="priceSpan" v-html="moneyFormat(choice.price)"></span>
              <span class="assembled">
                已拼
                <span>1981</span>件
              </span>
            </div>
          </div>
        </div>
      </li>
    </ul>
    <ul class="assemblUl">
      <li class="assemblUl-info">
        <van-row>
          <van-col
            :span="(24 - selectedGroupOrder.completionQuantity * 6) / 2"
            v-if="selectedGroupOrderParticipantInformation.length < 4"
          >&nbsp;</van-col>
          <van-col
            class="pinzhuCol"
            span="6"
            v-for="val in selectedGroupOrderParticipantInformation"
            :key="val.userId"
          >
            <i class="fontIcon">
              <img style="margin-top: 4%;" width="90%" height="90%" :src="val.userImageUrl" />
            </i>
            <span class="pinzhu" v-if="selectedGroupOrder.userId == val.userId">拼主</span>
          </van-col>
          <van-col span="6" v-for="index in selectedGroupOrder.other" :key="index">
            <i class="fontIcon">
              <img src="../../assets/goods/wenhao.png" width="100%" height="100%" />
            </i>
          </van-col>
          <van-col
            :span="(24 - selectedGroupOrder.completionQuantity * 6) / 2"
            v-if="selectedGroupOrderParticipantInformation.length < 4"
          >&nbsp;</van-col>
        </van-row>
      </li>
      <li class="assemblUl-other">
        仅剩
        <span>&nbsp;1&nbsp;</span>个名额,
        <span>&nbsp;{{goods.groupEndTime2.replace(/\s*/g,"")}}&nbsp;</span>后结束
        <p
          style="margin-top: 5px;"
        >注 ： 人满发货 , 人不满退款 , {{goods.groupEndTime2.split('.')[0].replace(/\s*/g,"")}}后结束</p>
      </li>
    </ul>
    <div class="btn" @click="show = true">参与 {{selectedGroupOrder.userNickname}} 的拼团</div>
    <!-- 选择商品参数 -->
    <van-actionsheet v-model="show" title>
      <div class="choice-zhan"></div>
      <div class="choice-cont">
        <div class="choice-top">
          <figure class="img">
            <img :src="goods.imageUrl.split(',')[0]  | imgSpecilize" alt />
          </figure>
          <h3>
            <small>¥</small>
            <span class="priceSpan" v-html="moneyFormat(choice.price)"></span>
          </h3>
          <p>
            <del>原价：{{choice.marketPrice | moneyFormat}}元</del>
          </p>
          <p>库存：{{choice.stock}}件</p>
          <i class="iocn-close" id="choice-close" @click="show = false"></i>
        </div>

        <div class="choice-item" v-if="choice.list && choice.list.length > 0">
          <product-money :v-goods="choice.list" @checkGoods="selectChoice"></product-money>
        </div>
        <div class="choice-num-cont">
          <div class="choice-num-cont-left">数量</div>
          <div class="choice-num-cont-right">
            <div class="choice-num-reduce" @click="choiceNumReduce">
              <i class="iconfont">&#xe638;</i>
            </div>
            <div class="choice-num-show">{{choice.num}}</div>
            <div class="choice-num-add" @click="choiceNumAdd">
              <i class="iconfont">&#xe602;</i>
            </div>
          </div>
        </div>
        <div class="choice-ok gradation" @click="toPin">参与 {{selectedGroupOrder.userNickname}} 的拼团</div>
      </div>
    </van-actionsheet>
  </div>
</template>

<script>
import Vheader from "../HeaderV";
import ProductMoney from "./ProductMoney";
export default {
  name: "AssembleOrders",
  components: {
    Vheader,
    ProductMoney
  },
  data() {
    var selectedGroupOrder = JSON.parse(
      sessionStorage.getItem("selectedGroupOrder")
    );
    return {
      groupOrderList: [],
      show: false,
      countDown: "", //倒计时
      goods: JSON.parse(sessionStorage.getItem("goods")),
      choice: JSON.parse(sessionStorage.getItem("choice")),
      selectedGroupOrder: selectedGroupOrder,
      selectedGroupOrderParticipantInformation: JSON.parse(
        selectedGroupOrder.participantInformation
      )
    };
  },
  created() {
    setTimeout(this.countTime, 500);
  },

  methods: {
    // 数量 +
    choiceNumAdd() {
      this.choice.num += 1;
    },

    // 数量 -
    choiceNumReduce() {
      if (this.choice.num > 1) {
        this.choice.num -= 1;
      } else {
        this.choice.num = 1;
      }
    },
    toPin() {
      let obj = {};
      this.goods.resProductSkuList.forEach((e, i) => {
        if (e.id == this.selectedGroupOrder.productSkuId) {
          obj = e;
        }
      });
      let postData = {
        reqCartList: [
          {
            id: "",
            productId: this.goods.id,
            productSkuId: this.choice.id,
            weight: this.choice.weight,
            // 默认拼1件
            quantity: this.choice.num,
            salesPrice: this.choice.groupPrice,
            specification: this.choice.specification,
            name: this.goods.name,
            imageUrl: this.goods.imageUrl,
            shippingMethodId: this.goods.shippingMethodId,
            shippingMethodName: this.goods.shippingMethodName,
            storeId: this.goods.storeId,
            storeName: this.goods.storeName,
            check: true
          }
        ],
        receiverId: "",
        shippingMethodId: this.goods.shippingMethodId,
        remark: "",
        // 订单类型（1：普通订单；2：拼团订单；）
        orderCategory: 2,
        // 拼团订单ID
        groupOrderId: this.selectedGroupOrder.id
      };
      debugger;
      this.$cookies.set("goodsOrder", JSON.stringify(postData));
      sessionStorage.setItem("goodsOrder", JSON.stringify(postData));
      this.$router.push("/product/orderPay");
    },
    selectChoice(obj) {
      this.choice.id = obj.id;
      let newObj = {};
      this.choice.list.forEach((e, i) => {
        if (e.id == obj.id) {
          newObj = e;
        }
      });
      this.choice.price = newObj.salesPrice;
      this.choice.marketPrice = newObj.marketPrice;
      this.choice.message = obj.message;
      this.choice.stock = newObj.stock;
    },
    countTime() {
      var endDate = new Date(this.goods.groupEndTime * 1000); //后一天
      var s = setInterval(() => {
        var date = new Date();
        var now = date.getTime();

        var end = endDate.getTime();
        var leftTime = end - now; //时间差
        var d, h, m, s, ms;
        if (leftTime >= 0) {
          h = Math.floor((leftTime / 1000 / 60 / 60) % 24);
          m = Math.floor((leftTime / 1000 / 60) % 60);
          s = Math.floor((leftTime / 1000) % 60);
          ms = Math.floor(leftTime % 1000);
          if (ms < 10) {
            ms = "00" + ms;
          } else if (ms < 100) {
            ms = "0" + ms;
          }

          if (s < 10) {
            s = "0" + s;
          }
          if (m < 10) {
            m = "0" + m;
          }
          if (h < 10) {
            h = "0" + h;
          }
          //将倒计时赋值到div中
          this.$set(
            this.goods,
            "groupEndTime2",
            h + " : " + m + " : " + s + " . " + ms
          );
        }
        if (leftTime <= 0) {
          this.$set(this.goods, "groupEndTime2", "00:00:00");
          clearInterval(s);
        }
      }, 50);
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
    moneyFormat(value) {
      if (value) {
        return (value / 100).toFixed(2);
      } else if (value === 0) {
        return 0;
      } else {
        return "";
      }
    }
  }
};
</script>

<style scoped>
/* 选择商品参数 */

.van-actionsheet {
  background: transparent;
  border: none;
}

.choice-cont {
  background: #fff;
}

.choice-zhan {
  height: 40px;
}

.choice-top {
  padding: 18px 27px 18px 212px;
  position: relative;
  border-bottom: 1px solid #eee;
}

.choice-top .img {
  display: inline-block;
  height: 166px;
  width: 166px;
  border: 1px solid #eee;
  border-radius: 8px;
  position: absolute;
  bottom: 24px;
  left: 27px;
  overflow: hidden;
}

.choice-top .img img {
  display: inline-block;
  height: 100%;
  width: 100%;
}

.choice-top h3 {
  line-height: 50px;
  font-size: 36px;
  color: #ff6372;
}

.choice-top h3 small {
  font-size: 18px;
}

.choice-top p {
  line-height: 30px;
  font-size: 22px;
  color: #808080;
}

.choice-top p del {
  color: #dddddd;
}

#choice-close {
  position: absolute;
  top: 18px;
  right: 18px;
  font-size: 32px;
}

.choice-item {
  padding: 10px 27px;
  font-size: 24px;
  color: #818181;
}

.choice-item h3 {
  line-height: 56px;
}

.choice-item li {
  display: inline-block;
  line-height: 46px;
  padding: 0 26px;
  border-radius: 4px;
  border: 1px solid #eee;
  margin-bottom: 18px;
  margin-right: 18px;
}

.choice-item .on {
  color: #fff;
  background-color: #ff5162;
  border: 1px solid #ff5162;
}

.choice-num-cont {
  border-top: 1px solid #eee;
  border-bottom: 1px solid #eee;
  padding: 20px 30px;
  line-height: 60px;
  display: flex;
  color: #4d4d4d;
  font-size: 24px;
}

.choice-num-cont-left {
  flex: 10;
}

.choice-num-cont-right {
  text-align: right;
  border-radius: 5px;
  display: flex;
  text-align: center;
}

.choice-num-reduce {
  width: 60px;
  background-color: #e4e4e4;
  border-radius: 4px;
}

.choice-num-show {
  width: 100px;
}

.choice-num-add {
  width: 60px;
  background-color: #e4e4e4;
  border-radius: 4px;
}

.choice-ok {
  color: #fff;
  line-height: 90px;
  text-align: center;
  background-color: #ff5162;
  font-size: 28px;
}

.choice-ok-cont {
  display: flex;
}

.choice-ok-cont .choice-ok {
  flex: 5;
}

/* 选择商品参数结束*/

div.btn {
  width: 100%;
  text-align: center;
  line-height: 100px;
  position: absolute;
  color: #ffffff;
  font-size: 30px;
  bottom: 0px;
  background: -webkit-gradient(
    linear,
    left top,
    right top,
    from(#ff7f62),
    to(#ff3666)
  );
  background: linear-gradient(90deg, #ff7f62, #ff3666);
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
  background: -webkit-gradient(
    linear,
    left top,
    right top,
    from(#ff7f62),
    to(#ff3666)
  );
  background: linear-gradient(90deg, #ff7f62, #ff3666);
  top: 4px;
  right: 0px;
}

.assemblUl-info .pinzhuCol {
  position: relative;
}

.assemblUl-info {
  padding: 50px 0px;
}

.assemblUl-other {
  color: #808080;
  padding: 20px 0px;
}

.assemblUl-other span {
  color: #ff5162;
}

.assemblUl li {
  text-align: center;
}

.produce-cont {
  padding-top: 84px;
}

.assembled {
  font-size: 28px;
  color: #9a9a9a;
  float: right;
}

.assembled span {
  color: #ff5162;
}

.ptzx {
  margin-top: 20px;
  font-size: 24px;
  display: inline-block;
  border: 1px solid #ff5162;
  border-radius: 5px;
  line-height: 100%;
  padding: 2px 15px;
  color: #ffffff;
  background: -webkit-gradient(
    linear,
    left top,
    right top,
    from(#ff8164),
    to(#ff3666)
  );
  background: linear-gradient(90deg, #ff8164, #ff3666);
}

/* 新列表 */

.list {
  margin-top: 20px;
}

.new-item {
  padding: 6px 26px 20px 26px;
}

.new-item-in {
  padding: 20px;
  border-radius: 6px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1);
}

.hot {
  position: absolute;
  top: -1px;
  left: -1px;
  height: 85px;
  width: 85px;
  background: url(../../assets/xb/hot.png) no-repeat;
  background-size: 100%;
}

.new-item-img {
  float: left;
  display: block;
  width: 240px;
  height: 240px;
  border-radius: 10px;
  border: 1px solid #eee;
  overflow: hidden;
  position: relative;
}

.new-item-img img {
  display: block;
  height: 100%;
  width: 100%;
}

.new-item-info {
  width: 390px;
  float: right;
}

.new-item-info-name {
  font-size: 28px;
  line-height: 54px;
  color: #4d4d4d;
}

.new-item-info-title {
  font-size: 24px;
  color: #4d4d4d;
  line-height: 36px;
  margin-top: 10px;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 2;
  overflow: hidden;
}

.new-item-info-many {
  font-size: 36px;
  color: #ff5162;
  line-height: 42px;
  margin-top: 70px;
}

.new-item-info-many small {
  font-size: 18px;
}
</style>
<style>
.assembleOrders .van-hairline--bottom {
  display: none;
}
</style>
