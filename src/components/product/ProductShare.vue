<template>
  <div class="container">
    <div ref="imageDom" @click="clickGeneratePicture()">
      <div class="gradation" style="height: 90px;">
        <div style="float: right; padding-top: 10px; padding-right: 5px;">
          <p style="color: #FFF; font-size: 14px;">全球精品&nbsp; | &nbsp;正品保障&nbsp; | &nbsp;售后无忧</p>
        </div>
      </div>
      <div class="choice" style="padding-top: 40px; text-align: center; margin-top: -39px;">
        <p style="color: #000; font-weight: 800;">
          <span style="font-size: 20px;">"</span>
          <span style="font-size: 28px; color: red;">精选</span>
          <span style="font-size: 20px;">好物</span>
          <span style="font-size: 28px; color: red;">限时</span>
          <span style="font-size: 20px;">特卖</span>
          <span style="font-size: 20px;">"</span>
        </p>
      </div>
      <div class="imgdetail">
        <span class="optimal">优选商品</span>
        <div class="price">
          <!-- 原价 -->
          <span
            style="color: #FFF; background-color:#ff5162 ;font-size: 18px; border-top-left-radius: 15%;
    padding: 5px; position: absolute; bottom: 0; right: 0;"
            v-if="goods.resProductSkuList"
            v-html="moneyFormat(goods.resProductSkuList[0].salesPrice)"
          ></span>
        </div>
        <div style="text-align: center;">
          <img
            style="height: 80%; width: 80%;"
            v-if="goods.imageUrl"
            :src="$init.imgIp + goods.imageUrl.split(',')[0]"
          />
        </div>
      </div>
      <div style="padding: 10px; padding-top: 20px; height: 100px;">
        <div class="literaldetail">
          <p style="font-size: 18px; font-weight: 600;">{{goods.name}}</p>
          <p
            style="background-color:#C0C0C0; color:#EA5D6E; width: 65px;
            padding: 2px; border-top-right-radius: 3px;
                border-bottom-right-radius: 3px;"
          >
            <span
              style="background-color:#EA5D6E; color:#fff; border-top-left-radius: 3px;
                border-bottom-left-radius: 3px; padding:4px; margin-left: -3px;margin-right: 2px;"
            >热卖</span>限时
          </p>
          <br />
          <p
            style="margin-top: 0px; margin-bottom: 0px; font-size: 16px; font-weight: 600; color:#C0C0C0;"
          >来自{{$init.user.cacheUser.nickname}}的精选推荐</p>
        </div>
        <!-- 二维码 -->
        <div class="qrcode">
          <img
            style="margin-top: -10px; padding-top: 5px; margin-bottom: -5px; height: 100px; width: 100px;"
            :src="qrcodeImgUrl"
          />
          <p style="color:#C0C0C0;">扫码立享热卖价</p>
        </div>

        <!-- <del
            style="padding-left: 5px;"
            v-if="goods.resProductSkuList"
        >原价：{{goods.resProductSkuList[0].marketPrice | moneyFormat}}</del>-->
      </div>
    </div>
    <div
      class="form-btn"
      style="margin-top: 35px; margin-bottom: 10px;"
      @click="clickGeneratePicture()"
    >生成分享截图</div>

    <!-- 截图开始 -->
    <div class="sb-cont" v-show="showTitle" @click="showTitle = false">
      <div style="z-index: 9999;"></div>
      <figure class="img-cont">
        <p
          style="color: #FFF; font-size: 18px; font-weight: 800; padding-bottom: 10px;"
        >请长按图片，然后保存到手机</p>
        <img :src="imgUrl" />
        <p
          style="color: #FFF; font-size: 18px; font-weight: 800; padding-bottom: 10px;margin-top: 10px;"
        >保存图片，分享到微信</p>
      </figure>
    </div>
    <!-- 截图结束 -->
  </div>
</template>

