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

这么说可能不那么好理解，那么接下来我们进入源码。
	
	Volley.newRequestQueue(getApplication());

还记得这句初始化吧！跟进代码 Volley.newRequestQueue(),在只传如 context 的情况下 HttpStack 传入的是 null；

	public static RequestQueue newRequestQueue(Context context, HttpStack stack) {
	    // 缓存目录
	    File cacheDir = new File(context.getCacheDir(), DEFAULT_CACHE_DIR);
	
	    // 拼装UA
	    String userAgent = "volley/0";
	    try {
	        String packageName = context.getPackageName();
	
	        PackageInfo info = context.getPackageManager().getPackageInfo(packageName, 0);
	        userAgent = packageName + "/" + info.versionCode;
	    } catch (NameNotFoundException e) {
	    }
	
	    // 如果HttpStack为空
	    if (stack == null) {
	        // 判断sdk版本
	        // HurlStack和HttpClientStack内部分别使用HttpUrlConnection和HttpClient
	        // 进行网络请求
	        if (Build.VERSION.SDK_INT >= 9) {
	            // 使用HttpUrlConnection
	            stack = new HurlStack();
	        } else {
	            // 使用HttpClient
	            // Prior to Gingerbread, HttpUrlConnection was unreliable.
	            // See: http://android-developers.blogspot.com/2011/09/androids-http-clients.html
	            stack = new HttpClientStack(AndroidHttpClient.newInstance(userAgent));
	        }
	    }
	
	    // 创建NetWork
	    Network network = new BasicNetwork(stack);
	    // 初始化请求队列，注意：**这里并不是一个线程**，并启动它
	    RequestQueue queue = new RequestQueue(new DiskBasedCache(cacheDir), network);
	    queue.start();
	
	    return queue;
	}

这个方法里面初始化了 HttpStack，Network，RequestQueue；并且调用了 RequestQueue 的 start() 方法；

> **注意：RequestQueue不是一个线程**

我们先来看一下 RequestQueue 的构造方法到底做了些什么事情

	public RequestQueue(Cache cache, Network network) {
        this(cache, network, DEFAULT_NETWORK_THREAD_POOL_SIZE);
    }

	public RequestQueue(Cache cache, Network network, int threadPoolSize) {
        this(cache, network, threadPoolSize,
                new ExecutorDelivery(new Handler(Looper.getMainLooper())));
    }

	public RequestQueue(Cache cache, Network network, int threadPoolSize,
            ResponseDelivery delivery) {
        mCache = cache;
        mNetwork = network;
        mDispatchers = new NetworkDispatcher[threadPoolSize];
        mDelivery = delivery;
    }

上面是直接贴出了从开始调用直到最后的的构造方法，可以看到在 threadPoolSize 参数传入的是一个常量 **DEFAULT_NETWORK_THREAD_POOL_SIZE**（int类型，值为4），在最后的构造方法可以看到作为一个数组的长度使用的，根据命名可以知道意思是线程池大小；而在最后的构造方法中初始化了 Cache , Network , NetworkDispatcher[] , ResponseDeliviery 这里只能获得这么多的信息了。

接下来就轮到 RequestQueue 的 start() 方法

	public void start() {
        stop();  // Make sure any currently running dispatchers are stopped.
        // Create the cache dispatcher and start it.
        mCacheDispatcher = new CacheDispatcher(mCacheQueue, mNetworkQueue, mCache, mDelivery);
        mCacheDispatcher.start();

        // Create network dispatchers (and corresponding threads) up to the pool size.
        for (int i = 0; i < mDispatchers.length; i++) {
            NetworkDispatcher networkDispatcher = new NetworkDispatcher(mNetworkQueue, mNetwork,
                    mCache, mDelivery);
            mDispatchers[i] = networkDispatcher;
            networkDispatcher.start();
        }
    }
	
	/** Stops the cache and network dispatchers. */
	public void stop() {
        if (mCacheDispatcher != null) {
            mCacheDispatcher.quit();
        }
        for (int i = 0; i < mDispatchers.length; i++) {
            if (mDispatchers[i] != null) {
                mDispatchers[i].quit();
            }
        }
    }

