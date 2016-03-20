# Volley 学习笔记

## 零、前言

由于Volley推出时间太早，对于现在大部分网络需求而言，原生的Volley可能已经不太适应现在的开发环境，此文仅仅用于自己学习与分享。

## 一、简介

Volley是在2013年Google I/O大会上推出的一个新的网络通信框架。Volley可是说是把AsyncHttpClient和Universal-Image-Loader的优点集于了一身，既可以像AsyncHttpClient一样非常简单地进行HTTP通信，也可以像Universal-Image-Loader一样轻松加载网络上的图片。除了简单易用之外，Volley在性能方面也进行了大幅度的调整，它的设计目标就是非常适合去进行数据量不大，但通信频繁的网络操作，而对于大数据量的网络操作，比如说下载文件等，Volley的表现就会非常糟糕。

> 注意，这个库要求最低SDK版本为Froyo，即至少要设置android:minSdkVersion为8以上。

### 优点
- 通信更快，更简单
- Get、Post网络请求及网络图像的高效率异步处理请求排序
- 网络请求的缓存
- 多级别取消请求
- 与Activity生命周期联动

### 缺点
- 不适合数据的上传和下载

## 二、使用

### （一）项目导入

使用 Volley 之前肯定需要先拿到 Volley 的 library 或者 jar , 如果装有 Git 可以使用如下命令下载 Volley 源码

	git clone https://android.googlesource.com/platform/frameworks/volley

下载完成后将它直接导入你的Eclipse项目，也可以编译为jar再import到项目中

