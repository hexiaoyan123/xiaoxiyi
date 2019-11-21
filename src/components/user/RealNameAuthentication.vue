<template>
  <div class="container address-list" style="overflow: hidden;">
    <Vheader name="实名认证"></Vheader>

    <div class="card" style="overflow: hidden;">
      <h1 style="font-size: 4.8vw; margin: 10px;">填写基本信息</h1>
      <p style="font-size: 3.2vw; color: #818181; margin: 5px 10px;">按照要求信息填写成为推广员，实名后无法修改，请认证填写实名信息</p>

      <ul class="card-form" style="overflow: hidden;">
        <li class="card-item" style="overflow: hidden;">
          <div class="card-item-lable">真实姓名</div>
          <input @blur="iptBlur" @focus="iptFocus" type="text" v-model="form.realName" />
        </li>
        <li class="card-item" style="overflow: hidden;">
          <div class="card-item-lable">身份证号</div>
          <input @blur="iptBlur" @focus="iptFocus" type="text" v-model="form.identity" />
        </li>
      </ul>

      <div v-show="isFirstUpdatePhone == true">
        <h1 style="font-size: 4.8vw; margin: 10px;">设置手机号</h1>
        <p style="font-size: 3.2vw; color: #818181; margin: 5px 10px;">请输入您当前使用的手机号码作为绑定号码</p>
      </div>
      <div v-show="isFirstUpdatePhone == false">
        <h1 style="font-size: 4.8vw; margin: 10px;">修改手机号</h1>
        <p
          style="font-size: 3.2vw; color: #818181; margin: 5px 10px;"
        >您当前绑定的手机号为{{phone}}，若继续使用该手机则无需修改</p>
        <p style="font-size: 3.2vw; color: #818181; margin: 5px 10px;">修改后，您当前账号中的订单信息不变</p>
      </div>

      <ul class="card-form" style="overflow: hidden;">
        <li class="card-item" style="overflow: hidden;">
          <div class="card-item-lable">+ 86</div>
          <input @blur="iptBlur" @focus="iptFocus" type="text" v-model="form.phone" />
        </li>
        <li class="card-item" style="overflow: hidden;">
          <div class="card-item-lable">验证码</div>
          <input @blur="iptBlur" @focus="iptFocus" type="text" v-model="form.authCode" />
          <div class="card-item-ctrl">
            <span class="red" v-show="codeNum == 60" @click="getCode">免费获取</span>
            <span class="red" v-show="codeNum < 60">{{codeNum}}秒</span>
          </div>
        </li>
      </ul>

      <div class="form-btn" @click="updateUserPhone">提交申请</div>
    </div>
  </div>
</template>

