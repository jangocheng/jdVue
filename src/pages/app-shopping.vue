<template>
    <div id="shopping">
      <el-container v-show="isShow">
        <el-main class="shop-main">
         <!-- 购物车头部-->
          <div class="header">
            <span>购物车</span>
          </div>
          <!--商品内容-->
          <div class="content" :class="{'checked':activeIndex[index]}" v-for="(item, index) in arr" :key="item.id">
            <div class="goods-name">
              <div class="btn" @click="checked(index)">
              </div>
              <div class="store-name">
                <span>{{item.user}}></span>
              </div>
            </div>
            <div class="goods-pic">
              <div class="btn" @click="checked(index)"></div>
              <div class="pics">
                <img :src=item.imgSrc alt="">
                <div class="goods-price">
                  <span class="text">{{item.title}}</span>
                  <span class="num">{{item.price}}</span>
                  <template>
                    <el-input-number size="small" v-model="num" :min="1" :max="10" label="描述文字"></el-input-number>
                  </template>
                </div>
              </div>
            </div>
          </div>
          <!--结算表-->
          <div class="all">
            <div class="all-checked" @click="allChecked">
              <div class="btn" :class="{'checked':checkedAll}"></div>
              <span>全选</span>
            </div>
            <div class="all-price">合计：{{count}}元</div>
            <div class="account" @click="submit">去结算({{count}} )</div>
          </div>
        </el-main>
        <el-footer>
          <my-footer></my-footer>
        </el-footer>
      </el-container>
      <router-view v-show="!isShow"></router-view>
    </div>
</template>

<script>
import myFooter from '../components/myFooter'
var flag = true
export default {
  name: 'shopping',
  data () {
    return {
      num: 1,
      activeIndex: [],
      checkedAll: false,
      imgs: [
        {imgSrc: '/static/imgs/shop_close.jpg'}
      ],
      arr: [1, 2, 3, 4],
      count: 0,
      isShow: true
    }
  },
  components: {
    myFooter
  },
  watch: {
    activeIndex: function (value, a) {
      console.log(value, a)
      var self = this
      let count = 0
      value.forEach(function (item, index) {
        if (item) {
          count = count + parseInt(self.arr[index].price)
        }
      })
      self.count = count
    }
  },
  methods: {
    checked: function (index) {
      var self = this
      if (self.activeIndex[index]) {
        self.$set(self.activeIndex, index, !self.activeIndex[index])
      } else {
        self.$set(self.activeIndex, index, true)
      }
    },
    allChecked: function () {
      var self = this
      self.checkedAll = !self.checkedAll
      if (flag) {
        self.arr.forEach(function (item, index) {
          self.activeIndex[index] = true
        })
        flag = false
      } else {
        self.arr.forEach(function (item, index) {
          self.activeIndex[index] = false
        })
        flag = true
      }
    },
    submit: function () {
      var self = this
      var shops = []
      // 获取当前已选择的商品信息
      self.activeIndex.forEach(function (item, index) {
        if (item) {
          shops.push(self.arr[index])
        }
      })
      console.log(shops)
      self.$router.push({name: 'confirmOrder', query: { count: self.count, shops: shops }})
      self.isShow = false
    }
  },
  created: function () {
    var self = this
    self.arr = self.$store.state.shops
  }
}
</script>

<style scoped lang="scss">
  #shopping{
    width: 100%;
    height: 100%;
  .el-container {
    width: 100%;
    height: 100%;
  .shop-main {
    padding: 0;
    .header {
      text-align: center;
      height: 50px;
      border-bottom: 1px solid #cccccc63;
      font-size: 16px;
      line-height: 50px;
    }
    .content {
      margin-bottom: 20px;
      .goods-name {
        height: 30px;
        line-height: 30px;
        text-align: left;
        position: relative;
        border-bottom: 1px solid #cccccc63;
        background-color: #cccccc3b;
          .btn {
            width: 20px;
            height: 20px;
            border-radius: 20px;
            background-color: transparent;
            padding: 0;
            margin-left: 20px;
            display: inline-block;
            border: 1px solid #ccc;
            position: absolute;
            left: 0;
            top: 4px;
            line-height: 18px;
          }
         .store-name {
          display: inline-block;
           width: auto;
           margin-left: 50px;
        }
      }
      .goods-pic {
        width: 100%;
        height: 100px;
        position: relative;
        border-bottom: 1px solid #cccccc63;
        .btn {
          width: 20px;
          height: 20px;
          border-radius: 20px;
          background-color: transparent;
          padding: 0;
          margin-left: 20px;
          display: inline-block;
          border: 1px solid #ccc;
          position: absolute;
          left: 0;
          top: 35px;
        }
       .pics {
         width: 100%;
         height: 100%;
         text-align: left;
         img {
           width: 98px;
           margin-left: 50px;
         }
         .goods-price {
           position: absolute;
           display: inline-block;
           width: 200px;
           height: 100%;
           .text {
             height: 70px;
             width: 100%;
             display: inline-block;
           }
           .num {
             margin: 10px;
           }
           .el-input-number {
             width: 100px;
             position: absolute!important;
             right: 0;
             bottom: 0;
           }
         }
       }
      }
    }
    .content.checked .btn {
        border: none;
    }
    .content.checked .btn:after {
      margin: auto;
      content: '\2714';
      color: white;
      width:20px;
      height:20px;
      text-align: center;
      font-size: 12px;
      background-color: red;
      display: inline-block;
      border-radius: 20px;
      line-height: 20px;
    }
    .all {
      width: 90%;
      height: 40px;
      position: absolute;
      left: 0;
      bottom: 60px;
      background-color: #f3f3f3;
      z-index: 99;
      text-align: left;
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 0 5%;
      .all-checked {
        .btn {
          width: 20px;
          height: 20px;
          border-radius: 20px;
          background-color: transparent;
          padding: 0;
          display: inline-block;
          border: 1px solid #ccc;
        }
        .checked:after {
          margin: auto;
          content: '\2714';
          color: white;
          width:20px;
          height:20px;
          text-align: center;
          font-size: 12px;
          background-color: red;
          display: inline-block;
          border-radius: 20px;
          line-height: 20px;
        }
      }
    }
  }
  .el-footer {
    padding: 0;
    background-color: #cccccca1;
  }
  }
  }
</style>
