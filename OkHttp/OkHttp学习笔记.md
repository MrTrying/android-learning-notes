# OkHttp 学习笔记

## 零、前言
随着使用 OkHttp 的人越来越多,在 Google 在6.0版本里面删除了 HttpClient 相关API的情况下我们有必要学习一下 OkHttp .

## 一、简介

OkHttp 是由 Square 推出的网络框架,可能在某些比较极端的情况会出现一些bug,但是已经是一个相对成熟的解决方案.

这里是OkHttp的wiki地址:[https://github.com/square/okhttp/wiki](https://github.com/square/okhttp/wiki "https://github.com/square/okhttp/wiki")

OkHttp 支持 Android 2.3 (API 9) 以及以上,对于Java最低要求是1.7

OkHttp 的jar地址:[http://repo1.maven.org/maven2/com/squareup/okhttp3/okhttp/3.2.0/okhttp-3.2.0.jar](http://repo1.maven.org/maven2/com/squareup/okhttp3/okhttp/3.2.0/okhttp-3.2.0.jar "http://repo1.maven.org/maven2/com/squareup/okhttp3/okhttp/3.2.0/okhttp-3.2.0.jar")

需要配合 OkIO 使用,OkIO地址:[https://search.maven.org/remote_content?g=com.squareup.okio&a=okio&v=LATEST](https://search.maven.org/remote_content?g=com.squareup.okio&a=okio&v=LATEST "https://search.maven.org/remote_content?g=com.squareup.okio&a=okio&v=LATEST")

AndroidStudio要使用的话使用一下的配置

MAVEN

	<dependency>
	  <groupId>com.squareup.okhttp3</groupId>
	  <artifactId>okhttp</artifactId>
	  <version>3.2.0</version>
	</dependency>

GRADLE

	compile 'com.squareup.okhttp3:okhttp:3.2.0'

## 二、使用

### (一) Http Get

网络请求中 Get 请求最常见了,话不多说,上代码

	// 创建okhttpClient对象
	OkHttpClient mOkHttpClient = new OkHttpClient();
	// 创建一个Request
	final Request request = new Request.Builder()
			.url("https://github.com/MrTrying")
			.build();
	// 创建 call
	Call call = mOkHttpClient.newCall(request);
	
	call.enqueue(new Callback() {
		
		@Override
		public void onResponse(final Response response) throws IOException {
			// 请求成功处理
			// final String text = response.body().string();
			
		}
		
		@Override
		public void onFailure(Request request, IOException e) {
			// 请求失败处理
			
		}
	});

这就是发送一个 Get 请求的步骤,创建一个 Request 对象,参数至少需要 Url ,如果需要设置更多的参数,可以通过Request.Builder设置更多的参数比如:header、method等。然后通过Request的对象去构造一个Call对象，最后调用call.enqueue，将call加入调度队列，然后等待执行任务完成，回调我们的Callback。

> **注：**
> 
> - Call.enqueue需要设置回调，则是异步请求；如果需要同步请求可以使用Call.execute()方法，返回一个Response对象，cancel()方法取消一个请求。
> - onResponse的回调参数是response，我们可以通过response.body().string()获取返回的字符串，或者通过response.body().bytes()获取二进制字节数组，或者通过response.body().byteStream()获取inputstream，这里支持大文件下载我们可以通过IO写文件，不过也说明onResponse执行线程不是UI线程，如果需要操作控件，需要handler配合。


### (二) Http Post

### (三) 基于Http上传文件


## 三、源码分析

## 四、参考文档
[https://github.com/square/okhttp](https://github.com/square/okhttp "https://github.com/square/okhttp")

[http://blog.csdn.net/lmj623565791/article/details/47911083](http://blog.csdn.net/lmj623565791/article/details/47911083 "http://blog.csdn.net/lmj623565791/article/details/47911083")

[http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0326/2643.html](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0326/2643.html "http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0326/2643.html")