<template>
    <div class="login-wrap">
        <div class="login-logo" :style="{'margin-left':Number(this.screenWidth - 1000) / 2 +'px','margin-top':Number(this.screeHeight - 500) / 2 +'px'}">
            <div class="login-logo-img">
                <img src="https://yunsav.oss-cn-hangzhou.aliyuncs.com/voros/logo.png" alt="">
            </div>
            <div class="login-logo-title">
                商家管理系统
            </div>
        </div>
        <div class="basicInformation-box" :style="{'margin-top':Number(this.screeHeight - 500) / 2 +'px'}">
            <div class="login-name-header">
                登录商家管理系统
            </div>
            <div class="login-name">
                <div class="login-name-title">
                    手机号码
                </div>
                <div class="login-name-input" :label-width="formLabelWidth">
                    <input type="text" v-model="form.phone" placeholder="">
                </div>
            </div>
            <div class="login-imgcode">
                <div class="login-imgcode-title">
                    图形验证
                </div>
                <div class="login-imgcode-input">
                    <input type="text" v-model="form.imgCode" placeholder="">
                </div>
                <div class="login-imgcode-btn" @click="getImgCode">
                    <img :src="form.imgCodeUrl" alt="">
                </div>
            </div>
            <div class="login-smscode">
                <div class="login-smscode-title">
                    短信验证
                </div>
                <div class="login-imgcode-input">
                    <input type="text" v-model="form.smsCode" placeholder="">
                </div>
                <div class="login-imgcode-btn" v-show="SMS_CODE_SHOW" @click="getSMSCode">
                    <p>{{SMS_CODE_CHANGE}}</p>
                </div>
                <div class="login-imgcode-btn" v-show="!SMS_CODE_SHOW">
                    <p>{{SMS_COUNT_DOWN}}秒重新发送</p>
                </div>
            </div>
            <div class="login-btn">
                <el-button type="primary" plain @click="login" size="medium">立即登录</el-button>
            </div>   
        </div>
    </div>
</template>

<script>
import wxlogin from 'vue-wxlogin';
import qs from 'qs'
export default {
    components: {
        wxlogin
    },
    data: function() {
        return {
            formLabelWidth:'350px',
            screenWidth: document.body.clientWidth,     // 屏幕宽
            screeHeight: document.body.clientHeight,    // 屏幕高
            code:'',
            form:{
                phone:'',
                imgCode:'',
                smsCode:'',
                imgCodeUrl:'',
                imgCodeKey:''
            },
            SMS_CODE_SHOW:true,
            SMS_CODE_CHANGE:'获取验证码',
            SMS_COUNT_DOWN:'',
        };
    },
    mounted(){
        this.getImgCode()
        console.log(this.screenWidth)
    },
    methods: {
        login(){
            let data = {
                phone:this.form.phone,
                smsCode:this.form.smsCode,
                tenantId: '000000',
                identity: '0',
                grant_type: 'businesssms',
            }
            this.$axios.post('/blade-auth/oauth/token',qs.stringify(data),
            {headers:{
                    Authorization: 'Basic aDU6aDVfd2Vi',
                    grant_type:'businesssms'
                },
            }).
            then(res=>{
                console.log("用户信息：",res)
                if(res.data.code == 200) {
                    sessionStorage.setItem('accessToken',res.data.data.access_token)
                    sessionStorage.setItem('nickName',res.data.data.nick_name)
                    sessionStorage.setItem('avatar',res.data.data.avatar)
                    this.$router.push({ path: `/dashboard`})
                } else {
                    this.$message({
                        showClose: true,
                        message: res.data.msg,
                        type: 'error'
                    });
                }
            }).catch(err=>{
                console.log("登录失败：",err)
            })
            // this.$router.push({ path: `/dashboard`})
        },
        getImgCode(){
            let _this = this;
            let log = console.log
            this.$axios.get('/blade-auth/oauth/captcha').then(res=>{
                log("图形验证码",res)
                if(res.status == 200) {
                    _this.form.imgCodeUrl = res.data.image
                    _this.form.imgCodeKey = res.data.key
                }
            }).catch(err=>{
                log(err)
            })
        },
        // 获取短信验证码
			getSMSCode(){
				// let _this = this;
				let log = console.log
				if(this.form.imgCode != ''){
					this.$axios.get('/blade-auth/oauth/sendSms',{
						params:{
							mobile:this.form.phone,
							randomCode:this.form.imgCode
						},
						headers:{
                            Authorization: 'Basic aDU6aDVfd2Vi',
                            "Captcha-Key":this.form.imgCodeKey
							// 'Blade-Auth': 'bearer ' + sessionStorage.getItem('ACCESS_TOKEN')
						}
					}).then(res=>{
						log(res)
						if(res.data.code == 200) {
							log(res)
							const TIME_COUNT = 60;
							if(!this.TIMER){
								
								this.SMS_COUNT_DOWN = TIME_COUNT;
								this.SMS_CODE_SHOW = false;
								this.TIMER = setInterval(() => {
									if(this.SMS_COUNT_DOWN > 0 && this.SMS_COUNT_DOWN <= TIME_COUNT) {
										this.SMS_COUNT_DOWN--
									} else {
										this.SMS_CODE_SHOW = true;
										clearInterval(this.TIMER)
										this.TIMER = null
									}
								},1000)
							}
						} else {
                            // Toast(res.data.msg)
                            this.getImgCode()
                            this.$message({
                                showClose: true,
                                message: res.data.msg,
                                type: 'error'
                            });
						}
					}).catch(err=>{
						log(err)
					})
				} else {
					this.$message({
                        showClose: true,
                        message: '请填写图片验证码',
                        type: 'error'
                    });
				}
			},
    },
};
</script>

