<template>
	<div class="pos">
		<div>
			<el-row>
				<el-col id='order-list1' :span='7'>
					<el-tabs>
						<el-tab-pane label="点餐">
							<el-table :data="tableData" style="width: 100%" border>
								<el-table-column prop="goodsName" label="商品名称">
								</el-table-column>
								<el-table-column prop="price" label="金额">
								</el-table-column>
								<el-table-column prop="count" label="量">
								</el-table-column>
								<el-table-column label="操作" width="100" fixed="right">
									<template scope="scope">
										<el-button type="text" size="small" @click="delSingleGoods(item)">删除</el-button>
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
							<el-button type="danger" @click="delAllGoods">删除</el-button>
							<el-button type="success" @click="checkout">结账</el-button>
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
				<el-col id="order-list2" :span="17">
					<div class="often-goods">
						<div class="title">常用商品</div>
						<div class="often-goods-list">

							<ul>
								<li v-for="item in oftenGoods" @click="addOrderList(item)">
									<span>{{item.goodsName}}</span>
									<span class="o-price">￥{{item.price}}元</span>
								</li>

							</ul>
						</div>
					</div>
					<div class="goods-type">

						<el-tabs>
							<el-tab-pane label="汉堡">
								<ul class='cookList'>
									<li v-for="item of type0Goods" @click="addOrderList(item)">
										<span class="foodImg"><img :src="item.goodsImg" width="100%"></span>
										<span class="foodName">{{item.goodsName}}</span>
										<span class="foodPrice">￥{{item.price}}.00元</span>
									</li>
								</ul>
							</el-tab-pane>
							<el-tab-pane label="小食">
								<ul class='cookList'>
									<li v-for="item of type1Goods" @click="addOrderList(item)">
										<span class="foodImg"><img :src="item.goodsImg" width="100%"></span>
										<span class="foodName">{{item.goodsName}}</span>
										<span class="foodPrice">￥{{item.price}}.00元</span>
									</li>
								</ul>
							</el-tab-pane>
							<el-tab-pane label="饮料">
								<ul class='cookList'>
									<li v-for="item of type2Goods" @click="addOrderList(item)">
										<span class="foodImg"><img :src="item.goodsImg" width="100%"></span>
										<span class="foodName">{{item.goodsName}}</span>
										<span class="foodPrice">￥{{item.price}}.00元</span>
									</li>
								</ul>
							</el-tab-pane>
							<el-tab-pane label="套餐">
								<ul class='cookList'>
									<li v-for="item of type3Goods" @click="addOrderList(item)">
										<span class="foodImg"><img :src="item.goodsImg" width="100%"></span>
										<span class="foodName">{{item.goodsName}}</span>
										<span class="foodPrice">￥{{item.price}}.00元</span>
									</li>
								</ul>
							</el-tab-pane>

						</el-tabs>
					</div>
				</el-col>
			</el-row>
		</div>
	</div>
</template>

<!-- 
  对外暴露
 -->
<script>
	import axios from 'axios'
	export default {
		name: "Pos",
		mounted: function() {
			var orderHeight = document.body.clientHeight;
			document.getElementById("order-list1").style.height = orderHeight + 'px';
			document.getElementById("order-list2").style.height = orderHeight + 'px';
		},
		methods: {
			//添加订单列表方法
			addOrderList(goods) {
				let isHave = false; //判断是否之前添加过该商品的

				//判断是否这个商品已经存在于订单列表
				for(let i = 0; i < this.tableData.length; i++) {
					// console.log(this.tableData[i].goodsId);
					if(this.tableData[i].goodsId == goods.goodsId) {
						isHave = true;
						break;
					}
				}

				//根据isHave值判断订单列表中是否出现过此商品
				if(isHave) {
					//数量增加
					let arr = this.tableData.filter(item => item.goodsId == goods.goodsId);
					arr[0].count++;
				} else {
					//不存在
					let newGoods = {
						goodsId: goods.goodsId,
						goodsName: goods.goodsName,
						price: goods.price,
						count: 1
					}
					this.tableData.push(newGoods);
				}
				this.getAllMoney();
			},
			//计算总数量和总价格
			getAllMoney() {
				this.totalCount = 0;
				this.totalMoney = 0;
				if(this.tableData) {
					for(var i = 0; i < this.tableData.length; i++) {
						var element = this.tableData[i];
						this.totalCount += element.count;
						this.totalMoney = this.totalMoney + (element.price * element.count);
					}
				}
			},
			//删除单个商品
			delSingleGoods(goods) {
				console.log(goods);
				var index = this.tableData.indexOf(goods);
				this.tableData.splice(index, 1);
				/*this.tableData=this.tableData.filter(o => o.goodsId !=goods.goodsId);*/

				this.getAllMoney();

			},
			//删除所有商品
			delAllGoods() {
				this.tableData = [];
				this.totalCount = 0;
				this.totalMoney = 0;
			},
			checkout() {
				if(this.totalCount != 0) {
					this.tableData = [];
					this.totalCount = 0;
					this.totalMoney = 0;
					this.$message({
						message: '结账成功，感谢你又为店里出了一份力!',
						type: 'success'
					});
					/*
					    post请求提交数据
					*/

				} else {
					this.$message.error('不能空结。老板了解你急切的心情！');
				}

			}
		},
		data: function() {
			return {
				tableData: [],
				oftenGoods: [],
				type0Goods: [],
				type1Goods: [],
				type2Goods: [],
				type3Goods: [],
				totalCount: 0, //总数量
				totalMoney: 0 //总价格

			}
		},
		created() {
			axios.get('http://jspang.com/DemoApi/oftenGoods.php')
				.then(response => {
					console.log(response);
					this.oftenGoods = response.data;
				})
				.catch(error => {
					console.log(error);
					alert('网络错误，不能访问');
				})
			//读取分类商品列表
			axios.get('http://jspang.com/DemoApi/typeGoods.php')
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
					alert('网络错误，不能访问');
				})
		},
	}
</script>
<style scoped>
	.title {
		height: 20px;
		border-bottom: 1px solid #D3DCE6;
		background-color: #F9FAFC;
		padding: 10px;
	}
	
	.often-goods-list ul li {
		list-style: none;
		float: left;
		border: 1px solid #E5E9F2;
		padding: 10px;
		margin: 5px;
		background-color: #fff;
	}
	
	.o-price {
		color: #58B7FF;
	}
	
	.goods-type {
		clear: both
	}
	
	.cookList li {
		list-style: none;
		width: 23%;
		border: 1px solid #E5E9F2;
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