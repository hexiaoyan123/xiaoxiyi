<template>
  <div>
    <Vheader name="拼单列表"></Vheader>
    <!-- 拼团开始 -->
    <div class="base-list">
      <div class="base-list-item" style="line-height: 30px;border-bottom: 1px solid #DDDDDD;">
        <div class="base-list-item-left">{{groupOrderList.length}}人在拼团, 可直接参与</div>
      </div>
      <div class="base-list-item assemble-item" v-for="val in groupOrderList" :key="val.id">
        <div class="assemble-user-item">
          <i class="assemble-user-img">
            <img :src="val.userImageUrl" style="width: 100%; height: 100%;border-radius: 60px;" />
          </i>
          {{val.userNickname}}
        </div>
        <div class="assemble-other-item">
          <p>还差{{val.completionQuantity - val.participantQuantity}}人拼成</p>
          <p>剩余{{val.overTime2}}</p>
        </div>
        <div class="assemble-to-item">
          <span @click="toAssemble(val)">去拼团</span>
        </div>
      </div>
    </div>
    <!-- 拼团结束 -->

    <!-- 拼团弹出开始 -->
    <van-dialog class="assembleDialog" v-model="assembleDialog" :show-confirm-button="1!=1">
      <i id="choice-close" class="iocn-close" @click="assembleDialog = false"></i>
      <ul>
        <li class="assembleDialog-user">参与{{selectedGroupOrder.userNickname}}的拼单</li>
        <li class="assembleDialog-other">
          仅剩{{ selectedGroupOrder.other}}个名额，
          <span>{{selectedGroupOrder.overTime2}}</span>后结束
        </li>
        <li class="assembleDialog-info">
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
        <li class="assembleDialog-btn">
          <span class="assemble-btn" @click="toPin">参与拼单</span>
        </li>
      </ul>
    </van-dialog>
    <!-- 拼团弹出结束 -->
  </div>
</template>

<script>
import Vheader from "../HeaderV";
export default {
  name: "AssembleList",
  components: {
    Vheader
  },
  data() {
    return {
      countDown: "", //倒计时
      assembleDialog: false,

      // 拼团订单
      groupOrderList: [],
      // 选中拼团订单
      selectedGroupOrder: {},
      // 选中拼团订单参与者信息
      selectedGroupOrderParticipantInformation: [],
      // 获取拼团订单商品
      goods: {}
    };
  },
  created() {
    // 获取拼团订单
    this.groupOrderList = JSON.parse(sessionStorage.getItem("groupOrderList"));
    setTimeout(this.countTime(), 500);
    // 获取拼团订单商品
    this.goods = JSON.parse(sessionStorage.getItem("groupOrderGoods"));
  },
  methods: {
    // 参与拼团
    toPin() {
      sessionStorage.setItem("goods", JSON.stringify(this.goods));
      sessionStorage.setItem(
        "selectedGroupOrder",
        JSON.stringify(this.selectedGroupOrder)
      );
      this.$router.push("/AssembleOrders");
    },

    // 去拼团
    toAssemble(val) {
      this.selectedGroupOrder = val;
      this.selectedGroupOrder.other =
        this.selectedGroupOrder.completionQuantity -
        this.selectedGroupOrder.participantQuantity;
      this.selectedGroupOrderParticipantInformation = JSON.parse(
        val.participantInformation
      );
      this.assembleDialog = true;
    },

    countTime() {
      this.groupOrderList.forEach((item, index) => {
        var endDate = new Date(item.overTime * 1000); //后一天
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
              this.groupOrderList[index],
              "overTime2",
              h + " : " + m + " : " + s + " . " + ms
            );
          }
          if (leftTime <= 0) {
            //将倒计时赋值到div中
            this.$set(this.groupOrderList[index], "overTime2", "00:00:00");
            clearInterval(s);
          }
        }, 50);
      });
    }
  }
};
</script>

<style scoped>
.assembleDialog .assembleDialog-btn {
  padding: 20px 0px 60px;
}

.assembleDialog .assembleDialog-info .fontIcon {
  display: inline-block;
  width: 100%;
  height: 100%;
}

.assembleDialog .assembleDialog-info .fontIcon img {
  border-radius: 50%;
}

.assembleDialog .assembleDialog-info .pinzhu {
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
  font-size: 24px;
}

.assembleDialog .assembleDialog-info .pinzhuCol {
  position: relative;
}

.assembleDialog .assembleDialog-info {
  padding: 50px 0px;
}

.assembleDialog .assembleDialog-user {
  font-size: 36px;
  color: #525252;
  padding: 50px 0px 5px;
}

.assembleDialog .assembleDialog-other {
  color: #808080;
  padding: 20px 0px;
}

.assembleDialog .assembleDialog-other span {
  color: #ff5162;
}

.assembleDialog ul li {
  text-align: center;
}

.assemble-btn {
  display: inline-block;
  padding: 20px 30px;
  border: 1px solid #ff5162;
  color: #ff5162;
  border-radius: 30px;
  line-height: 20px;
}

.assembleDialog {
  padding: 30px 40px;
}

.assembleDialog #choice-close {
  float: right;
}

.assemble-item > div {
  line-height: 120px;
}

.assemble-item .assemble-user-item {
  width: 80px;
  position: relative;
  padding-left: 140px;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.assemble-item .assemble-user-item .assemble-user-img {
  display: inline-block;
  width: 120px;
  height: 120px;
  /* background-image: url(../../assets/goods/user.png); */
  background-size: 100% 100%;
  border-radius: 60px;
  position: absolute;
  left: 0px;
}

.assemble-item .assemble-user-item .assemble-user-img img {
}

.assemble-item .assemble-other-item {
  line-height: 40px;
  padding-top: 15px;
  width: calc(100% - 400px);
  text-align: right;
  padding-right: 20px;
  color: #b5b5b5;
}

.assemble-item .assemble-to-item {
  width: 180px;
  text-align: center;
}

.assemble-item .assemble-to-item span {
  display: inline-block;
  padding: 20px 35px;
  border: 1px solid #ff5162;
  color: #ff5162;
  border-radius: 30px;
  line-height: 20px;
}

.base-list {
  margin-top: 75px;
  background-color: #ffffff;
  margin-bottom: 20px;
}

.base-list-item {
  display: flex;
  padding: 20px;
  line-height: 44px;
  font-size: 24px;
  color: #818181;
}

.base-list-item-left {
  flex: 10;
  position: relative;
}

.base-list-item-left > .van-rate {
  display: inline-block;
  margin-left: 10px;
  position: absolute;
  top: 1px;
}

.base-list-item-left > .pingfen {
  margin-left: 190px;
  color: #ff5162;
}

.base-list-item-right .iconfont {
  font-size: 24px;
}

.ping-name-right {
  padding-right: 30px;
  position: relative;
  font-size: 28px;
}

.ping-name-right .icon-more {
  position: absolute;
  right: 0;
  top: 11px;
}
/* 优选商城 */

.goods-info-many-good {
  position: absolute;
  right: 0;
  top: 20px;
  display: flex;
  font-size: 24px;
  height: 38px;
  line-height: 38px;
  padding-left: 38px;
  color: #4d4d4d;
}

.goods-info-many-good .img {
  height: 28px;
  width: 28px;
  position: absolute;
  left: 0;
  top: 4px;
}

.goods-info-many-good .img img {
  display: inline-block;
  height: 100%;
}
</style>