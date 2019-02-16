<template>
  <div id="pos">
  	<el-row>
  		<el-col :span='8' class="pos-order" id="order-list">
			<el-tabs>
				<el-tab-pane label="点餐">
					<el-table :data="tableData" border style="width:100%">
						<el-table-column prop="goodsName" label="商品名称"></el-table-column>
						<el-table-column prop="count" label="数量" width="70"></el-table-column>
						<el-table-column prop="price" label="金额" width="70"></el-table-column>
						<el-table-column prop="operation" label="操作" width="140">
							<template scope="scope">
								<el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
								<el-button type="text" size="small" @click="reduceGoods(scope.row)">减少</el-button>
								<el-button type="text" size="small" @click="delSingleGoods(scope.row)">删除</el-button>
							</template>
						</el-table-column>
					</el-table>
					<div class="totalDiv" v-show="tableData.length !=0">
						<small>数量:</small>{{totalCount}} &nbsp;&nbsp;&nbsp; <small>金额:</small>{{totalMoney}}元
					</div>
					<div class="div-btn">
						<el-button type="warning" @click="guadan()">挂单</el-button>
						<el-button type="danger" @click="delAllGoods()">删除</el-button>
						<el-button type="success" @click="checkout()">结账</el-button>
					</div>
				</el-tab-pane>
				<el-tab-pane label="挂单">
					挂单
				</el-tab-pane>
				<el-tab-pane label="外卖">
					外卖
				</el-tab-pane>
			</el-tabs>
  		</el-col>
  		<el-col :span='16'>
  			<div class="often-goods">
  				<div class="title">常用商品</div>
  				<div class="often-goods-list">
  					<ul>
  						<li v-for="goods in oftenGoods" :key="goods" @click="addOrderList(goods)">
  							<span>{{goods.goodsName}}</span>
  							<span class="o-price">￥{{goods.price}}元</span>
  						</li>
  					</ul>
  				</div>
  			</div>
  			<div class="goods-type">
  				<el-tabs>
  					<el-tab-pane label="汉堡">
  						<div>
  							<ul class="cookList">
  								<li v-for="typeGood in type0Goods" :key="typeGood" @click="addOrderList(typeGood)">
  									<span class="foodImg"><img :src="typeGood.goodsImg" width="100%"></span>
  									<span class="foodName">{{typeGood.goodsName}}</span>
  									<span class="foodPrice">￥{{typeGood.price}}元</span>
  								</li>
  							</ul>
  						</div>
  					</el-tab-pane>
  					<el-tab-pane label="小食">
  						<ul class="cookList">
  								<li v-for="typeGood in type1Goods" :key="typeGood" @click="addOrderList(typeGood)">
  									<span class="foodImg"><img :src="typeGood.goodsImg" width="100%"></span>
  									<span class="foodName">{{typeGood.goodsName}}</span>
  									<span class="foodPrice">￥{{typeGood.price}}元</span>
  								</li>
  							</ul>
  					</el-tab-pane>
  					<el-tab-pane label="饮料">
  						<ul class="cookList">
  								<li v-for="typeGood in type2Goods" :key="typeGood" @click="addOrderList(typeGood)">
  									<span class="foodImg"><img :src="typeGood.goodsImg" width="100%"></span>
  									<span class="foodName">{{typeGood.goodsName}}</span>
  									<span class="foodPrice">￥{{typeGood.price}}元</span>
  								</li>
  							</ul>
  					</el-tab-pane>
  					<el-tab-pane label="套餐">
  						<ul class="cookList">
  								<li v-for="typeGood in type3Goods" :key="typeGood" @click="addOrderList(typeGood)">
  									<span class="foodImg"><img :src="typeGood.goodsImg" width="100%"></span>
  									<span class="foodName">{{typeGood.goodsName}}</span>
  									<span class="foodPrice">￥{{typeGood.price}}元</span>
  								</li>
  							</ul>
  					</el-tab-pane>
  				</el-tabs>
  			</div>
  		</el-col>
  	</el-row>
  </div>
</template>

