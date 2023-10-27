---
title: 安卓逆向之APK打包流程
toc: true
date: 2022-09-21 10:17:44
tags:
categories:
- 安卓逆向
- 入门手册
---



# APK文件介绍

apk是Android Package的缩写，也就是Android安装包。其实，APK文件是压缩包形式呈现的。
通过将打包好的APK文件直接传到Androi d模拟器或Android手机中执行、安装，就形成我们
使用的各种APP程序;

![](C:\1myblog\myblog\source\_posts\安卓逆向之APK打包流程/1.png)

## 1.META-INF文件夹

META- INF文件夹下存放了APK文件的基本信息和签名信息，用来保证APK文件的完整性和系
统的安全。类似于jar文件, META- INF文件夹下的MANIFEST. MF文件，

说明Mani fest文件的版本和创建者，以及相关文件的校验信息。Android 系统在安装某个APK文件时会对APK中压缩的各个文件进行校验检查，如果校验结果与META-INF下的内容不一致，系统就不会安装这个APK文件，这就保证了程序安装包的安全性。例如解压一一 个APK文件，替换里面的一幅图片、一段代码，或一段版权信息，然后重新压缩打包，即使这个APK文件符合格式要求，但是也无法通过校验进行安装。所以这样就给病毒感染和恶意修改增加了难度，可以有效地保护系统安全。



META- INF文件夹只存在于签名后的apk文件中，其中包含MANIFEST. MF、CERT. SF和CERT. RSA
文件。
MANIFEST. MF文件包含了apk文件中所有文件的名称和此文件的SHA1摘要值。
CERT. SF和CERT. RSA文件是使用jarsigner工具(jdk里面)生成的签名文件和签名块文件。
在CERT. SF文件中默认会包含整个MANIFEST. MF文件的SHA1摘要值;
CERT. RSA文件存放对CERT. SF文件的签名，同时还包含从keystore文件中生成的证书或者证
书链。
在应用程序管理器安装apk文件的过程中会检查证书，对比每个文件的摘要值是否匹配，防
止应用程序被篡改。

![](C:\1myblog\myblog\source\_posts\安卓逆向之APK打包流程/2.png)

res文件夹:资源库目录一 般存放xml布局文件和图标
AndroidMainifest. xml :配置清单(二进制格式)
C lasses. dex:
安卓系统上的可执行文件，也是我们]逆向的主要的文件，源码都被编译在里面，如有
多个是因为每个dex里最多存放65535个方法，而项目的方法数超出了这个数量，所以被分成多个保存
resources. arsc:资源索引文件，包含语言包，汉化的话一般是反编译这个文件
assets :资源目录，一般存放图片
lib:是动态库目录一-般存放so文件

assets不经过appt编译

 resources,arsc经过aapt编译过的资源文件

![image-20220921102945709](C:\Users\曾淇\AppData\Roaming\Typora\typora-user-images\image-20220921102945709.png)



## 2.res文件夹
res文件夹存放资源文件。由此也可以看到APK文件对资源文件的保护性较差，任何人都
可以通过解压缩的方式从APK文件中提取相关的资源文件，如图片、音频等。

## 3.AndroidManifest. xml
Androi dManifest. xml是每个应用程序都必须定义和包含的全局配置文件，它描述了应用
程序的名称、版本、权限、引用的库文件等信息。但是不同于系统开发时在项目文件夹中所看到
的Androi dManifest. xm1文件，在APK文件中， Androi dMani fest. xm1是经过压缩的，如果直接打开
将看到乱码。可以通过相关工具将其解压。



## 4.classes. dex
Dalvik虛拟机运行dex文件，而不是传统的class文件，dx.工具将编译后的class文件转换
成一个dex文件在程序运行时，Dalvik虛拟机从dex中装载读取指令和数据。classes. dex是Java源代码编译后生成的Dalvik虚拟机字节码文件，类似于Java虛拟机使用的.class类文件.由于Android使用的Dalvik虛拟机与标准的Java虚拟机(JVM) 是不兼容的，因此dex文件与class文件相比，不论是文件结构还是操作码都不一样。如果编写的源程序有多个Java类，都会将其编译到同一个classes. dex文件中，也就是说不论程序是简单还是复杂，每个APK文件只有一个classes. dex文件(错误)，而传统的Java应用程序，往往包含多个class文件，这样就不可避免地增加了冗余信息。讲class文件做出改善:将class文件整合到一起，可以减小类文件的尺寸、I0操作， 提高类的查找速度,Android模拟器中提供了一个dex文件的反编译工具一dexdump, 可以对其进行反编译。

