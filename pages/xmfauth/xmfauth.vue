<template>
	<view>
		<view class="grace-idcard-main">
			
			<view class="grace-idcard-text">
				身份证照片 ( 正面 )
			</view>
			<view class="grace-idcard-items">
				<view class="grace-idcard-uper-btn" @tap="selectImg1">
					<view class="img"><image src="../../static/imgs/camera.png" mode="widthFix" /></view>
					<view class="text">拍摄或选择照片</view>
				</view>
				<view class="grace-idcard-preview">
					<image style="width: 100%; height: 110px; background-color: #eeeeee;" :src="idCard1"  @tap="previewImg1" mode="scaleToFill"></image>
				</view>
			</view>
			<view class="grace-idcard-text">
				身份证照片 ( 背面 )
			</view>
			<view class="grace-idcard-items">
				<view class="grace-idcard-uper-btn" @tap="selectImg2">
					<view class="img"><image src="../../static/imgs/camera.png" mode="widthFix" /></view>
					<view class="text">拍摄或选择照片</view>
				</view>
				<view class="grace-idcard-preview">
					<image style="width: 100%; height: 110px; background-color: #eeeeee;" :src="idCard2" @tap="previewImg2" mode="scaleToFill" />
				</view>
			</view>
			<view class="grace-idcard-text">
				洗车机照片
			</view>
			<view class="grace-idcard-items">
				<view class="grace-idcard-uper-btn" @tap="selectImg3">
					<view class="img"><image src="../../static/imgs/camera.png" mode="widthFix" /></view>
					<view class="text">拍摄或选择照片</view>
				</view>
				<view class="grace-idcard-preview">
					<image style="width: 100%; height: 110px; background-color: #eeeeee;" :src="washCar" @tap="previewImg3" mode="scaleToFill" />
				</view>
			</view>
			<view style="margin-top:38upx;">
				<button type="primary" @tap="uploadCards">上传</button>
			</view>
		</view>
	</view>