如果需要下载jar可以通过该链接下载：[http://mvnrepository.com/artifact/com.mcxiaoke.volley/library](http://mvnrepository.com/artifact/com.mcxiaoke.volley/library "http://mvnrepository.com/artifact/com.mcxiaoke.volley/library")

如果是使用AndroidStudio，可以前往[https://android.googlesource.com/platform/frameworks/volley](https://android.googlesource.com/platform/frameworks/volley "https://android.googlesource.com/platform/frameworks/volley")
获取Volley的library，添加为项目module，当然也可以添加Volley到gradle依赖，可惜没有官方的地址。

### （二）Get请求

使用 StringRequest 发送请求

	final String TAG_GET = "... ";
	String url = "...";
    RequestQueue mRequestQueue = Volley.newRequestQueue(context);
	StringRequest stringRequest = new StringRequest(Method.GET , url ,
		new Listener<String>(){
			@Override
			public void onResponse(String responseStr){

			}
		},
		new Response.ErrorListener(){
			@Override 
			public void onErrorResponse(VolleyError error){

			}
		});
	stringRequest.setTag(TAG_GET);
	mRequestQueue.add(stringRequest);

使用 JsonObjectRequest 请求与 StringRequest 不同的是 JsonObjectRequest 多了一个 JSONObject 参数，是用来发送 Post 请求的参数的。

	com.android.volley.toolbox.JsonObjectRequest.JsonObjectRequest(int method , String url , JSONObject jsonRequest , Listener<JSONObject> listener , ErrorListener errorListener)

示例代码：

	final String TAG_GET = "... ";
	String url = "...";
	RequestQueue mRequestQueue = Volley.newRequestQueue(context);
	JsonObjectRequest jsonObjectRequest = new JsonObjectRequest(Method.GET , url , null ,
		new Listener<JsonObject>(){
			@Override
			public void onResponse(JSONObject jsonObj){

			}
		},
		new Response.ErrorListener(){
			@Override 
			public void onErrorResponse(VolleyError error){

			}
		});
	jsonObjectRequest.setTag(TAG_GET);
	mRequestQueue.add(jsonObjectRequest);

JsonArrayRequest 的用法与 JsonObjectRequest 用法一致，参照即可。

> Volley实现了三种常见的请求类型：
>
> - StringRequest
> - ImageRequest
> - JsonRequest
>
> 接口 Response.Listener<?> 是请求成功的回调， Response.ErrorListener 是请求失败的回调。
>
> **注：接口 Response.Listener<?> 中的泛型决定 onResponse 方法的参数是什么类型，可以是 JSONObject，JSONArray ， Bitmap(后面加载图片时会使用到)；如果不知道是什么类型，可以使用 String 。**

### （三）Post请求

使用 StringRequest 发送 Post 请求与 Get 的区别在于，将 Method.GET 替换成 Method.POST ,然后还需要重写 StringRequest 的 getParams() 方法，在 getParams() 方法中将需要的参数拼接成 Map<String,String> 返回回去。

	final String TAG_GET = "... ";
	String url = "...";
    RequestQueue mRequestQueue = Volley.newRequestQueue(context);
	StringRequest stringRequest = new StringRequest(Method.POST , url ,
		new Listener<String>(){
			@Override
			public void onResponse(String responseStr){

			}
		},
		new Response.ErrorListener(){
			@Override 
			public void onErrorResponse(VolleyError error){

			}
		}){
			//重写 StringRequest 的 getParams() 方法
			@Override 
			protected Map<String,String> getParams() throws AutgFailureError {
				Map<String,String> params = new HashMap<>();
				params.put(key1 , value1);
				params.put(key2 , value2);
				//...
				return params;
			}
		};
	stringRequest.setTag(TAG_GET);
	mRequestQueue.add(stringRequest);

使用 JsonObjectRequest 发送 Post 请求时，与发送 Get 请求相比，只需要把 Method.GET 改成 Method.POST 再传入参数 jsonRequest 即可

	final String TAG_GET = "... ";
	String url = "...";
	RequestQueue mRequestQueue = Volley.newRequestQueue(context);
	Map<String,String> map = new HashMap<>();
	map.put(key1 , value1);
	map.put(key2 , value2);
	//...
	JSONObject jsonRequest = new JSONObject(map);
	JsonObjectRequest jsonObjectRequest = new JsonObjectRequest(Method.POST , url , jsonRequest ,
		new Listener<JsonObject>(){
			@Override
			public void onResponse(JSONObject jsonObj){

			}
		},
		new Response.ErrorListener(){
			@Override 
			public void onErrorResponse(VolleyError error){

			}
		});
	jsonObjectRequest.setTag(TAG_GET);
	mRequestQueue.add(jsonObjectRequest);

JsonArrayRequest 的用法还是与 JsonObjectRequest 用法一致，参照即可。

### （四）Volley 请求与 Activity 的生命周期关联

在 Volley 的请求设置 TAG 以后，在 Activity 的 onStop() 方法中调用 mRequestQueue.cancelAll(TAG) ,这样就可以和 Activity 的生命周期联动

示例代码：

	@Override
	protected void onStop(){
		super.onStop();
		mRequestQueue.cancelAll(TAG);
	}

如果你想取消所有请求，在onStop方法中添加如下代码：

	@Override
	protected void onStop(){
		super.onStop();
		mRequestQueue.cancelAll(new RequestQueue.RequestFilter(){
			@Override 
			public boolean apply(Request<?> request){
				return true;//always yes
			}
		});
	}

这样就不用担心在 onResponse 被调用的时候 Activity 已经被销毁，然后抛出异常了。

> 注：从代码可以看出来 RequestQueue 的对象应该设计成单例，一般情况下全局都使用该请求队列即可。

### （五）获取网络图片
图片加载的缓存需要使用到 LruCache 和 ImageCache

	RequestQueue mRequestQueue = Volley.newRequestQueue(context);
	String imgUrl = "https://www.baidu.com/img/bdlogo.png"；
	int imgWidth = 0;
	int imgHeight = ;
	//可以设置图片显示的大小
	ImageRequest imageRequest = new ImageRequest(imgUrl , new Listener<Bitmap>(){
			@Override 
			public void onResponse(Bitmap bmp){
		
			}
		},imgWidth,imgHeight,Config.RGB_565,
		new Response.ErrorListener(){
			@Override 
			public void onErrorResponse(VolleyError error){

			}
		});
	mRequestQueue.add(imageRequest):

以上只是一个简单的网络图片请求，为了提高性能肯定是需要对图片进行缓存的，先上 BitmapCache 的类代码

	public class BitmapCache implements ImageCache{
		public LruCahce<String , Bitmap> cache = null ;
		//缓存的最大空间
		public int max = 10 * 1024 * 1024;

		public BitmapCache(){
			//初始化LruCache
			cache = new LruCache<>(max){
				@Override protected int sizeOf(String key , Bitmap value){
					return value.getRowBytes() * value.getHeight();
				}
			}
		}
		
		@override 
		public Bitmap getBitmap(String key){
			return cache.get(key);
		}
		
		@override 
		public void putBitmap(String key , Bitmap value){
			cache.put(key , value);
		}
	}

使用 ImageView 加载图片代码
	
	String imgUrl = "https://www.baidu.com/img/bdlogo.png"；
	ImageLoader mImageLoader = new ImageLoader(mRequestQueue , new BitmapCache());
	ImageView imageView = (ImageView)findViewById(R.id.imageview);
	ImageListener imageListener = ImageLoader.getImageListener(imageView , R.drawable.icon_default , R.drawable.icon_error);
	mImageLoader.get(imgUrl,imageListener);

接下来使用 Volley 自带的 NetworkImageView 来加载图片

	String imgUrl = "https://www.baidu.com/img/bdlogo.png"；	
	ImageLoader mImageLoader = new ImageLoader(mRequestQueue , new BitmapCache());
	NetworkImageView netImageVIew = (NetworkImageView)findVIewById(R.id.netimageview);
	netImageView.setDefaultImageResId(R.darwable.icon_default);
	netImageView.setErrorImageResId(R.drawable.icon_error);
	netImageView.setImageUrl(imgUrl , loader);

### （六）管理 Cookie 和 Request 优先级
Volley doesn't provide a method for setting the cookies of a request, nor its priority. It probably will in the future, since it's a serious omission. For the time being, however, you have to extend the Request class.

Volley 没有提供方法设置一个请求的 Cookie 和优先级。也许将来会有，因为这是一个严重的疏忽。目前，你需要继承Request。

言下之意就是我们可以通过集成 Request 类，然后重写 getHeaders 方法来添加请求的 header ， 从而管理我们的 Cookie 。

	public class CustomRequest extends JsonObjectRequest{
		private Map<String,String> headers = new HashMap<>();
		
		//Since we're extending Request class
		//we just use its constructor
		public CustomRequest(int method , String url , JSONObject jsonRequest , Response.Listener<JSONObject> listener , Response.ErrorListener errorListener){
			super(method,url,jsonRequest,listener,errorListener);
		}
		
		//自己设置cookie
		public void setCookies(Lst<String> cookies){
			StringBuilder sb = new StringBuilder();
			for(String cookie : cookies){
				sb.append(cookie).append("; ");
			}
			headers.put("Cookie" , sb.toString());
		}

		//重写该方法，返回自定义的Cookie
		@Override
		public Map<String,String> getHeaders() throws AuthFailError{
			return headers;
		}
	}

通过继承 Request ，我们就可以使用 setCookies 方法为请求提供 cookie 列表了。

	List<String> cookies = new ArrayList<>();
	cookie.add("site=code");
	cookie.add("network=tusplus");
	//设置自定义的cookie
	customRequest.setCookies(cookies);
	//把自定义的request加入queue
	mRequestQueue.add(customRequest):

而对于 Request 的优先级的管理，我们同样需要继承 Request 类，然后重写getPriority方法，示例代码：

	Priority mPriority = null;

	public void setPriority(Priority priority){
		mPriority = priority;
	}

	@Override
	public Priority getPriority(){
		return mPriority != null ? mPriority : Priority.NORMAL;
	}

在主线程中调用 setPriority 方法来设置请求的优先级，可以从以下几个优先级中选择一个：

	Priority.LOW // images , thumbnails , ....
	Priority.NORMAL // residual
	Priority.HIGH // descriptions , lists , ...
	Priority.IMMEDIATE // login , logout , ...

## 三、源码分析

### Volley架构

![Volley架构图](https://github.com/MrTrying/android_Learning_Notes/blob/master/Volley/pic/Volley%E6%9E%B6%E6%9E%84%E5%9B%BE.png?raw=true)

由官方给出的 Volley 架构图可以看出，一个请求队列有三种线程，蓝色为 UI 线程（1个），绿色为 Cache 线程（1个），橙色为 Network 线程（默认4个）。

- UI 线程负责添加请求任务，执行任务结果
- Cache 线程负责检查缓存，命中后直接将任务结果分发到 UI 线程
- Network 线程由多个任务线程（NetworkDispatcher）组成的，相当于一个大小为 size 的线程池，这些线程会同时启动，并持续的从任务列表中获取待执行的任务，任务执行完后会将结果分发到UI线程

