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

###（一）导入

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

### （二）使用 ###

#### 基本方法 ####

Glide的一个完整的请求至少需要三个参数，代码如下：

	String url = "http://img1.dzwww.com:8080/tupian_pl/20150813/16/7858995348613407436.jpg";
	ImageView imageView = (ImageView) findViewById(R.id.imageView);
	Glide.with(context)
		.load(url)
		.into(imageView);

- with(Context context) - 需要上下文，这里还可以使用 Activity、FragmentActivity、android.support.v4.app.Fragment、android.app.Fragment 的对象。将 Activity/Fragment 对象作为参数的好处是，图片的加载会和 Activity/Fragment 的生命周期保持一致，例如：onPaused 时暂停加载，onResume 时又会自动重新加载。所以在传参的时候建议使用 Activity/Fragment 对象，而不是 Context。
- load(String url) - 这里我们所使用的一个字符串形式的网络图片的 URL，后面会讲解 load() 的更多使用方式
- into(ImageView imageView) - 你需要显示图片的目标 ImageView

#### 占位图设置 ####

偶尔出现图片加载慢或者加载不出来的情况是难以避免的，所以为了 UI 能好看一些，我们会使用占位图。Glide 也为我们提供这种方法 placeHolder() 和 error()

	String url = "http://img1.dzwww.com:8080/tupian_pl/20150813/16/7858995348613407436.jpg";
	ImageView imageView = (ImageView) findViewById(R.id.imageView);
	Glide.with(context)
		.load(url)
		.placeholder(R.drawable.place_image)//图片加载出来前，显示的图片
		.error(R.drawable.error_image)//图片加载失败后，显示的图片
		.into(imageView);

**注：这里需要注意一点，placeholder() 和 error() 的参数都是只支持 int 和 Drawable 类型的参数，这种设计应该是考虑到使用本地图片比网络图片更加合适做占位图。**

#### 动画开关 ####

动画效果可以让图片加载变得更加的平滑，crossFade() 方法强制开启 Glide 默认的图片淡出淡入动画，当前版本3.7.0是默认开启的。crossFade() 还有一个重载方法 crossFade(int duration)。可以控制动画的持续时间，单位ms。动画默认的持续时间是300ms。既然可以添加动画，那肯定就可以设置没有任何淡出淡入效果，调用 dontAnimate()

	Glide.with(context)
		.load(url)
		.crossFade()//或者使用 dontAnimate() 关闭动画
		.placeholder(R.drawable.place_image)
		.error(R.drawable.error_image)
		.into(imageView);

**PS：Glide 是可以自定义动画效果的，这个在后面会讲解**

#### 图片大小与裁剪 ####

在项目开发过程中，指定图片显示大小长长可能用到，毕竟从服务器获取的图片不一定都是符合设计图的标准的。我们在这里就可以使用 override(width,height) 方法，在图片显示到 ImageView 之前，重新改变图片大小。

	Glide.with(context)
		.load(url)
		.override(width,height)//这里的单位是px
		.into(imageView);

在设置图片到 ImageView 的时候，为了避免图片被挤压失真，ImageView 本身提供了 ScaleType 属性，这个属性可以控制图片显示时的方式，具体的属性使用还是去搜索吧！Glide 也提供了两个类似的方法 CenterCrop() 和 FitCenter()，CenterCrop() 方法是将图片按比例缩放到足矣填充 ImageView 的尺寸，但是图片可能会显示不完整；而 FitCenter() 则是图片缩放到小于等于 ImageView 的尺寸，这样图片是显示完整了，但是 ImageView 就可能不会填满了。

**注：其实 Glide 的 CenterCrop() 和 FitCenter() 这两个方法分别对应 ImageView 的 ScaleType 属性中的 CENTER_CROP 和 FIT_CENTER 命名基本一致。**
















## 三、源码分析