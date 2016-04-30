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




### (三) 基于Http上传文件




## 三、源码分析

## 四、参考文档
[https://github.com/square/okhttp](https://github.com/square/okhttp "https://github.com/square/okhttp")

[http://blog.csdn.net/lmj623565791/article/details/47911083](http://blog.csdn.net/lmj623565791/article/details/47911083 "http://blog.csdn.net/lmj623565791/article/details/47911083")

[http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0326/2643.html](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0326/2643.html "http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0326/2643.html")