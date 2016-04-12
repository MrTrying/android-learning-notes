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

>- with(Context context) - 需要上下文，这里还可以使用 Activity、FragmentActivity、android.support.v4.app.Fragment、android.app.Fragment 的对象。将 Activity/Fragment 对象作为参数的好处是，图片的加载会和 Activity/Fragment 的生命周期保持一致，例如：onPaused 时暂停加载，onResume 时又会自动重新加载。所以在传参的时候建议使用 Activity/Fragment 对象，而不是 Context。
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

>**注：这里需要注意一点，placeholder() 和 error() 的参数都是只支持 int 和 Drawable 类型的参数，这种设计应该是考虑到使用本地图片比网络图片更加合适做占位图。**

#### 缩略图 ####

Glide 的缩略图功能在这里不得不说，和占位图略有不同，占位图必须使用资源文件才行，而缩略图是动态的占位图可以从网络中加载。缩略图会在世纪请求加载完成或者处理完之后才显示。在原始图片到达之后，缩略图不会取代原始图片，只会被抹除。

Glide 为缩略图提供了2种不同的加载方式，比较简单的方式是调用 thumbnail() 方法，参数是 float 类型，作为其倍数大小。例如，你传入 0.2f 作为参数，Glide 将会显示原始图片的20%的大小，如果原图是 1000x1000 的尺寸，那么缩略图将会是 200x200 的尺寸。为缩略图明显比原图小得多，所以我们需要确保 ImageView 的 ScaleType 设置的正确。

	Glide.with( context )
		.load( url )
		.thumbnail( 0.2f )
		.into( imageView );

> **注：应用于请求的设置也将应用于缩略图。**

使用 thumbnail() 方法来设置是简单粗暴的，但是如果缩略图需要通过网络加载相同的全尺寸图片，就不会很快的显示了。所以 Glide 提供了另一种防止去加载缩略图，先看代码

	private void loadImageThumbnailRequest(){
		// setup Glide request without the into() method
		DrawableRequestBuilder<String> thumbnailRequest = Glide.with( context ).load( url );
		// pass the request as a a parameter to the thumbnail request
		Glide.with( context )
			.load( url )
			.thumbnail( thumbnailRequest )
			.into( imageView );
	}

与第一种方式不同的是，这里的第一个缩略图请求是完全独立于度二个原始请求的。该缩略图可以是不同的资源图片，同时也可以对缩略图做不同的转换，等等...

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

>**注：其实 Glide 的 CenterCrop() 和 FitCenter() 这两个方法分别对应 ImageView 的 ScaleType 属性中的 CENTER_CROP 和 FIT_CENTER 命名基本一致。**

#### 图片的缓存处理 ####

为了更快的加载图片，我们肯定希望可以直接拿到图片，而不是进行网络请求，所以我们需要缓存。Glide 通过使用默认的内存和磁盘缓存来避免不必要的网络请求，之后我们再详细的去看它的实现。

###3## 内存缓存 ######

内存缓存是 Glide 默认帮我们做了的，除非你不需要，可以调用 skipMemoryCache(true) 告诉 Glide 跳过内存缓存。这样 Glide 就不会把这张图片放到内存缓存中，该方法只影响内存缓存。（不要问调用skipMemoryCache(false)的问题，Glide 是默认将图片放入内存缓存中的）

###### 磁盘缓存 ######

磁盘缓存也是默认开启的，当然也是可以关闭的，不过关闭的方式略微有点不一样。

	Glide.with(context)
		.load(url)
		.skipMemoryCache(true)
		.diskCacheStrategy( DiskCacheStrategy.NONE )
		.into(imageView);

上面这段代码将内存缓存和磁盘缓存都禁用了，这里使用枚举 DiskCacheStrategy.NONE 将磁盘缓存禁用了，这里涉及到了自定义磁盘缓存行为，我们接下来就讲解这个。

###### 自定义磁盘缓存行为 ######

使用 DiskCacheStrategy 可以为 Glide 配置磁盘缓存行为。Glide 的磁盘缓存比较复杂，这也是在图片加载可以比 Picasso 的原因（之一）。Picasso 只缓存了全尺寸的图片，而 Glide 的不同之处在于，Glide 不仅缓存了全尺寸的图，还会根据 ImageView 大小所生成的图也会缓存起来。比如，请求一个 800x600 的图加载到一个 400x300 的 ImageView 中，Glide默认会将这原图还有加载到 ImageView 中的 400x300 的图也会缓存起来。

> DiskCacheStrategy 的枚举意义： 
> 
> - DiskCacheStrategy.NONE 什么都不缓存
> - DiskCacheStrategy.SOURCE 只缓存全尺寸图
> - DiskCacheStrategy.RESULT 只缓存最终的加载图
> - DiskCacheStrategy.ALL 缓存所有版本图（**默认行为**）

这只是举个例子而已

	Glide.with(context)
		.load(url)
		.diskCacheStrategy( DiskCacheStrategy.SOURCE )
		.into(imageView);

#### 图片请求的优先级 ####

同一时间加载多个图片，App 将难以避免这种情况。如果这个时候我们希望用户的体验更好，往往会选择先加载对于用户更加重要的图片。Glide 可以调用 .priority() 方法配合 Priority 枚举来设置图片加载的优先级。

	//设置 HIGH 优先级
	Glide.with( context )
		.load( highPriorityImageUrl )
		.priority (Priority.High )
		into( imageView );
	//设置 LOW 优先级
	Glide.with( context )
		.load( lowPriorityImageUrl )
		.priority( Priority.High )
		into( imageView );

> - Priority.LOW
> - Priority.NORMAL
> - Priority.HIGH
> - Priority.IMMEDIAT
>
> **这里有一点需要注意，优先级并不是完全严格遵守的。Glide 将会用他们作为一个准则，尽可能的处理这些请求，但是不能保证所有的图片都会按照所有要求的顺序加载。**





## 三、源码分析