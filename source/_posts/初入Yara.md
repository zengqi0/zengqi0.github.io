---
title: Yara入门
date: 2022-07-06 16:14:17
categories: 
- 技术研究
toc: true
---





# 前言

首先，YARA规则是VT的开发人员发布的，用于同类样本的批量检索和查杀。

通过Yara引擎和我们编写的Yara规则，可以快速对一批样本进行扫描和识别，从而找到我们希望得到的样本。

官方的github库地址：https://github.com/VirusTotal/yara/releases

官方文档说明：https://yara.readthedocs.io/en/v3.7.0/index.html

# 环境及安装

操作系统：win10

安装地址：https://github.com/VirusTotal/yara/releases/tag/v4.2.0-rc1

直接在这个地址下就可以

# 工具使用

然后启动cmd 直接输入yara —help 即可查看yara的参数

在cmd目录下打开
 命令： yara my_rule my_file

第一个参数是你的yara规则文件，第二个是检测文件，可以是目录

# yara规则编写

现在，以官方提供的yara规则示例我们来看看yara可以做什么。官方给出的示例yara如下：

```
rule silent_banker : banker
{
    meta:
        description = "This is just an example"
        thread_level = 3
        in_the_wild = true
    strings:
        $a = {6A 40 68 00 30 00 00 6A 14 8D 91}
        $b = {8D 4D B0 2B C1 83 C0 27 99 6A 4E 59 F7 F9}
        $c = "UVODFRYSIHLNWPEJXQZAKCBGMT"
    condition:
        $a or $b or $c
}
```



首先，第一行的rule silent_banker : banker是声明该规则用于检出banker类型的样本。

meta 后面的是一些描述信息，比如规则说明、作者信息等。

strings 定义了$a $b $c 两个十六进制字符串（十六进制字符串用大括号括起来）和一个文本字符串（文本字符串直接用双引号括起来）

最后condition 规定了匹配的条件，这里写的是or，表明样本中只要匹配到了$a $b $c 三个字符串中的任意一个，那么样本就会被识别为banker

我们新建一个demo1.yara文件，然后将这部分内容复制进去。