<script>
import Vheader from "../HeaderV";
export default {
  name: "UpdatePhone",
  components: {
    Vheader
  },
  data() {
    return {
      // 是否第一次更新手机号
      isFirstUpdatePhone: "",

      // 手机号
      phone: "",

      // 验证码倒计时
      codeNum: 60,

      form: {
        realName: "",
        identity: "",
        phone: "",
        authCode: ""
      }
    };
  },
  created() {
    this.getUserPhone();
  },
  methods: {
    // 获取用户手机号
    getUserPhone() {
      var vm = this;
      vm.$http
        .get("/v1/checkPhone")
        .then(function(response) {
          if ("1000" == response.data.code) {
            vm.isFirstUpdatePhone = false;
            if (response.data.result) {
              vm.phone = response.data.result.replace(
                /^(\d{3})\d{4}(\d+)/,
                "$1****$2"
              );
            }
          } else {
            vm.isFirstUpdatePhone = true;
          }
        })
        .catch(function(error) {
          vm.$toast("请求超时，请稍后再试！");
        });
    },

    // 获取验证码
    getCode() {
      let vm = this;
      if (vm.isPhone(vm.form.phone)) {
        vm.$http
          .get("/v1/getPhoneCode/" + this.form.phone)
          .then(function(response) {
            if ("1000" == response.data.code) {
              vm.countDown();
            } else {
              vm.$toast(response.data.msg);
            }
          })
          .catch(function(error) {
            vm.$toast("请求超时，请稍后再试！");
          });
      } else if ("" == vm.form.phone) {
        vm.$toast("请输入手机号!");
      } else {
        vm.$toast("手机号格式不符合系统要求！");
      }
    },

    // 倒计时
    countDown() {
      let vm = this;
      vm.codeNum = 59;
      function numDown() {
        if (vm.codeNum > 1) {
          vm.codeNum = vm.codeNum - 1;
        } else {
          vm.codeNum = 60;
          window.clearInterval(int);
          return false;
        }
      }
      var int = setInterval(numDown, 1000);
    },

    // 验证手机号
    isPhone(phone) {
      var regular = /^[1][0-9]{10}$/;
      if (regular.test(phone)) {
        return true;
      } else {
        return false;
      }
    },

    // 验证身份证号
    isIdentity(identity) {
      // var regular = /^[1-9]\d{5}(18|19|2([0-9]))\d{2}(0[0-9]|10|11|12)([0-2][1-9]|30|31)\d{3}[0-9Xx]$/;
      var regular = /\d{17}[\d|x]|\d{15}/;
      if (regular.test(identity)) {
        return true;
      } else {
        return false;
      }
    },

    // 提交申请
    updateUserPhone() {
      let vm = this;
      if ("" == vm.form.realName) {
        vm.$toast("请输入真实姓名！");
        return false;
      }
      if ("" == vm.form.identity) {
        vm.$toast("请输入身份证号！");
        return false;
      }
      if (!vm.isIdentity(vm.form.identity)) {
        vm.$toast("请输入正确的身份证号！");
        return false;
      }
      if ("" == vm.phone) {
        if ("" == vm.form.phone) {
          vm.$toast("请输入手机号！");
          return false;
        }
        if ("" == vm.form.authCode) {
          vm.$toast("请输入验证码！");
          return false;
        }
      }
      let postData = vm.form;
      this.$http
        .post("/v1/updateUserRealName", postData)
        .then(function(response) {
          if ("1000" == response.data.code) {
            vm.$toast("提交成功！");
            vm.$router.push("/user/home");
          } else {
            vm.$toast(response.data.msg);
          }
        })
        .catch(function(error) {
          vm.$toast("请求超时，请稍后再试！");
        });
    },

    // focus
    iptFocus() {
      this.errorMessage = "";
      this.inFocus = true;
    },

    // blur
    iptBlur() {
      let vm = this;
      vm.inFocus = false;
      setTimeout(function() {
        if (vm.inFocus == false) {
          // 当input失焦时,滚动一下页面就可以使页面恢复正常
          vm.checkWxScroll();
        }
      }, 200);
    },

    checkWxScroll() {
      var ua = navigator.userAgent.toLowerCase();
      var u = navigator.userAgent.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/);
      if (ua.match(/MicroMessenger/i) == "micromessenger" && !!u) {
        //在iphone 微信中
        this.temporaryRepair();
      }
    },

    temporaryRepair() {
      var currentPosition, timer;
      var speed = 1; //页面滚动距离
      timer = setInterval(function() {
        currentPosition =
          document.documentElement.scrollTop || document.body.scrollTop;
        currentPosition -= speed;
        window.scrollTo(0, 0); //页面向上滚动
        clearInterval(timer);
      }, 1);
    },

    // 返回上一页
    goBack() {
      this.$router.back(-1);
    }
  }
};
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
}
.address-list {
  padding-top: 85px;
}
.class-back {
  position: absolute;
  width: 80px;
  height: 80px;
  line-height: 92px;
  top: 0;
  left: 0;
  text-align: center;
  font-size: 28px;
  color: #fff;
}

/* 文本框 */
.card-item {
  display: flex;
  font-size: 24px;
  color: #818181;
  line-height: 75px;
  padding: 12px 20px;
  border-bottom: 1px solid #eee;
}
.card-item input {
  display: block;
  height: 75px;
  border: none;
  font-size: 28px;
  line-height: 75px;
  outline: none;
  flex: 10;
  padding: 0 10px;
  min-width: 10px;
}
.card-item-ctrl {
  position: relative;
}
.card-code {
  display: block;
  height: 75px;
  width: 160px;
  border-radius: 6px;
  margin-right: 70px;
}
.card-item-ctrl .iconfont {
  position: absolute;
  display: inline-block;
  top: 0;
  right: 0;
  height: 75px;
  line-height: 75px;
  width: 70px;
  text-align: center;
  font-size: 48px;
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
.card-item-ctrl-password {
  display: inline-block;
  line-height: 75px;
  width: 75px;
  text-align: right;
}
.card-item-ctrl .iconfont {
  font-size: 24px;
  text-align: right;
}
</style>
 