将class文件做出改善:将class文件整 合到起，可以减小类文件的尺寸、I0操作，提
高类的查找速度。Android模拟器中提供了-个dex 文件的反编译工具一dexdump, 可以对其进行
反编译。

![](C:\1myblog\myblog\source\_posts\安卓逆向之APK打包流程/3.png)



## 5.resources. arsc
resources. arsc是编译后的二进制资源文件，内容包含了开发程序时项目文件夹中res子文件夹下main. xm1、strings. xm1等文件的信息。

## 6.lib、assets 

除了以上文件夹和文件，在有些APK文件中，还有可能出现1ib文件夹和assets文件夹。这
两个文件夹中分别存放应用软件需要调用的库文件和其他资源配置文件。
assets.里面的文件不是被aapt编译过的文件; 

1. aapt是什么?有何用?
   aapt即Android Asset Packaging Tool.
   作用:可以查看，创建，更新ZIP格 式的文档附件(zip, jar, apk). 也可将资源文件编
   译成二进制文件

2) appt在哪里?
aapt不需要另外安装喔，有安装了adt的，可以直接在adt. . / sdk/build 
tools/android xx/下，找到aapt
3)获取APP的应用包名称和入口页面名称
aapt dump badging 后面+ . apk的路径，显示关于这个应用包的信息



# APK文件目录
## lib目录

存放应用依赖的native库文件，-般用C/C++ 编写。lib 目录根据CPU型号的不同，
可能包含四种文件夹: ARM, ARM- v7a, MIPS, X86, 分别对应ARM 架构，ARM v7a架构，MIPS架构
和X86 架构

在lib目录里则分别是armeabi、armeabi v7a、 mips和x86- 共4个目录。 每个目录中的. so库名字都
是一样的，实际上功能也是相同的，它们只是为了适配不同架构的CPU而存在。实际上，市面上的手
机几乎全都是ARM架构的，所以大多数情况下我们只需要有armeabi和armeabi- v7a两种类型的库就足
够了。

# APK打包流程

1.通过aapt工具打包资源文件，比如res/ Androi dMani fest. xm1/Android基础类库等，
生成R. java和resources.ap_文件。
2.处理AIDL文件，生成对应的. java文件。
3.通过javac命令编译Java文件，生成对应的. class文件。
4.通过dx工具把. class文件转化成Davik VM支持的. dex文件。
(. java=>. class=>. dex)
5.通过apkbuilder工具将相关文件打包生成未签名的. apk文件。
6.通过jarsigner.工具将未签名. apk文件进行签名，生成一个签
名apk文件。
7.通过zipalign工具将签名后的.apk文件进行对齐处理。

先是把代码编译成Dex,然后是和资源文件整合成一个APK,再者加签名，最后出来之前还要用zipalign工具优化一下。

![](C:\1myblog\myblog\source\_posts\安卓逆向之APK打包流程/4.png)

## 步骤详解:

### 1.打包资源文件，生成R. java文件

![image-20220921153159771](C:\Users\曾淇\AppData\Roaming\Typora\typora-user-images\image-20220921153159771.png)
aapt来打包res资源文件，生成R. java、resources. arsc和res文件。

R. java文件

R. java是我们在编写代码的时候会用到的，里面有静态内部类，资源等。

resources. arsc文件
resources. arsc这个文件记录了所有的应用程序资源目录的信息，包括每-一个资源名称、
类型、值、ID以及所配置的维度信息。是一个资源索引表，在给定资源ID和设备配
置信息的情况下能快速找到资源。



### 2.处理aidl文件，生成相应的Java文件
aidl (Android Interface Definition Language, Android接 口描述语
言)，位于android-sdk/platform tools目录下。aid1工具解析接口定义文件然
后生成相应的Java代码接口供程序调用。如果项F没用到aidl则跳过这一步。

### 3.编译项目源代码，生成class文件

Java Compiler阶段。项目中所有的Java代码，包括R. java和. aidl文件，
都会被Java编译器( javac)编译成. class文件，生成的class文件位于工程中的
bin/classes日录下。
通过Java Compi ler编译R. java、Java接口文件、Java源文件，生
成. class文件。如果有配置混淆的话，会编译成混淆的class文件，以防源代码被
偷看。

### 4.转换所有的class文件，生成classes. dex文件

dex阶段。通过dx工具，将. class文件和第三方库中的. class文件处理生
成classes. dex文件。该工具位于android-sdk/platform- tools目录下。dx工具的主要工作是将Java字节码转成成Dalvik字节码、压缩常量池、消除冗余信息等。注意: Android系 统的Dalvik虛拟机的可执行文件为DEX格式，程序运行所需的class. dex就是在这步生成的

