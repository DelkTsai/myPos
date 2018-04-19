<template>

  <div class="pos">

    <div>
      <el-row id="order-list">
        <el-col :span='7'>
          <el-tabs>
            <el-tab-pane label="点餐">

              <el-table :data="tableData" border style="width: 100%">

                <el-table-column prop="goodsName" label="商品"></el-table-column>
                <el-table-column prop="count" label="量" width="50">

                </el-table-column>
                <el-table-column prop="price" label="金额" width="70"></el-table-column>

                <el-table-column label="操作" width="160" fixed="right">
                  <template scope="scope">
                    <el-button type="text" size="small" @click="delSingleGoodsCount(scope.row)">-</el-button>
                    <el-button type="text" size="small" @click="delSingleGoods(scope.row)">删除</el-button>
                    <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                  </template>
                </el-table-column>
              </el-table>
              <div class="totalDiv">
                <small>数量：</small>
                <strong>{{totalCount}}</strong> &nbsp;&nbsp;&nbsp;&nbsp;
                <small>总计：</small>
                <strong>{{totalMoney}}</strong> 元
              </div>
              <el-button type="warning">挂单</el-button>
              <el-button type="danger" @click="delAllGoods()">删除</el-button>
              <el-button type="success">结账</el-button>
            </el-tab-pane>
            <el-tab-pane label="挂单">
              挂单
            </el-tab-pane>
            <el-tab-pane label="外卖">
              外卖
            </el-tab-pane>
          </el-tabs>
        </el-col>

        <!--商品展示-->
        <el-col :span="17">
          <div class="often-goods">
            <div class="title">常用商品</div>
            <div v-for="item in oftenGoods" class=" often-goods-list " @click="addOrderList(item)">
              <ul>
                <li>
                  <span>{{ item.goodsName }}</span>
                  <span class="o-price ">￥{{ item.price }}元</span>
                </li>
              </ul>
            </div>
          </div>

        </el-col>
        <el-col :span="17">
          <div class="goods-type">

            <el-tabs>
              <el-tab-pane label="汉堡">
                <ul class=" cookList ">
                  <li v-for="item in type0Goods" @click="addOrderList(item)">
                    <span class="foodImg"><img :src="item.goodsImg" width="100%"></span>
                    <span class="foodName">{{ item.goodsName}}</span>
                    <span class="foodPrice">￥{{ item.price}}元</span>
                  </li>
                </ul>
                <ul class=" cookList ">
                  <li v-for="item in type1Goods">
                    <span class="foodImg"><img :src="item.goodsImg" width="100%"></span>
                    <span class="foodName">{{ item.goodsName}}</span>
                    <span class="foodPrice">￥{{ item.price}}元</span>
                  </li>
                </ul>
                <ul class=" cookList ">
                  <li v-for="item in type2Goods">
                    <span class="foodImg"><img :src="item.goodsImg" width="100%"></span>
                    <span class="foodName">{{ item.goodsName}}</span>
                    <span class="foodPrice">￥{{ item.price}}元</span>
                  </li>
                </ul>
                <ul class=" cookList ">
                  <li v-for="item in type3Goods">
                    <span class="foodImg"><img :src="item.goodsImg" width="100%"></span>
                    <span class="foodName">{{ item.goodsName}}</span>
                    <span class="foodPrice">￥{{ item.price}}元</span>
                  </li>
                </ul>
              </el-tab-pane>
              <el-tab-pane label="小食">
                小食
              </el-tab-pane>
              <el-tab-pane label="饮料">
                饮料
              </el-tab-pane>
              <el-tab-pane label="套餐">
                套餐
              </el-tab-pane>

            </el-tabs>
          </div>
        </el-col>
      </el-row>
    </div>
  </div>
</template>
 
