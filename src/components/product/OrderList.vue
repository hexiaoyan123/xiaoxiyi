<template>
  <div class="goods-list-out-cont order-goods-list">
    <van-list v-model="falls.loading" :finished="falls.finished" @load="fallsOnLoad">
      <goods-item
        v-for="(item, index) in list"
        :key="index"
        :obj="item"
        @refreshList="getOrder(true)"
      ></goods-item>
      <div class="noOrderContent" v-if="list.length < 1 && falls.finished">
        <ul>
          <li>
            <i>
              <img src="@/assets/noThing/order.png" width="100%" />
            </i>
          </li>
          <li>
            <span>您还没有相关订单！</span>
          </li>
          <li>
            <i>
              <img src="@/assets/noThing/gwc-button.png" width="100%" @click="goPath('/index')" />
            </i>
          </li>
        </ul>
      </div>
    </van-list>
    <h3 v-show="falls.finished && list.length > 0" class="noData">
      <i class="keguan">
        <img src="../../assets/keguan.png" width="100%" />
      </i>
      <span>客官，没有更多了！</span>
    </h3>
  </div>
</template>

<script>
import OrderGoodsListItem from "./OrderListItem";
export default {
  name: "OrderGoodsList",
  components: {
    "goods-item": OrderGoodsListItem
  },
  props: ["dataStatu"],
  data() {
    return {
      list: [],
      pageNum: 1,
      // 瀑布流
      falls: {
        loading: true, // 加载中
        finished: false // 结束调用
      }
    };
  },
  created() {
    this.getOrder(true);
  },
  methods: {
    // 跳转路径
    goPath(path) {
      this.$router.push(path);
    },
    // 瀑布流加载数据
    fallsOnLoad() {
      // this.falls.loading = true;
      this.getOrder();
    },
    // 获取订单列表
    getOrder(clear) {
      // 页码处理
      if (clear) {
        this.list = [];
        this.pageNum = 1;
        this.falls.finished = false;
      } else {
        this.pageNum += 1;
      }
      let vm = this;
      let postData = {
        pageNo: this.pageNum,
        pageSize: 20,
        dataStatus: this.dataStatu
      };
      this.$http
        .post("/panda-shop-api/v2/pageOrder", postData)
        .then(function(response) {
          if ("1000" == response.data.code) {
            if (response.data.result && response.data.result.length > 0) {
              let arr = [];
              response.data.result.forEach((e, i) => {
                let obj = {};
                obj = e;
                if (e.groupOrder && e.groupOrder.participantInformation) {
                  obj.groupOrder.participantInformation = JSON.parse(
                    e.groupOrder.participantInformation
                  );
                }
                arr.push(obj);
              });

              vm.list = vm.list.concat(arr);
              if (response.data.result.length < postData.pageSize) {
                vm.falls.finished = true;
              }

              vm.$nextTick(() => {
                vm.falls.loading = false;
              });
            } else {
              vm.falls.loading = false;
              vm.falls.finished = true;
            }
            // vm.list.push({
            //   id: "190921182448599700",
            //   createTime: 1569061488,
            //   storeId: "190917133913486858",
            //   storeName: "健康衡安堂",
            //   dataStatus: 1,
            //   payStatus: 1,
            //   overTime: 1569063288,
            //   orderAmount: 18800,
            //   quantity: 1,
            //   shippedQuantity: 0,
            //   resPageOrderItemList: [
            //     {
            //       id: "190921182448600710",
            //       productId: "190919144356915241",
            //       name:
            //         "三元衡安堂166酸奶 发酵乳 优效复合益生菌风味低温酸奶 (200g*10瓶/盒*2)",
            //       imageUrl: "/201909/0C359DE5653D433ABBFE4EA4D15EC141.jpg",
            //       quantity: 1,
            //       productSkuId: "190920134318660483",
            //       specification: "三元衡安堂酸奶:200g*10瓶/盒*2",
            //       weight: 2000,
            //       salesPrice: 18800,
            //       marketPrice: 18800,
            //       afterSale: "1"
            //     }
            //   ]
            // });
            // vm.list.push({
            //   id: "190921182448599700",
            //   createTime: 1569061488,
            //   storeId: "190917133913486858",
            //   storeName: "健康衡安堂",
            //   dataStatus: 11,// 等待拼单
            //   payStatus: 1,
            //   overTime: 1569063288,
            //   orderAmount: 18800,
            //   quantity: 1,
            //   shippedQuantity: 0,
            //   resPageOrderItemList: [
            //     {
            //       id: "190921182448600710",
            //       productId: "190919144356915241",
            //       name:
            //         "三元衡安堂166酸奶 发酵乳 优效复合益生菌风味低温酸奶 (200g*10瓶/盒*2)",
            //       imageUrl: "/201909/0C359DE5653D433ABBFE4EA4D15EC141.jpg",
            //       quantity: 1,
            //       productSkuId: "190920134318660483",
            //       specification: "三元衡安堂酸奶:200g*10瓶/盒*2",
            //       weight: 2000,
            //       salesPrice: 18800,
            //       marketPrice: 18800,
            //       afterSale: "1"
            //     }
            //   ]
            // });
            // vm.list.push({
            //   id: "190921182448599700",
            //   createTime: 1569061488,
            //   storeId: "190917133913486858",
            //   storeName: "健康衡安堂",
            //   dataStatus: 12,
            //   payStatus: 1,
            //   overTime: 1569063288,
            //   orderAmount: 18800,
            //   quantity: 1,
            //   shippedQuantity: 0,
            //   resPageOrderItemList: [
            //     {
            //       id: "190921182448600710",
            //       productId: "190919144356915241",
            //       name:
            //         "三元衡安堂166酸奶 发酵乳 优效复合益生菌风味低温酸奶 (200g*10瓶/盒*2)",
            //       imageUrl: "/201909/0C359DE5653D433ABBFE4EA4D15EC141.jpg",
            //       quantity: 1,
            //       productSkuId: "190920134318660483",
            //       specification: "三元衡安堂酸奶:200g*10瓶/盒*2",
            //       weight: 2000,
            //       salesPrice: 18800,
            //       marketPrice: 18800,
            //       afterSale: "1"
            //     }
            //   ]
            // });
            // vm.list.push({
            //   id: "190921182448599700",
            //   createTime: 1569061488,
            //   storeId: "190917133913486858",
            //   storeName: "健康衡安堂",
            //   dataStatus: 13,
            //   payStatus: 1,
            //   overTime: 1569063288,
            //   orderAmount: 18800,
            //   quantity: 1,
            //   shippedQuantity: 0,
            //   resPageOrderItemList: [
            //     {
            //       id: "190921182448600710",
            //       productId: "190919144356915241",
            //       name:
            //         "三元衡安堂166酸奶 发酵乳 优效复合益生菌风味低温酸奶 (200g*10瓶/盒*2)",
            //       imageUrl: "/201909/0C359DE5653D433ABBFE4EA4D15EC141.jpg",
            //       quantity: 1,
            //       productSkuId: "190920134318660483",
            //       specification: "三元衡安堂酸奶:200g*10瓶/盒*2",
            //       weight: 2000,
            //       salesPrice: 18800,
            //       marketPrice: 18800,
            //       afterSale: "1"
            //     }
            //   ]
            // });
            // vm.list.push({
            //   id: "190921182448599700",
            //   createTime: 1569061488,
            //   storeId: "190917133913486858",
            //   storeName: "健康衡安堂",
            //   dataStatus: 14,
            //   payStatus: 1,
            //   overTime: 1569063288,
            //   orderAmount: 18800,
            //   quantity: 1,
            //   shippedQuantity: 0,
            //   resPageOrderItemList: [
            //     {
            //       id: "190921182448600710",
            //       productId: "190919144356915241",
            //       name:
            //         "三元衡安堂166酸奶 发酵乳 优效复合益生菌风味低温酸奶 (200g*10瓶/盒*2)",
            //       imageUrl: "/201909/0C359DE5653D433ABBFE4EA4D15EC141.jpg",
            //       quantity: 1,
            //       productSkuId: "190920134318660483",
            //       specification: "三元衡安堂酸奶:200g*10瓶/盒*2",
            //       weight: 2000,
            //       salesPrice: 18800,
            //       marketPrice: 18800,
            //       afterSale: "1"
            //     }
            //   ]
            // });
          } else {
            vm.$toast(response.data.msg);
          }
        })
        .catch(function(error) {
          vm.$toast("请求超时，请稍后再试！");
        });
    }
  }
};
</script>

<style scoped>
/* 商品列表 */

.noOrderContent ul {
  margin-top: 80px;
}

.noOrderContent ul li {
  text-align: center;
}

.noOrderContent ul li:nth-child(1) i {
  width: 160px;
  height: 160px;
  display: inline-block;
}

.noOrderContent ul li:nth-child(2) span {
  font-size: 30px;
  color: #969696;
}

.noOrderContent ul li:nth-child(3) {
  margin-top: 30px;
}

.noOrderContent ul li:nth-child(3) i {
  display: inline-block;
  width: 240px;
}

.goods-list-out-cont {
  /* margin-top: 20px; */
  box-sizing: border-box;
  height: 100%;
  overflow: auto;
}

.noData {
  position: relative;
  height: 50px;
}

.keguan {
  position: absolute;
  top: 15px;
  left: 36%;
  display: inline-block;
  height: 40px;
  width: 40px;
}

.noData span {
  position: absolute;
  top: 5px;
  left: calc(36% + 50px);
}

.van-tabs__content {
  background-color: #f8f8f8;
}
</style>
<style>
.order-goods-list .van-tabs {
  height: 100%;
  overflow: auto;
  box-sizing: border-box;
  background-color: #f8f8f8;
}
</style>