</template>
<script>
var upinfo=new Array();
var dsq=false;
var user=';'
var _self;
export default {
	data() {
		return {
			idCard1 : '../../static/imgs/idcard1.png',
			idCard2 : '../../static/imgs/idcard2.png',
			washCar : '../../static/logo.png'
		};
	},
	onLoad:function(){
		_self = this;
	},
	onShow:function(){
		try {
			user = uni.getStorageSync('user');
			if(user==''){
				uni.showModal({
					title: '',
					content: '用户未登录，不能进行认证',
					showCancel: false,
					cancelText: '取消',
					confirmText: '去登录',
					success: res => {
						if (res.confirm) {
							uni.reLaunch({
								url: '../login/login'
							});
						} else if (res.cancel) {
							uni.reLaunch({
								url: '../tabbar/tabbar-3/my'
							});
						}
					},
					fail: () => {},
					complete: () => {}
				});
			}
		} catch (e) {
			console.log(e);
		}
	},
	methods: {
		selectImg1 : function() {
			uni.chooseImage({
				count:1,
				success:function(res){
					_self.idCard1 = res.tempFilePaths[0];
				}
			})
		},
		selectImg2 : function() {
			uni.chooseImage({
				count:1,
				success:function(res){
					_self.idCard2 = res.tempFilePaths[0];
				}
			})
		},
		selectImg3 : function() {
			uni.chooseImage({
				count:1,
				success:function(res){
					_self.washCar = res.tempFilePaths[0];
				}
			})
		},
		previewImg1 : function(){
			uni.previewImage({
				urls:[_self.idCard1]
			});
		},
		previewImg2 : function(){
			uni.previewImage({
				urls:[_self.idCard2]
			});
		},
		previewImg3 : function(){
			uni.previewImage({
				urls:[_self.washCar]
			});
		},
		uploadCards : function(){
			let upimg1='';
			let upimg2='';
			let upimg3='';
			if(this.idCard1 == '../../static/imgs/idcard1.png' || this.idCard2 == '../../static/imgs/idcard2.png'){
				uni.showToast({title:"请选择身份证照片", icon:"none"});
				return;
			}
			// 因底层限制一次上传一个
			uni.showLoading({title:"上传中"});
			upinfo= [];
			// 上传正面
			var url=this.webUrl+'/Washapp/Main/outhupload';
			var uploadTask1 = uni.uploadFile({
				url: url,
				filePath: _self.idCard1,
				fileType: 'image',
				name: 'data',
				success: function (uploadFileRes) {
					let data=JSON.parse(uploadFileRes.data);
					let resinfo=data.data.savepath+data.data.savename;
					upimg1=resinfo;
					// 上传背面
					var uploadTask2 = uni.uploadFile({
					 	url: url,
					 	filePath: _self.idCard2,
					 	fileType: 'image',
					 	name: 'data',
					 	success: function (uploadFileRes) {
					 		let data=JSON.parse(uploadFileRes.data);
					 		let resinfo=data.data.savepath+data.data.savename;
							upimg2=resinfo;
							// 上传洗车机
							var uploadTask3 = uni.uploadFile({
							  	url: url,
							  	filePath: _self.washCar,
							  	fileType: 'image',
							  	name: 'data',
							  	success: function (uploadFileRes) {
							  		let data=JSON.parse(uploadFileRes.data);
							  		let resinfo=data.data.savepath+data.data.savename;
									upimg3=resinfo;
									
									let url=_self.webUrl+'/Washapp/Main/submitouth';
									uni.request({
										url: url,
										method: 'POST',
										header: {"Content-Type": "application/x-www-form-urlencoded"},
										dataType:"json",
										data: {
											'user':user,
											'ID1':upimg1,
											'ID2':upimg2,
											'washpic':upimg3
										},
										success: res => {
											if(res.data.code==0){
												plus.nativeUI.toast('审核信息提交成功，请等待审核')
												uni.reLaunch({
													url: '../tabbar/tabbar-3/my'
												});
											}else{
												uni.showModal({
													title: '提交错误',
													content: res.data.msg,
													showCancel: false,
													cancelText: '取消',
													confirmText: '确定',
													success: res => {
														
													}
												});
											}
										},
										fail: res =>{
											uni.showToast({
												title: '信息提交异常:'+JSON.stringify(res),
												icon:'none'
											});
										},
										complete: res => {
											uni.hideLoading();
										}
									});
									
							  	},
								fail:function (e){
									uni.showToast({
										title: '洗车机照片上传失败',
										icon:'none',
										duration: 2000
									});
									uni.hideLoading();
								}
							   });
					 	},
						fail:function(){
							uni.showToast({
								title: '身份证背面照片上传失败',
								icon:'none',
								duration: 2000
							});
							uni.hideLoading();
						}
					  });
				},
				fail:function () {
					uni.showToast({
						title: '身份证正面照片上传失败',
						icon:'none',
						duration: 2000
					});
					uni.hideLoading();
				}
			 });
			 
		}
	},
}
</script>
<style>
view{font-size:28upx;}
.grace-idcard-main{margin:20upx 30upx;}
.grace-idcard-desc{line-height:2em; background:#FFFFFF; padding:20upx; border-radius:10upx;}
.grace-idcard-text{line-height:2em; margin-top:20upx;}
.grace-idcard-items{background:#FFFFFF; padding:20upx 0; display:flex; margin:20upx 0; border-radius:10upx; align-items: flex-start;}
.grace-idcard-uper-btn{width:276upx; margin:0 60upx; background:#F1F1F1; padding-bottom:10upx; border-radius:10upx; text-align:center;}
.grace-idcard-uper-btn .img{width:100upx; height:100upx; margin:0 auto; margin-top:30upx;}
.grace-idcard-uper-btn .img image{width:100upx; height:100upx;}
.grace-idcard-uper-btn .text{width:100%; margin-top:10upx; text-align:center; line-height:2em;}
.grace-idcard-preview{width:50%; margin:0 30upx;}
.grace-idcard-preview image{width:100%;}
</style>