<template>
  <div class="container" ref="backScroller">
    <!-- 头部开始 -->
    <!-- <header>
      <div class="header-back" @click="goBack">
        <van-icon name="arrow-left" />
      </div>
      <div class="header-cont"  @click="city.show = true">
        {{city.city.name}} <i class="iconfont">&#xe608;</i>
      </div>
      <div class="header-more">
        
      </div> -->
    <!-- </header> -->
    <!-- 头部结束 -->

    <go-top vlass="container"></go-top>

    <!-- 新头部开始 -->
    <div class="new-header">
      <div class="nh-left" @click="city.show = true">
        <van-icon name="location" />
        <em>{{city.city.name}}</em>
      </div>
      <div class="nh-center">
        <div class="nh-center-out">
          <div class="nh-center-item" :class="{'nh-center-item-on': showType == 1}" @click="showType = 1">热映</div>
          <div class="nh-center-item" :class="{'nh-center-item-on': showType == 2}" @click="showType = 2">待映</div>
        </div>
      </div>
    </div>
    <!-- 新头部结束 -->

    <!-- 城市选择开始 -->
    <van-popup v-model="city.show" position="right" :overlay="false">
      <movie-city :v-city="city.city" @closeCity="closeCityModo" @clickCity="getCity"></movie-city>
    </van-popup>
    <!-- 城市选择结束 -->

    <ul class="move-list" v-show="showType == 1">
      <li class="move-item clearfix" v-for="(item, index) in hotFilm.list" :key="index" @click="goMovieFilm(item.id)">
        <div class="move-item-l">
          <figure class="img">
            <img :src="item.poster" alt="">
          </figure>
        </div>
        <div class="move-item-r">
          <h3 class="unoverflow">{{item.name}}</h3>
          <div class="move-item-lable unoverflow">上映日期: {{item.publishDate}}</div>
          <p class="unoverflow">导演: {{item.director}}</p>
          <p class="unoverflow">演员: {{item.cast}}</p>
          <span class="move-item-btn" @click.stop="goMovieCinema(item.id)">购票</span>
        </div>
      </li>
    </ul>

    <ul class="move-list" v-show="showType == 2">
      <li class="move-item clearfix" v-for="(item, index) in awaitFilm.list" :key="index" @click="goMovieFilm(item.id)">
        <div class="move-item-l">
          <figure class="img">
            <img :src="item.poster" alt="">
          </figure>
        </div>
        <div class="move-item-r">
          <h3 class="unoverflow">{{item.name}}</h3>
          <div class="move-item-lable unoverflow">上映日期: {{item.publishDate}}</div>
          <p class="unoverflow">导演: {{item.director}}</p>
          <p class="unoverflow">演员: {{item.cast}}</p>
        </div>
      </li>
    </ul>

    <!-- 定位开始 -->
    <div v-if="! $store.state.position.lng">
      <v-map></v-map>
    </div>
    <!-- 定位结束 -->
  </div>
</template>

