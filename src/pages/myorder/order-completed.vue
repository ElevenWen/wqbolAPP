<style type="text/css" lang="less" scoped>
@import "../../common/index.less";
@import "./order.less";
// 服务进度
.swiperWrap{
	width: 100%;
	height: 2.4rem;
	background: #fff;
}
.processWrap{
	width: 100%;
	height: 2.4rem;
	background: #fff;
	.processCompany{
		margin-left: .413333rem;
		font-size: .4rem;
		font-weight: 600;
		.textIndex1(7.6rem);
		padding-top: .373333rem;
	}
	.processDetailWrap{
		margin: .373333rem 0 0 .386667rem;
		.processTime,.processIcon,.processDetail{
			display: inline-block;
		}
		.processTime{
			font-size: .32rem;
			color: rgb(102,102,102);
			vertical-align: top;
			div:nth-child(2){
				text-align: right;
    			margin-top: .133333rem;
			}  
		}
		.processIcon{
			position: relative;
			width: .026667rem;
			height: .88rem;
			background: rgb(216,216,216);
			margin-left: .346667rem;
			vertical-align: top;
			div{
				position: absolute;
				width: .186667rem;
				height: .186667rem;
				background: rgb(255,62,8);
				border-radius: 50%;
				top: .346667rem;
    			left: -0.08rem;
			}
		}
		.processDetail{
			margin-left: .373333rem;
			div:nth-child(1){
				font-size: .346667rem;
				font-weight: 400;
			}
			div:nth-child(2){
				margin-top: .2rem;
			}
		}
		.rightArrow{
			width: .2rem;
			height: .346667rem;
			.bg-image("../../assets/images/myorder/right_arrow");
			background-size: 100%;
			float: right;
			margin-right: .32rem;
		}
	}
}
.seeProcess{
  border: 1px solid #b2b2b2 !important;
  color: #000 !important;
	float: right;
	margin-top: .26667rem;
	margin-right: .133333rem;
}
#orderDetail ul.server {
  height: 100%;
  width: 20%;
  overflow: hidden;
}
#orderDetail .server li {
  padding-top: 0px;
  margin-top: 0.266667rem;
  width: 0.7027027027027027rem;
  .bg-image("../../assets/images/mine/myorder/server");
  background-size: 0.5945945945945946rem 0.5675675675675675rem;
  background-position: 0.05rem 0rem;
  color: white;
  font-size: 0.32432432432432434rem;
  display: inline-block;
  float: right;
  margin-right: 30%;
  text-align: center;
  line-height: 1.706667rem;
}

