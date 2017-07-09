##最好的Android网络框架，让网络快速简单

原文：[The Best Android Networking Library for Fast and Easy Networking](https://github.com/amitshekhariitbhu/Fast-Android-Networking)

![](http://www.jcodecraeer.com/uploads/20161008/1475900429857838.png)

关于[Fast Android Networking Library](https://github.com/amitshekhariitbhu/Fast-Android-Networking)（支持所有类型的`HTTP/HTTPS`请求像`GET`,`POST`,`DELETE`,`HEAD`,`PUT`,`PATCH`）

我最近发布了一个[Library](https://github.com/amitshekhariitbhu/Fast-Android-Networking)，我相信这是Android上处理网络最简单的方式。

#### 有这些优点是优于其他网络框架的：
1. 可以更简单的自定义每一个[OkHttpClient](https://github.com/square/okhttp)请求，像超时时间
2. 比[OkHttpClient](https://github.com/square/okhttp)和[Okio](https://github.com/square/okio)更快
3. 支持[RxJava](https://github.com/ReactiveX/RxJava) - [点击这里](https://github.com/amitshekhariitbhu/Fast-Android-Networking/wiki/Using-Fast-Android-Networking-Library-With-RxJava)
4. 支持把`JSON`解析成`Java`对象（也支持`Jackson`解析）
5. 任何请求都能得到完整的分析。你可以知道发送的字节数，接收的字节数，和任何请求使用的时间。这些分析是很重要的，你能识别慢的请求。
6. 你能获取当前的**带宽**和**连接质量**写出更好的逻辑代码。
7. **适当的相应缓存** - 减少带宽的使用
8. 一个`executor`可以传递给任何请求，响应在另一个线程中获取。如果你在响应中做了很繁重的任务，你不能在主线程中做这些。
9. 一个`Library`具有所有类型的网络 - download,upload,multipart
10. `Prefetching`（预取）的任何请求，在请求需要的时候都能立即返回缓存
11. 所有类i系那个的订制是可能的（有点不太信）
12. 立即请求真的是立即发生的
13. 很好的取消请求
14. 如果请求完成了一定的百分比，可以阻止请求被取消
15. 一个简单的`interface`做任何类型的请求

#### 为什么要使用`Fast Android Networking Library`

- 最近Android M移除了HttpClient，使其他的网络库成为过时的
- 没有单一的库做所有事情像请求,下载任何文件,上传文件,加载网络图片到`ImageView`,等等。有很多库但是已经过时
- 因为它用OkHttp，最重要的是它支持HTTP/2
- 没有其他的库提供简单的`interface`做所有类型的网络情况，像设置优先级,取消等

#### 如何使用`Fast Android Networking`

`build.gradle`添加

```
compile 'com.amitshekhar.android:android-networking:0.2.0'
```

不要忘记在`manifast`中添加网络权限

```
<uses-permission android:name="android.permission.INTERNET" />
```

然后在`Application`类的`onCreate()`初始化：

```
AndroidNetworking.initialize(getApplicationContext());
```

初始化一些定制，因为用`OkHttp`作为网络层，你可以在初始化的时候自定义`okHttpClient`

```
# Adding an Network Interceptor for Debugging purpose :
OkHttpClient okHttpClient = new OkHttpClient() .newBuilder()
                        .addNetworkInterceptor(new StethoInterceptor())
                        .build();
AndroidNetworking.initialize(getApplicationContext(),okHttpClient);
```

`Jackson`与`Fast Android Networking`一起使用

```
compile 'com.amitshekhar.android:jackson-android-networking:0.2.0'
```

```
// Then set the JacksonParserFactory like below
AndroidNetworking.setParserFactory(new JacksonParserFactory());
```

### 发起一个GET请求

```
AndroidNetworking.get("https://fierce-cove-29863.herokuapp.com/getAllUsers/{pageNumber}")
                 .addPathParameter("pageNumber", "0")
                 .addQueryParameter("limit", "3")
                 .addHeaders("token", "1234")
                 .setTag("test")
                 .setPriority(Priority.LOW)
                 .build()
                 .getAsJSONArray(new JSONArrayRequestListener() {
                    @Override
                    public void onResponse(JSONArray response) {
                      // do anything with response
                    }
                    @Override
                    public void onError(ANError error) {
                      // handle error
                    }
                });
```

### 发起一个POST请求

```
AndroidNetworking.post("https://fierce-cove-29863.herokuapp.com/createAnUser")
                 .addBodyParameter("firstname", "Amit")
                 .addBodyParameter("lastname", "Shekhar")
                 .setTag("test")
                 .setPriority(Priority.MEDIUM)
                 .build()
                 .getAsJSONObject(new JSONObjectRequestListener() {
                    @Override
                    public void onResponse(JSONObject response) {
                      // do anything with response
                    }
                    @Override
                    public void onError(ANError error) {
                      // handle error
                    }
                });
```

你也可以使用`POST`请求发送`json`,`file`等

```
JSONObject jsonObject = new JSONObject();
try {
    jsonObject.put("firstname", "Rohit");
    jsonObject.put("lastname", "Kumar");
} catch (JSONException e) {
  e.printStackTrace();
}

AndroidNetworking.post("https://fierce-cove-29863.herokuapp.com/createUser")
                 .addJSONObjectBody(jsonObject) // posting json
                 .setTag("test")
                 .setPriority(Priority.MEDIUM)
                 .build()
                 .getAsJSONArray(new JSONArrayRequestListener() {
                    @Override
                    public void onResponse(JSONArray response) {
                      // do anything with response
                    }
                    @Override
                    public void onError(ANError error) {
                      // handle error
                    }
                });

AndroidNetworking.post("https://fierce-cove-29863.herokuapp.com/postFile")
                 .addFileBody(file) // posting any type of file
                 .setTag("test")
                 .setPriority(Priority.MEDIUM)
                 .build()
                 .getAsJSONObject(new JSONObjectRequestListener() {
                    @Override
                    public void onResponse(JSONObject response) {
                      // do anything with response
                    }
                    @Override
                    public void onError(ANError error) {
                      // handle error
                    }
                });
```

### 和`Java`对象一起使用 - JSON Parser
```
/*--------------Example One -> Getting the userList----------------*/
AndroidNetworking.get("https://fierce-cove-29863.herokuapp.com/getAllUsers/{pageNumber}")
                .addPathParameter("pageNumber", "0")
                .addQueryParameter("limit", "3")
                .setTag(this)
                .setPriority(Priority.LOW)
                .build()
                .getAsParsed(new TypeToken<List<User>>() {}, new ParsedRequestListener<List<User>>() {
                    @Override
                    public void onResponse(List<User> users) {
                      // do anything with response
                      Log.d(TAG, "userList size : " + users.size());
                      for (User user : users) {
                        Log.d(TAG, "id : " + user.id);
                        Log.d(TAG, "firstname : " + user.firstname);
                        Log.d(TAG, "lastname : " + user.lastname);
                      }
                    }
                    @Override
                    public void onError(ANError anError) {
                     // handle error
                    }
                });
/*--------------Example Two -> Getting an user----------------*/
AndroidNetworking.get("https://fierce-cove-29863.herokuapp.com/getAnUserDetail/{userId}")
                .addPathParameter("userId", "1")
                .setTag(this)
                .setPriority(Priority.LOW)
                .build()
                .getAsParsed(new TypeToken<User>() {}, new ParsedRequestListener<User>() {
                     @Override
                     public void onResponse(User user) {
                        // do anything with response
                        Log.d(TAG, "id : " + user.id);
                        Log.d(TAG, "firstname : " + user.firstname);
                        Log.d(TAG, "lastname : " + user.lastname);
                     }
                     @Override
                     public void onError(ANError anError) {
                        // handle error
                     }
                 });
/*-- Note : TypeToken and getAsParsed is important here --*/
```

### 从服务器下载文件
```
AndroidNetworking.download(url,dirPath,fileName)
                 .setTag("downloadTest")
                 .setPriority(Priority.MEDIUM)
                 .build()
                 .setDownloadProgressListener(new DownloadProgressListener() {
                    @Override
                    public void onProgress(long bytesDownloaded, long totalBytes) {
                      // do anything with progress
                    }
                 })
                 .startDownload(new DownloadListener() {
                    @Override
                    public void onDownloadComplete() {
                      // do anything after completion
                    }
                    @Override
                    public void onError(ANError error) {
                      // handle error
                    }
                });
```

### 上传文件到服务器

```
AndroidNetworking.upload(url)
                 .addMultipartFile("image",file)    
                 .addMultipartParameter("key","value")
                 .setTag("uploadTest")
                 .setPriority(Priority.HIGH)
                 .build()
                 .setUploadProgressListener(new UploadProgressListener() {
                    @Override
                    public void onProgress(long bytesUploaded, long totalBytes) {
                      // do anything with progress 
                    }
                 })
                 .getAsJSONObject(new JSONObjectRequestListener() {
                    @Override
                    public void onResponse(JSONObject response) {
                      // do anything with response
                    }
                    @Override
                    public void onError(ANError error) {
                      // handle error 
                    }
                 });
```

### 获取响应并在另一个`executor`中完成
（Note：错误和进度总是在主线程中返回）

```
AndroidNetworking.upload(url)
                 .addMultipartFile("image",file)  
                 .addMultipartParameter("key","value")  
                 .setTag("uploadTest")
                 .setPriority(Priority.HIGH)
                 .build()
                 .setExecutor(Executors.newSingleThreadExecutor()) // setting an executor to get response or completion on that executor thread
                 .setUploadProgressListener(new UploadProgressListener() {
                    @Override
                    public void onProgress(long bytesUploaded, long totalBytes) {
                      // do anything with progress
                    }
                 })
                 .getAsJSONObject(new JSONObjectRequestListener() {
                    @Override
                    public void onResponse(JSONObject response) {
                      // below code will be executed in the executor provided
                      // do anything with response
                    }
                    @Override
                    public void onError(ANError error) {
                      // handle error
                    }
                 });
```

### 设置请求完成一定百分比不取消的百分比阈值

···
AndroidNetworking.download(url,dirPath,fileName)
                 .setTag("downloadTest")
                 .setPriority(Priority.MEDIUM)
                 .setPercentageThresholdForCancelling(50) // even if at the time of cancelling it will not cancel if 50%
                 .build()			// downloading is done.But can be cancalled with forceCancel.
                 .setDownloadProgressListener(new DownloadProgressListener() {
                    @Override
                    public void onProgress(long bytesDownloaded, long totalBytes) {
                      // do anything with progress
                    }
                 })
                 .startDownload(new DownloadListener() {
                    @Override
                    public void onDownloadComplete() {
                      // do anything after completion
                    }
                    @Override
                    public void onError(ANError error) {
                      // handle error
                    }
                });
···

### 取消一个请求

任何给定`tag`的请求可以取消。像这么做

```
AndroidNetworking.cancel("tag"); // All the requests with the given tag will be cancelled.
AndroidNetworking.forceCancel("tag");  // All the requests with the given tag will be cancelled , even if any percent threshold is
                                       // set , it will be cancelled forcefully.
AndroidNetworking.cancelAll(); // All the requests will be cancelled.
AndroidNetworking.forceCancelAll(); // All the requests will be cancelled , even if any percent threshold is
                               // set , it will be cancelled forcefully.
```

### 加载网络图片到ImageView

```
      <com.androidnetworking.widget.ANImageView
          android:id="@+id/imageView"
          android:layout_width="100dp"
          android:layout_height="100dp"
          android:layout_gravity="center" />

      imageView.setDefaultImageResId(R.drawable.default);
      imageView.setErrorImageResId(R.drawable.error);
      imageView.setImageUrl(imageUrl);
```

### 通过`url`与一些制定参数获取`Bitmap`

```
AndroidNetworking.get(imageUrl)
                 .setTag("imageRequestTag")
                 .setPriority(Priority.MEDIUM)
                 .setBitmapMaxHeight(100)
                 .setBitmapMaxWidth(100)
                 .setBitmapConfig(Bitmap.Config.ARGB_8888)
                 .build()
                 .getAsBitmap(new BitmapRequestListener() {
                    @Override
                    public void onResponse(Bitmap bitmap) {
                    // do anything with bitmap
                    }
                    @Override
                    public void onError(ANError error) {
                      // handle error
                    }
                });
```

### 错误码处理

```
public void onError(ANError error) {
                           if (error.getErrorCode() != 0) {
                                // received error from server
                                // error.getErrorCode() - the error code from server
                                // error.getErrorBody() - the error body from server
                                // error.getErrorDetail() - just an error detail
                                Log.d(TAG, "onError errorCode : " + error.getErrorCode());
                                Log.d(TAG, "onError errorBody : " + error.getErrorBody());
                                Log.d(TAG, "onError errorDetail : " + error.getErrorDetail());
                           } else {
                                // error.getErrorDetail() : connectionError, parseError, requestCancelledError
                                Log.d(TAG, "onError errorDetail : " + error.getErrorDetail());
                           }
                        }
```

### 移除`Bitmap`缓存或者清除缓存

```
AndroidNetworking.evictBitmap(key); // remove a bitmap with key from LruCache
AndroidNetworking.evictAllBitmap(); // clear LruCache
```

### 预取请求（需要时立刻从缓存中返回）

```
AndroidNetworking.get("https://fierce-cove-29863.herokuapp.com/getAllUsers/{pageNumber}")
                .addPathParameter("pageNumber", "0")
                .addQueryParameter("limit", "30")
                .setTag(this)
                .setPriority(Priority.LOW)
                .build()
                .prefetch();
```

### 针对特殊需求订制`OkHttpClient`

```
OkHttpClient okHttpClient = new OkHttpClient().newBuilder()
                .addInterceptor(new GzipRequestInterceptor())
                .build();

AndroidNetworking.get("https://fierce-cove-29863.herokuapp.com/getAllUsers/{pageNumber}")
                 .addPathParameter("pageNumber", "0")
                 .addQueryParameter("limit", "3")
                 .addHeaders("token", "1234")
                 .setTag("test")
                 .setPriority(Priority.LOW)
                 .setOkHttpClient(okHttpClient) // passing a custom okHttpClient 
                 .build()
                 .getAsJSONArray(new JSONArrayRequestListener() {
                    @Override
                    public void onResponse(JSONArray response) {
                      // do anything with response
                    }
                    @Override
                    public void onError(ANError error) {
                    // handle error
                    }
                });
```

### `ConnectionClass`监听获得当前网络质量和带宽

```
// Adding Listener
AndroidNetworking.setConnectionQualityChangeListener(new ConnectionQualityChangeListener() {
            @Override
            public void onChange(ConnectionQuality currentConnectionQuality, int currentBandwidth) {
              // do something on change in connectionQuality
            }
        });

// Removing Listener   
AndroidNetworking.removeConnectionQualityChangeListener();

// Getting current ConnectionQuality
ConnectionQuality connectionQuality = AndroidNetworking.getCurrentConnectionQuality();
if(connectionQuality == ConnectionQuality.EXCELLENT) {
  // do something
} else if (connectionQuality == ConnectionQuality.POOR) {
  // do something
} else if (connectionQuality == ConnectionQuality.UNKNOWN) {
  // do something
}
// Getting current bandwidth
int currentBandwidth = AndroidNetworking.getCurrentBandwidth(); // Note : if (currentBandwidth == 0) : means UNKNOWN
```

### 通过设置`AnalyticsListener`获取请求的分析

```
AndroidNetworking.download(url,dirPath,fileName)
                 .setTag("downloadTest")
                 .setPriority(Priority.MEDIUM)
                 .build()
                 .setAnalyticsListener(new AnalyticsListener() {
                      @Override
                      public void onReceived(long timeTakenInMillis, long bytesSent, long bytesReceived, boolean isFromCache) {
                          Log.d(TAG, " timeTakenInMillis : " + timeTakenInMillis);
                          Log.d(TAG, " bytesSent : " + bytesSent);
                          Log.d(TAG, " bytesReceived : " + bytesReceived);
                          Log.d(TAG, " isFromCache : " + isFromCache);
                      }
                  })
                 .setDownloadProgressListener(new DownloadProgressListener() {
                    @Override
                    public void onProgress(long bytesDownloaded, long totalBytes) {
                      // do anything with progress
                    }
                 })
                 .startDownload(new DownloadListener() {
                    @Override
                    public void onDownloadComplete() {
                      // do anything after completion
                    }
                    @Override
                    public void onError(ANError error) {
                      // handle error
                    }
                });
Note : If bytesSent or bytesReceived is -1 , it means it is unknown 
```

### 在响应中获取`OkHttpResponse`

```
AndroidNetworking.get("https://fierce-cove-29863.herokuapp.com/getAnUserDetail/{userId}")
                .addPathParameter("userId", "1")
                .setTag(this)
                .setPriority(Priority.LOW)
                .setUserAgent("getAnUser")
                .build()
                .getAsOkHttpResponseAndParsed(new TypeToken<User>() {
                }, new OkHttpResponseAndParsedRequestListener<User>() {
                    @Override
                    public void onResponse(Response okHttpResponse, User user) {
                      // do anything with okHttpResponse and user
                    }
                    @Override
                    public void onError(ANError anError) {
                      // handle error
                    }
                });
```

### 做同步请求

```
ANRequest request = AndroidNetworking.get("https://fierce-cove-29863.herokuapp.com/getAllUsers/{pageNumber}")
                        .addPathParameter("pageNumber", "0")
                        .addQueryParameter("limit", "3")
                        .build();
ANResponse<List<User>> response = request.executeForParsed(new TypeToken<List<User>>() {});
if (response.isSuccess()) {
   List<User> users = responseTwo.getResult();
} else {
   //handle error
}
```

### 启用日志记录

```
AndroidNetworking.enableLogging(); // simply enable logging
AndroidNetworking.enableLogging("tag"); // enabling logging with some tag
AndroidNetworking.disableLogging(); // disable logging
```

### 混淆

如果你是用混淆，在`proguard-project.txt`中添加此规则

