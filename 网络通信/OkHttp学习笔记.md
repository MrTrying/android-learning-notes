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

## 二、基本使用

### (一) 同步Get

下载一个文件，输出它的头，以String的形式输出response的body。

response的body的string()方法对于小文件来说很方便。但是，如果response的body比较大（大于1MiB）避免使用string()，因为它将会加载这个文件到内存中。这种情况下，以流的形式处理更好。

	private final OkHttpClient client = new OkHttpClient();

	public void run() throws Exception {
    	Request request = new Request.Builder()
        	.url("http://publicobject.com/helloworld.txt")
        	.build();

    	Response response = client.newCall(request).execute();
    	if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);

    	Headers responseHeaders = response.headers();
    	for (int i = 0; i < responseHeaders.size(); i++) {
      		System.out.println(responseHeaders.name(i) + ": " + responseHeaders.value(i));
    	}

    	System.out.println(response.body().string());
	}

可以配合自己的线程池使用。

### (二) 异步Get

在工作线程中下载一个文件，当response返回时调用回调。该回调是在response的headers准备好之后执行的。OkHttp暂时不支持异步API接收response。

	private final OkHttpClient client = new OkHttpClient();

	public void run() throws Exception {
	    Request request = new Request.Builder()
	        .url("http://publicobject.com/helloworld.txt")
	        .build();
	
	    client.newCall(request).enqueue(new Callback() {
	      @Override public void onFailure(Call call, IOException e) {
	        e.printStackTrace();
	      }
	
	      @Override public void onResponse(Call call, Response response) throws IOException {
	        if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);
	
	        Headers responseHeaders = response.headers();
	        for (int i = 0, size = responseHeaders.size(); i < size; i++) {
	          System.out.println(responseHeaders.name(i) + ": " + responseHeaders.value(i));
	        }
	
	        System.out.println(response.body().string());
	      }
	    });
	}

### (三) 提取响应头

典型的HTTP的header像一个`Map<String,String>`每个域有一个值或者个没有。但是有些headers允许有多个值，像Guava's Multimap.例如一个普通合法的HTTP reponse提供多重Vary headers。OkHttp的API让这两种轻卡un个都变得更加好用。

当写入request headers时，用 `header(name,value)` 设置唯一的name、value。如果已经存在，将会移除之前的值重新添加。用 `addHeader(name,value)` 添加header，不会移除已有的。

当读取response的header时，用 `header(name)` 获得最后出现的name、value。通常这也是唯一的。如果当前没有value，`header(name)`将会返回null。用 `headers(name)` 可以获取对应字段所有值的list。

为了获取所有的headers，`Headers` 类支持用index访问。

	private final OkHttpClient mOkHttppClient = new OkHttpClient();

    public void run() throws IOException {
        Request request = new Request.Builder()
                .url("https://api.github.com/repos/square/okhttp/issues")
                .header("User-Agent" , "OkHttp Headers.java")
                .addHeader("Accept" , "aaplication/json;q=0.5")
                .addHeader("Accept" , "aaplication/vnd.gethub.v3+json")
                .build();

        Response response = mOkHttppClient.newCall(request).execute();
        if(!response.isSuccessful())
            throw new IOException("Unexpectes code " + response);

        System.out.println("Server: " + response.header("Server"));
        System.out.println("Date: " + response.header("Date"));
        System.out.println("Vary: " + response.header("Vary"));
    }

### (四) Post提交String

用 HTTP Post 返送请求到服务器。例如post一个markdown文档到web服务器，以HTML方式渲染markdown。因为整个request body在内存中，所以避免使用此API提交大文档（大于1MiB）。

	private final OkHttpClient mOkHttppClient = new OkHttpClient();

	public static final MediaType MEDIA_TYPE_MERKDOWN 
		= MediaType.parse("text/x-markdown; charset-utf-8");

    public  void run() throws IOException {
        String postBody = ""
                + "Releases\n"
                + "--------\n"
                + "\n"
                + " * _1.0_ May 6, 2013\n"
                + " * _1.1_ June 15, 2013\n"
                + " * _1.2_ August 11, 2013\n";

        Request request = new Request.Builder()
                .url("https://api.github.com/markdown/raw")
                .post(RequestBody.create(MEDIA_TYPE_MERKDOWN , postBody))
                .build();

        Response response = mOkHttppClient.newCall(request).execute();
        if(!response.isSuccessful())
            throw new IOException("Unexpectes code " + response);

        System.out.println(response.body().string());
    }

### (五) Post提交流

