<template>
  <div class="product-cont" ref="productGoodsCont" @touchmove="sMove" @touchstart="sStart">
    <!-- 轮播图开始 -->
    <div class="goods-top" v-if="goods.imageUrl">
      <van-swipe>
        <van-swipe-item v-for="(item, index) in goods.imageUrl.split(',')" :key="index">
          <img :src="$init.imgIp + item" @load="imgLoad(index)" />
        </van-swipe-item>
      </van-swipe>
      <div
        class="good-assemble-bg"
        v-if="goods.groupLimitNum > 0 && goods.groupStartTime < currentTimestamp && goods.groupEndTime > currentTimestamp"
      >
        <div class="good-assemble">
          <i class="clock"></i>限时拼团
          <span class="timeSpan">{{goods.groupEndTime2}}</span>后结束
          <!-- &nbsp;&nbsp;&nbsp;已拼<span class="assemble-count">1891</span>件 -->
        </div>
      </div>

      <!-- 比价开始 -->
      <div
        v-show="internetPrice.val"
        class="gradation bijia-cont clearfix"
        :class="{'bijia-cont-open': showB}"
        v-swipeleft="openB"
        v-swiperight="closeB"
      >
        <div class="bijia-logo">
          <figure class="img">
            <img src="@/assets/xb/bijia.png" alt />
          </figure>
        </div>
        <div class="bijia-link unoverflow" @click="goLink(internetPrice.val)">
          <div class="unoverflow bijia-link-in">{{internetPrice.show}}</div>
        </div>
      </div>
      <!-- 比价结束 -->
    </div>
    <!-- 轮播图结束 -->

    <!-- 商品信息开始 -->
    <div class="goods-info">
      <h3>{{goods.name}}</h3>
      <p v-show="goods.digest">{{goods.digest}}</p>
      <div class="goods-info-many on">
        <div class="goods-info-many-item1">分享拿佣金</div>
        <div class="goods-info-many-item2">
          <div class="sanjiao"></div>
        </div>
        <div class="goods-info-many-good">
          <figure class="img">
            <img src="@/assets/xb/good.png" alt />
          </figure>优选商品
          <div @click="sharecard">
            <img style="margin:0 5px; height: 16px; width: 16px;" src="@/assets/xb/share.png" />
          </div>
        </div>
        <small>¥</small>
        <span v-if="specGood.price" class="priceSpan" v-html="moneyFormat(specGood.price)"></span>
        <span
          v-else-if="goods.resProductSkuList && goods.resProductSkuList[0]"
          class="priceSpan"
          v-html="moneyFormat(goods.resProductSkuList[0].salesPrice)"
        ></span>
        <span
          class="ptzx"
          v-if="goods.groupLimitNum > 0 && goods.groupStartTime < currentTimestamp && goods.groupEndTime > currentTimestamp"
        >拼团专享</span>
      </div>
      <div class="goods-info-market">
        <del>原价：{{specGood.marketPrice | moneyFormat}}元</del>
      </div>
    </div>
    <!-- 商品信息结束 -->

    <!-- 信息块开始 -->
    <div class="base-list">
      <div class="base-list-item">
        <div class="base-list-item-left" v-if="specGood.id">已选择： {{specGood.message}}</div>
        <div class="base-list-item-left" v-if="! specGood.id">请选择</div>
        <div class="base-list-item-right" @click="openSelect">
          <i class="icon-more"></i>
        </div>
      </div>
    </div>
    <!-- 信息块结束 -->

    <!-- 服务保障开始 -->
    <div class="base-list" @click="goEv(3)">
      <div class="base-list-item">
        <div class="base-list-item-left" v-if="specGood.id">服务保障 & 推广说明</div>
        <div class="base-list-item-right">
          <i class="icon-more"></i>
        </div>
      </div>
    </div>
    <!-- 服务保障结束 -->

    <!-- 拼团开始 -->
    <div
      class="base-list"
      v-if="goods.groupLimitNum > 0 && goods.groupStartTime < currentTimestamp && goods.groupEndTime > currentTimestamp"
    >
      <div class="base-list-item">
        <div class="base-list-item-left">{{groupOrderList.length}}人在拼团, 可直接参与</div>
        <div class="base-list-item-right ping-name-right" @click="goAssembleList">
          more
          <i class="icon-more"></i>
        </div>
      </div>
      <div
        class="base-list-item assemble-item"
        v-for="(val, index) in groupOrderList"
        v-if="index < 4"
        :key="val.id"
      >
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

    <!-- 评价开始 -->
    <div class="base-list">
      <div class="base-list-item">
        <div class="base-list-item-left">
          评价 ({{ev.count}})
          <van-rate
            v-if="goods.id == '190919144356915241'"
            v-model="ev.score"
            disabled
            :size="12"
            disabled-color="#ff5162"
          />
          <span class="pingfen" v-if="goods.id == '190919144356915241'" v-html="moneyFormat2(8.8)"></span>
        </div>
        <div class="base-list-item-right ping-name-right" @click="goEv(4)">
          more
          <i class="icon-more"></i>
        </div>
      </div>
      <div class="base-list-item" v-for="(item, index) in ev.list" :key="index">
        <div class="base-list-item-left">
          <p class="base-list-item-top">
            <van-rate v-model="item.score" disabled :size="12" disabled-color="#ff5162" />
          </p>
          <p>{{item.content}}</p>
        </div>
        <div class="base-list-item-right">{{item.nickname}}</div>
      </div>
      <div v-show="ev.list.length == 0" class="noData">暂无数据！</div>
    </div>
    <!-- 评价结束 -->

    <div class="getmore">
      <i class="iconfont">&#xe66a;</i>
      <span class="getmore-text">上拉查看图文详情</span>
    </div>
    <!-- 拼团弹出结束 -->
    <!-- 分享弹出卡片-->
    <div class="sharelayer" v-if="shareStatus" @click="showclose">
      <div class="share">
        <i class="iocn-close" id="choice-close" @click="showclose"></i>
        <div class="sharediv">
          <div class="friends" align="center" @click="guide()">
            <figure class="img">
              <img src="@/assets/xb/pyq.png" width="50px" height="50px" alt />
            </figure>
            <p class="title">分享至朋友圈</p>
          </div>
          <div class="wechat" align="center" @click="guide()">
            <figure class="img">
              <img src="@/assets/xb/wx.png" width="50px" height="50px" alt />
            </figure>
            <p class="title">分享至微信好友</p>
          </div>
          <div class="saveimg" align="center" @click="goPath('/productShare/')">
            <figure class="img">
              <img src="@/assets/xb/baocun.png" width="50px" height="50px" alt />
            </figure>
            <p class="title">保存至手机</p>
          </div>
        </div>
      </div>
    </div>
    <!-- 分享朋友圈指引 -->
    <div class="mongolia" v-if="guideStatus" @click="guideShow">
      <div class="arrow">
        <img src="@/assets/xb/arrow.png" width="50px" height="50px" alt />
      </div>
      <div class="titleH2">
        <p class="titlep">
          点击右上角
          <span>
            <img src="@/assets/xb/dot.png" width="30px" height="24px" alt />
          </span>,转发给好友
        </p>
      </div>
      <div class="guide">
        <div class="sharediv">
          <div class="friends" align="center">
            <figure class="img">
              <img src="@/assets/xb/forward.png" width="50px" height="50px" alt />
            </figure>
            <p class="title">发送给朋友</p>
          </div>
          <div class="wechat" align="center">
            <figure class="img">
              <img src="@/assets/xb/pyq1.png" width="50px" height="50px" alt />
            </figure>
            <p class="title">分享到朋友圈</p>
          </div>
          <div class="saveimg" align="center">
            <figure class="img">
              <img src="@/assets/xb/collect.png" width="50px" height="50px" alt />
            </figure>
            <p class="title">收藏</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "ProductGoods",
  props: ["info", "spec"],
  components: {},
  data() {
    return {
      shareStatus: false,
      guideStatus: false,
      choice: "",
      countDown: "", //倒计时
      // 轮播图
      bannerImages: [],
      channel: this.$route.params.channel, // 商品渠道
      // 评价
      ev: {
        count: "", // 评价条数
        list: [] // 评价列表
      },
      showB: false, // 打开比价
      internetPrice: {}, // 比价

      // 旧的滚动距离
      startScroll: 0,
      touchStart: 0,
      touchCurrent: 0,

      // 当前时间戳
      currentTimestamp: Date.parse(new Date()) / 1000,
      // 拼团订单
      groupOrderList: [],
      // 选中拼团订单
      selectedGroupOrder: {},
      // 选中拼团订单参与者信息
      selectedGroupOrderParticipantInformation: []
    };
  },
  created() {
    setTimeout(this.countTime, 500);
    this.countTime();
    this.getReview();
    // 比价配置
    if (this.info.internetPrice) {
      try {
        this.internetPrice = JSON.parse(this.info.internetPrice)[0];
      } catch (err) {
        this.internetPrice = {};
      }
    }

    // 获取拼团订单
    this.listGroupOrder();
  },
  methods: {
    initChoice(data) {
      this.choice = data;
    },
    toAssemble(val) {
      this.$emit("toAssemble", val, this.goods);
    },
    // 获取拼团订单
    listGroupOrder() {
      let vm = this;
      vm.$http
        .get("/panda-shop-api/v2/listGroupOrder/" + this.$route.params.id)
        .then(function(response) {
          if ("1000" == response.data.code) {
            if (response.data.result) {
              vm.groupOrderList = response.data.result;
            }
          } else {
            vm.$toast(response.data.msg);
          }
        })
        .catch(function(error) {
          vm.$toast("请求超时，请稍后再试！");
        });
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
            this.$set(this.groupOrderList[index], "overTime2", "00:00:00");
            clearInterval(s);
          }
        }, 50);
      });
    },

    // 拼单列表
    goAssembleList() {
      sessionStorage.setItem(
        "groupOrderList",
        JSON.stringify(this.groupOrderList)
      );
      sessionStorage.setItem("groupOrderGoods", JSON.stringify(this.goods));
      sessionStorage.setItem("choice", JSON.stringify(this.choice));
      this.$router.push("/AssembleList");
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
    },
    moneyFormat2(value) {
      if (value) {
        var moneyValue = value.toFixed(1).split(".");
        return "<big>" + moneyValue[0] + "</big>." + moneyValue[1];
      } else if (value === 0) {
        return "0";
      } else {
        return "";
      }
    },
    // 首页图片加载完成
    imgLoad(index) {
      if (index == 0) {
        this.$store.commit("ctrlLoader", false);
      }
    },
    // 移动开始
    sStart(e) {
      let vm = this;
      vm.startScroll = vm.$refs.productGoodsCont.scrollTop;
      vm.touchStart = e.targetTouches[0].pageY;
    },
    // 移动结束
    sMove(e) {
      let vm = this;
      vm.touchCurrent = e.targetTouches[0].pageY;

      var touchesDiff = vm.touchCurrent - vm.touchStart;

      var slide = vm.$refs.productGoodsCont;
      var onlyScrolling =
        slide.scrollHeight > slide.offsetHeight &&
        ((touchesDiff < 0 && vm.startScroll === 0) ||
          (touchesDiff > 0 &&
            vm.startScroll === slide.scrollHeight - slide.offsetHeight) ||
          (vm.startScroll > 0 &&
            vm.startScroll < slide.scrollHeight - slide.offsetHeight));
      if (onlyScrolling) {
        e.stopPropagation();
      }
    },
    // 打开比价
    openB() {
      this.showB = true;
    },
    // 关闭比价
    closeB() {
      this.showB = false;
    },
    // 更多评价
    goEv(num) {
      this.$emit("gotoev", num);
    },
    // 打开选择框
    openSelect() {
      this.$emit("showModu", 3);
    },

    // 分享跳转路径（保存图片功能）
    goPath(path) {
      this.$router.push(path + this.goods.id);
    },
    //分享卡片
    sharecard() {
      this.shareStatus = true;
    },
    //点击关闭
    showclose() {
      this.shareStatus = false;
    },
    //点击出现蒙层指引
    guide() {
      this.guideStatus = true;
    },
    guideShow() {
      this.guideStatus = false;
      //   this.shareStatus = false;
    },
    // 获取评价信息
    getReview() {
      let vm = this;
      let postData = {
        productId: this.$route.params.id,
        pageNo: 1,
        pageSize: 10
      };
      this.$http
        .post("/panda-shop-api/v1/pageReview", postData)
        .then(response => {
          if ("1000" == response.data.code) {
            vm.ev = {
              count: response.data.count, // 评价条数
              list: response.data.result ? response.data.result : [] // 评价列表
            };
            // if (this.goods.id == "190919144356915241") {
            //   vm.ev = {
            //     count: 23397, // 评价条数
            //     score: 4,
            //     list: [
            //       {
            //         content: "目前用起来还不错,性能给力",
            //         score: 4,
            //         nickname: "小***m"
            //       },
            //       {
            //         content: "目前用起来还不错,性能给力",
            //         score: 4,
            //         nickname: "小***m"
            //       },
            //       {
            //         content: "目前用起来还不错,性能给力",
            //         score: 4,
            //         nickname: "小***m"
            //       }
            //     ]
            //   };
            // }
          } else {
            vm.$toast(response.data.msg);
          }
        })
        .catch(function(error) {
          vm.$toast("请求超时，请稍后再试！");
        });
    },
    // 跳转
    goLink(url) {
      if (url) {
        window.location.href = url;
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
  },
  computed: {
    goods() {
      let obj = {};
      if (this.info) {
        obj = this.info;
      }
      return obj;
    },
    specGood() {
      let obj = {};
      return this.spec;
    }
  },
  watch: {
    info() {
      // 比价配置
      if (this.info.internetPrice) {
        try {
          this.internetPrice = JSON.parse(this.info.internetPrice)[0];
        } catch (err) {
          this.internetPrice = {};
        }
      }
    }
  }
};
</script>

