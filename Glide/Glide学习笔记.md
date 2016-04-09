# Glide 学习笔记

## 零、前言

本文所使用的Glide版本为3.7.0

## 一、简介
Glide，一个被google所推荐的图片加载库，作者是bumptech。这个库被广泛运用在google的开源项目中，包括2014年的google I/O大会上发布的官方app。（PS：总所周知的简介就到此为止了）

Glide 对于 Android SDK 的最低要求是 API level 10

Glide滑行的意思，可以看出这个库的主旨就在于让图片加载变的流畅。现在被广泛使用，当然还是有很多开发者使用Square公司的picasso，也有两个库的对比

原文链接：[http://inthecheesefactory.com/blog/get-to-know-glide-recommended-by-google/en](http://inthecheesefactory.com/blog/get-to-know-glide-recommended-by-google/en "原文链接")

译文链接：[http://blog.csdn.net/fancylovejava/article/details/44747759](http://blog.csdn.net/fancylovejava/article/details/44747759 "译文链接")

## 二、使用

（一）导入
在AndroidStudio上添加依赖非常简单

	dependencies {  
	    compile 'com.github.bumptech.glide:glide:3.7.0'  
    	compile 'com.android.support:support-v4:23.2.1'  
	}  

Glide 也支持 Maven 项目形式：

	<dependency>
	  <groupId>com.github.bumptech.glide</groupId>
	  <artifactId>glide</artifactId>
	  <version>3.7.0</version>
	</dependency>
	<dependency>
	  <groupId>com.google.android</groupId>
	  <artifactId>support-v4</artifactId>
	  <version>r7</version>
	</dependency>

如果是Eclipse使用去下载Glide的jar在项目中使用就可以了，jar的链接[https://github.com/bumptech/glide/releases](https://github.com/bumptech/glide/releases "https://github.com/bumptech/glide/releases")

（二）基本使用

Glide的一个完整的请求至少需要三个参数，代码如下：

	String url = "http://img1.dzwww.com:8080/tupian_pl/20150813/16/7858995348613407436.jpg";
	ImageView imageView = (ImageView) findViewById(R.id.imageView);
	Glide.with(context)
		.load(url)
		.into(imageView);

- with(Context context) - 需要上下文，这里还可以使用 Activity、FragmentActivity、android.support.v4.app.Fragment、android.app.Fragment 的对象。将 Activity/Fragment 对象作为参数的好处是，图片的加载会和 Activity/Fragment 的生命周期保持一致，例如：onPaused 时暂停加载，onResume 时又会自动重新加载。所以在传参的时候建议使用 Activity/Fragment 对象，而不是 Context。
- load(String url) - 这里我们所使用的一个字符串形式的网络图片的 URL，后面会讲解 load() 的更多使用方式
- into(ImageView imageView) - 你需要显示图片的目标 ImageView

偶尔出现图片加载慢或者加载不出来的情况是难以避免的，所以为了 UI 能好看一些，我们会使用占位图。Glide 也为我们提供这种方法 placeHolder() 和 error()

	String url = "http://img1.dzwww.com:8080/tupian_pl/20150813/16/7858995348613407436.jpg";
	ImageView imageView = (ImageView) findViewById(R.id.imageView);
	Glide.with(context)
		.load(url)
		.placeholder(R.drawable.place_image)//图片加载出来前，显示的图片
		.error(R.drawable.error_image)//图片加载失败后，显示的图片
		.into(imageView);

> 这里需要注意一点，


## 三、源码分析