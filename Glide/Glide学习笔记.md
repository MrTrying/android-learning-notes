# Glide 学习笔记

## 一、简介
Glide，一个被google所推荐的图片加载库，作者是bumptech。这个库被广泛运用在google的开源项目中，包括2014年的google I/O大会上发布的官方app。（PS：总所周知的简介就到此为止了）

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

如果是Eclipse使用去下载Glide的jar在项目中使用就可以了，jar的链接[https://github.com/bumptech/glide/releases](https://github.com/bumptech/glide/releases "https://github.com/bumptech/glide/releases")


## 三、源码分析