yara的的每一条规则都由一系列[字符串](https://so.csdn.net/so/search?q=字符串&spm=1001.2101.3001.7020)和一个bool型表达式构成，并且都具有关键字。

规则以字母、数字、下划线组成，字符串首字符不能是数字，单条描述不超过128字符

yara的基本规则定义如下：

```
rule ExampleRule
{
    strings:
		$my_text_string = "text here"	
        $my_hex_string = {E2 34 A1 C8 23 FB}
    condition:
    	$my_text_string or $my_hex_string
}
```

其中字符串区域非必需，条件区域必需。

可以存在只有条件区域的规则，如：

```
rule Dummy
{
	condition:
		true
}
```

## strings部分

Strings部分可以使用3种字符串：文本字符串、十六进制字符串、正则表达式。

文本字符串：用来定义文件或内存中可读的部分；

十六进制字符串：用来定义十六进制字节内容；

正则表达式：可用来在文本字符串和十六进制字符串种

```
文本字符串采用""引用；
十六进制采用{}引用，每个十六进制字符间用空格间隔，并且只能包含十六进制字符；
正则表达式直接在文本字符串和十六进制字符串种使用即可使用即可，也可单独使用//引用。
```

### 转义

字符串的转义直接参科与其他语言的转义一致。

### 大小写

yara默认对大小写敏感，可以使用nocase关键字来使yara对大小写不敏感。

```
rule NocaseTextExample
{
    strings:
        $text_string ="foobar" nocase	//foobar可以替换为FOOBAR、Foobar等
    condition:
        $text_string
}
```

### 字符集表示

如果在目标文件中一个字符由两个字节表示，即宽字符形式，则可以使用wide关键词来表示文本字符串。如果既想表示宽字符，也想表示ASCII码，则可以再使用ascii关键词。

例如：

```
rule WideAsciiTextExample
{
    strings:
        $text_string ="foobar" nocase wide ascii
    condition:
        $text_string
}
```

### 匹配单个词组文本字符串

如果想匹配单个词组文本字符串，可以使用fullword关键词。

```
rule FullwordTextExample
{
    strings:
        $text_string = "foobar"fullword
    condition:
        $text_string
}
/*
abcfoobar
foobar-abc
foobar.abc
在上述3种情况中，只会匹配到第三种情况
*/

```

## condition部分

condition部分通常由一个bool型表达式构成。若该表达式成立则会被检测到，反之则不能。

该表达式由bool操作符、关系操作符、算数操作符、按位操作符和yara关键字组成。

condition部分涉及到的关键字及其使用方法。

#用来表示出现的次数

```
rule CountExample
{
    strings:
        $a = "dummy1"
        $b = "dummy2"

    condition:
        #a == 6 and #b > 10
}
/*
将$替换成#即可
#a即代表$a出现的次数
*/

```

### in

in：用来确定相对于文件或进程的偏移地址的范围。

```
rule InExample
{
    strings:
        $a = "dummy1"
        $b = "dummy2"

    condition:
        $a in (0..100) and $b in (100..filesize)
}
/*
$a出现在偏移地址0~0x100并且$b出现在0x100之后
*/

```

### filesize

filesize代表文件大小，若目标不为文件将无视

```
rule FileSizeExample
{
    condition:
       filesize > 200KB
}

```

### at

表示字符串在文件中的偏移位置或虚拟地址

```
rule CountExample
{
    strings:
        $a = "text1"
        $b = "text2"
        $c = "text3"
    condition:
        $a at 100 and $b at 0x004C0000 and $cat 300
}
```

### entrypoint

表示文件入口点，适用于PE文件和ELF文件，也适用于正在运行的程序

该变量已被弃用，在yara3.0后被彻底弃用

```
rule FileSizeExample
{
    string:
        $a = {E8 00 00 00}
    condition:
       $a at entrypoint
}

```

### int8/16/32、uint8/16/32

在相应地址访问相应数据类型，如果要读取大端整数，请使用以结尾的相应函数be

```
int8(<offset or virtualaddress>)

int16(<offset or virtualaddress>)

int32(<offset or virtualaddress>)

uint8(<offset or virtualaddress>)

uint16(<offset or virtualaddress>)

uint32(<offset or virtual address>)

```

例如：

```
rule IsPE

{

    condition:

        // MZ signature at offset 0

        uint16(0) == 0x5A4D and

        // PE signature at offset stored in MZheader at 0x3C

        uint32(uint32(0x3C)) == 0×00004550

}

```

### of 集合

```
rule Example
{    
    strings:        
        $a = "dummy1"        
        $b = "dummy2"
        $c = "dummy3"
    condtion：
        2 of ($a,$b,$c)  //当$a$b$c中有两个字符串存在于文件时，表示匹配
}
```

### ??

匹配一字节未知数，即通配符
### ？

匹配半字节未知数，即通配符
### [X-Y]

字符长度在X到Y的范围中即可匹配

限制：X<=Y; 若X=Y，则[X]与[X-Y]等价;

从YARA 2.0开始，还可以使用[X-]或[-X]这种无界跳转

```
rule JumpExample
{
        strings:
           $hex_string = { F4 23 [4-6] 62 B4 }

        condition:
           $hex_string

}
/*
F4 23 01 02 03 04 62 B4
F4 23 00 00 00 00 00 62 B4
F4 23 15 82 A3 04 45 22 62 B4
以上3个字符串均可匹配
*/
```

### them/($*)

```
可以使用them/($*)来代指所有规则

rule OfExample4
{
    strings:
        $a = "dummy1"
        $b = "dummy2"
        $c = "dummy3"

    condition:
        1 of them // equivalent to 1 of ($*)

}
```



### @

用来表示字符串在文件或内存中出现的偏移或虚拟地址

```
@a[i]表示字符串a在文件或内存中第i次出现的偏移或虚拟地址

如果i大于实际出现次数，将返回NaN(not a number)

rule OfExample4
{
    strings:
        $a = "dummy1"
        $b = "dummy2"
        $c = "dummy3"

    condition:
        @a[1]=0x123456

}
```



### !

!用来表示字符串在文件或内存中出现的字符串长度

用法与@基本一致

```
!a=!a[1]

rule OfExample4
{
    strings:
        $a = "dummy1"
        $b = "dummy2"
        $c = "dummy3"

    condition:
        !a[1]=0x123456

}
```



### for xxx of xxx ：(xxx)

使用for n of ($a,$b,$c) : ($ at entrypoint)来表示在字符集中存在n个字符串符合条件

$用来代指字符集中的每一个字符串，其他如@、!、#等均可同样省略

```
rule OfExample4
{
    strings:
        $a1 = "dummy1"
        $a2 = "dummy2"
        $a3 = "dummy3"

    condition:
        for 2 of ($a*) : ($ at entrypoint)

}
```



### for xxx i in (xxx) ：(xxx)

```
功能用法与上一个基本一致

rule OfExample4
{
    strings:
        $a1 = "dummy1"
        $a2 = "dummy2"
        $a3 = "dummy3"

    condition:
        for 2 i in (1..#a) : ( @a[i] < 100 )	//遍历所有$a出现位置，偏移均要小于100

}
```

### 其他yara关键字

```
yara关键字具体如下：
global

global rule：全局规则，优先级最高
private

private rule：私有规则，进行匹配时不会输出任何匹配信息
TagRule

rule TagRule：规则标签，可以让你在YARA输出的时候只显示你感兴趣的规则，而过滤掉其它规则的输出信息

 你可以为规则添加多个标签

rule TagRule : dummy1 dummy 2 
{    
    strings:        
        $a = "dummy1"        
        $b = "dummy2"      
    condition:        
        $a and $b 
}
```

### 引用规则

在另一个规则中，直接引用即可
import

可以使用import导入其他模块，可以是第三方或官方模块，或自己写的模块
include

该部分与C语言类似，可以用来包含其他规则文件
注释

yara使用//进行单行注释，使用/**/进行多行注释。
mate

mate是yara中可有可无的一部分，用来放置有关该规则的其他信息
外部变量

可以使用在ext_var中添加一个外部变量，在编写规则时直接引用即可

# YarGen

YarGen是用于生成YARA规则的工具。 给定恶意软件文件，它可以生成YARA规则。  它通过识别在恶意软件文件中找到的字符串来生成YARA规则，同时还删除在非恶意文件中也出现的已知字符串。  YarGen包含一个庞大的字符串和操作码数据库，已知它们也会出现在非恶意文件中。 

 首先，在其Github页面的中下载最新版本的YarGen并解压缩存档。 源代码以zip文件或tarball的形式提供。  接下来，确保已安装所有依赖项。 您可以运行以下命令： 

https://github.com/Neo23x0/yarGen

```undefined
sudo pip install pefile cdsudo pip install scandir lxml naiveBayesClassifier
```

最后，使用**cd**进入YarGen目录并运行以下命令以下载内置数据库。 数据库将保存到**./dbs**子目录中。 

```sql
python yarGen.py —-update
```

## 运行YarGen

 YarGen有许多用于规则生成的选项。 要查看命令行参数，可以运行： 

```python
python yarGen.py —-help
```

 要将包含的数据库用于规则生成，只需运行以下命令： 

```undefined
python yarGen.py -m PATH_TO_MALWARE_DIRECTORY
```

该命令将扫描并在PATH_TO_MALWARE_DIRECTORY下创建恶意软件文件的规则。 将在当前目录中创建一个名为**yargen_rules.yar**的文件，其中包含生成的规则。 

可以使用“-u”参数更新曾经创建的数据库

```
yarGen.py -u --opcodes -i office -g /opt/packs/office365
```

先用cs生成一波码

![](C:\1myblog\myblog\source\_posts\初入Yara\1.png)

我这里分别生成了beacon、foreign、HTML、powershell、VBS、office宏等cs马。接下里先分析分析这些木马尝试提取CobaltStrike特征

# CobaltStrike样本查杀



# 总结

这篇先看着就当是白皮书了，多的就不说了，怎么提就需要多练了。

# 引用链接



https://blog.csdn.net/weixin_43781139/article/details/123375282

https://www.anquanke.com/post/id/211501

https://blog.csdn.net/abel_big_xu/article/details/125381650

https://blog.csdn.net/m0_37552052/article/details/78986097

https://www.nextron-systems.com/2015/02/16/write-simple-sound-yara-rules/ 