<script>
import axios from 'axios';
export default {
	name: 'pos',
  	data(){
  	return{
  		tableData:[

  		],
        oftenGoods:[
        	
        ],
        type0Goods:[
        	
        ],
        type1Goods:[
        	
        ],
        type2Goods:[
        	
        ],
        type3Goods:[
        	
        ],
        totalMoney:0,
        totalCount:0
  	}
  	},
  	created:function(){
  	// 拉取常用商品数据
  		axios.get('http://jspang.com/DemoApi/oftenGoods.php')
  		.then(reponse=>{
  		//console.log(reponse);
  		this.oftenGoods=reponse.data;
  	})
  	.catch(error=>{
  		//console.log(error);
  		alert('网络错误');
  	})
  	//拉取分类商品数据
  	axios.get('http://jspang.com/DemoApi/typeGoods.php')
  	.then(reponse=>{
  		this.type0Goods=reponse.data[0];
  		this.type1Goods=reponse.data[1];
  		this.type2Goods=reponse.data[2];
  		this.type3Goods=reponse.data[3];
  	})
  	.catch(error=>{
  		alert('网络异常');
  	})
  	},
  // 使用mounted钩子函数来设置订单栏高度。
 	  mounted:function(){
    	  var orderHeight=document.body.clientHeight;
      	document.getElementById("order-list").style.height=orderHeight+'px';
  	},
  	methods:{
  		//增加
  		addOrderList(goods){
  			this.totalMoney=0;
  			this.totalCount=0;
  			//商品是否在订单列表中
  			let isHave=false;
  			for(let i=0;i<this.tableData.length;i++){
  				if(this.tableData[i].goodsId == goods.goodsId){
  					isHave=true;
  				}
  			}
  			//根据判断值编写业务逻辑
  			if(isHave==true){
  				//改变列表中商品数量
  				let arr=this.tableData.filter(o=>o.goodsId == goods.goodsId);
  				arr[0].count++;
  			}else{
  				let newGoods={goodsId:goods.goodsId,goodsName:goods.goodsName,price:goods.price,count:1};
  				this.tableData.push(newGoods);
  			}
  			this.getAllMeony();
  		},
  		//减少
  		reduceGoods(goods){

  			let arr=this.tableData.filter(o=>o.goodsId == goods.goodsId);
  			if(arr[0].count>=2){
  				arr[0].count--;
  			}else{
  				this.tableData=this.tableData.filter(o=>o.goodsId != goods.goodsId);
  			}			
  				this.getAllMeony();
  		},
  		//删除单个商品
  		delSingleGoods(goods){
  			this.tableData=this.tableData.filter(o=>o.goodsId != goods.goodsId);
  			this.getAllMeony();
  		},
  		//删除所有商品
  		delAllGoods(){
  			this.tableData=[];
  			this.totalCount=0;
  			this.totalMoney=0;
  		},
  		//模拟结账
  		checkout(){
  			if(this.totalCount!=0){
  				this.tableData=[];
  				this.totalCount=0;
  				this.totalMoney=0;
  				this.$message({
  					message:'结账成功!',
  					type:'success'
  				});
  			}else{
  				this.$message.error('不能空结!');
  			}
  		},
  		//模拟挂单
  		guadan(){
  			this.$message.warning('挂单成功!');
  		},
  		//汇总数量和金额
  		getAllMeony(){
  			this.totalMoney=0;
  			this.totalCount=0;
  			if(this.tableData){
  			// 计算汇总金额和数量
  				this.tableData.forEach((element)=>{
  					this.totalCount+=element.count;
  					this.totalMoney=this.totalMoney+(element.price*element.count);
  				})
  			}
  		}
  	}
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
	.pos-order{
		background-color: #f9fafc;
		border-right:1px solid #ccc;
	}
	.div-btn{
		margin-top: 10px;
	}
	.title{
		height: 20px;
		border-bottom: 1px solid #d3dce6;
		background-color: #f9fafc;
		padding: 10px;
		text-align: left;
	}
	.often-goods-list ul li{
		list-style: none;
		float: left;
		border: 1px solid #E5E9F2;
		padding: 10px;
		margin: 10px;
		background-color: #fff;
		cursor: pointer;
	}
	.o-price{
		color: #58b7ff;
	}
	.goods-type{
		clear: both;
	}
	.cookList li{
       list-style: none;
       width:23%;
       border:1px solid #E5E9F2;
       height: auot;
       overflow: hidden;
       background-color:#fff;
       padding: 2px;
       float:left;
       margin: 2px;
       cursor: pointer;
   }
   .cookList li span{
       
        display: block;
        float:left;
   }
   .foodImg{
       width: 40%;
   }
   .foodImg img{
   		width: 70%;
   		float: left;
   }
   .foodName{
       font-size: 18px;
       padding-left: 10px;
       color:brown;
   }
   .foodPrice{
       font-size: 16px;
       padding-left: 10px;
       padding-top:10px;
   }
   .totalDiv{
		background-color: #fff;
		padding: 10px;
		border-bottom: 1px solid #d3dce6;
   }
</style>