<script>
import Vheader from "../HeaderV";
import html2canvas from "html2canvas";
export default {
  name: "ProductShare",
  components: {
    Vheader
  },
  data() {
    return {
      userImg:
        this.$init.user.cacheUser.imageUrl &&
        this.$init.user.cacheUser.imageUrl.indexOf("http://") >= 0
          ? this.$init.user.cacheUser.imageUrl
          : this.$init.imgIp + this.$init.user.cacheUser.imageUrl,
      goods: "",
      imgUrl: "",
      showTitle: false,
      qrcodeImgUrl: ""
    };
  },
  created() {
    this.getGoods();
    this.getShareQrcode();
  },
  methods: {
    // 获取商品详情
    getGoods() {
      let vm = this;
      this.$http
        .get("/panda-shop-api/v1/getProduct/" + this.$route.params.id)
        .then(function(response) {
          if ("1000" == response.data.code) {
            vm.goods = response.data.result;
          } else {
            vm.$toast(response.data.msg);
          }
        })
        .catch(function(error) {
          vm.$toast("请求超时，请稍后再试！");
        });
    },

    // 获取二维码
    getShareQrcode() {
      var vm = this;
      vm.$http
        .get(
          "/panda-shop-api/v2/getShareQrcode?redirectUrl=http%3a%2f%2fapp.100032.xiaoxiyi.cn%2f%23%2fproduct%2f" +
            this.$route.params.id
        )
        .then(function(response) {
          vm.qrcodeImgUrl = "data:image/jpg;base64," + response.data.result;
        })
        .catch(function(error) {
          vm.$toast("请求超时，请稍后再试！");
        });
    },

    // 格式化价格
    moneyFormat(value) {
      if (value) {
        var moneyValue = (value / 100).toFixed(2).split(".");
        return "<big>￥" + moneyValue[0] + "</big>." + moneyValue[1];
      } else if (value === 0) {
        return "0";
      } else {
        return "";
      }
    },

    /**
     * 将页面指定节点内容转为图片
     * 1.拿到想要转换为图片的内容节点DOM；
     * 2.转换，拿到转换后的canvas
     * 3.转换为图片
     */
    clickGeneratePicture() {
      let vm = this;
      html2canvas(this.$refs.imageDom, {
        useCORS: true,
        logging: true
      }).then(canvas => {
        // 转成图片，生成图片地址
        vm.imgUrl = canvas.toDataURL("image/png");
        vm.showTitle = true;
      });
    }
  },
  filters: {
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
.container .choice {
  background-color: #fff;
  border-top-left-radius: 50%;
  border-top-right-radius: 50%;
}
.container .imgdetail {
  border: 5px solid red;
  margin-top: 45px;
  margin-left: 20px;
  margin-right: 20px;
  margin-bottom: 30px;
  position: relative;
}
.imgdetail .optimal {
  background-color: #ff5162;
  width: 110px;
  height: 30px;
  position: absolute;
  top: 0;
  left: 0;
  padding: 10px;
  color: #fff;
  font-size: 26px;
  border-bottom-right-radius: 15%;
}
.container .literaldetail {
  float: left;
  width: 54%;
}
.container .qrcode {
  float: right;
}
.sb-cont {
  position: fixed;
  height: 100%;
  width: 100%;
  text-align: center;
  left: 0;
  top: 0;
  z-index: 90;
  background-color: rgba(0, 0, 0, 0.9);
}

.sb-cont .img-cont {
  position: absolute;
  height: 60%;
  width: 60%;
  left: 18%;
  top: 10%;
}

.sb-cont .img-cont img {
  display: block;
  width: 100%;
}

.sb-cont-close {
  position: absolute;
  right: 10px;
  top: 10px;
  font-size: 32px;
  color: #c06;
}

.form-btn {
  width: 600px;
  margin: 0 auto;
  line-height: 80px;
  border-radius: 40px;
  color: #fff;
  font-size: 28px;
  text-align: center;
  background-color: #ff5162;
  margin-top: 60px;
}
</style>