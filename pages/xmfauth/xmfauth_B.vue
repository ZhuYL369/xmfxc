<template>
	<view class="content">
	    <view class="input-group">
	        <view class="input-row border">
	            <text class="title">身份证正面照片：</text>
				<image style="width: 200px; height: 140px; " mode="scaleToFill" :src="img1" @error="imageError" @click="cimg1"></image>
	        </view>
			<view class="input-row border">
			    <text class="title">身份证背面照片：</text>
			   <image style="width: 200px; height: 140px; " mode="scaleToFill" :src="img2" @error="imageError" @click="cimg2"></image>
			</view>
	        <view class="input-row border">
	            <text class="title">洗车机照片：</text>
	            <image style="width: 200px; height: 140px; " mode="scaleToFill" :src="img3" @error="imageError" @click="cimg3"></image>
	        </view>
	    </view>
	    <view class="btn-row">
	        <button type="primary" class="primary" @tap="outh_submit">提交认证</button>
	    </view>
	</view>
</template>

<script>
	var img1=false;
	var img2=false;
	var img3=false;
	var img=new Array();
	
	export default {
		data() {
			return {
				login: false,
				img1:'../../static/img/addimage.jpg',
				img2:'../../static/img/addimage.jpg',
				img3:'../../static/img/addimage.jpg'
			}
		},
		methods: {
			imageError(e){
				console.log(e);
			},
			cimg1(){
				var that=this;
				uni.chooseImage({
					count:1,
					sizeType: ['original', 'compressed'],
					sourceType:['album','camera'],
					success: function (res) {
						//console.log(JSON.stringify(res.tempFilePaths));
						that.img1=res.tempFilePaths[0];
						img1=true;
						img.push(that.img1);
					}
				})
			},
			cimg2(e){
				var that=this;
				uni.chooseImage({
					count:1,
					sizeType: ['original', 'compressed'],
					sourceType:['album','camera'],
					success: function (res) {
						that.img2=res.tempFilePaths[0];
						img2=true;
						img.push(that.img2);
					}
				})
			},
			cimg3(e){
				var that=this;
				uni.chooseImage({
					count:1,
					sizeType: ['original', 'compressed'],
					sourceType:['album','camera'],
					success: function (res) {
						//console.log(JSON.stringify(res.tempFilePaths));
						that.img3=res.tempFilePaths[0];
						img3=true;
						img.push(that.img3);
					}
				})
			},
			outh_submit:function(){
				var that=this;
				if(!img1) {
					plus.nativeUI.toast('请选择身份证正面照片');
					return false;
				}
				if(!img2) {
					plus.nativeUI.toast('请选择身份证背面照片');
					return false;
				}
				if(!img3) {
					plus.nativeUI.toast('请选择洗车机照片');
					return false;
				}
				
				let file=[{
					name:'img1',
					url:that.img1
					},{
					name:'img2',
					url:that.img2
					},{
					name:'img3',
					url:that.img3
					}];
				var url=this.webUrl+'/Washapp/Main/outhupload';
				console.log(img);
				let upres=new  Array();
				for (var i = 0; i <img.length; i++){
					uni.uploadFile({
						url:url,
						//files:file,
						fileType:'image',
						filePath:img[i],
						name:'img',
						success: (uploadFileRes) => {
							//console.log(uploadFileRes);
							//console.log('success :' +uploadFileRes.data);
							let data=JSON.parse(uploadFileRes.data);
							var resinfo=data.img.savepath+data.img.savename;
							console.log(resinfo);
							upres.push(resinfo);
							console.log(upres);
						},
						fail: (uploadFileRes) => {
							console.log('fail: '+uploadFileRes.data);
						}
					});
				}
				
				console.log(upres);
				/*
				uni.uploadFile({
					url:url,
					//files:file,
					fileType:'image',
					filePath:that.img1,
					name:'img1',
					success: (uploadFileRes) => {
						console.log('success :' +uploadFileRes.data);
					},
					fail: (uploadFileRes) => {
						console.log('fail: '+uploadFileRes.data);
					}
				});
				*/
			}
		}
	}
</script>

<style>
	page {
		min-height: 100%;
		display: flex;
	}
	image{
		padding: 15px;
	}
	.content {
		display: flex;
		flex: 1;
		flex-direction: column;
		background-color: #efeff4;
		padding: 1px;
	}
	.input-group {
		background-color: #ffffff;
		margin-top: 10px;
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
</style>
