---
title: Mysql数据库完整学习
date: 2023-07-17 16:03:05
tags:
---



## SQL通用语法
1.SQL语句可以单行或多行书写，以分号结尾。

SQL语句可以使用空格/缩进来增强语句的可读性。
3.MySQL数据库的SQL语句不区分大小写，关键字建议使用大写。
4.注释:
-单行注释:--注释内容或#注释内容(MySQL特有)
.多行注释:{*注释内容*

![image-20230718155253869](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230718155253869.png)



![image-20230718155552190](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230718155552190.png)

![image-20230718155914679](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230718155914679.png)

![image-20230718155935518](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230718155935518.png)



![image-20230718160005186](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230718160005186.png)

![image-20230718160103489](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230718160103489.png)



![image-20230718160418171](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230718160418171.png)



![image-20230718160433721](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230718160433721.png)



![image-20230718160601021](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230718160601021.png)



![image-20230718160646827](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230718160646827.png)

![image-20230718161024930](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230718161024930.png)

![image-20230718161234277](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230718161234277.png)



![image-20230718161317619](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230718161317619.png)

![image-20230718161549963](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230718161549963.png)



![image-20230718161652049](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230718161652049.png)



![image-20230718162020392](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230718162020392.png)

![image-20230718162240368](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230718162240368.png)



![image-20230718162355863](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230718162355863.png)



![image-20230718162504888](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230718162504888.png)

## 约束

外键约束

外键是用来让两张表的数据之间建立连接，从而保证数据的一致性和完整性

比如说 员工表的外键 关联的是部门当中的主键    员工属于部门嘛

部门表是父表 员工表是子表

### 语法

```
ALTER TABLE 表名 ADD CONSTRAINT外键名称FOREIGN KEY(外键字段名）REFERENCES主表(主表列名);
```

![image-20230718103312974](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230718103312974.png)

![image-20230718162844006](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230718162844006.png)

![image-20230718162919024](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230718162919024.png)

![image-20230718163020772](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20230718163020772.png)