### 5.打包生成APK文件
apkbuilder阶段。通过apkbuilder 工具，将aapt生 成的resources. arsc和
res文件、assets文件 和classes. dex-起打 包生成apk.打包的工具apkbui lder位
于android sdk/tools目录下。
将classes. dex、resources. arsc、res文件夹(res/ raw资源被原装不动地
打包进APK之外，其它的资源都会被编译或者处理)、0ther Resources (assets文
件夹)、AndroidMani fest. xm1打包成apk文件。

res/ raw和assets的相同点:
1.都是存放与资源有关的文件;
2.两个文件夹都可以新建文件夹以及文件目录，既可以都有新的目录结构; 
res/raw和assets的不同点:

1. res/raw中的文件会被映射到R.java文件中，访问的时候直接使用资源ID即
   R. id. filename; assets文件夹 下的文件不会被映射到R. java中，访问的时候需要
   AssetManager类。

2. res文件夹下面的资源会被编译，而aserts文件夹 下面的资源不会被编译;

### 6.对APK文件进行签名
Jarsigher阶段。通过Jarsigner工具，对上面的apk进行debug或release
签名。release mode下 使用
aipal ign进行align,即对签名后的apk进行对齐处
理。

### 7.对签名后的APK文件进行对齐处理

通过zipalign工具，将签名后的apk进行对齐处理。工具位于android-sdk/tools目录下。对齐的主要过程是将APK包中所有的资源文件距离文件起始偏移为4字节整数倍，这样通过内存映射访问apk文件时的速度会更快。对齐的作用就是减少运行时内存的使用。Zipal ign是一个android平台上整理APK文件的工具，它对apk中 未压缩的数据进行4字节对齐，对齐后就可以使用mmap函数读取文件，可以像读取内存一样对普通文件进行操作。如果没有4字节对齐，就必须显式的读取，这样比较缓慢并且会耗费额外的内存。在Android SDK中包含一个名为zipalign”的工具，它能够对打包后的app进行优化。其位于SDK的build- tools目录下

# APK按照流程

## 01 安装方式
四种安装方式: 
系统程序安装、通过Android市场安装、手机自带安装、使用ADB安装
adb介绍:
ADB: ADB的全称为Android Debug Bridge, 就是安卓调试桥接，它是Android系统提供的一套
工具，通过它，我们可以在电脑上建立一一个连接到手机的通道，然后可以在电脑上向手机发送
一些指令，完成一些我们需要做的工作

## 02 安装过程 

涉及文件夹:
首先复制APK安装包到/data/app目录下，解压并扫描安装包，把dex 文件(Dalvik字节码)
保存到/data/dalvik cache目录，并在/data/data目录下创建对应的应用数据目录。
安装后文件所在目录:

/data/app
用户程序安装的目录，安装时把apk文件复制到此目录
/data/dalvik-cache将apk中 的dex文件安装到dalvik-cache目录下
/data/data
存放应用程序的数据
/system/app
系统自带的应用程序，获得adb root权限才能删除

## 03 卸载过程

删除安装过程中在上述三个目录下创建的文件及目录。
/data/app将 安装的用户程序删除
/data/ dalvik cache将dalvik cache目录下中的dex文件删除
/data/data将应用程序的数据删除
使用MT管理器，查看相关目录，文件已删除



## 04 adb常用命令
adbdevices:显示当前连接的设备
adb install: 安装包路径
adb shell dumpsys
activity top获取当前活动界面信息
adb shell dumpsys package
[packagename]获取包信息
adb shell dumpsys geminfo [pname/pid] 获取内存数据
adb shell dumpsys dbinfo [packagename] 获取数据库文件
adb shell pm list packages列出当前所有已安装的程序包名
adb shell pm path 
[packagename]获取指定程序所在路径



# java虚拟机
java,虚拟机有自己完善的硬体架构，如处理器、堆栈、寄存器等，还具有
相应的指令系统。Java虚拟机屏蔽了与具体操作系统平台相关的信息，使得Java程
序只需生成在Java虚拟机上运行的目标代码(字节码)，就可以在多种平台上不加
修改地运行。
Java虚拟机(JVM) -种用于计算机设备的规范，可用不同的方式(软件
或硬件)加以实现。编译虚拟机的指令集与编译微处理器的指令集非常类似。Java
虚拟机包括一套字节码指令集、一组寄存器、一个栈、一个垃圾回收堆和一个存储
方法域。

Java虚拟机(JVM)是可运行Java代码的假想计算机。只要根据JVM规格描
述将解释器移植到特定的计算机上，就能保证经过编译的任何Java代码能够在该系
统上运行。
Java虚拟机是个想象中的机器， 在实际的计算机上通过软件模拟来实现。
Java虚拟机有自己想象中的硬件，如处理器、堆栈、寄存器等，还具有相应的指令
系统。 

