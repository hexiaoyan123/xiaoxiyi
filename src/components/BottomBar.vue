<template>
  <div class="botton-bar-cont" v-show="show">
    <div
      class="botton-bar-item"
      :class="{'botton-bar-item-on': $route.path == '/index'}"
      @click="goPath('/index')"
    >
      <i class="iconfont">&#xe604;</i>
      <p>首页</p>
    </div>
    <!-- <div class="botton-bar-item" :class="{'botton-bar-item-on': $route.path == '/QRcode'}" @click="goPath('/QRcode')">
      <i class="iconfont">&#xe6a9;</i>
      <p>二维码</p>
    </div>-->
    <div
      class="botton-bar-item"
      :class="{'botton-bar-item-on': $route.path == '/product/searchClass'}"
      @click="goPath('/product/searchClass')"
    >
      <i class="iconfont">&#xe696;</i>
      <p>分类</p>
    </div>
    <div
      class="botton-bar-item"
      :class="{'botton-bar-item-on': $route.path == '/product/cart'}"
      @click="goPath('/product/cart')"
    >
      <i class="iconfont">&#xe698;</i>
      <p>购物车</p>
    </div>
    <!-- <div class="botton-bar-item" :class="{'botton-bar-item-on': $route.path == '/ac/home'}" @click="goPath('/ac/home')">
      <i class="iconfont">&#xe63e;</i>
      <p>活动</p>
    </div>-->
    <div
      class="botton-bar-item"
      :class="{'botton-bar-item-on': $route.path == '/user/home'}"
      @click="goPath('/user/home')"
    >
      <i class="iconfont">&#xe6b8;</i>
      <p>我的</p>
    </div>
  </div>
</template>

<script>
export default {
  name: "BottomBar",
  data() {
    return {
      urls: [
        "/index",
        "/QRcode",
        "/product/searchClass",
        "/product/cart",
        "/user/home"
      ]
    };
  },
  methods: {
    goPath(path) {
      this.$router.push(path);
    }
  },
  computed: {
    show() {
      if (this.urls.indexOf(this.$route.path) >= 0) {
        return true;
      } else {
        return false;
      }
    }
  }
};
</script>

<style scoped>
.botton-bar-cont {
  /* position: absolute; */
  position: fixed;
  /* -webkit-overflow-scroll: touch; */
  bottom: 0;
  left: 0;
  background-color: #ffffff;
  width: 100%;
  height: 88px;
  display: flex;
  box-shadow: 0 1px 6px #ccc;
  box-sizing: border-box;
  padding-top: 3px;
  z-index: 90;
}
.botton-bar-item {
  flex: 3;
  text-align: center;
}
.botton-bar-item .iconfont {
  font-size: 36px;
  line-height: 50px;
}
.botton-bar-item p {
  font-size: 24px;
  line-height: 30px;
}
.botton-bar-cont .botton-bar-item-on {
  color: #ff5162;
}
</style>

