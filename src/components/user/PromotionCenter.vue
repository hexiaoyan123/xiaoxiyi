<template>
  <div class="container">
    <ul class="list">
      <li class="role">
        <div class="role-img">
          <figure class="img">
            <img v-show="! $init.user.cacheUser.imageUrl" src="../../assets/user.jpg" alt />
            <img v-if="$init.user.cacheUser.imageUrl" :src="$init.user.cacheUser.imageUrl" alt />
          </figure>
        </div>
        <div class="role-name">{{$init.user.cacheUser.nickname}}</div>
        <div class="role-name">{{user.userLevelName}}</div>
      </li>
    </ul>

    <div class="orders o-cont">
      <div class="orders-in">
        <ul>
          <li style="font-size: 14px; font-weight: 700;">
            <p style="color: #FF5C6B;">{{page.count}}</p>
            <p>团队人数</p>
          </li>
          <li style="font-size: 14px; font-weight: 700;">
            <p style="color: #FF5C6B;">{{user.distributionAmount | priceFormat}}</p>
            <p>分销金额</p>
          </li>
        </ul>
      </div>
    </div>
    <div v-show="!user.realName">
      <div style="margin-top: 30px; text-align: center;">
        <ul>
          <li>
            <img style="width: 80px; height: 80px;" src="@/assets/user/bstgy.png" />
          </li>
        </ul>
        <ul style="margin-top: 5px;">
          <li style="font-size: 14px; font-weight: 700; color: #BEBEBE;">您还不是推广员，暂无信息！</li>
        </ul>

        <ul style="margin-top: 5px;">
          <li @click="goPath('/article/detail/191106163453163417')">
            <p class="warn-content">
              <i>
                <img src="../../assets/goods/warn.png" width="100%" height="100%" />
              </i> 什么是推广员？
            </p>
          </li>
        </ul>
      </div>
      <div
        class="choice-ok gradation"
        style="position: fixed; width: 100%; bottom: 0;"
        @click="goPath('/user/realNameAuthentication')"
      >成为推广员</div>
    </div>

    <!-- 推广员列表信息 -->
    <div v-show="user.realName" style="font-size: 13px; font-weight: 500;">
      <div style="padding-left: 10px; padding-top: 5px;">团队列表</div>
      <ul>
        <li
          v-for="val in distributionUserList"
          :key="val.id"
          style="clear: both; height: 75px; border-bottom: 1px solid #eee;"
        >
          <div style="float: left; margin-top: 5px; padding-left: 10px; width: 60px; height: 60px;">
            <img style="border-radius: 50%; width: 100%; height: 100%;" :src="val.imageUrl" />
          </div>
          <div style="float: left; padding-left: 20px; margin-top: 10px; text-align: left;">
            <p style="margin-top: 10px;">{{val.nickname}}</p>
            <p style="margin-top: 5px;">{{val.phone}}</p>
          </div>
          <div style="float: right; padding-right: 20px; margin-top: 10px; text-align: left;">
            <p style="margin-top: 10px;">分销金额：{{val.distributionAmount | priceFormat}}</p>
            <p style="margin-top: 5px;">{{val.userLevelName}}</p>
          </div>
        </li>
      </ul>
      <!-- text-align: center 给ul加 这样文字居中-->
      <ul style="margin-top: 5px;">
        <li @click="goPath('/article/detail/191106163453163417')">
          <p
            class="warn-content"
            style="margin-top: 200px;"
            @click="goPath('/article/detail/191106163453163417')"
          >
            <i>
              <img src="../../assets/goods/warn.png" width="100%" height="100%" />
            </i> 什么是推广员？
          </p>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: "Home",
  components: {},
  data() {
    return {
      // 用户
      user: {},
      // 分销用户
      distributionUserList: [],

      // 分页
      page: {
        // 总条数
        count: 0,
        // 当前页码
        no: 1,
        // 每页数量
        size: 20
      }
    };
  },
  created() {
    this.getUser();
    this.listDistributionUser();
  },
  methods: {
    // 跳转路径
    goPath(path) {
      this.$router.push(path);
    },

    // 获取用户
    getUser() {
      let vm = this;
      vm.$http
        .get("/panda-admin-api/v1/getUser")
        .then(function(response) {
          if ("1000" == response.data.code) {
            vm.user = response.data.result;
          } else {
            vm.$toast(response.data.msg);
          }
        })
        .catch(function(error) {
          vm.$toast("请求超时，请稍后再试！");
        });
    },

    // 获取分销用户
    listDistributionUser() {
      var vm = this;
      vm.loading = true;
      vm.$http
        .get(
          "/panda-admin-api/v1/pageDistributionUser/1/20/" +
            this.$init.user.cacheUser.id
        )
        .then(function(response) {
          vm.loading = false;
          if ("1000" == response.data.code) {
            vm.distributionUserList = response.data.result;
            vm.page.count = response.data.count;
          } else {
            vm.$message.warning(response.data.msg);
          }
        })
        .catch(function(error) {
          vm.$message.error("请求超时，请稍后再试！");
        });
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
    }
  }
};
</script>