特点:
Java语言的一个非常重要的特点就是与平台的无关性，而使用Java虚拟机是实现
这一特点的关键。一般的高级语言如果要在不同的平台上运行，至少需要编译成不同的
●目标代码。而引入ava语言虚拟机后，Java语言在不同平台上运行时不需要重新编译。
Java语言使用模式Java虚拟机屏蔽了与具体平台相关的信息，使得Java语言编译程序只需
生成在Java虚拟机上运行的目标代码(字节码)，就可以在多种平台上不加修改地运行。
Java虚拟机在执行字节码时，把字节码解释成具体平台上的机器指令执行。

![image-20220921155817279](C:\Users\曾淇\AppData\Roaming\Typora\typora-user-images\image-20220921155817279.png)

# dalvik虚 拟机(jit机制)

Dalvik虛拟机是Androi d程序的虚拟机，是Androi d中Java程序的运行
基础。其指令集基于寄存器架构，执行其特有的文件格式一
dex字 节码来完成对象生命周期管理、堆栈管理、线程管理、安全异常管理、垃圾回收等重
要功能。它的核心内容是实现库(libdvm. so)，大体由C语言实现。依赖于Linux内核的一部分 功能
线程机制、内存管理机制，能高效使用内存，并在低速CPU上表现出的高性能。每一个Androi d应用在 
3、常量池已被修改为只使用32位的索引，以简化解释器



比较JVM虚拟机:补充
1. Da1vik虚拟机 与Java虛拟机的最显著区别是它们分别具有不同的类文件格式以及指
令集。Dalvik虚拟机使用的是dex (Dalvik Executable) 格式的类文件，而Java虚拟机使用的
是class格式的类文件。一个dex文件可以包含若干个类，而一个class文件只包括一个类。由于
一个dex文件可以包含若干个类，因此它就可以将各个类中重复的字符串和其它常数只保存一次, ，
从而节省了空间，这样就适合在内存和处理器速度有限的手机系统中使用。一般来说，包含有
相同类的未压缩dex文件稍小于一个已经压缩的jar文件。
18378
2. Dalvik虚 拟机使用的指令是基于寄存器的，而Java虚拟机使用的指令集是基于堆栈的。事实
上，基于寄存器和基于堆栈的指令集之争，就如精简指令集(RISC) 和复杂指令集(CISC) 之
争，谁优谁劣，至今是没有定论的。
3.将多个类文件收集到同一个dex文件中，以便节省空间;

简单概述: Android 5. 0以下
dalvik虛拟机运行的是dalvik字节码，所有的dalvik字节码由
java字节码转换而来，并被打包到一个DEX可执行文件中，
●dalvik虚拟机通过解释DEX文件执行这此字节码
dalvik虚拟机基于寄存器架构，数握的包问通过寄存器间直接传递，这样的
访问方式比基于栈方式要快很多。
dalvik可执行文件体积更小

# 04 art虚拟机(aot机制)

art虚拟机介绍
Android
4, 4提供了一种与Dalvik截然不同的运行环境ART (Android runt ime)
支持，ART源于google收购的F1exycore的公司。ART的机制与Dalvik 不同。在Dalvik
下，应用每次运行的时候，字节码都需要通过即时编译器( just in time
JIT)转换
为机器码，这会拖慢应用的运行效率，而在ART环境中，应用在第次安装的时候， 字
节码就会预先编译成机器码，
使其成为真正的本地应用。这个过程叫做预编译(AOT, Ahead Of- Time)。这样的话，应用的启动(首次)和执行都会变得更加快速。

Dalvik虚拟机性能优化
1、DEX代码安装时或第一次动态 加载时odex化处理。
●
2、Android2. 3版本提供了JIT机制提升性能;

![image-20220921160239574](C:\Users\曾淇\AppData\Roaming\Typora\typora-user-images\image-20220921160239574.png)

![image-20220921160224581](C:\Users\曾淇\AppData\Roaming\Typora\typora-user-images\image-20220921160224581.png) 

![image-20220921160215697](C:\Users\曾淇\AppData\Roaming\Typora\typora-user-images\image-20220921160215697.png)

![image-20220921160207175](C:\Users\曾淇\AppData\Roaming\Typora\typora-user-images\image-20220921160207175.png)

![image-20220921160231875](C:\Users\曾淇\AppData\Roaming\Typora\typora-user-images\image-20220921160231875.png)

# 篡改app图标名称

![](C:\1myblog\myblog\source\_posts\安卓逆向之APK打包流程/5.png)
