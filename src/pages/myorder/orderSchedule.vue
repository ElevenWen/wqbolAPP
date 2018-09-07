<template>
	<div id="orderSchedule">
		<div class="popularity_return" id="title">
            <span  class="return" @click.stop.prevent="goBack()"></span>
			<span id="title_name">办理进度</span>
		</div>

		<div class="wraper">
		<div class="swipe-wrapper" ref="swipe_wrapper" id="swipe_wrapper" >
	        <mt-swipe :auto="0" ref="swipeWrapper" :show-indicators="false" @change="handleChange" :continuous="false">
	            <mt-swipe-item class="swip-item-1 item" v-for="(item,index) in companyArr" :key="index" ref="mtItem">
					<div class="topWrapper">
						<div class="scheduleTop">
							<div class="schedule">
								<div class="scheduleImg">
									<!-- <img src="../../assets/images/home/book.png"> -->
									<img :src="item.Image">
								</div>
								<span>{{item.ProductName}}</span>
								<span>{{item.TypeName}}</span>
								<span>X{{item.Num}}</span>
							</div>
						</div>
						<div class="companyName">{{allName}}<span @click="moreCompany(item,index)" v-if="item.Num > 1"></span></div>
	            		<button class="prev-button flex-item" @click="prev(index)" v-if="index != 0"></button>
		        		<button class="next-button flex-item" @click="next(index)" v-if="index != (companyArr.length - 1)"></button>
					</div>
					<div class="progressBar" v-if="isProcess">
	            		<div class="progressOne" v-for="(items,index) in processArr" :key="index">
	            			<ul>
	            				<li><span>{{items.month}}</span><span>{{items.hours}}</span></li>
	            				<li :class="{isCompleted:items.TaskState == 2,isProcess:items.TaskState == 1,noStart:items.TaskState == 0}">
	            					<span class="processStep"></span>
									<span :class="{noStartWord:items.TaskState == 0}" v-if="items.TaskState == 0">未开始</span>
									<span :class="{isProcessWord:items.TaskState == 1}" v-if="items.TaskState == 1">进行中</span>
									<span :class="{isCompletedWord:items.TaskState == 2}" v-if="items.TaskState == 2">已完成</span>
									<div class="proDetail">
										<span>{{items.Name}} <span v-show="items.TaskState != 2">（预估{{items.WorkHour}}）</span></span>
										<span v-if="items.TaskRemark">{{items.TaskRemark}}</span>
										<span v-if="items.TaskExpressName">{{items.TaskExpressName}}：<span @click="toLogistics(items)" v-if="items.TaskExpressNo">{{items.TaskExpressNo}}</span></span>
									</div>
	            				</li>
	            			</ul>
	            		</div>
	            	</div>
					<!-- 没有服务进度时 -->
					<div class="noProcess" v-else>
						<!-- <img src="../../assets/images/businessQuery/bgD@2x.png" alt=""> -->
						<div></div>
						<div>服务进度暂未更新，请耐心等待~</div>
					</div>
	            </mt-swipe-item>
	        </mt-swipe>
			<!-- 公司弹窗 -->
			<div class="companyWindow" v-if="isShowWindow">
				<div class="windowWrap">
					<div class="windowTitle">请选择服务商品</div>
					<li v-for="(items,index) in nameArr" :key="index" :class="{windowName:index == isSelect,windowNameB: !(index == isSelect)}" @click.stop.prevent="selectCompany(items,index)">{{items.ProductName}}-{{items.CompanyName}}
						<span :class="{isSelectName:index == isSelect}"></span>
					</li>
					<div class="cancelBtn" @click="cancelWindow">取消</div>
				</div>
			</div>
	    </div>
		</div>
	</div>
</template>