顺带把 stop() 代码也贴上了，在初始化之前现调用了 stop() 保证 CacheDispatcher 和所有的 NetworkDispatcher 都 quit，然后新建 CacheDispatcher 并启动，CacheDispatcher 是一个线程，内部是一个死循环，然后创建了几个 NetworkDispatcher 并全部启动，到这里就明白了前面在 RequestQueue 的构造其中初始化的那个 NetworkDispatcher[] 在这个方法中完成了所有初始化的工作，NetworkDispatcher 也是一个线程，同样内部是死循环。
 
接下来我们看看 CacheDispatcher 的 run 方法

	@Override
    public void run() {
        if (DEBUG) VolleyLog.v("start new dispatcher");
        Process.setThreadPriority(Process.THREAD_PRIORITY_BACKGROUND);

        // Make a blocking call to initialize the cache.
        mCache.initialize();

        while (true) {
            try {
                // Get a request from the cache triage queue, blocking until
                // at least one is available.
				// 从缓存的请求中获取请求，如果没有请求，这里会阻塞
                final Request<?> request = mCacheQueue.take();
				// 标记：从缓存中获取的
                request.addMarker("cache-queue-take");

                // If the request has been canceled, don't bother dispatching it.
				// 如果请求cancel了，结束请求
                if (request.isCanceled()) {
                    request.finish("cache-discard-canceled");
                    continue;
                }

                // Attempt to retrieve this item from cache.
				// 根据缓存的请求的key，试图获取本地缓存中的http
                Cache.Entry entry = mCache.get(request.getCacheKey());
				// 如果获取到的为null
                if (entry == null) {
					// 标记：缓存丢失
                    request.addMarker("cache-miss");
                    // Cache miss; send off to the network dispatcher.
					// 将请求放到网络请求队列中
                    mNetworkQueue.put(request);
                    continue;
                }

                // If it is completely expired, just send it to the network.
				// 如果本地缓存过期
                if (entry.isExpired()) {
					// 标记：缓存过期
                    request.addMarker("cache-hit-expired");
                    request.setCacheEntry(entry);
					// 将请求加入网络请求队列
                    mNetworkQueue.put(request);
                    continue;
                }

                // We have a cache hit; parse its data for delivery back to the request.
				// 标记：本地缓存可用
                request.addMarker("cache-hit");
				// 将数据解析成Response----mark
                Response<?> response = request.parseNetworkResponse(
                        new NetworkResponse(entry.data, entry.responseHeaders));
                request.addMarker("cache-hit-parsed");

				//　如果数据不需要重新获取
                if (!entry.refreshNeeded()) {
                    // Completely unexpired cache hit. Just deliver the response.
					//　直接回调到我们设置的listener----mark
                    mDelivery.postResponse(request, response);
                } else {
                    // Soft-expired cache hit. We can deliver the cached response,
                    // but we need to also send the request to the network for
                    // refreshing.
					// 标记：本地的缓存需要刷新
                    request.addMarker("cache-hit-refresh-needed");
                    request.setCacheEntry(entry);

                    // Mark the response as intermediate.
                    response.intermediate = true;

					// 将结果回调并又将请求放到请求队列当中----mark
                    // Post the intermediate response back to the user and have
                    // the delivery then forward the request along to the network.
                    mDelivery.postResponse(request, response, new Runnable() {
                        @Override
                        public void run() {
                            try {
                                mNetworkQueue.put(request);
                            } catch (InterruptedException e) {
                                // Not much we can do about this.
                            }
                        }
                    });
                }

            } catch (InterruptedException e) {
                // We may have been interrupted because it was time to quit.
                if (mQuit) {
                    return;
                }
                continue;
            }
        }
    }

