<template>
    <view class="page">
		<map 
			id="mymap" 
			class="map" 
			v-bind:style="{width:mapwidth+'px',height:mapheight+'px' }"
			:latitude="latitude" 
			:longitude="longitude" 
			:markers="covers" 
			:controls="controlsdata"
			@controltap="dingwei"
			show-location
		>
			<!-- <cover-image class="dingwei img" @click="dingwei" src="../../../static/weizhi.png"></cover-image> -->
			<!-- <cover-view class="controls-start" @click="toonline" >{{online}}</cover-view> -->
		</map>
    </view>
</template>

<script>
var user='';
var longitude='';
var latitude=''
var ONLINEC;
var onstart=false;
var _self;
var outn=false;
export default {
	data() {
        return {
			online:"上线",
			mapheight:'',
			mapwidth:'',
            title: 'map',
            latitude: 39.909,
            longitude: 116.39742,
            covers: [{
                latitude: 0,
                longitude: 0,
                iconPath: '../../../static/dingwei.png'
            }],
			controlsdata:[{
					id:1,
					position:{
						left:5,
						top:520,
						width:40,
						height:40
					},
					iconPath:'../../../static/weizhi.png',
					clickable:true
				},{
					id:2,
					position:{
						left:0,
						top:0,
						width:0,
						height:0
					},
					iconPath:'',
					clickable:true
				}]
        }
    },
    methods: {
		dingwei(e) {
			//var log=JSON.stringify(e)
			//console.log(log);
			if(e.controlId==1){
				uni.getLocation({
					type: 'gcj02',
					success: res => {
						this.longitude=res.longitude;
						this.latitude=res.latitude;
						this.covers[0].latitude=res.latitude;
						this.covers[0].longitude=res.longitude;
					}
				});
			}
			if(e.controlId==2){
				this.toonline(e);
			}
            },
		toonline(event){
			let that=this;
			//let user='';
			let latitude=this.latitude;
			let longitude=this.longitude;
			
			uni.showLoading({
				title: '操作执行中，请稍等...',
				mask: true
			});
			/*
			try {
				const value = uni.getStorageSync('user');
				if (value) {
					user=value;
				}else{
					uni.showToast({
						icon: 'none',
						title: '未获取到用户登录信息'
					});
					return false;
				}
			} catch (e) {
				uni.showToast({
					title: '获取用户信息错误:'+JSON.stringify(e)
				});
				return false;
			}
			*/
			if (_self.onlines==true){
				
				var url=this.webUrl+'/Washapp/Main/offline';
				uni.request({
					url: url,
					method: 'POST',
					header: {"Content-Type": "application/x-www-form-urlencoded"},
					dataType:"json",
					data: {
						'user':user
					},
					success: res => {
						if(res.data.code==0){
							uni.showToast({
								icon:'success',
								title: res.data.msg
							});
							_self.onlines=false;
							that.controlsdata[1].iconPath='../../../static/start.png'
						}else{
							uni.showToast({
							    icon: 'none',
							    title: res.data.msg
							});
						}
					},
					fail: (res) => {
						uni.showModal({
							title: '错误',
							content: JSON.stringify(res)
						});
					},
					complete: (res) => {
						uni.hideLoading();
					}
					
				});
				
			}else{
				var url=this.webUrl+'/Washapp/Main/getouth';
				uni.request({
					url: url,
					method: 'POST',
					dataType:"json",
					header: {"Content-Type": "application/x-www-form-urlencoded"},
					data: {'user':user},
					success: res => {
						//console.log(res);
						if(res.data.code!=9){
							if(res.data.code==3){
								outn=true;
								_self.Tostart();
							}else{
								uni.showToast({
									title: res.data.msg,
									icon:'none'
								});
								return false;
							}
						}
					},
					fail: (e) => {
						uni.showToast({
							title: '信息获取错误：'+JSON.stringify(e)
						});
					},
					complete: (res) => {
						uni.hideLoading();
					}
				});
				
			}
			
		},
		Tostart(){
			if(outn){
				var url=_self.webUrl+'/Washapp/Main/online';
				uni.request({
					url: url,
					method: 'POST',
					header: {"Content-Type": "application/x-www-form-urlencoded"},
					dataType:"json",
					data: {
						'user':user,
						'longitude':longitude,
						'latitude':latitude
					},
					success: res => {
						//console.log(JSON.stringify(res));
						if(res.data.code==0){
							uni.showToast({
								icon:'success',
								title: res.data.msg
							});
							_self.onlines=true;
							_self.controlsdata[1].iconPath='../../../static/onstart.png';
							//console.log(_self.controlsdata);
						}else{
							uni.showToast({
								icon: 'none',
								title: res.data.msg
							});
						}
					},
					fail: (res) => {
						uni.showModal({
							title: '错误',
							content: JSON.stringify(res)
						});
					}
				});
			}
		}
    },
	onShow() {
		let onlineshow=false;
		let _that=this;
		//console.log(_that.controlsdata[0]);
		const sysingo = uni.getSystemInfoSync();
		// 计算主体部分高度,单位为px
		this.mapheight = sysingo.windowHeight;
		this.mapwidth = sysingo.windowWidth;
		
		let onlinedata={
				"id": 2,
				"position": {
					"left": 100,
					"top": 455,
					"width": 160,
					"height": 50
				},
				"iconPath": "../../../static/start.png",
				"clickable": true
			};
		_that.controlsdata[0].position.top=sysingo.windowHeight-40;
		onlinedata.position.top=sysingo.windowHeight-60;
		onlinedata.position.left=sysingo.windowWidth/2-onlinedata.position.width/2;
		ONLINEC=onlinedata;
		user=false;
		try {
			const value = uni.getStorageSync('user');
			if (value) {
				onlineshow=true;
				user=value;
			}
		} catch (e) {
			uni.showToast({
				title: '获取用户信息错误:'+JSON.stringify(e)
			});
			return false;
		}
		//console.log(user);
		if(user){
			var url=this.webUrl+'/Washapp/Main/getstate';
			if(user){
				uni.request({
					url: url,
					method: 'POST',
					header: {"Content-Type": "application/x-www-form-urlencoded"},
					dataType:"json",
					data: {
						'user':user
					},
					success: res => {
						//console.log(res);
						if(res.data.code==1){
							/*
							uni.showToast({
								icon:'success',
								title: res.data.msg
							});
							*/
							onstart=true;
							onlinedata.iconPath='../../../static/onstart.png';
							//_that.controlsdata[1].iconPath='../../../static/onstart.png';
							_that.controlsdata.pop();
							_that.controlsdata.push(onlinedata);
							_self.onlines=true;
							//console.log(JSON.stringify(_that.controlsdata));
						}
						if(res.data.code==0){
							/*
							uni.showToast({
								icon:'success',
								title: res.data.msg
							});
							*/
							onstart=false;
							onlinedata.iconPath='../../../static/start.png';
							//_self.controlsdata[1].iconPath='../../../static/start.png';
							_that.controlsdata.pop();
							_that.controlsdata.push(onlinedata);
							_self.onlines=false;
							//console.log(JSON.stringify(_that.controlsdata));
						}
					},
					fail: (res) => {
						uni.showModal({
							title: '错误',
							content: JSON.stringify(res)
						});
					}
				});
			}
		}
		/*
		if(onlineshow){
			_that.controlsdata.push(ONLINEC);
		}
		*/
	},
	onLoad(Object) {
		_self=this;
		var that=this;
		uni.getLocation({
			type: 'gcj02',
			success: function (res) {
				that.latitude=res.latitude;
				that.longitude=res.longitude;
				that.covers[0].latitude=res.latitude;
				that.covers[0].longitude=res.longitude;
				latitude=res.latitude;
				longitude=res.longitude;
				/*
				uni.showToast({
					title: res.latitude+'  '+res.longitude,
					duration: 2000
				});
				*/
			},
			fail:function(e){
				uni.showModal({
					title: '错误',
					content: 'error: '+JSON.stringify(e)
				});
			}
		});
	},
	onReady() {
		
	}
};
</script>

<style>
.page {
	display: flex;
	justify-content: center;
}
.dingwei{
	left: 30upx;
}
.map {
    position: relative;
    }
cover-image {
	display: inline-block;
}
cover-view{
	display: inline-block;
    }
.img {
	width: 60upx;
	height: 60upx;
	bottom: 13%;
	margin-top: -50upx;
}
.controls-start {
        text-align: center;
        color: #FFFFFF;
		background-color: #912CEE;
		width: 200upx;
		height: 80upx;
		top:5000px;
		left:70%;
		border-radius: 25upx;
    }
</style>
