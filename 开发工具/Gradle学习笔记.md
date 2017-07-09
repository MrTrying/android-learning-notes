# Gradle  学习笔记#

##零、前言

文中使用的是 `Gradle3.0`，系统为 `win 10`。 

## 一、简介

Gradle是一个基于 `JVM` 的构建工具

- 支持多工程构建，有强大的依赖管理（基于 `ApacheIvy`）
- 对已有的 `maven` 和 `ivy` 仓库全面支持
- 支持传递性依赖管理，而不需要远程仓库或者 `pom.xml` 或者 `ivy` 配置文件
- 基于 `groovy` ，其 build 脚本使用 `groovy dsl` 编写
- 具有广泛的领域模型支持你的构建

##二、安装

`AndroidStudio` 会替我们下载安装所以可忽略此环节。

### 先决条件
`Gradle` 需要 `JDK` 版本1.7 以上。`Gradle` 自带 `Groovy` 库，我们不需要单独安装 `Groovy` 环境，`Gradle` 会忽略已经安装的 `Groovy` ；而 `JDK` 的环境 `Gradle`则是会使用系统环境变量中的 `JDK` 。

### 下载

[Gradle 官方网站下载](https://gradle.org/gradle-download/ "Gradle 官方网站下载")

> 貌似需要科学上网，我在下载时遇到下载不完整的情况，建议复制下载链接，然后使用迅雷

官方提供了三个链接，一个是完整版 zip，包含：环境、文档和源码；第二个是不带源码和文档的压缩包，这个根据自己需要吧！第三个则是源码下载的链接。

### 配置环境变量

直接接解压上一步下载下来的压缩包，放到你想放的目录下；在 `PATH` 环境变量中添加 `bin` 目录的路径；例如：`C:\Users\Administrator\.gradle\wrapper\dists\gradle-3.0-all\bin`。我为了方便，使用了 `AndroidStudio` 安装 `Gradle` 目录

接下来需要检查一下时候安装成功了，在终端中运行 `gradle -v`会显示出当前的 `JVM` 版本和 `Gradle` 版本。 

![](https://raw.githubusercontent.com/MrTrying/android-learning-notes/master/_pic/gradle%E5%AE%89%E8%A3%85%E6%A3%80%E6%B5%8B.png)

## 三、