这里我们以流的形式`POST`请求体。请求体的内容由流写入。这个例子是流直接写入 Okio 的 BufferedSink。你的项目可能使用`OutputStream`，你可以使用`BufferendSink.outputStream()`获取。

	private final OkHttpClient mOkHttppClient = new OkHttpClient();

	public static final MediaType MEDIA_TYPE_MERKDOWN 
		= MediaType.parse("text/x-markdown; charset-utf-8");

    public void run() throws IOException {
        RequestBody requestBody = new RequestBody() {
            @Override
            public MediaType contentType() {
                return MEDIA_TYPE_MERKDOWN;
            }

            @Override
            public void writeTo(BufferedSink sink) throws IOException {
                sink.writeUtf8("Numbers\n");
                sink.writeUtf8("-------\n");
                for(int i = 2 ; i <= 997 ; i++){
                    sink.writeUtf8(String.format(" * %s = %s\n", i, factor(i)));
                }
            }

            private String factor(int n) {
                for (int i = 2; i < n; i++) {
                    int x = n / i;
                    if (x * i == n)
                        return factor(x) + " × " + i;
                }
                return Integer.toString(n);
            }
        };

        Request request = new Request.Builder()
                .url("https://api.github.com/markdown/raw")
                .post(requestBody)
                .build();

        Response response = mOkHttppClient.newCall(request).execute();
        if(!response.isSuccessful())
            throw new IOException("Unexpectes code " + response);

        System.out.println(response.body().string());
    }

### (六) Post提交文件

以文件作为请求体是十分简单的。

	private final OkHttpClient mOkHttppClient = new OkHttpClient();

	public static final MediaType MEDIA_TYPE_MERKDOWN 
		= MediaType.parse("text/x-markdown; charset-utf-8");

    public void run () throws IOException {
	    File file = new File("README.md");
	
	    Request request = new Request.Builder()
	            .url("https://api.github.com/markdown/raw")
	            .post(RequestBody.create(MEDIA_TYPE_MERKDOWN , file))
	            .build();
	
	    Response response = mOkHttppClient.newCall(request).execute();
	    if(!response.isSuccessful())
	        throw new IOException("Unexpectes code " + response);
	
	    System.out.println(response.body().string());
    }

### (七) Post提交表单

用 `FormBody.Builder` 创建一个请求体就像使用HTML的`<form>`标签一样。

	private final OkHttpClient mOkHttppClient = new OkHttpClient();

    public void run() throws IOException {
        RequestBody formBody = new FormBody.Builder()
                .add("search" , "Jurassic Park")
                .build();

        Request request = new Request.Builder()
                .url("https://en.wikipedia.org/w/index.php")
                .post(formBody)
                .build();

        Response response = mOkHttppClient.newCall(request).execute();
        if(!response.isSuccessful())
            throw new IOException("Unexpectes code " + response);

        System.out.println(response.body().string());
    }

### (八) Post提交分块请求

MultipartBodyBuilder 能构建复杂的请求体，兼容HTML文件上传。分块请求体的每一部分都有各自的请求体，能拥有自己的headers。这些headers可以描述对应部分的请求体，例如
`Content-Disposition`。如果`Content-Length`和`Content-Type`是可用的，会自动被加入到headers中。

	private final OkHttpClient mOkHttppClient = new OkHttpClient();

    public static final String IMAGE_CLIENT_ID = "...";

    public static final MediaType MEDIA_TYPE_PNG = MediaType.parse("image/png");

    public void run() throws IOException{
        // Use the imgur image upload API as documented at https://api.imgur.com/endpoints/image
        RequestBody requestBody = new MultipartBody.Builder()
                .setType(MultipartBody.FORM)
                .addFormDataPart("title" , "Square Logo")
                .addFormDataPart("image" , "logo-square.png" ,
                        RequestBody.create(MEDIA_TYPE_PNG , new File("website/static/logo-square.png")))
                .build();

        Request request = new Request.Builder()
                .header("Authorization" , "Client-ID " + IMAGE_CLIENT_ID)
                .url("https://api.imgur.com/3/image")
                .post(requestBody)
                .build();

        Response response = mOkHttppClient.newCall(request).execute();
        if(!response.isSuccessful())
            throw new IOException("Unexpectes code " + response);

        System.out.println(response.body().string());
    }


## 参考文档
[https://github.com/square/okhttp](https://github.com/square/okhttp "https://github.com/square/okhttp")

[http://blog.csdn.net/lmj623565791/article/details/47911083](http://blog.csdn.net/lmj623565791/article/details/47911083 "http://blog.csdn.net/lmj623565791/article/details/47911083")

[http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0326/2643.html](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0326/2643.html "http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0326/2643.html")