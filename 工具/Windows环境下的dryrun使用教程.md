## Windows环境下的dryrun使用教程

### 零、前言

作为`Android`攻城狮逛`Github`，就像逛贴吧、论坛一样平常，遇到有兴趣的开源项目肯定会先想运行在自己的手机上看看效果，但是往往这个过程是相当复杂的。首先需要下载项目，然后导入该项目，然后`Gradle`构建项目、编译，最后运行到手机上，这个过程耗时太长也很折磨人。但是`dryrun`只需要一句话就可以达到在手机上预览的效果。

[dryrun的Github地址](https://github.com/cesarferreira/dryrun)

> 本文针对Window系统的教程

### 一、安装环境

`dryrun`的作者使用的是Mac，是基于Mac环境的。但是Window环境也是可以跑的，不过需要有一定的环境。

首先我们需要`Ruby`和`Devkit`，[下载地址](http://rubyinstaller.org/downloads/)

选择自己需要的版本，先安装Ruby，我是用的`.exe`安装的，建议以管理员身份运行

##### Step 1

有英文和日语可选，如果你觉得你对日文的造诣更高可以选择日语，直接点击OK

![Step 1](https://github.com/MrTrying/android-learning-notes/blob/master/_pic/ruby%E5%AE%89%E8%A3%85_step1.png?raw=true)

##### Step 2

这里毫无疑问必须同意，除非你不想装了

![Step 2](https://github.com/MrTrying/android-learning-notes/blob/master/_pic/ruby%E5%AE%89%E8%A3%85_step2.png?raw=true)

##### Step 3

可以修改安装的路径（随意，我也拦不住你），勾选的东西看着意思像是安装相关的支持、添加`Ruby`路径到环境变量，第三个我也看不太懂，不过我还是勾上了，不勾之后的步骤执行的不顺利的话，你可以在重新安装一次

![Step 3](https://github.com/MrTrying/android-learning-notes/blob/master/_pic/ruby%E5%AE%89%E8%A3%85_step3.png?raw=true)

##### Step 4

等待完成...

![Step 4](https://github.com/MrTrying/android-learning-notes/blob/master/_pic/ruby%E5%AE%89%E8%A3%85_step4.png?raw=true)

##### Step 5

老实点Finish

![Step 5](https://github.com/MrTrying/android-learning-notes/blob/master/_pic/ruby%E5%AE%89%E8%A3%85_step5.png?raw=true)

到此为止，`Ruby`的环境算是完事了，我接下来处理`Devkit`的 `.exe`文件，同样建议以管理员身份运行

##### Step 1

选择你想要放的目录

![Step 1](https://github.com/MrTrying/android-learning-notes/blob/master/_pic/Devkit_install_step1.png?raw=true)

##### Step 2

老实等着就好

![Step 2](https://github.com/MrTrying/android-learning-notes/blob/master/_pic/Devkit_install_step2.png?raw=true)

这里基础环境准备好了，我们可以正式开始了。

打开`cmd`窗口，如果你安装在系统盘（建议使用以管理员身份运行`cmd`），然后执行`ruby dk.rb init`

![](https://github.com/MrTrying/android-learning-notes/blob/master/_pic/dk_init.png?raw=true)

然后在`Devkit`的目录下会生成`config.yml`文件，打开文件将你Ruby的安装路径填写进去，例如：`C:\Program Files (x86)\Ruby23-x64`，然后我们在执行`ruby dk.rb install`命令

![](https://github.com/MrTrying/android-learning-notes/blob/master/_pic/dk_install.png?raw=true)

安装完成后在执行`gem install rdiscount --source http://rubygems.org`

![](https://github.com/MrTrying/android-learning-notes/blob/master/_pic/gem_install.png?raw=true)

这一步成功之后我们就可以安装`dryrun`了。`cmd`中切换到你安装`ruby`的目录中的`bin`目录下，执行`gem install dryrun --source http://rubygems.org`，完成之后`bin`目录下会多出`dryrun`和`dryrun.bat`的文件

![](https://github.com/MrTrying/android-learning-notes/blob/master/_pic/dryrun_install.png?raw=true)

最后我们就能愉快的使用`dryrun`了，将手机链接到电脑，执行`dryrun https://github.com/cesarferreira/android-helloworld`就能直接安装该项目到你的手机上了

![](https://github.com/MrTrying/android-learning-notes/blob/master/_pic/dryrun_run.png?raw=true)

以后就能愉快预览`Github`上`Library`的效果了。。。



