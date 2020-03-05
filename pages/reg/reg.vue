<template>
    <view class="content">
        <view class="input-group">
            <view class="input-row border">
                <text class="title">手机号：</text>
                <m-input type="text" focus clearable v-model="account" placeholder="请输入手机号"></m-input>
            </view>
            <view class="input-row border">
                <text class="title">密码：</text>
                <m-input type="password" displayable v-model="password" placeholder="请输入密码"></m-input>
            </view>
            <view class="input-row border">
                <text class="title">邮箱：</text>
                <m-input type="text" clearable v-model="email" placeholder="请输入邮箱"></m-input>
            </view>
			<view class="input-row">
			    <text class="title">验证码：</text>
			    <m-input type="text" clearable v-model="vcode" placeholder="请输入验证码"> </m-input>
			<!-- 	<button type="warn" size="default" class="warn" @tap="getvcode">获取验证码</button> -->
				<text class="vcode" id="vcodeid" v-bind:class="{ active: isActive }" @tap="getvcode">{{vcodetxt}}</text>
			</view>
        </view>
        <view class="btn-row">
            <button type="primary" class="primary" @tap="register">注册</button>
        </view>
    </view>
</template>

<script>
    import service from '../../service.js';
    import mInput from '../../components/m-input.vue';

    export default {
        components: {
            mInput
        },
        data() {
            return {
				isActive:false,
				vcodetxt:'获取验证码',
                account: '',
                password: '',
                email: '',
				vcode:''
            }
        },
        methods: {
            register() {
                /**
                 * 客户端对账号信息进行一些必要的校验。
                 * 实际开发中，根据业务需要进行处理，这里仅做示例。
                 */
                if (this.account.length != 11) {
                    uni.showToast({
                        icon: 'none',
                        title: '手机号为 11 个字符'
                    });
                    return;
                }
                if (this.password.length < 6) {
                    uni.showToast({
                        icon: 'none',
                        title: '密码最短为 6 个字符'
                    });
                    return;
                }
                if (this.email.length < 3 || !~this.email.indexOf('@')) {
                    uni.showToast({
                        icon: 'none',
                        title: '邮箱地址不合法'
                    });
                    return;
                }
				if (this.vcode.length !=4) {
				    uni.showToast({
				        icon: 'none',
				        title: '验证码输入错误'
				    });
				    return;
				}

                const data = {
                    account: this.account,
                    password: this.password,
                    email: this.email,
					vcode:this.vcode
                }
				var url=this.webUrl+'/Washapp/Reg/Toreg';
				uni.request({
					url: url,
					method: 'POST',
					header: {"Content-Type": "application/x-www-form-urlencoded"},
					data: {
						"phone":data.account,
						"passwd":data.password,
						"vercode":data.vcode,
						"email":data.email
						},
					dataType:"json",
					success: res => {
						if(res.data.code==0){
							uni.showToast({
							    icon: 'none',
								duration:2000,
							    title: res.data.msg
							});
							uni.navigateBack({
							    delta: 1
							});
						}else{
							uni.showModal({
								title: '错误',
								content: JSON.stringify(res)
							});
						}
					},
					fail: (e) => {
						uni.showToast({
						    icon: 'none',
						    title: '注册失败！'+JSON.stringify(e)
						});
					}
					});
            },
			getvcode(){
				var that=this;
				if(that.isActive==true){
					return;
				}
				if (this.account.length != 11) {
				    uni.showToast({
				        icon: 'none',
				        title: '手机号为 11 个字符'
				    });
				    return;
				}
				var t=60;
					this.isActive=true;
					var url=this.webUrl+'/Washapp/Vercode/getvercode';
					uni.request({
						url: url,
						method: 'POST',
						header: {"Content-Type": "application/x-www-form-urlencoded"},
						data: {"phone":that.account},
						dataType:"json",
						success: res => {
							uni.showToast({
							    icon: 'none',
							    title: res.msg
							});
						},
						fail: () => {
							uni.showToast({
							    icon: 'none',
							    title: '验证码发送失败'
							});
						},
						complete:()=>{
							var id=setInterval(function(){
								that.vcodetxt=t+'(s)';
								if(t==0){
									that.vcodetxt='获取验证码';
									clearInterval(id);
									that.isActive=false;
								}
								t--;
							},1000);
						}
					});
				
			}
        }
    }
</script>

<style>
@import "../../components/m-icon/m-icon.css";
	/*每个页面公共css */
	page {
		min-height: 100%;
		display: flex;
	}

	/* #ifdef MP-BAIDU */
	page {
		width: 100%;
		height: 100%;
		display: block;
	}

	swan-template {
		width: 100%;
		min-height: 100%;
		display: flex;
	}

	/* 原生组件模式下需要注意组件外部样式 */
	custom-component {
		width: 100%;
		min-height: 100%;
		display: flex;
	}

	/* #endif */

	/* #ifdef MP-ALIPAY */
	page {
		min-height: 100vh;
	}

	/* #endif */

	/* 原生组件模式下需要注意组件外部样式 */
	m-input {
		width: 100%;
		/* min-height: 100%; */
		display: flex;
		flex: 1;
	}
	.content {
		display: flex;
		flex: 1;
		flex-direction: column;
		background-color: #efeff4;
		padding: 10px;
	}

	.input-group {
		background-color: #ffffff;
		margin-top: 20px;
		position: relative;
	}

	.input-group::before {
		position: absolute;
		right: 0;
		top: 0;
		left: 0;
		height: 1px;
		content: '';
		-webkit-transform: scaleY(.5);
		transform: scaleY(.5);
		background-color: #c8c7cc;
	}
	.vcode{
		position: relative;
		right: 20upx;
		font-size: 12pt;
	}
	.input-group::after {
		position: absolute;
		right: 0;
		bottom: 0;
		left: 0;
		height: 1px;
		content: '';
		-webkit-transform: scaleY(.5);
		transform: scaleY(.5);
		background-color: #c8c7cc;
	}

	.input-row {
		display: flex;
		flex-direction: row;
		position: relative;
		font-size: 18px;
		line-height: 40px;
	}

	.input-row .title {
		width: 72px;
		padding-left: 15px;
	}

	.input-row.border::after {
		position: absolute;
		right: 0;
		bottom: 0;
		left: 8px;
		height: 1px;
		content: '';
		-webkit-transform: scaleY(.5);
		transform: scaleY(.5);
		background-color: #c8c7cc;
	}

	.btn-row {
		margin-top: 25px;
		padding: 10px;
	}

	button.primary {
		background-color: #0faeff;
	}
	.active{
		color: #C8C7CC;
	}
</style>