.footer span:nth-child(1),
.footer span:nth-child(2) {
  display: inline-block;
  // float: right;
  font-size: 0.40540540540540543rem;
  color: #666666;
  margin: 0.2702702702702703rem 0.40540540540540543rem 0 0;
  padding: 0.1891891891891892rem 0.1891891891891892rem;
  .border-1px(#666666);
  background: white;
}
.footer span:nth-child(1) {
  color: #fa2802;
  .border-1px(#FA2802);
}
.seeInvoice {
  color: black;
  border: 1px solid black;
  padding: 0rem 0.08108108108108109rem;
  border-radius: 0.08108108108108109rem;
  text-decoration: none;
  margin-left: 0.13513513513513514rem;
}
#orderDetail .footer .evaluat_b{
  background-color: #fff !important;
  margin-left: 3.306667rem;
  border: 1px solid #b2b2b2 !important;
  color: #666;
  margin-right: 0 !important;
}
#orderDetail .footer .buy_b{
  border:1px solid #FA2802 !important;
  color: #FA2802 !important;
  background: #fff !important;
  float: right;
}
// 完善信息
.completeInfo{
	height: 2.7027027027027026rem; 
	font-size: 0.373333rem;
	background-color: white;
	margin-top: .32rem;
	.companyNameInfo,.person,.personTel{
		padding: 0.5405405405405406rem 0 0 0.4rem;
		font-weight: 600;
		span{
			padding-left: 0.16216216216216217rem;
    		color: #666666;
		}
	}
	.person,.personTel{
		padding: 0.2702702702702703rem 0 0 0.4rem;
	}
	.person{
		letter-spacing: .119333rem;
		span{
			letter-spacing: 0;
		}
	}
}
</style>
<template>
	<div id="orderDetail">
		<header-title title="订单详情"  v-if="arr.IsElectronicContract" source="electronicContract" @showProps="showPropsFn"></header-title>
		<header-title title="订单详情"   v-else @showProps="showPropsFn"></header-title>
		<!-- <div style="height: 44px; width: 100%;"></div>
		<div class="headerHint" v-if="seen">
			<p>商家请求完结订单，您确认完成之后，平台将打款给商户；请谨慎操作！</p>
			<span class="closeHint" v-on:click="closeHint"></span>
		</div> -->
		<div class="header">
			<div class="headerIcon">;</div>
			<div class="headerWord">已完成</div>
			<ul class="server">
				<li  @click="customerService">客服</li>
			</ul>
		</div>
    <!-- 服务进度 -->
		<div class="siteSchedule" v-if="cusDeliveryData.success">
			<div class="orderIcon"><img src="../../assets/images/mine/site@3x.png">地址</div>
			<div class="orderName">{{cusDeliveryData.data.ConsigneeName}}    {{cusDeliveryData.data.ConsigneeMobile}}</div>
			<div class="orderDate">{{cusDeliveryData.data.ProvinceName}}{{cusDeliveryData.data.CityName}}{{cusDeliveryData.data.AreaName}} {{cusDeliveryData.data.Address}}</div>
			<span class="shippingAddress"></span>
		</div>
		<main class="cart-mianWrap"  >
	      <ul class="cart-itemsWrap ">
	        <li class="items-list singS"   v-for="(item,index) in arr.OrderDetails" :key="index">
		          <!-- 单品  -->
	          	<div v-if="item.ProductDetails.length == 0">
		            <div class="listMain">
			            <div class="infos">
			                <div class="innerWrap">
			                  	<img class="listImg" :src="item.ThumbImgURL" alt="" />
			                  	<div class="brief">
				                    <p>
										{{ item.Name }}
				                    </p>
				                    <!-- <p>
				                     类型：有限类型；刻章：不刻章
				                    </p> -->
				                    <p>{{item.ProductType}}</p>
			                 	</div>
			                  	<div class="purchWrap">
				                    <span class="themeC  nowPrice">{{item.Price | formatPrice | currency}}<b>{{item.Price | formatPriceX  }}</b></span> 
			                  	</div>
			                </div>
			            </div>
		            </div>
		           <!--  	勿删这个div
		            <div class="listfooter">
		              <span>促销&nbsp;&nbsp;</span><span>  ==暂无数据</span>
		            </div> -->
	          	</div> 
		    	 <!-- /单品  -->
			 	
			<!-- t套餐 -->
		        <div v-if="item.ProductDetails.length != 0">  
		        	<div  > 
	               	<h3  class="title" >
		                <span class="themeC"><strong class="font11">套装</strong></span>
		                <span class="themeC">{{ item.Price }}</span>  
		                <span class="couBtn themeC" v-if="(item.Price - item.OldPrice) != 0">
		                 	<strong class="font11">立省￥{{ item.Price - item.OldPrice }}</strong>
		                </span> 
	               	</h3>
	            </div>
							<div  v-for="(itemsTemp,index) in item.ProductDetails" :key="index"> 
					            
		
					            <div class="listMain" >
					              	<div class="checkboxWrap " v-if="0==0"></div> 
					              	<div class="infos">
						                <div class="innerWrap">
						                   	<img class="listImg"   :src="itemsTemp.ThumbImgURL" alt="" >
						                    <div class="brief">
						                         <p >
						                          {{ itemsTemp.Name}}
						                           
						                        </p>
						                         <p>
						                        	{{itemsTemp.ProductType}}
						                        </p> 
						                    </div>
						                    <div class="purchWrap">
												<span class="themeC  nowPrice">{{  itemsTemp.Price | formatPrice | currency}}<b>{{  itemsTemp.Price | formatPriceX  }}</b></span> 
						                    </div>
						                </div> 
					              	</div>
					            </div> 
							</div> 
		            <!-- 预留套餐促销 -->
		            <!-- <div  class="listfooter" v-if="a == 2" >
		                <span>促销&nbsp;&nbsp;</span><span>满199.00减50.00</span>
		            </div>    --> 
		        </div> 
	        <!-- /t套餐 -->
	        </li>
	      </ul>
	    </main>
	    <!-- 订单信息 -->
	    <div class="order">
	    	<div class="orderNumber">订单编号：<span>{{arr.OrderNumber}}</span>   
	    		<!-- &nbsp&nbsp&nbsp&nbsp<span>复制</span> -->
	    	</div>
	    	<div class="orderTime">下单时间：<span>{{ currentTime }}</span></div>
	    </div>
	    <!-- 支付方式 -->
	    <div class="payMethod">支付方式：<span>{{ payMethod(arr.PayType) }}</span></div>
	    <!-- 发票信息 -->
	    <div class="invoice">
	    	<div class="invoiceType">发票类型：<span>{{ arr.CusInvoiceType }}</span>
	    		<!-- <a :href="'https://112.74.135.230:3214'+arr.InvoicePath" v-if="arr.InvoicePath != null" download="发票" class="seeInvoice" @click="checkInvoice">
	    			测试发票
	    		</a> -->
	    		<a :href="'https://manage.wqbol.cn'+arr.InvoicePath" v-if="arr.InvoicePath != null" download="发票" class="seeInvoice" @click="checkInvoice">
	    			发票
	    		</a>
	    	</div>

	    	<div class="invoiceHead">发票抬头：<span>{{ arr.CusInvoiceName }}</span></div>
	    	<div class="invoiceDetail">发票内容：<span>明细</span></div>
	    </div>
      <!-- 完善资料信息 -->
      <div class="completeInfo">
        <div class="companyNameInfo">公司全称：<span>深圳市微企宝计算机有限公司</span></div>
        <div class="person">联系人：<span>文梅</span></div>
        <div class="personTel">联系电话：<span>18822843215</span></div>
      </div>
	    <div class="moneyWrap">
	    	<!-- 服务金额 -->
		    <div class="money">
				<div class="totalMoney">服务金额
					<span class="themeC  nowPrice">{{  money | formatPrice | currency}}<b>{{  money | formatPriceX  }}</b></span> 
				</div>
				<div class="discountMoney" v-if="arr.PreferentialAmount != '0.00'">优惠<span>-¥{{ arr.PreferentialAmount}}</span></div>
				<div class="discountMoney"  v-if="arr.BookkeepCurrency != 0">记账币<span>-¥{{ arr.BookkeepCurrency}}</span></div>
		    </div>
		    <div class="actualMoney"><span>实付款：</span>
				<span class="themeC  nowPrice">{{   arr.Amount | formatPrice | currency}}<b>{{   arr.Amount | formatPriceX  }}</b></span>
			</div>
	    </div>
	    <div class="finally"><span>————已经到底了————</span></div>
	    <!-- 取消订单 -->
	    <div class="footer">
        <span class="evaluat_b" @click="toEvaluation(arr)" v-if='arr.ProcessingState !=="已完成"'>去评价</span>
	    	<span class="buy_b" @click="buyAgain(arr)">再次购买</span>
        <span class="seeProcess" @click="orderSchedule(arr)">查看进度</span>
	    </div>
	</div>