这段代码可能比较长，但是逻辑还是很清楚的。从缓存的请求中获取请求，判断本地缓存的http是否存在、过期，根据判断的状态来决定是将请求放到网络请求队列中，还是直接从本地缓存直接获取数据。代码里面有几个mrak的地方，我们过会再看，接下来我们看看NetworkDispatcher.run()

	@Override
    public void run() {
        Process.setThreadPriority(Process.THREAD_PRIORITY_BACKGROUND);
		// 这是死循环
        while (true) {
            long startTimeMs = SystemClock.elapsedRealtime();
            Request<?> request;
            try {
                // Take a request from the queue.
				// 请求队列中获取请求
                request = mQueue.take();
            } catch (InterruptedException e) {
                // We may have been interrupted because it was time to quit.
                if (mQuit) {
                    return;
                }
                continue;
            }

            try {
                request.addMarker("network-queue-take");

                // If the request was cancelled already, do not perform the
                // network request.
				// 如果请求canceled，结束请求
                if (request.isCanceled()) {
                    request.finish("network-discard-cancelled");
                    continue;
                }

                addTrafficStatsTag(request);

                // Perform the network request.
				// 通过 mNetwork.performRequest 请求网络，并将分析后的结果封装到 NetworkResponse 中返回
				// 这里面包含了 statusCode , data , headers , notModified ----mark
                NetworkResponse networkResponse = mNetwork.performRequest(request);
                request.addMarker("network-http-complete");

                // If the server returned 304 AND we delivered a response already,
                // we're done -- don't deliver a second identical response.
                if (networkResponse.notModified && request.hasHadResponseDelivered()) {
                    request.finish("not-modified");
                    continue;
                }

                // Parse the response here on the worker thread.
				// 这里解析网络请求获取到的 NetworkResponse 对象，并根据我们使用的不同的 Request 进行解析
                Response<?> response = request.parseNetworkResponse(networkResponse);
                request.addMarker("network-parse-complete");

                // Write to cache if applicable.
                // TODO: Only update cache metadata instead of entire record for 304s.
				// 允许缓存的话，将请求缓存起来
                if (request.shouldCache() && response.cacheEntry != null) {
                    mCache.put(request.getCacheKey(), response.cacheEntry);
                    request.addMarker("network-cache-written");
                }

                // Post the response back.
				// 标记请求已经投递
                request.markDelivered();
				// 将结果投递带我们的Listener----mark
                mDelivery.postResponse(request, response);
            } catch (VolleyError volleyError) {
                volleyError.setNetworkTimeMs(SystemClock.elapsedRealtime() - startTimeMs);
                parseAndDeliverNetworkError(request, volleyError);
            } catch (Exception e) {
                VolleyLog.e(e, "Unhandled exception %s", e.toString());
                VolleyError volleyError = new VolleyError(e);
                volleyError.setNetworkTimeMs(SystemClock.elapsedRealtime() - startTimeMs);
                mDelivery.postError(request, volleyError);
            }
        }
    }

分析到这里，基本上整个流程走通了，但是还有部分逻辑上的实现细节还没有理清楚，前面有几个地方的注释都打上了 mark 标记，接下来我们就看看这些 mark 的地方的细节。

CacheDispatcher.run
	
	// 将数据解析成Response----mark
    Response<?> response = request.parseNetworkResponse(new NetworkResponse(entry.data, entry.responseHeaders));

这里将数据解析成 Response，注意这里是本地的缓存；那么我们继续跟进 request.parseNetworkResponse 方法的代码，但是需要注意的是 Request 是一个 interface , 我们所使用的都是它的实现类，我们先来看看 JsonObjectRequest 中是怎么实现 parseNetworkResponse() 方法的。

	@Override
    protected Response<JSONObject> parseNetworkResponse(NetworkResponse response) {
        try {
            String jsonString = new String(response.data,
				HttpHeaderParser.parseCharset(response.headers, PROTOCOL_CHARSET));
            return Response.success(
				new JSONObject(jsonString),HttpHeaderParser.parseCacheHeaders(response));
        } catch (UnsupportedEncodingException e) {
            return Response.error(new ParseError(e));
        } catch (JSONException je) {
            return Response.error(new ParseError(je));
        }
    }

首先将 byte[] response.data 按照 http 头信息中的 charset 构成一个 String，然后返回 Response 对象，而 Response.success() 参数是我们 new 的 JSONObject，还记得在使用 JsonObjectRequest 的时候 onResponse 中返回给我们的是 JSONObject ，继续看看 Resopnse.success 方法代码

	/** Returns a successful response containing the parsed result. */
    public static <T> Response<T> success(T result, Cache.Entry cacheEntry) {
        return new Response<T>(result, cacheEntry);
    }

用我们传进来的两个参数构造了一个 Response 对象，看看 Response 的构造方法

	private Response(T result, Cache.Entry cacheEntry) {
        this.result = result;
        this.cacheEntry = cacheEntry;
        this.error = null;
    }

    private Response(VolleyError error) {
        this.result = null;
        this.cacheEntry = null;
        this.error = error;
    }

