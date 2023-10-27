---
title: codeql初入
date: 2021-03-29 14:14:01
categories:
 - 技术研究
---

# codeql

## codeql简介

codeql 是一个静态源码扫描工具，支持 c, python, java 等语言，用户可以使用 ql 语言编写自定义规则识别软件中的漏洞，也可以使用ql自带的规则进行扫描。

## codeql环境搭建

在windows下在vscode里面安装数据库来操作codeql

首先我们安装 

 CodeQL CLI  https://github.com/github/codeql-cli-binaries/releases

然后在codeqlcli的根目录下 执行

```bash
git clone --recursive https://github.com/github/vscode-codeql-starter/
```

这个时候我们就会出现两个目录，一个是codeql，一个是ql

然后在vscode 扩展里面下载一个codeql，路径就填写codeql.cmd文件的路径

之后在vscode终端并且cd 到codeql根目录下使用`codeql database create` 来创建一个用于查询的数据库 `--language=python`指定语言是python，如果要创建cpp这种编程语言，则需要在后面加上command，编译命令，可以用gcc或者make

目标database创建好后，目录结构为

- log/                  # 输出的日志信息
- db-cpp/               # 编译的数据库
- src.zip               # 编译所对应的目标源码
- codeql-database.yml   # 数据库相关配置

这样环境就搭建好了

## codeql 语法

我们可以在https://lgtm.com/query 这里不用安装环境就可以用codeql 可以看作云codeql，先入手一下，

```
import <language> /* 导入对应的语言包 */
/* 可能存在的 一些谓词 类的设置 */
from /* 声明变量等 */
where /* 设置逻辑表达式 */
select /* 打印结果 */
```

字符型

```
from string s
where s = "hello"
select s
```

其中，在from语句中，我们定义了一个字符串类型的变量s，然后，我们在where语句中，将字符串”hello”赋值给了变量s，最后，我们在select语句中返回变量s的值。如果在查询控制台运行上述代码的话，运行结果将为：

```
hello
```

**整型与浮点型**

简单来说，整型变量用于保存整数，如306；而浮点型变量则用于保存浮点数，也就是带小数位的数，如3.14。例如：

```
 from float x, int y
 where x = 3.6 and y = 3
 select x.pow(y)
```

### 类型

codeql中存在5种类型: `int` `date` `float` `boolean` `string`， 每个类型有对应的谓词(也可以先理解为函数)可以被调用， 如下例：

```sql
select "hello world".length() 
```

### [#](https://lingze.xyz/pages/1948eb/#where逻辑表达式) where逻辑表达式

可以使用`and` `or` 等连接各个表达式，使用exists定义局部变量，

有一种比较有趣的使用方式，这表示`t.getHairColor()`肯定和一个字符串匹配，

```sql
exists(string c| t.getHairColor() = c)
```

下面这是另一个示例， 表示肯定存在一个person的年龄比t大，但我们也不想知道这个大的是哪个，只是表示t不是最大年龄这个意思。

```sql
exists(Person p| p.getAge() > t.getAge()) 
```

### [#](https://lingze.xyz/pages/1948eb/#聚合) 聚合

聚合函数，`max` `count` `min` `sum` `avg`

下面的示例就是配合`exists`选取最大的年龄，

```sql
from Person t 
where t.getAge() = max(int i | exists(Person p | p.getAge() = i) | i)
select t
```

但是我们也可以使用`order by` 关键字

```sql
select max(Person p | | p order by p.getAge())
```

### [#](https://lingze.xyz/pages/1948eb/#谓词-predicate) 谓词 **Predicate**

谓词有点类似函数的意思，但是并不完全等同于函数，他也是一种描述，来指示某种状态，

无返回值的谓词其实有点像宏的意思，他会直接替换过来,

```sql
predicate isSouthern(Person p){
	p.getLocation() = "south"
}

from Person p 
where isSouthern(p)
select p 
```

有返回值的谓词通过`result`来表示返回的值，而且这里的`result`也可以理解为一个类型是该谓词返回类型的变量，也可以被函数调用等，如下示：

```sql
Person relativeOf(Person p){
	parentOf(result) = parentOf(p)
}
Person childOf(Person p){
  p = parentOf(result)
}
```

### [#](https://lingze.xyz/pages/1948eb/#判断语句) 判断语句

在ql语言中是不存在if for等语法的，循环一般通过递归实现，判断一般通过逻辑表达式实现：

```sql
string other() {
  this = "Left" and result = "Right" 
  or 
  this = "Right" and result = "Left" 
}
```

### [#](https://lingze.xyz/pages/1948eb/#定义类) 定义类

可以自己定义类型，定义类，这里的类其实是类似集合的概念，表示符合某种属性的集合

```sql
class SmallInt extends int {3
	SmallInt() {this in [1..10] }
	int square() {result = this * this}
}

class Southerner extends Person {
	Southerner () {
		this.getLocation() = "south"
	}
}
```

类中的谓词也可以重写，这里语法有点类似oop中的类中的方法的重写

```sql
class Child extends Person {
	Child () {
		this.getAge() < 10 
	}
  	override predicate isAllowedIn(string region){
      region = this.getLocation()
  }
}
```

### [#](https://lingze.xyz/pages/1948eb/#递归) 递归

这其实是谓词的一种特性，可以轻易的使用递归，

这里介绍一个最简单的递归类型, 在定义中调用了自己本身

```sql
Person ancestorOf(Person p){
  result = parentOf(p) or 
  result = parentOf(ancestorOf(p))
}
```

### [#](https://lingze.xyz/pages/1948eb/#传递闭包) 传递闭包

同一个操作被多次使用(这里的`ancestorOf()`操作)在ql中比较常见，这种操作称为传递闭包，

其中两个符号非常有用，`+` `*`

在递归的例子中展示的`parentOf()`来演示：

- `parentOf+(p)` 将会调用p一次到多次，等同于上面的`ancestorOf(p)`，
- `parentOf*(p)` 将会调用p 0次到多次，他会返回p和p的祖先，

### [#](https://lingze.xyz/pages/1948eb/#codeql查询) codeql查询

首先是在最开始导入对应的语言库，`import <language>`



























参考 

1. https://codeql.github.com/docs/codeql-overview/

2. https://kiprey.github.io/2020/12/CodeQL-setup/

3. https://paper.seebug.org/1078/#_1

4. https://zhuanlan.zhihu.com/p/92769710?utm_source=wechat_session

5. https://lingze.xyz/pages/1948eb/#%E7%8E%AF%E5%A2%83