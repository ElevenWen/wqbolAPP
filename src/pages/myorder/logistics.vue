<style lang="less" scoped>
@import "../../common/index.less";
    #logistics{
        background: #fff;
    }
    .headerImg{
        width: 100%;
        height: 5.733333rem;
        // background: plum;
        .bg-image("../../assets/images/myOrder/Courier");
        background-size: 100% 100%;
        margin-top: 1.173333rem;
        position: relative;
        span:nth-child(1){
            display: inline-block;
            width: .56rem;
            height: .533333rem;
            .bg-image("../../assets/images/myOrder/Courier2");
            background-size: 100% 100%;
            position: absolute;
            bottom: .4rem;
            left: .4rem;
        }
        span:nth-child(2){
            display: inline-block;
            font-size: .4rem;
            position: absolute;
            bottom: 0.4rem;
            left: 1.333333rem;
        }
    }
    .orderNum{
        height: 1.2rem;
        font-size: .4rem;
        line-height: 1.2rem;
        padding-left: .4rem;
        background: #fff;
        border-bottom: 1px solid rgb(240,240,240);
    }
    .progressBar{
        // height: 12.162162162162161rem;
        width: 100%;
        padding: 0.6756756756756757rem .533333rem;
        background: #fff;
        div{
            li{
                display: inline-block;
            }
            li:nth-child(1){
                text-align: right;
                vertical-align: top;
                span:nth-child(1){
                    display: block;
                    font-size: .346667rem;
                    color: rgb(102,102,102);
                }
                span:nth-child(2){
                    display: block;
                    font-size: .32rem;
                    color: rgb(102,102,102);
                    margin-top: .133333rem;
                }
            }
            .grayIcon{
                position: relative;
                width: .026667rem;
                min-height: 2.2rem;
                margin-left: .466667rem;
                vertical-align: top;
                background: rgb(207,207,207);
            }
            .carIcon,.grayBall{
                position: absolute;
                display: inline-block;
                width: .4rem;
                height: .4rem;
                background-size: 100%;
                background-size: 100% 100%;
                left: -0.196667rem;
            }
            .carIcon{
                .bg-image("../../assets/images/myOrder/location");
            }
            .grayBall{
                width: .213333rem;
                height: .213333rem;
                background: rgb(207,207,207);
                border-radius: 50%;
                left: -0.096667rem;
            }
            li:nth-child(3){
                width: 75%;
                margin-left: .4rem;
                font-size: .346667rem;
            }
        }
        div:nth-last-child(1){
            li:nth-child(2){
                background: #fff;
            }
        }
        .proDetail,.proDetail2{
            width: 6.666667rem;
            display: inline-block;
            padding-left: .8rem;
            line-height: .533333rem;
            margin-top: -0.153333rem;
            margin-bottom: .533333rem;
            a:nth-child(2){
                color: rgb(48,161,248) !important;
            }
        }
        .proDetail{
            .processWord{
                color: rgb(255,120,0);
            }
            .processWordGray{
                color: rgb(216,216,216);
            }
        }
        .proDetail2{
            .processWord{
                color: rgb(216,216,216);
            }
            .processWordGray{
                color: rgb(216,216,216);
            }
        }

   }
   .noInfoWrap{
        margin-top: 1rem;
        background: #fff;
        div:nth-child(1){
            width: 4.56rem;
            height: 4rem;
            .bg-image("../../assets/images/mine/nullCart");
            background-size: 100%;
            margin: 0 auto;
        }
        div:nth-child(2){
            width: 6rem;
            margin: .626667rem auto;
            font-size: .373333rem;
            color: rgb(102,102,102);
            text-align: center;
        }
   }
</style>
<template>
    <div id="logistics">
        <header-title title="快递查询"></header-title>
        <div class="headerImg">
            <span></span>
            <span>{{logisticsCompany}}</span>
        </div>
        <div class="orderNum">快递单号：{{logisticsNo}}</div>
        <!-- 进度 -->
        <div class="progressBar" v-if="isProcess">
            <div class="progress" v-for="(items,index) in logistics" :key="index">
                <ul>
                    <li><span>{{items.AcceptTime.substring(5,10)}}</span><span>{{items.AcceptTime.substring(11,16)}}</span></li>
                    <li class="grayIcon">
                        <span :class="items.step == 1 ? 'carIcon' : 'grayBall'"></span>
                        <p class="proDetail" :class="isProcess ? 'proDetail' : 'proDetail2'">
                            <!-- 离开龙华中转站发往龙华大浪营业厅 -->
                            <span :class="items.step == 1 ? 'processWord' : 'processWordGray'">{{items.AcceptStation}}</span>
                            <span v-if="items.Remark" class="mark">备注：{{items.Remark}}</span>
                            <!-- <a  :href="'tel:'+telphone"  @click="contact(arr); return false;">联系业务员</a> -->
                            <!-- <a href="tel:18822843215">18822843215</a> -->
                        </p>
                    </li>
                </ul>
            </div>
        </div>
        <!-- 暂无物流信息 -->
        <div class="noInfoWrap" v-else>
            <div></div>
            <div>暂无物流信息</div>
        </div>
    </div>
</template>
<script>
import headerTitle from '../components/header-title';
import getData from '../../vuex/getData.js';
export default {
    data(){
        return {
            current:0,
            current1:1,
            current2:2,
            current3:3,
            isProcess:true,
            logistics:[],
            logisticsCompany:'',
            logisticsNo:'',
        }
    },
    mounted(){
        this.logisticsCompany = this.$route.query.logistics;
        this.logisticsNo = this.$route.query.logisticsCode;
        let params = {
            // "3967950525457"
            LogisticCode : this.logisticsNo
        }
        getData.getOrderTraces(params).then(res => {
            console.log("物流信息",res)
            this.logistics = res.data.list.reverse();
            let currentStep = 0;
            this.logistics.forEach((val,i) => {
                currentStep++;
                val.step = currentStep
            })
            console.log("this.logistics",this.logistics)
        }) 
    },
    components:{
        headerTitle,
    },
}
</script>