这里对传进去的数据做了简单的保存，那我们怎么使用保存的数据呢？Response 有 Listener 和 ErrorListener 接口

	/** Callback interface for delivering parsed responses. */
    public interface Listener<T> {
        /** Called when a response is received. */
        public void onResponse(T response);
    }

    /** Callback interface for delivering error responses. */
    public interface ErrorListener {
        /**
         * Callback method that an error has been occurred with the
         * provided error code and optional user-readable message.
         */
        public void onErrorResponse(VolleyError error);
    }

至于什么时候回调接口，我们看一下 mark 的地方

	//　直接回调到我们设置的listener----mark
    mDelivery.postResponse(request, response);

ResponseDelivery 是一个 interface，我看看实现类--ExecutorDelivery

	/**
     * Creates a new response delivery interface.
     * @param handler {@link Handler} to post responses on
     */
    public ExecutorDelivery(final Handler handler) {
        // Make an Executor that just wraps the handler.
        mResponsePoster = new Executor() {
            @Override
            public void execute(Runnable command) {
                handler.post(command);
            }
        };
    }

只传入了一个 handler 参数，这个 handler 是在 RequestQueue 的一个构造器里面初始化的，之前的代码有贴出来过，这个 handler 是通过 new Handler(Looper.getMainLooper())) 初始化的，handler 被指定到了 UI 线程上的 Looper , 回到 ExecutorDelivery 的构造器，new 了一个 Executor，并重写了 execute 方法，这个方法里面用 handler post 了一个 Runnable。我们接着看 postResponse 的细节

	@Override
    public void postResponse(Request<?> request, Response<?> response) {
        postResponse(request, response, null);
    }

    @Override
    public void postResponse(Request<?> request, Response<?> response, Runnable runnable) {
        request.markDelivered();
        request.addMarker("post-response");
        mResponsePoster.execute(new ResponseDeliveryRunnable(request, response, runnable));
    }

标记了 request 并且 post 了一个 new ResponseDeliveryRunnable() , ResponseDeliveryRunnable 是 ExecutorDelivery 的一个内部类，来看看它的代码

	private class ResponseDeliveryRunnable implements Runnable {
	    private final Request mRequest;
	    private final Response mResponse;
	    private final Runnable mRunnable;
	
	    public ResponseDeliveryRunnable(Request request, Response response, Runnable runnable) {
	        mRequest = request;
	        mResponse = response;
	        mRunnable = runnable;
	    }
	
	    @SuppressWarnings("unchecked")
	    @Override
	    public void run() {
	        // If this request has canceled, finish it and don't deliver.
	        if (mRequest.isCanceled()) {
	            mRequest.finish("canceled-at-delivery");
	            return;
	        }
	
	        // Deliver a normal response or error, depending.
	        if (mResponse.isSuccess()) {
	            mRequest.deliverResponse(mResponse.result);
	        } else {
	            mRequest.deliverError(mResponse.error);
	        }
	
	        // If this is an intermediate response, add a marker, otherwise we're done
	        // and the request can be finished.
	        if (mResponse.intermediate) {
	            mRequest.addMarker("intermediate-response");
	        } else {
	            mRequest.finish("done");
	        }
	
	        // If we have been provided a post-delivery runnable, run it.
	        if (mRunnable != null) {
	            mRunnable.run();
	        }
   		}
	}

实现 Runnable 接口，有三个参数，一路回溯，我们会发现 runnable 为 null，而 request 和 response 这两个参数是从 mDelivery.postResponse(request, response); 这句传递过来的，也就是 CacheDispatcher 传递过来的。request 是我们从 cache 队列中获得的，response 则是 request.parseNetworkResponse() 返回的。现在来源清楚了我们继续分析

	// Deliver a normal response or error, depending.
    if (mResponse.isSuccess()) {
        mRequest.deliverResponse(mResponse.result);
    } else {
        mRequest.deliverError(mResponse.error);
    }

这里是在UI线程中执行了，利用我们的request.deliverResponse

	@Override
	protected void deliverResponse(T response) {
   		mListener.onResponse(response);
	}