<style scoped>
    .login-wrap{
        background: #049ec4;
        height: 100%;
    }
    .basicInformation-box{
        width: 500px;
        height: 500px;
        text-align: center;
        /* padding-top:60px; */
        float: left;
        background: #fff;
        box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1)
    }
    .login-header{
        font-size: 40px;
        text-align: center;
        padding-top: 20px;
    }
    .login-name,.login-imgcode,.login-smscode{
        height: 50px;
        padding-top: 20px;
    }
    .login-name-header{
        width: 500px;
        height: 120px;
        text-align: center;
        line-height: 120px;
        font-size: 28px;
    }
    .login-name-title,.login-imgcode-title,.login-smscode-title{
        width: 100px;
        height: 40px;
        float: left;
        line-height: 35px;
        font-size: 14px;
    }
    .login-name-input{
        width: 350px;
        height: 40px;
        float: left;
    }
    .login-name-input input{
        width: 350px;
        height: 35px;
        font-size: 15px;
        border: none;
        border-bottom: 1px solid #ccc;
    }
    .login-imgcode-input{
        width: 240px;
        height: 40px;
        float: left;
        font-size: 22px;
        
    }
    .login-imgcode-input input{
        width: 240px;
        height: 35px;
        font-size: 15px;
        border: none;
        border-bottom: 1px solid #ccc;
    }
    .login-imgcode-btn{
        width: 100px;
        height: 38px;
        background: #fff;
        border:1px solid #f5f5f5;
        float: left;
        margin-left: 10px;
        line-height: 38px;
        font-size: 14px;
    }
    .login-imgcode-btn img{
        width: 100%;
        height: 100%;
    }
    .login-btn{
        padding-top: 25px;
    }
    .login-logo{
        width: 500px;
        height: 500px;
        float: left;
        background: #409eff;
        box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1)
    }
    .login-logo-img{
        width: 150px;
        height: 150px;
        margin-left: 175px;
        margin-top: 150px;
    }
    .login-logo-img img{
        width: 150px;
        height: 150px;
    }
    .login-logo-title{
        width: 500px;
        height: 50px;
        text-align: center;
        line-height: 50px;
        font-size: 30px;
        color:#fff
    }
</style>