<style scoped>
.container {
  background-color: #f4f4f4;
  padding-bottom: 88px;
}

.top img {
  display: block;
  width: 100%;
}

header {
  height: 85px;
  line-height: 85px;
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

.list {
  margin-bottom: 10px;
  border-bottom: 1px solid #eee;
}

.item {
  border-top: 1px solid #eee;
  padding: 20px;
  line-height: 44px;
  font-size: 24px;
  color: #818181;
  display: flex;
  background-color: #fff;
}

.item .iconfont {
  font-size: 26px;
  display: inline-block;
  width: 50px;
  text-align: center;
}

.item-left {
  flex: 10;
}

.role {
  padding: 20px;
  background: -webkit-linear-gradient(left, #ff8e62, #ff3766);
  /* Safari 5.1 - 6.0 */
  background: -o-linear-gradient(right, #ff8e62, #ff3766);
  /* Opera 11.1 - 12.0 */
  background: -moz-linear-gradient(right, #ff8e62, #ff3766);
  /* Firefox 3.6 - 15 */
  background: linear-gradient(to right, #ff8e62, #ff3766);
  /* 标准的语法（必须放在最后） */
  padding-top: 40px;
  padding-bottom: 80px;
  color: #fff;
}

.role-name {
  margin-top: 10px;
  text-align: center;
  font-size: 28px;
  color: #fff;
}

.role-img {
  width: 100%;
  text-align: center;
}

.role-img .img {
  display: inline-block;
  height: 100px;
  width: 100px;
  border-radius: 50%;
  overflow: hidden;
}

.role-img .img img {
  display: block;
  height: 100%;
  width: 100%;
}

.item .red {
  color: #ff4403;
  border-color: #ff4403;
}

.item .blue {
  color: #00b202;
  border-color: #00b202;
}

.list .item.center {
  display: block;
  text-align: center;
}

.orders {
  padding: 10px 20px;
}

.orders-in {
  background-color: #fff;
  border-radius: 10px;
  padding: 20px 10px;
  box-shadow: 0px 10px 20px #e0e0e0;
}

.orders-in ul {
  display: flex;
  margin-bottom: 10px;
}

.orders-in li {
  flex: 4;
  text-align: center;
  font-size: 20px;
  padding-top: 10px;
}

.orders-in li .iconfont {
  display: inline-block;
  width: 36px;
  height: 30px;
  margin-bottom: 10px;
}

orders-in li .iconfont.big {
  width: 34px;
  height: 36px;
}

.iconfont {
}

.o-cont {
  margin-top: -70px;
}

.o-name {
  margin-bottom: 10px;
  padding: 5px 20px 20px 20px;
  border-bottom: 1px solid #eee;
}

.logo-out {
  line-height: 36px;
  width: 72px;
  text-align: center;
  background-color: #fff;
  border-radius: 10px;
  color: #ff5162;
}

.logo-out-cont {
  padding: 12px 0 0 0;
}

.choice-ok {
  color: #fff;
  line-height: 90px;
  text-align: center;
  background-color: #ff5162;
  font-size: 28px;
}

.warn-content {
  color: #fe8103;
  position: relative;
  padding-left: 30px;
  display: inline-block;
}

.warn-content i {
  position: absolute;
  left: 0px;
  top: 4px;
  width: 24px;
  height: 23px;
  display: inline-block;
}
</style>