<script>
	import headerTitle from '../components/header-title';
	import { MessageBox,Toast  } from 'mint-ui';  
	import getData from '../../vuex/getData.js';
	import util from '../../util/tool';
	export default {
		data(){
			return {
				isSelect:'0',
				isShowWindow:false,
				isProcess:'',
				companyArr:[],
				processId:"",
				processArr:[],
				orderId:"",
				nameArr:[],
				currentIndex:'',
				detailId:"",
				allName:'',
			}
		},
		mounted(){
			let params = {
				params : {
					id : this.$route.query.orderId
				}
			}
			this.getCompanyName(params);
		},
		methods:{
			goBack(){
				this.$router.go(-1);
			},
			handleChange(index){
				this.currentIndex = index;
				this.companyArr.forEach((val,i) => {
					if(i == index){
						this.processId = val.ServiceStepId;
						this.orderId = val.DetailId;
						if(val.CompanyName){
							this.allName = val.ProductName + "-" +val.CompanyName;
						}else{
							this.allName = val.ProductName;
						}
					}
				})
				let data = {
					params : {
						id : this.processId,
						OrderId : this.$route.query.orderId,
						DetailId: this.orderId
					}
				}
				this.getProcess(data);
			},
			prev(i) {
				this.$refs.swipeWrapper.prev();
            },
            next(i) {
				this.$refs.swipeWrapper.next();
			},
			selectCompany(val,i){
				console.log(val,i);
				this.isSelect = i;
				this.isShowWindow = false;
				if(val.CompanyName){
					this.allName = val.ProductName + "-" +val.CompanyName;
				}else{
					this.allName = val.ProductName;
				}
				// this.allName = val.ProductName + "-" + val.CompanyName;
				this.companyArr.forEach((items,index) => {
					if(i == index){
						this.detailId = items.DetailId
					}
				})
				let params = {
					params : {
						id : val.ServiceStepId,
						OrderId : this.$route.query.orderId,
						DetailId:this.detailId
					}
				}
				this.getProcess(params);
			},
			cancelWindow(){
				this.isShowWindow = false;
			},
			moreCompany(val,i){
				this.isShowWindow = true;
				let params = {
					params : {
						OrderDetailId:val.DetailId
					}
				}
				this.getName(params);
			},
			toLogistics(val){
				this.$router.push({path:"/mine/toMyOrder/logistics",query:{logisticsCode:val.TaskExpressNo,logistics:val.TaskExpressName}});
			},
			getCompanyName(params){
				getData.GetCommodityList(params).then((res) => {
					// console.log("getCompanyName",res)
					this.companyArr = res.data.list;
					this.processId = this.companyArr[0].ServiceStepId;
					this.orderId = this.companyArr[0].DetailId;
					if(this.companyArr[0].CompanyName){
						this.allName = this.companyArr[0].ProductName + '-' +this.companyArr[0].CompanyName	
					}else{
						this.allName = this.companyArr[0].ProductName
					}
					
						let data = {
							params : {
								id : this.processId,
								OrderId : this.$route.query.orderId,
								DetailId: this.orderId
							}
						}
						this.getProcess(data);
						let params = {
							params : {
								OrderDetailId:this.orderId
							}
						}
						this.getName(params);
				})
			},
			getProcess(params){
				getData.GetServiceProcessById(params)
				.then((res) => {
					console.log("getProcess",res)
					let arr = res.data.list;
					this.processArr = arr.reverse();

					if(this.processArr.length != 0){
						this.isProcess = true;
						for(let i = 0;i<this.processArr.length;i++){
							// 根据不同的状态来判断时间
							if(this.processArr[i].TaskState == 1){
								let currentTime1 = this.processArr[i].HandleTime.replace(/\D/gi, "");
								this.processArr[i].month = util.formatDate(currentTime1,"MM-dd");
								this.processArr[i].hours = util.formatDate(currentTime1,"hh:mm");
							}else if(this.processArr[i].TaskState == 2){
								let currentTime2 = this.processArr[i].EndTime.replace(/\D/gi, "");
								this.processArr[i].month = util.formatDate(currentTime2,"MM-dd");
								this.processArr[i].hours = util.formatDate(currentTime2,"hh:mm");
							}
						}
					}else{
						this.isProcess = false;
					}
				})
			},
			getName(params){
				getData.GetOrderDetailList(params).then(res => {
					console.log("GetOrderDetailList",res)
					this.nameArr = res.data.list;
				})  
			},
		},
		components:{
		   headerTitle,
		},
	}
</script>

<style lang="less" type="stylesheet/css" scoped>
 @import "../../common/index.less";
 @import "./order";
</style>