现在我们去看看，这个 mLIstener 是不是我们传入的那个 listener

	public JsonRequest(String url, String requestBody, Listener<T> listener,
        ErrorListener errorListener) {
    this(Method.DEPRECATED_GET_OR_POST, url, requestBody, listener, errorListener);
	}
	
	public JsonRequest(int method, String url, String requestBody, Listener<T> listener,
	        ErrorListener errorListener) {
	    super(method, url, errorListener);
	    mListener = listener;
	    mRequestBody = requestBody;
	}

到这里为止，一个请求从加入到缓存队列，然后从缓存队列加入到请求队列，判断时候有对应的本地缓存，包装请求结果，然后各种调用，到最后回调到我们的 lisnter 都已经分析清楚了，现在还剩下网络请求部分了，我们继续来看其他 mark 的地方

	// 这里将结果回调并且又将请求放到请求队列中----mark
	// Post the intermediate response back to the user and have
	// the delivery then forward the request along to the network.
	mDelivery.postResponse(request, response, new Runnable() {
		@Override
		public void run() {
		    try {
		        mNetworkQueue.put(request);
		    } catch (InterruptedException e) {
		        // Not much we can do about this.
		    }
		}
	});

这里执行了那个三个构造的 ResponseDeliveryRunnable ， mRunnable 肯定不为 null 所以 mRunnable.run() 中的代码得以执行,也就是说我们重新将 request 加入到了请求队列中,继续看mark的地方,NetworkDispatcher.run 中不断的从 mNetworkQueue 中获取一个 request 并且执行

	// Perform the network request.
	// 通过NetWork.performRequest来真正的请求网络
	// 并将分析后的结果封装到networkResponse中返回
	// 这里面包含了statusCode，data，headers，notModified
	// ----mark
	NetworkResponse networkResponse = mNetwork.performRequest(request);
	request.addMarker("network-http-complete");


NetWork是一个接口，我们来看它的实现类——BasicNetwork，也就是我们在刚开始看到new的那个，BasicNetwork.performRequest

	@Override
	public NetworkResponse performRequest(Request<?> request) throws VolleyError {
	    long requestStart = SystemClock.elapsedRealtime();
	    while (true) {
	        HttpResponse httpResponse = null;
	        byte[] responseContents = null;
	        Map<String, String> responseHeaders = new HashMap<String, String>();
	        try {
	            // Gather headers.
	            Map<String, String> headers = new HashMap<String, String>();
	            // 从Cache中获取header，并添加到Map中
	            addCacheHeaders(headers, request.getCacheEntry());
	            // 执行网络请求
	            httpResponse = mHttpStack.performRequest(request, headers);
	            // 获取状态
	            StatusLine statusLine = httpResponse.getStatusLine();
	            int statusCode = statusLine.getStatusCode();
	
	            // 将header放到上面定义的responseHeaders中
	            responseHeaders = convertHeaders(httpResponse.getAllHeaders());
	            // Handle cache validation.
	            // 内容没有修改
	            if (statusCode == HttpStatus.SC_NOT_MODIFIED) {
	               // 这里构造了NetworkResponse
	                return new NetworkResponse(HttpStatus.SC_NOT_MODIFIED,
	                        request.getCacheEntry().data, responseHeaders, true);
	            }
	            // 将返回的内容转化为byte数组
	            responseContents = entityToBytes(httpResponse.getEntity());
	            // if the request is slow, log it.
	            // 标记访问慢的请求
	            long requestLifetime = SystemClock.elapsedRealtime() - requestStart;
	            logSlowRequests(requestLifetime, request, responseContents, statusLine);
	
	            if (statusCode != HttpStatus.SC_OK && statusCode != HttpStatus.SC_NO_CONTENT) {
	                throw new IOException();
	            }
	            // 构造NetworkResponse并返回
	            // 这里面包含状态吗， 返回的内容， header
	            return new NetworkResponse(statusCode, responseContents, responseHeaders, false);
	        } catch (SocketTimeoutException e) {
	            attemptRetryOnException("socket", request, new TimeoutError());
	        } catch (ConnectTimeoutException e) {
	            attemptRetryOnException("connection", request, new TimeoutError());
	        } catch (MalformedURLException e) {
	            throw new RuntimeException("Bad URL " + request.getUrl(), e);
	        } catch (IOException e) {
	            int statusCode = 0;
	            NetworkResponse networkResponse = null;
	            if (httpResponse != null) {
	                statusCode = httpResponse.getStatusLine().getStatusCode();
	            } else {
	                throw new NoConnectionError(e);
	            }
	            VolleyLog.e("Unexpected response code %d for %s", statusCode, request.getUrl());
	            if (responseContents != null) {
	                networkResponse = new NetworkResponse(statusCode, responseContents,
	                        responseHeaders, false);
	                if (statusCode == HttpStatus.SC_UNAUTHORIZED ||
	                        statusCode == HttpStatus.SC_FORBIDDEN) {
	                    attemptRetryOnException("auth",
	                            request, new AuthFailureError(networkResponse));
	                } else {
	                    // TODO: Only throw ServerError for 5xx status codes.
	                    throw new ServerError(networkResponse);
	                }
	            } else {
	                throw new NetworkError(networkResponse);
	            }
	        }
	    }
	}
