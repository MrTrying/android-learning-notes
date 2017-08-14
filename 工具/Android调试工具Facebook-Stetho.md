![Stetho](http://upload-images.jianshu.io/upload_images/595349-439268be18ed574e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
一、简介
Stetho是一个Android应用调试工具。集成后，开发人员可以通过Chrome的开发工具查看App相关的信息和调试；可视化操作，不需要自己使用adb也不需要root权限。

二、APP集成
下载[最新的jar](https://github.com/facebook/stetho/releases/latest)，或者通过Gradle引入stetho的libraay
```
compile 'com.facebook.stetho:stetho:1.5.0'
```
也可以使用Maven
```
<dependency>
  <groupId>com.facebook.stetho</groupId>
  <artifactId>stetho</artifactId>
  <version>1.5.0</version>
</dependency>
```
只需要在`Application`的`onCreate`方法中调用
```
Stetho.initializeWithDefaults(this);
```
如果还想查看网络请求的话，需要引入另外的Library
```
//使用Okhttp需要引入
compile 'com.facebook.stetho:stetho-okhttp3:1.5.0'//okhttp3.0以上使用
compile 'com.facebook.stetho:stetho-okhttp:1.5.0'

//使用urlconnection需要引入
//compile 'com.facebook.stetho:stetho-urlconnection:1.5.0'
```
另外，使用`okhttp`还需要添加`StethoInterceptor`
```
new OkHttpClient.Builder()
    .addNetworkInterceptor(new StethoInterceptor())
    .build()
```

你还可以启动一个JavaScript控制台
```
compile 'com.facebook.stetho:stetho-js-rhino:1.5.0'
```
三、使用
配置完成之后，在Chrome地址栏输入chrome://inspect
![](http://upload-images.jianshu.io/upload_images/595349-62764da55a984c3d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
选取需要调试的App进程，点击insoect

![](http://upload-images.jianshu.io/upload_images/595349-9057dd5c736f38e2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

UI层次结构，还可以修改其中的内容

![查看SQLite](http://upload-images.jianshu.io/upload_images/595349-fbcf84b9baaef191.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



![查看SharedPreferences文件](http://upload-images.jianshu.io/upload_images/595349-f489d692a72fd000.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)