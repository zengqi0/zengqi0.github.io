---
title: BurpSuite学习
date: 2023-09-06 15:34:11
tags:
---



# Burpsuite安装：

下载地址：
 链接：https://pan.baidu.com/s/1ppfoZGVwEW7DIyCVZdK3VQ?pwd=uqxo
 提取码：uqxo
 解压zip包：

![](BurpSuite学习\1.png)

双击打开"注册机.vbs"

点机run安装会弹出如图窗口，如果未弹出，直接点击"StartBurpSuite"

将license内容负责黏贴到弹窗，下一步；

选择Manual activation（手动激活）；

将弹出的小框中的第二栏的内容复制到图（一）的“Activation Request”中，则会在其“Activation Response”生成一串条文，最后将这串条文复制粘贴到小框的第三栏即可。

![](BurpSuite学习\2.png)

# Burpsuite使用

## 代理设置

![image-20230906154442833](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230906154442833.png)

![image-20230906155431525](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230906155431525.png)

![image-20230906155626305](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230906155626305.png)

![image-20230906155742033](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230906155742033.png)

## Proxy

![image-20230906160225375](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230906160225375.png)

设置代理

![image-20230906160235692](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230906160235692.png)

对客户端请求做拦截

![image-20230906160256962](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230906160256962.png)

是否拦截服务端返回

![image-20230906160437241](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230906160437241.png)

修改返回值

```
sda
```