基本的流程看我写的注释，重要的看这里的代码，

	...
	httpResponse = mHttpStack.performRequest(request, headers);
	...
还记得HttpStack是什么吗？来回忆一下吧，Volley.newRequestQueue中

	...
	if (stack == null) {
	    if (Build.VERSION.SDK_INT >= 9) {
	        stack = new HurlStack();
	    } else {
	        // Prior to Gingerbread, HttpUrlConnection was unreliable.
	        // See: http://android-developers.blogspot.com/2011/09/androids-http-clients.html
	        stack = new HttpClientStack(AndroidHttpClient.newInstance(userAgent));
	    }
	}
	...
通过判断SDK的版本来选择使用HttpUrlConnection还是HttpClient，我们来看看HttpClientStack也就是使用HttpClient怎么实现的，

	@Override
	public HttpResponse performRequest(Request<?> request, Map<String, String> additionalHeaders)
	        throws IOException, AuthFailureError {
	    // 构造请求方式
	    HttpUriRequest httpRequest = createHttpRequest(request, additionalHeaders);
	    // 添加从缓存中获取的header
	    addHeaders(httpRequest, additionalHeaders);
	    // 添加我们重写getHeaders中自定义的header
	    addHeaders(httpRequest, request.getHeaders());
	    // nothing
	    onPrepareRequest(httpRequest);
	    // 获取我们重写的getParams方法中的参数
	    HttpParams httpParams = httpRequest.getParams();
	    int timeoutMs = request.getTimeoutMs();
	    // TODO: Reevaluate this connection timeout based on more wide-scale
	    // data collection and possibly different for wifi vs. 3G.
	    HttpConnectionParams.setConnectionTimeout(httpParams, 5000);
	    HttpConnectionParams.setSoTimeout(httpParams, timeoutMs);
	    // 执行网络请求并返回结果
	    return mClient.execute(httpRequest);
	}
第一行代码createHttpRequest其实就是根据我们使用的请求方式(GET,POST,PUT…)来构造不同的请求类(HttpGet, HttpPost, HttpPut)，接下来是想请求中添加header，添加了两次，第一次是从缓存中获取的header，第二次获取的我们重写getHeaders方法中返回的那个map，接下来onPrepareRequest是一个空方法，继续代码，是调用我们重写getParams获取参数，最后执行HttpClient.execute(HttpUriRequest request)执行网络请求，并返回结果。 
这样我们终于把Volley整个的请求过程走通了，但是还有一个问题？ 我们目前为止看到的RequestQueue和CacheQueue都是空的，并没有往里添加request，那request是什么时候添加的呢？还记得我们怎么往volley添加一个请求吗？

	mRequestQueue.add(request);