</template>
<script>
import headerTitle from "../components/header-title";
import util from "../../util/tool";
import getData from "../../vuex/getData.js";
export default {
  data() {
    return {
      //ab : require('@/assets/images/test.png'),
      cusDeliveryData: {} //收货地址
    };
  },
  components: {
    headerTitle
  },
  computed: {
    arr() {
      var arrTemp = this.$store.state.myorder.productDetail;
      // //console.log("123454444",arrTemp);
      let local = {};
      local.isProvideContract = arrTemp.ElectronicContractTemplate;
      local.isSignContract = arrTemp.IsSignedElectronicContract;
      local.status = arrTemp.Status;
      local.contractId = arrTemp.ContractID;
      local.orderId = arrTemp.Id;
      local.orderstyle = arrTemp.ProcessingState;
      local.ContractTemplateID = arrTemp.ContractTemplateID;
      local.IsSignedElectronicContract = arrTemp.IsSignedElectronicContract;
      local.Id = arrTemp.Id;
      // console.log("已完成",local);
      util.saveToLocal(
        "orderDetail123",
        "orderDetail123",
        "orderDetail123",
        local
      );
      return arrTemp;
    },
    money() {
      var arrTemp = this.$store.state.myorder.productDetail;
      return arrTemp.OldAmount;
      // var moneyTemp = Number(arrTemp.Amount) + Number(arrTemp.PreferentialAmount);
      // var moneyTemp1 = moneyTemp.toString().split(".");
      // // 实现保留两位小数
      // if(moneyTemp1.length==1){
      //  	moneyTemp=moneyTemp.toString()+".00";
      //  	return moneyTemp;
      // }
      // if(moneyTemp1.length>1){
      //  	if(moneyTemp1[1].length<2){
      //   		moneyTemp=moneyTemp.toString()+"0";
      // 	}
      // 	return moneyTemp;
      // }
      // return moneyTemp1;
    },
    currentTime() {
      let Create = this.$store.state.myorder.productDetail.CreateTime;
      if(!Create){return}
      let timer = Create.substring(6, Create.lastIndexOf(")"));
      let time = new Date(parseInt(timer));
      var year = time.getFullYear();
      var month = time.getMonth() + 1;
      var date = time.getDate();
      var day = time.getDay();
      var h = time.getHours();
      var mm = time.getMinutes();
      var s = time.getSeconds();
      var currentTime =
        year + "/" + month + "/" + date + " " + h + ":" + mm + ":" + s;
      // //console.log("currentTime",currentTime);
      return currentTime;
    }
  },
  created(value) {
    //获取收货地址数据
    let params = {
      id: this.$route.query.id,
      dataType: "json"
    };
    getData
      .getOrderAddress(params)
      .then(res => {
        //获取收货地址
        this.cusDeliveryData = res;
      })
      .catch(err => {
        console.log(err);
      });
    var data = {
      params: {
        id: this.$route.query.id
      }
    };
    this.$store.dispatch("request_data_orderDetail", data);
  },
  methods: {
    //订单进度
    orderSchedule(val){
			// this.$router.push({path:"/mine/toMyOrder/orderSchedule"});
			this.$router.push({path:"/mine/toMyOrder/orderSchedule",query:{orderId:val.Id}});
    },
    toEvaluate() {
      this.$router.push("/myorder/toEvaluate");
    },
    payMethod(val) {
      switch (val) {
        case "Alipay":
          return "支付宝支付";
          break;
        case "WeChat":
          return "微信支付";
          break;
        case "Balance":
          return "余额支付";
          break;
        case "Unionpay":
          return "银联支付";
          break;
        case "Substitute":
          return "朋友代付";
          break;
        case "BookCurrency":
          return "记账币支付";
          break;
      }
    },
    // download(value){
    // 	//console.log(value);
    // 	 // let url = urls("downloadQRcode")+"?size=" + self.sizeCode;
    //   //            window.open(url, "_self" , "top=5,left=5,resizable=yes,menubar=yes,toolbar=yes,scrollbars=yes,status=yes");
    // },
    showPropsFn(data) {
      this.$emit("showProps", true);
    },
    buyAgain(value) {
      // //console.log("dfdsf",value);
      //清除购物车的本地记录 防止重复
      util.removeLocal("_CART");
      var id = {
        params: {
          id: value.Id
        }
      };

      // 再次购买调接口
      this.$store.dispatch("request_data_bugagain", id).then(res => {
        // //console.log("00000000",this.$store.state.myorder.buyagain.list);

        if (!this.$store.state.myorder.buyagain) {
          return;
        }

        var count = this.$store.state.myorder.buyagain.list[0].Num;
        // 再次购买接口成功调起后再调购物车列表接口
        this.$store.dispatch("request_data_cart").then(res => {
          let localS = {
            list: [],
            recordCount: count
          };
          localS.list = this.$store.state.myorder.buyagain.list;

          // //console.log("",this.$store.state.cart.cartInfos.list);
          // 本地存储
          var localstorage = util.saveToLocal(
            "_CART",
            this.getToken(),
            "data",
            localS
          );
          // 跳转到确认订单页面
          this.$router.push({
            path: "/cart/comfirmOrder",
            query: { id: value.Id }
          });
        });
      });
    },
    toEvaluation(val) {
      // //console.log("valvalval",val);
      var orderD = val.OrderDetails;
      orderD.forEach((item, index) => {
        var _ProductId = item.ProductIdd;
        var _orderNumber = item.OrderId;
        this.$router.push({
          path: "/mine/toMyOrder/toEvaluate",
          query: {
            ProductId: _ProductId,
            OrderId: _orderNumber,
            listDate: val.OrderDetails
          }
        });
        return _ProductId;
      });
    },
    checkInvoice() {
      // alert("已下载到本地，请查看");
      MessageBox("提示", "已下载到本地，请查看");
    },
    customerService() {
      //客服聊天页面
      //window.open("https://html.ecqun.com/kf/sdk/openwin.html?corpid=7331675&cstype=rand&mode=0&cskey=SqGFFE7Glw76x6szdw&scheme=0");
      this.$router.push('/userChatSever')
    }
  },
  filters: {
    formatPrice(detail) {
      return util.formatPrice(detail);
    },
    formatPriceX(detail) {
      return util.formatPriceX(detail);
    },
    currency(detail) {
      return util.currency(detail);
    }
  }
};
</script>