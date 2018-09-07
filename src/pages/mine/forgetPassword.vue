<style type="text/css" lang="less" scoped>
	@import "../../common/index.less";
	@import '../../../node_modules/mint-ui/lib/swipe/style.css';
	@import 'forgetPassword.less';

</style>
<template>
	<div id="newRegister">
		<div class="popularity_return">
			<span class="return" @click="goBack"></span>
			<span >忘记密码</span>
		</div>

		<div class="register_input">
			<form  @submit.prevent="submit"   >
				<div class="phone_numbor"><span>手机号</span>
					<input type="number" v-model="register.phone" placeholder="请输入手机号" @blur="verifyP()"/>
					<span class="eliminate" @click.stop.prevent="empty('phone')" v-if="register.phone"></span>
				</div>
				<div class="verification">
					<span class="code">验证码</span>
					<input id="code" v-model="register.noteCode" placeholder="短信验证码" @blur="verifyM"/>
					<span class="get_verification get_verification_end"     @click.stop.prevent="getVerification" v-if="isGet">获取验证码</span>
					<span class="get_verification get_verification_waiting"  v-if="!isGet">重新获取{{time}}s</span>


				</div>
				<div class="phone_numbor">
					<span>新密码</span>
					<input type="password" v-model="register.password" placeholder="请设置新密码" @blur="verifyPassword()"/>
					<span class="eliminate" @click.stop.prevent="empty('password')" v-if="register.password"></span>
				</div>
				<div class="phone_numbor phone_enterPass">
					<span>确认密码</span>
					<input type="password" v-model="register.enterPassword" placeholder="请再次输入密码" @blur="verifyEnterPassword()"/>
					<span class="eliminate" @click.stop.prevent="empty('enterPassword')" v-if="register.enterPassword"></span>
				</div>
				<div class="register_button" >
					<button type="submit" :class="{active:register.phone&&register.password&&register.enterPassword&&register.noteCode}"  :disabled="!(register.phone&&register.password&&register.enterPassword&&register.noteCode)">确认重置</button>
				</div>
			</form>

		</div>
	</div>
</template>

<script type="text/javascript">

import { Indicator,Toast } from 'mint-ui';
import tools from '../../util/tool';
import getd from '../../vuex/getData.js';
import CryptoJS from 'crypto-js'
	export default {
		data(){
			return {
				ab:"aa",
            	allChecked:true,
            	 register: {
                    phone: "",
                    password:"",
                    enterPassword:"",
                    noteCode:"",//验证码
                    rec:"",
                }  ,
                time:45,
                isPhone:false,//判断手机号
                ispassword:false,//判断密码
                enterPassword:false,//判断确认密码
                isCode:false,//判断验证码
                isGet:true,//是否获取验证码
                isLogin:false,//是否可以点击注册
                isDis:false,//推荐人是否可以编辑
                something:""
			}
		},

		methods:{
			goBack(){
        		this.$router.go(-1);
        	},
			verifyP(){//手机号码
			    if(!this.register.phone.trim()){
                    return;
                }

				var status = this.commonTool.regularJudgement("phone",this.register.phone);
				if(status){
					this.isPhone = true;
				}else{
					this.isPhone = false;
				}

			},
			verifyM(){//手机验证码
			    if(!this.register.noteCode.trim()){
                    return;
                }
				var status = this.commonTool.regularJudgement("noteCode",this.register.noteCode);

				if(status){
					this.isCode = true;
				}else{
					this.isCode = false;
				}
			},
			verifyPassword(){//新密码
			    if(!this.register.password.trim()){
                    return;
                }
				var status = this.commonTool.regularJudgement("password",this.register.password);
				if(status){
					this.ispassword = true;
				}else{
					this.ispassword = false;
				}
			},
			verifyEnterPassword(){//确认密码 
				if(!this.register.password.trim()  || !this.register.enterPassword.trim()){
                    return;
                }
				var status = this.commonTool.regularJudgement("enterPassword",{
					password:this.register.password,
					enterPassword:this.register.enterPassword
				});
				////console.log( "statusstatusstatus",status )
				if (status) {
					this.enterPassword = true;
				}else{
					this.enterPassword = false;
				}
			},
			empty(value){ //清空输入框内容
        		switch(value){
        			case "phone":
        			this.register[value] = ""
        			break;
        			case 'password':
        			this.register[value] = ""
        			break;
        			case 'enterPassword':
        			this.register[value] = ""
        			break;
        		}
        	},
        	getVerification(){//获取验证码
        		var timer = null;
        		if(this.isPhone){
        			this.isGet = false;
        			var params = {
	        			mobile:this.register.phone
	        		};
        			getd.getVerification(params)
	        		.then((res)=>{
	        			Indicator.close();
	        		})
	        		//倒计时45s
	        		timer = setInterval(()=>{
	        			if(this.time==1){
	        				this.time = 45;
	        				clearInterval(timer);
	        				this.isGet = true;
	        			}else{
	        				this.time--;
	        			}
	        		},1000)
        		}
        	},
        	submit(){//确认重置 
        		let  passwordEncrypt =  CryptoJS.AES.encrypt(this.register.password, '59964e5d540e446cf98bc2af78a2ea58');//密码加密
        		if(this.isPhone&&this.ispassword&&this.enterPassword&&this.isCode){
        		 	let params = {
	        			Mobile:this.register.phone,
						VerifyCode:this.register.noteCode,
						NewPassword: encodeURI(passwordEncrypt.toString()),
				        openSSL:true,
              		    datatype: 'json'
	        		};
	        		getd.FORGETPASSWORD_FORGETPASSWORD(params)
	        		.then((res)=>{
	        			Indicator.close();
	        			this.$router.push('/mine/login');

	        		})
                .catch((err)=>{
                 // console.log('重置密码失败',err);
                  Indicator.close();
                  Toast({
                    message: err.data.msg,
                    duration: 2000
                  });
                })
        		 }


        	}
		}
	}
</script>
