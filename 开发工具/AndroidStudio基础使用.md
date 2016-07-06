# `Android Studio` 使用 #

## 零、前言 ##
本文不是不属于纯粹的教程类型，是对于自身学习时的一种记录，希望对你的学习使用 `Android Studio` 开发会有帮助。

本文中使用到的 `Android Studio` 为 Windows 平台的 2.1.2 版本。

## 一、简介 ##
Google 在 2013 的 I/O 开发者大会上正式对外宣布 `Android Studio` 将作为 Android 开发的主要 IDE，它是基于 IntelliJ IDEA 打造的一款专门开发 Android 的 IDE 。 Adnroid Studio 支持 `Windows` 、`MAC` 和 `Linux` 等操作系统。

## 二、安装 ##

#### 官方下载链接  ####

[Android Studio 2.1.2](https://dl.google.com/dl/android/studio/install/2.1.2.0/android-studio-bundle-143.2915827-windows.exe "https://dl.google.com/dl/android/studio/install/2.1.2.0/android-studio-bundle-143.2915827-windows.exe")

[Android Studio 2.2 Preview 4](https://dl.google.com/dl/android/studio/ide-zips/2.2.0.3/android-studio-ide-145.3001415-windows.zip "https://dl.google.com/dl/android/studio/ide-zips/2.2.0.3/android-studio-ide-145.3001415-windows.zip")

> 需要科学上网

#### 网盘 ####

[Android Studio 2.1.2](http://url.cn/2KShD0V "http://url.cn/2KShD0V")

[Android Studio 2.2 Preview 4](http://url.cn/2Blmdwq "http://url.cn/2Blmdwq")

> **注：这里所给出的下载链接都是`Windows`平台的**
> 
> 这里所给出的是正式版和预览版，建议使用正式版。
> 该预览版也是相对稳定的版本（[Canary](http://tools.android.com/download/studio/canary "http://tools.android.com/download/studio/canary")版），当然还有[Dev](http://tools.android.com/download/studio/dev "http://tools.android.com/download/studio/dev")版、[Beta](http://tools.android.com/download/studio/beta "http://tools.android.com/download/studio/beta")版。

[Android Studio 官网](https://developer.android.com/studio/index.html "https://developer.android.com/studio/index.html") 有需要可以上官网下载自己需要的版本

这里我们还需要提前安装`JDk`这里可以下载[Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html "http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html")

使用正式版安装包安装`Android Studio`，基本一直Next就行；如果是预览版，直接将.zip压缩包解要到你需要的目录就可以了。

第一次启动`Android Studio`时，其中有一个步骤会提示安装`Android SDK`，按Next就会进行安装，不多很多人都会卡在这一步，我在这里提供国内的下载链接[http://tools.android-studio.org/index.php/sdk](http://tools.android-studio.org/index.php/sdk "http://tools.android-studio.org/index.php/sdk")，貌似官网已经没有独立下载SDK的地方了。

在长时间安装SDK仍然没有成功时，就可以启动`Android Studio`了，点击下面的图标会打开`SDK Manager`的设置框。

![](https://raw.githubusercontent.com/MrTrying/android-learning-notes/master/_pic/SDKManager_icon.png)

![](https://raw.githubusercontent.com/MrTrying/android-learning-notes/master/_pic/SDKManager_Dialog.png)

这里建议点击红框中的的文字连接，使用独立的`SDK Manager`进行下载

![](https://raw.githubusercontent.com/MrTrying/android-learning-notes/master/_pic/SDKManager_Lancher.png)

这里下载不会影响`Android Studio`的正常使用

如果觉得下载速度不够快可以试试如下设置，`Tools -> Options`按照下图设置；然后 `Packages -> Reload` 就可以下载 `SDK` 了。

![](https://raw.githubusercontent.com/MrTrying/android-learning-notes/master/_pic/SDKManager_Option.png)

其中 Tools 直接勾选全部下载比较好；`API` 选择需要的版本的 `SDK Platform`，`Sources for Android SDK` 建议最好也下载，`Extras`中的最好也是全部下载；都勾选好了，既可以点击右下角的`Install packagers`，然后Accept所有选项，点击`Install`，就可以了，下载完成之后建议`SDK`备份一下。

![](https://raw.githubusercontent.com/MrTrying/android-learning-notes/master/_pic/SDKManager_DownLoad_Options.png)

进入`Android Studio`的`Setting`界面设置一下SDK路径就ok了。

![](https://raw.githubusercontent.com/MrTrying/android-learning-notes/master/_pic/SDK_Path_Setting.png)

`Android Studio` 的安装基本已经结束了，如果需要其他的插件自己寻找，这里推荐`Sexy Editor`和[Color Themes](http://color-themes.com/?view=index "http://color-themes.com/?view=index")

## 三、使用 ##

### （1）创建项目 ###

首次开启 `Android Studio` 会进入以下界面，第一个就是创建一个新项目，首次我们选这个就可以了。

![icon](https://raw.githubusercontent.com/MrTrying/android-learning-notes/master/_pic/Welcome_to_AndroidStudio.png)

> - 第二个选项是打开一个已经存在的 `Android Studio` 项目
> - 第三个选项是是从版本控制检出一个项目，例如：`Git`、`Svn`、`Github`等等
> - 第四个选项是导入一个由 `Eclipse` 导出的 `Android Studio` 项目
> - 第五个选项是导入`Android`的实例项目

创建一个新项目以后，就会进入如下界面，图中标记1处是项目名称，标记2处是公司的域名，填写完这两个以后标记3处的包名会自动生成，当然你可以可以通过最后面的Edit进行编辑；标记4处可以修改项目的存放路径，如果都配置好了就可以Next进入下一步了。

![](https://raw.githubusercontent.com/MrTrying/android-learning-notes/master/_pic/Create_New_Project.png)

这里需要我们选择项目的类型和最低支持`SDK`的`API`版本，项目类型除了手机，还支持 `TV`、`Wear`、`Glass`、`Auto`；选择类型以后还需要选择最低支持SDK版本，这个关系到设备的覆盖率，图中的标记2处可以查看各个SDK版本能覆盖到的百分比，从而帮助我们选择。

![](https://raw.githubusercontent.com/MrTrying/android-learning-notes/master/_pic/Choice_SDK_Min.png)

下图是 `SDK `分布的百分比截图

![](https://raw.githubusercontent.com/MrTrying/android-learning-notes/master/_pic/API_Version_Distribution.png)

选择完 `MinimumSDK`，接下即使创建项目的第一个 `Activity`，`Android Studio` 提供了一些默认的 `Activity` 模版，我们选择一个样式

![](https://raw.githubusercontent.com/MrTrying/android-learning-notes/master/_pic/Create_Activity.png)

然后，我们可以修改 `Activity` 的一些命名，输入 `Activity　Name` 的同时 `Layout Name` 也会自动生成，当然你也可以自己修改

![](https://raw.githubusercontent.com/MrTrying/android-learning-notes/master/_pic/Customize_Activity_info.png)

点击Finish按钮，`Android Studio` 会开始自动创建项并编译应用，编译结束后，我们创建项目就完成了。

### （2）IDE基础介绍 ###

将使用较多的地方分为4个区域，接下来会一一介绍这些区域的使用，区域划分如下图

![](https://raw.githubusercontent.com/MrTrying/android-learning-notes/master/_pic/IDE_UI.png)

#### 区域一介绍 ####

该区域主要是与运行，调试相关的操作

![](https://raw.githubusercontent.com/MrTrying/android-learning-notes/master/_pic/IDE_UI_Part1.png)

1. **编译 2 中选中的 `Module`**
2. **当前项目的 `Module` 列表**
3. **运行 2 中的 `Module`**
4. **调试 2 中的 `Module`**
5. **暂缺（说实话这个我还没有用过）**
6. **暂缺（说实话这个我还没有用过）**
7. **重新运行 2 中显示的 `Module`**
8. **停止运行 2 中显示的 `Module`**
9. **打开 `Android Studio Setting` 界面，可以修改 `Android Studio` 的设置**
10. **打开 `Project Structrue` 界面，可以修改项目中相关的设置**
11. **同步项目的 `Gradle` 文件**
12. **虚拟设备管理**
13. **`Android SDK` 管理**
14. **`DDMS Android`设备监控**

#### 区域二介绍 ####

该区域主要是显示项目文件资源目录

![](https://raw.githubusercontent.com/MrTrying/android-learning-notes/master/_pic/IDE_UI_Part2_1.png)

1. **项目目录的展示方式，默认是 `Android` 方式展示， 可选择"`Project`、`Packages`、`Scratches`、`Project Files`、`Problems`、`Production`..."，其中 `Android` 和 `Project` 是最常使用的，`Android`是为了方便开发所展现的目录，并不是文件存储的正式目录，而 `Project` 展示的是项目的正式目录**
2. **定位当前打开的文件在项目中的位置**
3. **折叠项目展开的所有目录**
4. **可以调整项目目录的一些展示行为，如下图**

![](https://raw.githubusercontent.com/MrTrying/android-learning-notes/master/_pic/IDE_UI_Part2_Option.png)

接下来看看 `Projdect` 模式下的目录结构

![](https://raw.githubusercontent.com/MrTrying/android-learning-notes/master/_pic/IDE_UI_Project_Directory.png)

1. `Gradle` 编译系统
2. `Android Studio` 需要的文件
3. 应用相关的文件目录
4. 编译产生的文件
5. 存放依赖库的地方
6. 项目的源文件
7. 暂时还没用过，应该是单元测试用的
8. java代码存放目录
9. 资源文件存放目录
10. `Manifest` 文件
11. 暂时还没用过，应该是测试用的
12. `git` 版本管理忽略文件，标记出哪些文件不用进入git库中
13. `Android Studio` 工程文件
14. 应用 `app module` 的 `Gradle` 相关配置
15. 代码混淆的配置文件
16. 项目的 `Gradle` 相关配置 
17. `Gradle` 相关的全局属性设置
18. 本地属性设置（key设置，android sdk、ndk位置等属性）
19. 为项目导入 `Module`

#### 区域三介绍 ####

该区域主要是编写代码和xml布局

![](https://raw.githubusercontent.com/MrTrying/android-learning-notes/master/_pic/IDE_UI_Part3.png)

1. 已打开文件的Tab
2. 布局预览区域
3. `xml` 布局编辑方式的切换，一般都是直接使用 `Text` 编辑模式，`Design` 编辑模式都是可视化的设计布局

#### 区域四介绍 ####

该区域主要是查看信息输出

![](https://raw.githubusercontent.com/MrTrying/android-learning-notes/master/_pic/IDE_UI_Part4.png)

1. **运行App的一些相关信息**
2. **边有个TODO注释的列表**
3. **可以查看app输出的一些信息**
4. **终端 - 可以直接使用终端，不用而外启动中断了**
5. **编译项目是输出的一些信息**
6. **事件日志**
7. **输出一些 `Gradle` 构建应用时的信息**

> **PS：以上都是简单的基础使用，其他的会在文章的后面讲解，或者单开一篇文章记录**














