---
title: 安卓逆向之Xposed框架
toc: true
date: 2022-09-29 14:53:11
tags:
categories:
- 安卓逆向
---



# 介绍

Xpose是一款特殊的安卓应用，诞生于著名的XDA论坛,它的原理是替换安卓系统/system/bin目录下的app_process来控制zygote进程，使得app_pross在启动时会加载XposedBridge.jar，从而实现对zygode进程以及其创建的虚拟机的劫持，最终对系统的某些功能实现接管。

zygote是Android的核心，每运行一个App，Zygote 就会fork 一个虚拟机实例来运行app, Xposed
Framework深入到了Android 核心机制中

Zygote的启动配置在/init.rc 脚本中，由系统启动的时候开启此进程，对应的执行文件是
/system/bin/app_process,这个文件完成类库加载及些函数调用的工作。当系统中安装了Xposed Framework 之后，会对app_ process 进行扩展，

也就是说，Xposed Framework会拿自己实现的app_ process 覆盖掉Android 原生提供的app_ process 文件，当系统启动的时候，就会加载由Xposed Framework 替换过的进程文件，并且，Xposed Framework 还定义了一个jar 包，系统启动的时候，也会加载这个包:/data/data/de . robv . android. xposed. installer/bin/XposedBridge.jar

* 优点:xpose可以在我们不破坏apk自身的情况下实现对函数的hook，修改函数的参数和返回值，改变函数的结构并执行我们自己的代码,用好了xposed可以对我们的逆向过程起到事半功倍的作用。

* 缺点:本身不能对so中的函数进行修改 需要结合其他框架。

所需框架和jar包:

1.XposedBridge的jar包

2.xposedInstaller框架

补充:上面下载链接所给的xpose框架和模块都是事先编译好的,我们平常做项目时直接拿来用就可以。

xpose是一个开源项目,开源地址：

https://github.com/rovo89

## 一 .搭建hook环境用来编译框架所需要的模块

* 1.打开AS在项目app目录下新建lib目录并将XposedBridgeApi-54.jar放到lib目录下 右键Add As Library将jar包添加进依赖。

* 2.打开项目分支src/main目录下的AndroidManifest.xml 在application标签里面添加内容如下图所示：

```
 <meta-data
        android:name="xposedmodule"
        android:value="true" />
<meta-data
        android:name="xposeddescription"  
        android:value="Easy example" />
 <meta-data
        android:name="xposedminversion"  
        android:value="54" />
```

* 3.打开app目录下的build.gradle将compile files('lib/XposedBridgeApi-54.jar')更改为provided files('lib/XposedBridgeApi-54.jar')

* 4.新建Hook入口类HookMain实现xposed的接口IXposedHookLoadPackage并重写方法handleLoadPackage 如图所示 这个写法格式是固定的。

    ```
    public class HookMain implements IXposedHookLoadPackage {
    public void handleLoadPackage(LoadPackageParam lpparam) throws Throwable {
    }
    }
    ```
    
    

 



