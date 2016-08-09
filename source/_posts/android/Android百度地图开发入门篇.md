title: Android百度地图开发应用入门
date: 2016-01-29 09:48:46
categories: [Android]
tags: [百度地图,android]
---
最近，公司要做的项目需要使用到百度地图。来学习一下关于百度地图如何使用。<!--more-->
要使用百度地图，需要先注册一个百度账号，获取到地图的SDK和秘钥，导入到自己的项目中才能使用。

# 秘钥和SDK

秘钥的申请需要项目的包名和SHA1值。所以，需要先创建好项目再进行秘钥的申请。

## 申请秘钥

要申请秘钥需要到[百度地图开放平台](http://lbsyun.baidu.com)申请。当然，百度有提供相应的教程，步骤我就不再列出来了。这边需要注意的是创建应用的时候。

![创建应用](http://7xpi7i.com1.z0.glb.clouddn.com/%E7%99%BE%E5%BA%A6%E5%88%9B%E5%BB%BA%E5%9C%B0%E5%9B%BE%E5%BA%94%E7%94%A8.jpg)

应用类型，因为我是在Android上开发的，所以选的是**Android SDK**。数字签名（SHA1）的值使用的是签名文件的SHA1的值，因为我没有导出APK测试，直接运行到手机上，所以用的是测试证书，测试证书的SHA1可以在eclipse中的***Window-preferences中的Android-Build***获取。包名则是在清单文件AndroidManifest.xml中的package。我创建的项目是FreeMap，包名是“com.jarvis.freemap”。
```
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.jarvis.freemap"
    android:versionCode="1"
    android:versionName="1.0" >
```
应用创建完毕后，就可以得到秘钥了。

## 下载SDK

[百度SDK下载](http://lbsyun.baidu.com/sdk/download)

这个根据自己的需求下载相应的开发包就可以了。我因为想熟悉一下百度地图，所以下了全部的开发包。开发包下完后是导入到项目的lib中的。

![开发包导入](http://7xpi7i.com1.z0.glb.clouddn.com/%E5%BC%80%E5%8F%91%E5%8C%85%E5%AF%BC%E5%85%A5.jpg)

# 必要的配置

## 权限添加

要使用百度地图，需要额权限可不少，权限需添加到清单文件中AndroidManifest.xml中，需要的权限有
```
<!-- 这个权限用于进行网络定位 -->
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<!-- 这个权限用于访问GPS定位 -->
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<!-- 用于访问wifi网络信息，wifi信息会用于进行网络定位 -->
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<!-- 获取运营商信息，用于支持提供运营商信息相关的接口 -->
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<!-- 这个权限用于获取wifi的获取权限，wifi信息会用来进行网络定位 -->
<uses-permission android:name="android.permission.CHANGE_WIFI_STATE" />
<!-- 用于读取手机当前的状态 -->
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
<!-- 写入扩展存储，向扩展卡写入数据，用于写入离线定位数据 -->
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<!-- 访问网络，网络定位需要上网 -->
<uses-permission android:name="android.permission.INTERNET" />
<!-- SD卡读取权限，用户写入离线定位数据 -->
<uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS" />
```

## 服务添加

需要添加一个百度的远程服务到清单文件中的application中
```
<service
    android:name="com.baidu.location.f"
    android:enabled="true"
    android:process=":remote" >
</service>
```

## 添加秘钥

秘钥也是在清单文件中的application中添加
```
<meta-data
    android:name="com.baidu.lbsapi.API_KEY"
    android:value="your api-key" />
```

# 测试

百度提供的测试方法是创建一个mapView，运行看是否有生成地图来测试是否配置正确。我们这边要进行的是定位测试，以文本形式展现出来。官方有提供相应的demo可参考。

## 添加默认配置

对定位的一些基本参数进行配置，需要修改配置的话也是在这个类中进行修改。

```
public class LocationService {
	private LocationClient client = null;
	private LocationClientOption mOption,DIYoption;
	private Object  objLock = new Object();

	/***
	 * 
	 * @param locationContext
	 */
	public LocationService(Context locationContext){
		synchronized (objLock) {
			if(client == null){
				client = new LocationClient(locationContext);
				client.setLocOption(getDefaultLocationClientOption());
			}
		}
	}
	
	/***
	 * 
	 * @param listener
	 * @return
	 */
	
	public boolean registerListener(BDLocationListener listener){
		boolean isSuccess = false;
		if(listener != null){
			client.registerLocationListener(listener);
			isSuccess = true;
		}
		return  isSuccess;
	}
	
	public void unregisterListener(BDLocationListener listener){
		if(listener != null){
			client.unRegisterLocationListener(listener);
		}
	}
	
	/***
	 * @param option
	 * @return isSuccessSetOption
	 */
	public boolean setLocationOption(LocationClientOption option){
		boolean isSuccess = false;
		if(option != null){
			if(client.isStarted())
				client.stop();
			DIYoption = option;
			client.setLocOption(option);
		}
		return isSuccess;
	}
	
	public LocationClientOption getOption(){
		return DIYoption;
	}
	/***
	 * @return DefaultLocationClientOption
	 */
	public LocationClientOption getDefaultLocationClientOption(){
		if(mOption == null){
			mOption = new LocationClientOption();
			mOption.setLocationMode(LocationMode.Hight_Accuracy);//可选，默认高精度，设置定位模式，高精度，低功耗，仅设备
			mOption.setCoorType("bd09ll");//可选，默认gcj02，设置返回的定位结果坐标系，如果配合百度地图使用，建议设置为bd09ll;
			mOption.setScanSpan(3000);//可选，默认0，即仅定位一次，设置发起定位请求的间隔需要大于等于1000ms才是有效的
		    mOption.setIsNeedAddress(true);//可选，设置是否需要地址信息，默认不需要
		    mOption.setIsNeedLocationDescribe(true);//可选，设置是否需要地址描述
		    mOption.setNeedDeviceDirect(false);//可选，设置是否需要设备方向结果
		    mOption.setLocationNotify(false);//可选，默认false，设置是否当gps有效时按照1S1次频率输出GPS结果
		    mOption.setIgnoreKillProcess(true);//可选，默认true，定位SDK内部是一个SERVICE，并放到了独立进程，设置是否在stop的时候杀死这个进程，默认不杀死   
		    mOption.setIsNeedLocationDescribe(true);//可选，默认false，设置是否需要位置语义化结果，可以在BDLocation.getLocationDescribe里得到，结果类似于“在北京天安门附近”
		    mOption.setIsNeedLocationPoiList(true);//可选，默认false，设置是否需要POI结果，可以在BDLocation.getPoiList里得到
		    mOption.SetIgnoreCacheException(false);//可选，默认false，设置是否收集CRASH信息，默认收集
		}
		return mOption;
	}
	
	public void start(){
		synchronized (objLock) {
			if(client != null && !client.isStarted()){
				client.start();
			}
		}
	}
	public void stop(){
		synchronized (objLock) {
			if(client != null && client.isStarted()){
				client.stop();
			}
		}
	}
	
}
```

## 主要逻辑

展示页面上只添加一个Button和一个TextView两个组件。在进行使用之前也需要一些必要的初始化，这里为了简便，全部都整合到了同一个Activity类中，项目中使用的话，不建议这么写。

### 初始化SDK

在使用之前需要先初始化一下百度sdk。这个初始化建议在自定义的Applicaition中进行。需要注意的是，初始化SDK必须在setContentView()之前进行。
```
locationService = new LocationService(getApplicationContext());
mVibrator =(Vibrator)getApplicationContext().getSystemService(Service.VIBRATOR_SERVICE);
SDKInitializer.initialize(getApplicationContext());  
```

### 监听的方法

这边我是直接拷贝demo中的方法来使用，也可根据自己的需要进行修改。

```
private BDLocationListener mListener = new BDLocationListener() {

	@Override
	public void onReceiveLocation(BDLocation location) {
		if(null != location && location.getLocType() != BDLocation.TypeServerError){
			StringBuffer sb = new StringBuffer(256);
			sb.append("time: ");
			/**
			 * 时间也可以使用systemClock.elapsedRealtime()方法 获取的是自从开机以来，每次回调的时间；
			 * location.getTime() 是指服务端出本次结果的时间，如果位置不发生变化，则时间不变
			 */
			sb.append(location.getTime());
			sb.append("\nerror code : ");
			sb.append(location.getLocType());
			sb.append("\nlatitude : ");
			sb.append(location.getLatitude());
			sb.append("\nlontitude : ");
			sb.append(location.getLongitude());
			sb.append("\nradius : ");
			sb.append(location.getRadius());
			sb.append("\nCountryCode : ");
			sb.append(location.getCountryCode());
			sb.append("\nCountry : ");
			sb.append(location.getCountry());
			sb.append("\ncitycode : ");
			sb.append(location.getCityCode());
			sb.append("\ncity : ");
			sb.append(location.getCity());
			sb.append("\nDistrict : ");
			sb.append(location.getDistrict());
			sb.append("\nStreet : ");
			sb.append(location.getStreet());
			sb.append("\naddr : ");
			sb.append(location.getAddrStr());
			sb.append("\nDescribe: ");
			sb.append(location.getLocationDescribe());
			sb.append("\nDirection(not all devices have value): ");
			sb.append(location.getDirection());
			sb.append("\nPoi: ");

			if(location.getPoiList() != null && !location.getPoiList().isEmpty()){
				for (int i = 0; i < location.getPoiList().size(); i++) {
					Poi poi = (Poi) location.getPoiList().get(i);
					sb.append(poi.getName() +";");
				}
			}

			if(location.getLocType() == BDLocation.TypeGpsLocation){
				//GPS定位结果
				sb.append("\nspeed : ");
				sb.append(location.getSpeed());// 单位：km/h
				sb.append("\nsatellite : ");
				sb.append(location.getSatelliteNumber());
				sb.append("\nheight : ");
				sb.append(location.getAltitude());// 单位：米
				sb.append("\ndescribe : ");
				sb.append("gps定位成功");
			}else if(location.getLocType() == BDLocation.TypeNetWorkLocation){
				// 网络定位结果
				// 运营商信息
				sb.append("\noperationers : ");
				sb.append(location.getOperators());
				sb.append("\ndescribe : ");
				sb.append("网络定位成功");
			}else if (location.getLocType() == BDLocation.TypeOffLineLocation) {// 离线定位结果
				sb.append("\ndescribe : ");
				sb.append("离线定位成功，离线定位结果也是有效的");
			} else if (location.getLocType() == BDLocation.TypeServerError) {
				sb.append("\ndescribe : ");
				sb.append("服务端网络定位失败，可以反馈IMEI号和大体定位时间到loc-bugs@baidu.com，会有人追查原因");
			} else if (location.getLocType() == BDLocation.TypeNetWorkException) {
				sb.append("\ndescribe : ");
				sb.append("网络不同导致定位失败，请检查网络是否通畅");
			} else if (location.getLocType() == BDLocation.TypeCriteriaException) {
				sb.append("\ndescribe : ");
				sb.append("无法获取有效定位依据导致定位失败，一般是由于手机的原因，处于飞行模式下一般会造成这种结果，可以试着重启手机");
			}
			logMsg(sb.toString());
		}
	}
};
```
### 注册与注销监听

用Activity的生命周期，对监听进行注册和注销
```
/***
 * Stop location service
 */
@Override
protected void onStop() {
	// TODO Auto-generated method stub
	locationService.unregisterListener(mListener); //注销掉监听
	locationService.stop(); //停止定位服务
	super.onStop();
}
@Override
protected void onStart() {
	super.onStart();
	//location config
	locationService.registerListener(mListener);
	//注册监听
	int type = getIntent().getIntExtra("from", 0);
	if(type == 0){
		locationService.setLocationOption(locationService.getDefaultLocationClientOption());
	}else if(type == 1){
		locationService.setLocationOption(locationService.getOption());
	}

	getLocation.setOnClickListener(new OnClickListener() {

		@Override
		public void onClick(View v) {
			if(getLocation.getText().toString().equals("单点定位")){
				locationService.start();
				getLocation.setText("定位ING");
			}else{
				locationService.stop();
				getLocation.setText("单点定位");
			}
		}
	});
}
```

### 获取信息展现

```
public void logMsg(String str) {
	try {
		if (showLocation != null){
			showLocation.setText(str);
		}
	} catch (Exception e) {
		e.printStackTrace();
	}
}
```
### 测试效果

5. 一个简单的定位测试功能就完成了，测试效果如下

![运行效果图](http://7xpi7i.com1.z0.glb.clouddn.com/%E5%AE%9A%E4%BD%8D%E8%BF%90%E8%A1%8C%E6%95%88%E6%9E%9C%E5%9B%BE.jpg)