<script>
import axios from "axios";
export default {
  name: "Pos",
  data() {
    return {
      tableData: [],
      oftenGoods: [],
      type0Goods: [],
      type1Goods: [],
      type2Goods: [],
      type3Goods: [],
      totalCount: 0,
      totalMoney: 0
    };
  },
  methods: {
    delAllGoods() {
      this.tableData = [];
      this.totalCount = 0;
      this.totalMoney = 0;
    },
    //降低商品数量1
    delSingleGoodsCount(goods) {
      let arr = this.tableData.filter(o => o.goodsId == goods.goodsId);
      arr[0].count--;
      if (arr[0].count==0)
        this.tableData = this.tableData.filter(o => o.goodsId != goods.goodsId);
      this.getAllMoney();
    },
    //添加订单列表的方法
    addOrderList(goods) {
      this.totalCount = 0; //汇总数量清0
      this.totalMoney = 0;
      let isHave = false;
      //判断是否这个商品已经存在于订单列表
      for (let i = 0; i < this.tableData.length; i++) {
        console.log(this.tableData[i].goodsId);
        if (this.tableData[i].goodsId == goods.goodsId) {
          isHave = true; //存在
        }
      }
      //根据isHave的值判断订单列表中是否已经有此商品
      if (isHave) {
        //存在就进行数量添加
        let arr = this.tableData.filter(o => o.goodsId == goods.goodsId);
        arr[0].count++;
        //console.log(arr);
      } else {
        //不存在就推入数组
        let newGoods = {
          goodsId: goods.goodsId,
          goodsName: goods.goodsName,
          price: goods.price,
          count: 1
        };
        this.tableData.push(newGoods);
      }

      //进行数量和价格的汇总计算
      this.getAllMoney();
    },
    delSingleGoods(goods) {
      console.log(goods);
      this.tableData = this.tableData.filter(o => o.goodsId != goods.goodsId);
      this.getAllMoney();
    },
    //汇总数量和金额
    getAllMoney() {
      this.totalCount = 0;
      this.totalMoney = 0;
      if (this.tableData) {
        this.tableData.forEach(element => {
          this.totalCount += element.count;
          this.totalMoney = this.totalMoney + element.price * element.count;
        });
      }
    }
  },
  created() {
    axios
      .get("http://jspang.com/DemoApi/oftenGoods.php")
      .then(response => {
        console.log(response);
        this.oftenGoods = response.data;
      })
      .catch(error => {
        console.log(error);
        alert("网络错误，不能访问");
      }),
      //读取分类商品列表
      axios
        .get("http://jspang.com/DemoApi/typeGoods.php")
        .then(response => {
          console.log(response);
          //this.oftenGoods=response.data;
          this.type0Goods = response.data[0];
          this.type1Goods = response.data[1];
          this.type2Goods = response.data[2];
          this.type3Goods = response.data[3];
        })
        .catch(error => {
          console.log(error);
          alert("网络错误，不能访问");
        });
  },
  mounted: function() {
    var orderHeight = document.body.clientHeight;
    document.getElementById("order-list").style.height = orderHeight + "px";
  }
};
</script>
<style scoped>
.title {
  height: 20px;
  border-bottom: 1px solid #d3dce6;
  background-color: #f9fafc;
  padding: 10px;
}
.often-goods-list ul li {
  list-style: none;
  float: left;
  border: 1px solid #e5e9f2;
  padding: 10px;
  margin: 5px;
  background-color: #fff;
}
.o-price {
  color: #58b7ff;
}
.cookList li {
  list-style: none;
  width: 23%;
  border: 1px solid #e5e9f2;
  height: auot;
  overflow: hidden;
  background-color: #fff;
  padding: 2px;
  float: left;
  margin: 2px;
}
.cookList li span {
  display: block;
  float: left;
}
.foodImg {
  width: 40%;
}
.foodName {
  font-size: 18px;
  padding-left: 10px;
  color: brown;
}
.foodPrice {
  font-size: 16px;
  padding-left: 10px;
  padding-top: 10px;
}
</style>