<template>
	<div id="getCoupon">
		<header-title title="领券中心" source="share"></header-title>
		
		<mt-navbar v-model="selected" class="tabbar"  >
			<mt-tab-item id="1" class="swiper">通用券</mt-tab-item>
			<mt-tab-item :id="index+2" class="swiper" v-for="(data,index) in serverType" :key="index" @click.native="goRequest(data.Id,data.Name)">{{data.Name}}</mt-tab-item>
		</mt-navbar>
		

		<mt-tab-container v-model="selected"  >
		    <mt-tab-container-item id="1">
		  		<div class="selected_list">
		  			<div class="robbed" :class="{already_received:data.isUsed,grab_the:data.ActivatedAmount==data.Amount}" v-for="(data,index) in couponData">
		  				<div class="robbed_left">
		  					<span>¥</span>
		  					<span>{{data.Price}}</span>
		  					<span>满{{data.Money}}元可用</span>
		  				</div>
		  				<div class="robbed_center">
		  					<span>通用类（除特殊商品）</span>
		  					<span>{{data.ExpiryDate}}</span>
		  				</div>
		  				<div class="robbed_right" v-if="(!(data.ActivatedAmount==data.Amount))&&!data.isUsed">
		  					<span>已抢{{parseInt((data.ActivatedAmount/data.Amount)*100)}}%</span>
		  					<span @click="goReceive(false,index,data.Id)">立即领取</span>
		  				</div>
		  				<div class="robbed_right" v-if="data.ActivatedAmount==data.Amount">
		  					<span></span>
		  					<span @click="toUsed(data.Id)">进场看看</span>
		  				</div>
		  				<div class="robbed_right" v-if="data.isUsed">
		  					<span></span>
		  					<span @click="toUsed(data.Id)">去使用</span>
		  				</div>
		  			</div>
		  		</div>
		  		<div class="dataNull"    v-if="couponData.length == 0"></div>
		    </mt-tab-container-item>
	    	<mt-tab-container-item :id="index+2" v-for="(data,index) in serverType" :key="index">
		   		<div class="selected_list">
		   			<div class="robbed" :class="{already_received:val.isUsed,grab_the:val.ActivatedAmount==val.Amount}" v-for="(val,i) in allCouponData">
		  				<div class="robbed_left">
		  					<span>¥</span>
		  					<span>{{val.Price}}</span>
		  					<span>满{{val.Money}}元可用</span>
		  				</div>
		  				<div class="robbed_center" v-if="val.CouType=='品类卷'">
		  					<span>{{val.ProductName}}</span>
		  					<span>{{val.ExpiryDate}}</span>
		  				</div>
		  				<div class="robbed_center" v-if="val.CouType=='单品卷'">
		  					<span>{{val.ProductName}}</span>
		  					<span>{{val.ExpiryDate}}</span>
		  				</div>
		  				<div class="robbed_right" v-if="(!(val.ActivatedAmount==val.Amount))&&!val.isUsed">
		  					<span>已抢{{parseInt((val.ActivatedAmount/val.Amount)*100)}}%</span>
		  					<span @click="goReceive(true,i,val.Id)">立即领取</span>
		  				</div>
		  				<div class="robbed_right" v-if="val.ActivatedAmount==val.Amount">
		  					<span></span>
		  					<span @click="toUsed(val.Id)">进场看看</span>
		  				</div>
		  				<div class="robbed_right" v-if="val.isUsed">
		  					<span></span>
		  					<span @click="toUsed(val.Id)">去使用</span>
		  				</div>
		  			</div>
		  		</div>
		  		<div class="dataNull" v-if="allCouponData.length == 0"></div>
	  		</mt-tab-container-item>
		</mt-tab-container>
		
		
		
	</div>
</template>

<script>
import headerTitle from "../components/header-title";
import footerNav from '../../footer-nav';
import { Navbar, TabItem, Indicator, Toast } from 'mint-ui';

import getd from '../../vuex/getData.js';
export default{
    data(){
    	return {
    		selected:"1", //tab选项卡默认选中第一个
    		couponData:[],
    		universalCouponData:[],//通用卷
    		serverType:[],//服务类型
    		allCouponData:[],
    	}
    },
    components:{
    	headerTitle,
    	footerNav,
	    Navbar,
	    TabItem
    },
    created(){
    	//获取服务类型
    	getd.SERVERLIST()
    	.then((res)=>{
    		this.serverType = res.data.list;
    	})
    	
    	//筛选出通用卷类型
    	getd.getAllCouponList()
    	.then((res)=>{
    		this.otherCouponData = res.data;
    		res.data.forEach((arr,i)=>{
    			arr.isUsed = false;
    			//通用卷
    			if (arr.CouType == "通用卷") {
    				this.couponData.push(arr);
    			}
    		})
    		Indicator.close();
    	})
    	
   },
   methods:{
   	   	goReceive(index,i,id){
	   	   	if (index) {
	   	   		this.allCouponData[i].isUsed = true;
	   	   		this.$set(this.allCouponData,i,this.allCouponData[i]);
	   	   	}else{
	   	   		this.couponData[i].isUsed = true;
	   	   	}
	   	   	var params ={
	   	   		id:id,
	   	   		datatype:"json"
	   	   	}
	   	   	getd.usedCusCoupon(params)
	   	   	.then((res)=>{
	   	   		Toast({
					message: res.msg,
				  	position: 'middle',
				  	duration: 2000
				})
	   	   	})
	   	   	.catch((error)=>{
	   	   		Toast({
					message: error.data.msg,
				  	position: 'middle',
				  	duration: 2000
				})
	   	   		Indicator.close();
	   	   	})
   	   	},
   	   	
   	   	toUsed(id){
    		this.$router.push({path:'/home/searchList',query:{id:id,style:2}});
    	},
    	goRequest(id,name){
    		var params = {
	    		id:id
	    	}
	    	getd.getCouponListByClassId(params)
	    	.then((res)=>{
	    		this.allCouponData = res.data.list;
	    		this.allCouponData.forEach((val)=>{
	    			val.isUsed = false;
	    			if(val.CouType=="品类卷"){
	    				val.ProductName = "适用于所有"+name;
	    			}
	    		})
	    	})
    	}
   }
}
</script>

<style lang="less" type="stylesheet/css" scoped>
	@import "../../common/index.less";
	@import './mine.less';
	.popularity_return{
     padding-left: 0rem;
}
 
</style>