我们来看看这个add方法，
	
	public Request add(Request request) {
	    // Tag the request as belonging to this queue and add it to the set of current requests.
	    // 标记这个请求放入了请求队列中
	    request.setRequestQueue(this);
	    synchronized (mCurrentRequests) {
	        mCurrentRequests.add(request);
	    }
	
	    // Process requests in the order they are added.
	    request.setSequence(getSequenceNumber());
	    request.addMarker("add-to-queue");
	
	    // If the request is uncacheable, skip the cache queue and go straight to the network.
	    // 如果请求允许缓存，则添加到缓存队列中
	    // 并且返回
	    if (!request.shouldCache()) {
	        mNetworkQueue.add(request);
	        return request;
	    }
	
	    // Insert request into stage if there's already a request with the same cache key in flight.
	    synchronized (mWaitingRequests) {
	        String cacheKey = request.getCacheKey();
	        // 如果有相同的请求正在等待
	        // 将这个请求放到这个具有相同cacheKey的队列中
	        // 这个cacheKey其实就是我们访问的url，
	        // 也就是具有相同url的请求我们放一块
	        if (mWaitingRequests.containsKey(cacheKey)) {
	            // There is already a request in flight. Queue up.
	            Queue<Request> stagedRequests = mWaitingRequests.get(cacheKey);
	            if (stagedRequests == null) {
	                stagedRequests = new LinkedList<Request>();
	            }
	            stagedRequests.add(request);
	            mWaitingRequests.put(cacheKey, stagedRequests);
	            if (VolleyLog.DEBUG) {
	                VolleyLog.v("Request for cacheKey=%s is in flight, putting on hold.", cacheKey);
	            }
	        } else {
	            // Insert 'null' queue for this cacheKey, indicating there is now a request in
	            // flight.
	            // 如果没有，则添加一个null
	            // 并放到cache队列中
	            mWaitingRequests.put(cacheKey, null);
	            mCacheQueue.add(request);
	        }
	        return request;
	    }
	}

为什么要这么麻烦呢？ 好几个队列，有点晕了，我们来看看RequestQueue的finish方法，这个方式是在请求结束后调用的，上面的代码中，很多地方地方都调用了Request.finish(tag)方法，
	
	void finish(final String tag) {
	    if (mRequestQueue != null) {
	        mRequestQueue.finish(this);
	    }
	    if (MarkerLog.ENABLED) {
	        final long threadId = Thread.currentThread().getId();
	        if (Looper.myLooper() != Looper.getMainLooper()) {
	            // If we finish marking off of the main thread, we need to
	            // actually do it on the main thread to ensure correct ordering.
	            Handler mainThread = new Handler(Looper.getMainLooper());
	            mainThread.post(new Runnable() {
	                @Override
	                public void run() {
	                    mEventLog.add(tag, threadId);
	                    mEventLog.finish(this.toString());
	                }
	            });
	            return;
	        }
	
	        mEventLog.add(tag, threadId);
	        mEventLog.finish(this.toString());
	    } else {
	        long requestTime = SystemClock.elapsedRealtime() - mRequestBirthTime;
	        if (requestTime >= SLOW_REQUEST_THRESHOLD_MS) {
	            VolleyLog.d("%d ms: %s", requestTime, this.toString());
	        }
	    }
	}

这里面调用了RequestQueue的finish方法，并将当前request对象传递过去，

	void finish(Request request) {
	    // Remove from the set of requests currently being processed.
	    // 从当前将要执行的request队列中移除
	    synchronized (mCurrentRequests) {
	        mCurrentRequests.remove(request);
	    }
	
	    if (request.shouldCache()) {
	        synchronized (mWaitingRequests) {
	            String cacheKey = request.getCacheKey;
	            Queue<Request> waitingRequests = mWaitingRequests.remove(cacheKey);
	            if (waitingRequests != null) {
	                if (VolleyLog.DEBUG) {
	                    VolleyLog.v("Releasing %d waiting requests for cacheKey=%s.",
	                            waitingRequests.size(), cacheKey);
	                }
	                // Process all queued up requests. They won't be considered as in flight, but
	                // that's not a problem as the cache has been primed by 'request'.
	                mCacheQueue.addAll(waitingRequests);
	            }
	        }
	    }
	}

这里面将request从mCurrentRequests中移除，并且判断waitingRequests是否含有该url的请求，如果有，则移除，并且将移除的队列全部放到mCacheQueue,为什么要这么干？还记得我们在CacheDispatcher中那一系列判断吗？如果该请求的url已经缓存很有可能直接将结果回调了。这种做法解决了一个很重要的问题，我们连续访问两次同一个url，真正去访问网络的其实就一个，第二次直接从缓存中获取结果了。 
ok，到目前为止Volley的过程我们就分析完毕了。

## 四、参考文献

[http://www.kancloud.cn/qibin0506/android-src/117690](http://www.kancloud.cn/qibin0506/android-src/117690 "http://www.kancloud.cn/qibin0506/android-src/117690")
[http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0720/3204.html](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0720/3204.html "http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0720/3204.html")
[http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0526/2934.html](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0526/2934.html "http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0526/2934.html")