<style scoped>
.ptzx {
  margin-left: 10px;
  font-size: 24px;
  display: inline-block;
  border: 1px solid #ff5162;
  border-radius: 10px;
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
  position: absolute;
  top: 10px;
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

.good-assemble-bg {
  background-color: #f5f5f5;
  padding-top: 1px;
  padding-bottom: 20px;
}

.good-assemble-bg .good-assemble {
  text-align: center;
  line-height: 60px;
  background-color: #ffffff;
  position: relative;
}

.good-assemble-bg .good-assemble .clock {
  width: 28px;
  height: 28px;
  display: inline-block;
  background-image: url(../../assets/goods/clock.png);
  background-size: 100% 100%;
  position: absolute;
  top: 15px;
  margin-left: -40px;
}

.good-assemble-bg .good-assemble span {
  color: #ff5162;
}

.getmore {
  text-align: center;
  font-size: 24px;
  padding: 20px;
  line-height: 40px;
  background-color: #fff;
  color: #818181;
}

.getmore-text {
  margin-left: 10px;
}

.product-cont {
  height: 100%;
  overflow: auto;
}

.goods-top {
  width: 100%;
  position: relative;
  overflow: hidden;
}

.goods-top img {
  display: block;
  width: 100%;
}

.goods-top .van-swipe-item img {
  height: 300px;
}

.goods-info {
  background-color: #ffffff;
  padding: 10px 20px;
  border-top: 1px solid #eee;
  margin-bottom: 20px;
}

.goods-info h3 {
  font-size: 24px;
  line-height: 38px;
  padding-bottom: 10px;
}

.goods-info p {
  font-size: 20px;
  color: #c1c1c1;
  line-height: 38px;
}

.goods-info-many {
  font-size: 36px;
  line-height: 50px;
  position: relative;
  color: #ff5162;
  margin-top: 20px;
}

.goods-info-many small {
  font-size: 18px;
}

.goods-info-market {
  font-size: 22px;
  color: #dddddd;
  line-height: 38px;
}

.base-list {
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

.goods-info-many-item1 {
  position: absolute;
  right: 0;
  top: -30px;
  color: #757575;
  border: 1px solid #c8c8c8;
  background-color: #f9f9f9;
  padding: 0px 10px;
  line-height: 38px;
  font-size: 20px;
  border-radius: 5px;
}

.goods-info-many-item2 {
  position: absolute;
  right: 10px;
  top: 9px;
}

.goods-info-many-item2 .sanjiao {
  position: relative;
  width: 0;
  height: 0;
  border-width: 10px 10px 0;
  border-style: solid;
  border-color: #c8c8c8 transparent transparent;
}

.goods-info-many-item2 .sanjiao:after {
  content: "";
  position: absolute;
  top: -10px;
  left: -8px;
  border-width: 8px 8px 0;
  border-style: solid;
  border-color: #f9f9f9 transparent transparent;
}

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
/* 比价 */

.bijia-cont {
  position: absolute;
  right: -0;
  top: 50px;
  height: 76px;
  line-height: 76px;
  border-top-left-radius: 38px;
  border-bottom-left-radius: 38px;
  font-size: 28px;
  color: #fff;
  overflow: hidden;
  transition: all 0.8s;
  min-width: 132px;
  width: auto;
}

.bijia-logo {
  float: left;
  width: 132px;
  box-sizing: border-box;
  padding-left: 20px;
  padding-top: 18px;
  margin-right: 10px;
}

.bijia-logo .img {
  display: block;
  height: 41px;
  width: auto;
  margin: 0 auto;
}

.bijia-logo .img img {
  display: block;
  width: 100%;
}

.bijia-cont-open .bijia-link {
  width: 300px;
  text-align: center;
}

.bijia-link {
  width: 0;
  transition: all 0.8s;
  box-sizing: border-box;
}

.bijia-link-in {
  padding-right: 20px;
  padding-left: 20px;
}
.sharelayer {
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.3);
  position: fixed;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
}

.share {
  position: fixed;
  width: 85%;
  height: 200px;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  border-radius: 10px;
  background-color: #f8f8f8;
  z-index: 1000;
}
.share .iocn-close {
  position: absolute;
  top: 2.4vw;
  right: 2.4vw;
  font-size: 4.267vw;
  content: "viewport-units-buggyfill; top: 2.4vw; right: 2.4vw; font-size: 4.267vw";
}
.share .sharediv {
  float: left;
  margin-left: 7%;
  width: 88%;
  margin-top: 2%;
  height: 200px;
}
.sharediv div {
  float: left;
  /* background-color: #7fffd4; */
  margin-top: 2.5%;
  margin-left: 2.5%;
  width: 30%;
}
.sharediv div .title {
  padding-top: 3px;
}
.mongolia {
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.3);
  position: fixed;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
}
.mongolia .guide {
  position: fixed;
  width: 85%;
  height: 180px;
  left: 50%;
  padding-top: 10px;
  top: 30%;
  transform: translate(-50%, -50%);
  border-radius: 10px;
  background-color: #f8f8f8;
}
.mongolia .arrow {
  position: fixed;
  right: 10%;
  top: 2%;
}
.mongolia .titleH2 {
  position: fixed;
  color: #fff;
  width: 85%;
  height: 80px;
  left: 58%;
  padding-top: 10px;
  top: 15%;
  transform: translate(-50%, -50%);
}
.mongolia .titleH2 .titlep {
  display: block;
  font-size: 38px;
}
</style>