<script>
import City from './City'
import vMap  from '../Map'
import GoTop from '../GoTop'
export default {
  name: 'MovieCinema',
  components: {
    'movie-city': City,
    'v-map': vMap,
    'go-top': GoTop
  },
  data () {
    return {
      // 城市
      city: {
        show: false, //城市弹窗
        city: {}, // 选中的地址
      },
      // 热门
      hotFilm: {
        list: [],
      },
      // 待映
      awaitFilm: {
        list: []
      },
      showType: 1, // 类型切换
    }
  },
  created () {
    // 城市处理
    if(this.$init.moveCity){
      this.getCity(this.$init.moveCity);
    }else if(this.$cookies.get('moveCity')){
      let data = JSON.parse(this.$cookies.get('moveCity'));
      this.getCity(data);
    }else{
      let city = {"id":"110100","parentId":"110000","grade":"2","name":"北京","abbr":"B"}
      this.$cookies.set('moveCity', JSON.stringify(city));
      this.getCity(city);
    }
    this.getListHotFilm();
    this.getListAwaitFilm();
  },
  methods: {
    // 关闭城市选择
    closeCityModo (data) {
      this.city.show = false;
    },
    // 获取选中的地址
    getCity (data) {
      this.city.city = data;
      this.$init.moveCity = data;
      this.$cookies.set('moveCity', JSON.stringify(data));
      this.city.show = false;
    },
    // 获取热影片
    getListHotFilm () {
      let vm = this;
      this.$http.get('/panda-cinema-api/v1/listHotFilm').then(function (response) {
        if("1000" == response.data.code){
          if(response.data.result){
            vm.hotFilm.list = response.data.result
          }else{
            vm.hotFilm.list = [];
          }
        }else{
          vm.$toast(response.data.msg)
        }
      })
      .catch(function (error) {
        vm.$toast('请求超时，请稍后再试！')
      });
    },
    // 获取待映影片
    getListAwaitFilm () {
      let vm = this;
      this.$http.get('/panda-cinema-api/v1/listAwaitFilm').then(function (response) {
        if("1000" == response.data.code){
          if(response.data.result){
            vm.awaitFilm.list = response.data.result
          }else{
            vm.awaitFilm.list = [];
          }
        }else{
          vm.$toast(response.data.msg)
        }
      })
      .catch(function (error) {
        vm.$toast('请求超时，请稍后再试！')
      });
    },
    // 购票
    goMovieCinema (id) {
      this.$router.push('/movie/CinemaByFilm/'+id);
    },
    // 电影详情
    goMovieFilm (id) {
      this.$router.push('/movie/film/'+id);
    },
    // 返回上一页
    goBack () {
      this.$router.back(-1);
    },
  },
  beforeRouteEnter (to,from,next) {
    let scrollTop = sessionStorage.getItem('movieBackScroller')
    if(to.meta.isBack && scrollTop){
      next(vm => {
        if(vm && vm.$refs.backScroller){
          vm.$refs.backScroller.scrollTo(0, scrollTop)
        }
      })
    }else{
      next()
    }
  },
  beforeRouteLeave (to, from, next) {
    to.meta.isBack = false;
    from.meta.isBack = true;
    window.sessionStorage.setItem('movieBackScroller', this.$refs.backScroller.scrollTop)
    next()
  }
}
</script>

<style scoped>
.container{
  background-color: #fff;
  /* padding-top: 85px; */
  padding-bottom: 88px;
}
.top img{
  display: block;
  width: 100%;
}

header{
  height: 85px;
  line-height: 85px;
  /* position: absolute; */
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
.header-back{
  width: 70px;
  text-align: center;
}
.header-cont{
  flex: 10;
}
.header-more{
  width: 75px;
  text-align: center;
}
header .iconfont{
  font-size: 28px;
}

.move-list{
  width: 100%;
  overflow: hidden;
}
.move-item{
  padding: 20px 20px 20px 20px;
  box-sizing: border-box;
  border-bottom: 1px solid #eee;
}
.move-item-r{
  padding-left: 30px;
  width: 82%;
  float: right;
  box-sizing: border-box;
  position: relative;
}
.move-item-l{
  width: 18%;
  float: left;
}
.move-item-l .img{
  display: block;
  width: 100%;
  height: 160px;
  overflow: hidden;
  border-radius: 5px;
}
.move-item-l .img img{
  display: block;
  height: 100%;
  width: 100%;
}
.move-item-r h3{
  font-size: 28px;
  line-height: 38px;
  font-weight: 700;
  /* height: 120px; */
  padding-bottom: 10px;
}
.move-item-lable{
  font-size: 24px;
  line-height: 38px;
}
.move-item-r p{
  font-size: 24px;
  line-height: 38px;
  /* margin-top: 16px; */
  width: 70%;
}
.move-item-btn{
  position: absolute;
  display: block;
  line-height: 60px;
  padding: 0 30px;
  border-radius: 6px;
  color: #fff;
  background-color: #ff5162;
  right: 0;
  bottom: 0;
  font-size: 24px;
}

/* 改版头部 */
.new-header{
  position: relative;
  box-sizing: border-box;
  background-color: #fff;
  z-index: 9;
  font-size: 24px;
  line-height: 80px;
  text-align: center;
}
.nh-left{
  position: absolute;
  left: 20px;
  color: #ff5162;
}
.nh-center-out{
  display: inline-block;
  margin: 0 auto;
  border: 4px solid #ff5162;
  line-height: 52px;
  color: #ff5162;
  border-radius: 4px;
  /* overflow: hidden; */
}
.nh-center-item{
  display: inline-block;
  width: 120px;
}
.nh-center-item-on{
  color: #fff;
  background-color: #ff5162;
}
</style>

<style>
.van-popup--right{
  height: 100%;
  width: 100%;
  background-color: #fff;
}
</style>
