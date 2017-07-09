# Retrofit学习笔记 #
## 一、简介 ##
Retrofit又是square的一大作品，官方只有一句简单、直接的介绍"A type-safe HTTP client for Android and Java."；
Retrofit为我们做封装，使得我们网络请求的代码可以更加的简介。

Retrofit 支持 Java 7 以上或者 Android 2.3 以上 

本文中所使用的是Retrofit 2.1.0版本

官网地址：[http://square.github.io/retrofit/](http://square.github.io/retrofit/ "http://square.github.io/retrofit/")

项目地址：[https://github.com/square/retrofit](https://github.com/square/retrofit "https://github.com/square/retrofit")

## 二、使用 ##

### （一）导入 ###

使用Gradle

    compile 'com.squareup.retrofit2:retrofit:2.1.0'

或者使用Maven

    <dependency>
      <groupId>com.squareup.retrofit2</groupId>
      <artifactId>retrofit</artifactId>
      <version>2.1.0</version>
    </dependency>

[也可以使用最新jar包](https://search.maven.org/remote_content?g=com.squareup.retrofit&a=retrofit&v=LATEST"https://search.maven.org/remote_content?g=com.squareup.retrofit&a=retrofit&v=LATEST")

proguard相关需要注意的

    # Platform calls Class.forName on types which do not exist on Android to determine platform.
    -dontnote retrofit2.Platform
    # Platform used when running on RoboVM on iOS. Will not be used at runtime.
    -dontnote retrofit2.Platform$IOS$MainThreadExecutor
    # Platform used when running on Java 8 VMs. Will not be used at runtime.
    -dontwarn retrofit2.Platform$Java8
    # Retain generic type information for use by reflection by converters and adapters.
    -keepattributes Signature
    # Retain declared checked exceptions for use by a Proxy instance.
    -keepattributes Exceptions

### （二）基础使用 ###

#### GET请求

首先我们需要先定义一个 `REST API` 接口，例如：

	public interface GithubService{
    	@GET("/users/{user}/repos")
        List<Repo> listRepos(@Path("user") String user);
    }

该接口定义了一个函数 `listRepos` ,该函数会通过 `HTTP GET` 请求去访问服务器的 `/users/{user}/repos` 路径并把返回的结果封装成 `List<Repo>` 对象返回给我们。
我们通过 `RestAdapter` 类来生成一个 `GithubService` 接口的实例：

    RestAdapter restAdapter = new RestAdapter.Builder()
        .setEndpoint("https://api.github.com")
        .build();
    GitHubService service = restAdapter.create(GitHubService.class);
	//获取接口的实现后就可以调用接口函数，来获取服务器返回的内容了
    List<Repo> repos = service.listRepos("octocat");

从上面可以看出，`Retrofit` 使用注解来声明 `HTTP` 请求

- 支持  `URL` 参数
- 返回结果转换为 `Java` 对象

其实 `GET` 请求还支持使用查询参数

可以在 `Url` 中置顶查询参数

	@GET("users/list?sort=desc")

也可以作为参数传入

    @GET("group/{id}/users")
    Call<List<User>> groupList(@Path("id") int groupId, @Query("sort") String sort);

复杂的查询参数可以使用 `Map` 进行组合

    @GET("group/{id}/users")
    Call<List<User>> groupList(@Path("id") int groupId, @QueryMap Map<String, String> options);

**注：
1.`@Path` 标记传入的字符串替换`URL`中相应位置，实现动态请求
2.`@Query` 是传入查询参数，`@QueryMap`是传入查询参数的`Map`**

#### POST请求

