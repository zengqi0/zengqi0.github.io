---
layout: htb学院使用FFUF 攻击 WEB 应用程序
title: 应用程序
date: 2023-06-28 16:14:55
tags:
categories:
- HackTheBox
---



# htb学院使用FFUF 攻击 WEB 应用程序

## 介绍

有许多工具和方法可用于目录和参数模糊测试/暴力破解。在本模块中，我们将主要关注用于 Web 模糊测试的[ffuf](https://github.com/ffuf/ffuf)工具，因为它是可用于 Web 模糊测试的最常用和最可靠的工具之一。

将讨论以下主题：

- 目录模糊测试
- 文件和扩展的模糊测试
- 识别隐藏的虚拟主机
- 对 PHP 参数进行模糊测试
- 参数值的模糊测试

诸如此类的工具为`ffuf`我们提供了一种便捷的自动化方式来模糊测试 Web 应用程序的各个组件或网页。这意味着，例如，如果网络服务器上存在具有我们列表中的名称的页面，我们将使用一个列表来向网络服务器发送请求。如果我们得到一个响应代码 200，那么我们就知道这个页面存在于网络服务器上，我们可以手动查看它。

## Web Fuzzing

我们将从学习使用ffuf来模糊网站目录的基础知识开始。我们运行下面问题中的练习，并访问它提供给我们的URL，我们看到以下网站:

![](htb学院使用FFUF 攻击 WEB 应用程序\4.png)

该网站没有任何其他链接，也没有提供任何可以引导我们进入更多页面的信息。所以，看起来我们唯一的选择是“模糊”网站。

## Fuzzing

术语Fuzz指的是一种测试技术，它将各种类型的用户输入发送到某个界面，以研究它将如何反应。如果我们对SQL注入漏洞进行模糊分析，我们将发送随机的特殊字符并观察服务器将如何反应。

如果我们对缓冲区溢出进行模糊检测，我们将发送长字符串并增加它们的长度，以查看二进制文件是否会中断以及何时会中断。

我们通常使用预先定义的词汇表来进行每种类型的web模糊测试，看看web服务器是否会接受它们。这样做是因为web服务器通常不提供所有可用链接和域名的目录(除非配置得很糟糕)，所以我们必须检查各种链接，看看哪些链接返回页面。

例如，如果我们访问https://www.hackthebox.eu/doesnotexist，我们会得到一个HTTP代码404 Page Not Found，并看到下面的页面:

![](htb学院使用FFUF 攻击 WEB 应用程序\1.png)



然而，如果我们访问一个存在的页面，比如/login，我们会得到登录页面，并得到一个HTTP代码200 Found，并看到下面的页面:

![](htb学院使用FFUF 攻击 WEB 应用程序\2.png)

这是网页和目录模糊化背后的基本思想。不过，我们不能手动完成，因为这将花费很长时间。这就是为什么我们有工具可以自动、高效、快速地做到这一点。

这样的工具每秒发送数百个请求，研究响应HTTP代码，并确定页面是否存在。因此，我们可以快速确定存在哪些页面，然后手动检查它们以查看其内容。

## Wordlists

为了确定哪些页面存在，我们应该有一个包含web目录和页面常用单词的单词列表，这与密码字典攻击非常相似，我们将在本模块稍后讨论。虽然这不会显示特定网站下的所有页面，因为有些页面是随机命名的或使用唯一的名称，但一般来说，这将返回大多数页面，在一些网站上成功率高达90%。我们不需要手动创建这些单词列表来重新发明轮子，因为我们已经在网上搜索并确定每种类型的模糊最常用的单词。

一些最常用的单词列表可以在GitHub SecLists https://github.com/danielmiessler/SecLists存储库下找到，该存储库将单词列表分类为各种类型的模糊，甚至包括常用的密码，稍后将用于密码暴力强制。

在我们的PwnBox中，我们可以在/opt/useful/SecLists下找到整个SecLists回购。我们将用于页面和目录模糊的特定单词列表是另一个常用的单词列表，称为directory-list-2.3，它有各种形式和大小。我们可以在下面找到我们将要使用的:

```shell-session
vnswer77@htb[/htb]$ locate directory-list-2.3-small.txt

/opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt
```

提示:看一下这个单词列表，我们会注意到它在开头包含版权注释，这可以被认为是单词列表的一部分，并使结果混乱。我们可以使用下面的命令，用-ic标志去掉这些行。

## Directory Fuzzing

现在我们理解了Web Fuzzing的概念，知道了我们的单词列表，我们应该准备好开始使用ffuf来查找网站目录。

### Ffuf

Ffuf已预安装在PwnBox实例上。如果你想在自己的机器上使用它，你可以使用“apt install ffuf -y”，或者从GitHub Repo下载并使用它。作为这个工具的新用户，我们将通过发出ffuf -h命令来了解如何使用这些工具:

```shell-session
vnswer77@htb[/htb]$ ffuf -h

HTTP OPTIONS:
  -H               Header `"Name: Value"`, separated by colon. Multiple -H flags are accepted.
  -X               HTTP method to use (default: GET)
  -b               Cookie data `"NAME1=VALUE1; NAME2=VALUE2"` for copy as curl functionality.
  -d               POST data
  -recursion       Scan recursively. Only FUZZ keyword is supported, and URL (-u) has to end in it. (default: false)
  -recursion-depth Maximum recursion depth. (default: 0)
  -u               Target URL
...SNIP...

MATCHER OPTIONS:
  -mc              Match HTTP status codes, or "all" for everything. (default: 200,204,301,302,307,401,403)
  -ms              Match HTTP response size
...SNIP...

FILTER OPTIONS:
  -fc              Filter HTTP status codes from response. Comma separated list of codes and ranges
  -fs              Filter HTTP response size. Comma separated list of sizes and ranges
...SNIP...

INPUT OPTIONS:
...SNIP...
  -w               Wordlist file path and (optional) keyword separated by colon. eg. '/path/to/wordlist:KEYWORD'

OUTPUT OPTIONS:
  -o               Write output to file
...SNIP...

EXAMPLE USAGE:
  Fuzz file paths from wordlist.txt, match all responses but filter out those with content-size 42.
  Colored, verbose output.
    ffuf -w wordlist.txt -u https://example.org/FUZZ -mc all -fs 42 -c -v
...SNIP...
```

正如我们所看到的，帮助输出非常大，所以我们只保留了在这个模块中可能与我们相关的选项。

从上面的例子中我们可以看到，主要的两个选项是-w表示单词列表，-u表示URL。我们可以将关键字分配给单词列表，以便在我们想要模糊的地方引用它。

例如，我们可以选择我们的单词列表，并通过在其后添加:FUZZ来为其分配关键字FUZZ:

```shell-session
vnswer77@htb[/htb]$ ffuf -w /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ
```

接下来，因为我们想要对web目录进行fuzzing，我们可以将FUZZ关键字放在目录将在我们的URL中的位置，使用:

```shell-session
vnswer77@htb[/htb]$ ffuf -w <SNIP> -u http://SERVER_IP:PORT/FUZZ
```

现在，让我们开始下面问题中的目标，并对其运行最后的命令:

```shell-session
vnswer77@htb[/htb]$ ffuf -w /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ


        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.1.0-git
________________________________________________

 :: Method           : GET
 :: URL              : http://SERVER_IP:PORT/FUZZ
 :: Wordlist         : FUZZ: /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
________________________________________________

<SNIP>
blog                    [Status: 301, Size: 326, Words: 20, Lines: 10]
:: Progress: [87651/87651] :: Job [1/1] :: 9739 req/sec :: Duration: [0:00:09] :: Errors: 0 ::

```



![](htb学院使用FFUF 攻击 WEB 应用程序/5.png)

自己的机子上 wordlist下载下来

我们看到ffuf在不到10秒的时间内测试了近90k个url。这个速度可能取决于你的网速和ping(如果你在你的机器上使用ffuf)，但它仍然是非常快的。

如果我们很着急，我们甚至可以通过将线程数增加到200来让它运行得更快，例如，使用-t 200，但不建议这样做，特别是在远程站点上使用时，因为它可能会破坏它，并导致拒绝服务，或者在严重的情况下导致您的互联网连接中断。我们确实得到了一些匹配，我们可以访问其中一个来验证它的存在:

![](htb学院使用FFUF 攻击 WEB 应用程序\3.png)

我们得到一个空页面，表明目录没有专用页面，但也表明我们没有访问它，因为我们没有得到HTTP代码404 not Found或403 access Denied。在下一节中，我们将查找这个目录下的页面，看看它是否真的为空，或者是否有隐藏的文件和页面。

### 练习

除了上面我们找到的目录外，还有一个目录可以找到。它是什么？

```
 ffuf -w /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ
```

直接运行，我们看到他正在跑

## Page Fuzzing(页面模糊测试)

通过使用单词表和关键字，我们现在了解了ffuf的基本用法。接下来，我们将学习如何定位页面。

注意:对于本节的示例，我们也可以从上一节生成相同的目标。

### Extension Fuzzing

​	在上一节中，我们发现我们可以访问/blog，但是目录返回一个空页面，并且我们不能手动定位任何链接或页面。因此，我们将再次利用web模糊来查看目录是否包含任何隐藏页面。然而，在我们开始之前，我们必须找出网站使用的页面类型，像.html， .aspx， .php，或其他东西。

​	一种常见的识别方法是通过HTTP响应标头找到服务器类型并猜测扩展名。例如，如果服务器是apache，那么它可能是.php，或者如果它是IIS，那么它可能是.asp或。aspx，等等。不过，这种方法不是很实用。因此，我们将再次使用ffuf来模糊扩展，类似于我们如何模糊目录。我们不是将FUZZ关键字放在目录名所在的位置，而是将它放在扩展名为.FUZZ的位置，并使用一个常用扩展名的单词列表。

我们可以在SecLists中使用下面的wordlist作为扩展:

```shell-session
vnswer77@htb[/htb]$ ffuf -w /opt/useful/SecLists/Discovery/Web-Content/web-extensions.txt:FUZZ <SNIP>
```

在开始模糊之前，必须指定扩展名将位于哪个文件的末尾!我们总是可以使用两个单词列表，每个单词都有一个唯一的关键字，然后执行FUZZ_1.FUZZ_2到fuzz。然而，有一个文件，我们总是可以在大多数网站找到，这是index.*，所以我们将使用它作为我们的文件和fuzz扩展。

注意:我们选择的单词列表已经包含了一个点(.)，所以我们不必在模糊处理中在“index”后面添加点。

现在，我们可以重新运行我们的命令，小心地将FUZZ关键字放在index之后的扩展名位置:

```shell-session
vnswer77@htb[/htb]$ ffuf -w /opt/useful/SecLists/Discovery/Web-Content/web-extensions.txt:FUZZ -u http://SERVER_IP:PORT/blog/indexFUZZ


        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.1.0-git
________________________________________________

 :: Method           : GET
 :: URL              : http://SERVER_IP:PORT/blog/indexFUZZ
 :: Wordlist         : FUZZ: /opt/useful/SecLists/Discovery/Web-Content/web-extensions.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 5
 :: Matcher          : Response status: 200,204,301,302,307,401,403
________________________________________________

.php                    [Status: 200, Size: 0, Words: 1, Lines: 1]
.phps                   [Status: 403, Size: 283, Words: 20, Lines: 10]
:: Progress: [39/39] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: 0 ::
```

我们确实得到了几次点击，但只有.php给出了代码200的响应。太棒了!我们现在知道这个网站运行在PHP上，开始模糊PHP文件。

### Page Fuzzing

我们现在将使用与ffuf相同的关键字概念，使用.php作为扩展名，将FUZZ关键字放在文件名应该放在的位置，并使用与用于fuzzing目录相同的单词列表:

```shell-session
vnswer77@htb[/htb]$ ffuf -w /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/blog/FUZZ.php


        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.1.0-git
________________________________________________

 :: Method           : GET
 :: URL              : http://SERVER_IP:PORT/blog/FUZZ.php
 :: Wordlist         : FUZZ: /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
________________________________________________

index                   [Status: 200, Size: 0, Words: 1, Lines: 1]
REDACTED                [Status: 200, Size: 465, Words: 42, Lines: 15]
:: Progress: [87651/87651] :: Job [1/1] :: 5843 req/sec :: Duration: [0:00:15] :: Errors: 0 ::
```

我们得到了一些点击;它们的HTTP代码都是200，这意味着我们可以访问它们。Index.php的大小为0，这表明它是一个空页面，而另一个则不是，这意味着它有内容。我们可以访问这些页面来验证这一点:

![](htb学院使用FFUF 攻击 WEB 应用程序\6.png)

### 练习

尝试使用您在本节学到的知识来模糊'/blog'目录并找到所有页面。其中一个应该包含一个标志。flag是什么?

## Recursive Fuzzing

到目前为止，我们已经fuzzing了目录，然后在这些目录下，然后fuzzing了文件。但是，如果我们有几十个目录，每个目录都有自己的子目录和文件，这将需要很长时间才能完成。为了实现自动化，我们将利用所谓的Recursive Fuzzing

### Recursive Flags

当我们递归扫描时，它会自动在任何新识别的目录下开始另一次扫描，直到它Fuzzing了主网站及其所有子目录。有些网站可能有一个很大的子目录树，

像/login/user/content/uploads/…等等，这将扩大扫描树，可能需要很长时间来扫描它们。这就是为什么总是建议为我们的递归扫描指定一个深度，这样它就不会扫描比这个深度更深的目录。一旦我们fuzzing了第一个目录，我们就可以选择最有趣的目录并运行另一个扫描，以更好地指导扫描。

在ffuf中，我们可以使用-recursion标志启用递归扫描，并且可以使用-recursion-depth标志指定深度。

如果我们指定-recursive-depth 1，它将只模糊主目录及其直接子目录。如果识别了任何子目录(如/login/user)，则不会为页面模糊它们。在ffuf中使用递归时，可以使用-e .php指定扩展名

注意:我们仍然可以使用' .php '作为我们的页面扩展名，因为这些扩展名通常是站点范围的。

最后，我们还将添加标记-v来输出完整的url。否则，可能很难判断哪个.php文件位于哪个目录下。

```shell-session
vnswer77@htb[/htb]$ ffuf -w /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ -recursion -recursion-depth 1 -e .php -v


        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.1.0-git
________________________________________________

 :: Method           : GET
 :: URL              : http://SERVER_IP:PORT/FUZZ
 :: Wordlist         : FUZZ: /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt
 :: Extensions       : .php 
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
________________________________________________

[Status: 200, Size: 986, Words: 423, Lines: 56] | URL | http://SERVER_IP:PORT/
    * FUZZ: 

[INFO] Adding a new job to the queue: http://SERVER_IP:PORT/forum/FUZZ
[Status: 200, Size: 986, Words: 423, Lines: 56] | URL | http://SERVER_IP:PORT/index.php
    * FUZZ: index.php

[Status: 301, Size: 326, Words: 20, Lines: 10] | URL | http://SERVER_IP:PORT/blog | --> | http://SERVER_IP:PORT/blog/
    * FUZZ: blog

<...SNIP...>
[Status: 200, Size: 0, Words: 1, Lines: 1] | URL | http://SERVER_IP:PORT/blog/index.php
    * FUZZ: index.php

[Status: 200, Size: 0, Words: 1, Lines: 1] | URL | http://SERVER_IP:PORT/blog/
    * FUZZ: 

<...SNIP...>
```

正如我们所看到的，这次扫描花费的时间要长得多，发送的请求数量几乎是以前的六倍，单词列表的大小增加了一倍(一次使用.php，一次不使用)。尽管如此，我们还是得到了大量的结果，包括我们之前确定的所有结果，所有这些结果都使用了一行命令。

### 练习

尝试重复到目前为止学习的内容，以查找更多的文件/目录。他们中应该有人给你一个flag。flag的内容是什么?

## DNS Records

当我们访问/blog下面的页面时，我们收到一条消息，说管理面板移到了academy.htb。如果我们在浏览器中访问该网站，我们将无法连接到www.academy.htb上的服务器:

![](htb学院使用FFUF 攻击 WEB 应用程序\7.png)

这是因为我们所做的练习不是任何人都可以访问的公共网站，而是HTB内的本地网站。浏览器只知道如何访问IP，如果我们提供给他们一个URL，他们会尝试通过查看地/etc/hosts文件和公共DNS域名系统将URL映射到IP。

如果URL不在其中，它将不知道如何连接到它。如果我们直接访问IP，浏览器会直接访问该IP并知道如何连接它。但在这种情况下，我们让它去学院.Htb，所以它查看本地的/etc/hosts文件，并没有发现任何提及它。

它询问有关它的公共DNS(如谷歌的DNS 8.8.8.8)，但没有找到它的任何提及，因为它不是一个公共网站，最终无法连接。所以，为了与academy.Htb，我们必须将它添加到我们的/etc/hosts文件。我们可以用下面的命令来实现:138.68.155.81:31978

```shell-session
vnswer77@htb[/htb]$ sudo sh -c 'echo "SERVER_IP  academy.htb" >> /etc/hosts'
```

现在我们可以访问网站(不要忘记在URL中添加PORT)，看到我们可以到达网站:

![](htb学院使用FFUF 攻击 WEB 应用程序\8.png)

然而，当我们直接访问IP时，我们得到的是相同的网站。HTB是我们到目前为止一直在测试的领域。我们可以通过访问/blog/index.php来验证这一点，并查看是否可以访问该页面。

当我们在这个IP上运行测试时，我们没有发现任何关于管理或面板的信息，即使我们对目标进行了完整的递归扫描。

因此，在这种情况下，我们开始寻找'*.academy下的子域名。Htb '，看看我们是否发现了什么，这是我们将在下一节尝试的。

## Sub-domain Fuzzing

在本节中，我们将学习如何使用ffuf为任何网站识别子域名(即*.website.com)。

### Sub-domains

​	子域名是另一个域名下的任何网站。例如，https://photos.google.com是google.com的photos子域。

​	在这种情况下，我们只是检查不同的网站，看看它们是否存在，检查它们是否有一个公共DNS记录，将我们重定向到一个工作的服务器IP。我们扫描一下，看看能不能找到线索。在开始扫描之前，我们需要两样东西:

- A `wordlist`
- A `target`

​	幸运的是，在SecLists repo中，有一个专门的子域词表部分，由通常用于子域的常用词组成。我们可以在/opt/useful/SecLists/Discovery/DNS/中找到它。

在本例中，我们将使用一个较短的单词列表，即subdomains- top100 -5000.txt。

如果我们想要扩展扫描，我们可以选择一个更大的列表。至于我们的目标，我们将使用hackthebox。Eu是我们的目标，对它进行扫描。让我们使用ffuf并将FUZZ关键字放在子域的位置，看看我们是否得到任何命中:

```shell-session
vnswer77@htb[/htb]$ ffuf -w /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u https://FUZZ.hackthebox.eu/


        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.1.0-git
________________________________________________

 :: Method           : GET
 :: URL              : https://FUZZ.hackthebox.eu
 :: Wordlist         : FUZZ: /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200
________________________________________________

forum                   [Status: 200, Size: 72197, Words: 3664, Lines: 675]
www                     [Status: 200, Size: 21268, Words: 1720, Lines: 1]
help                    [Status: 200, Size: 25830, Words: 5049, Lines: 364]
<...SNIP...>
```

我们看到我们确实得到了一些反馈。我们可以通过访问其中一个来验证这些是实际的子域:

![](htb学院使用FFUF 攻击 WEB 应用程序\9.png)

我们看到这些确实是工作的子域。现在，我们可以试着在academy.htb里做同样的事情。看看能不能得到回复

```shell-session
vnswer77@htb[/htb]$ ffuf -w /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://FUZZ.academy.htb/


        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.1.0-git
________________________________________________

 :: Method           : GET
 :: URL              : https://FUZZ.academy.htb/
 :: Wordlist         : FUZZ: /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
________________________________________________

:: Progress: [4997/4997] :: Job [1/1] :: 131 req/sec :: Duration: [0:00:38] :: Errors: 4997 ::
```

我们看到我们没有得到任何回复。这是否意味着academy.htb下没有子域? ——no。

​	这意味着在学院下没有公共子域名.htb，因为它没有公共DNS记录，如前所述。尽管我们增加了学院。在我们的/etc/hosts文件中，我们只添加了主域，所以当ffuf在寻找其他子域时，它不会在/etc/hosts中找到它们，并会询问公共DNS，显然公共DNS不会有它们。

### 练习

HackTheBox has an online Swag Shop. Try running a sub-domain fuzzing test on 'hackthebox.eu' to find it. What is the full domain of it?

## Vhost Fuzzing

​	正如我们在前一节中看到的，我们能够使用公共DNS记录模糊公共子域。然而，当涉及到模糊子域名没有公共DNS记录或子域名下的网站不是公共的，我们不能使用相同的方法。在本节中，我们将学习如何使用Vhost Fuzzing来做到这一点。

### Vhosts vsSub-domains

​	VHost和子域之间的关键区别是，VHost基本上是服务于同一台服务器上的“子域”，具有相同的IP，这样一个IP可以服务于两个或多个不同的网站。

​	VHosts可能有也可能没有公共DNS记录。

​	在许多情况下，许多网站实际上都有不公开的子域名，不会在公共DNS记录中发布它们，因此，如果我们在浏览器中访问它们，我们将无法连接，因为公共DNS不会知道它们的IP。

​	同样，如果我们使用子域模糊，我们将只能识别公共子域，而不能识别任何非公共子域。这就是我们在已经拥有的IP上利用VHosts Fuzzing的地方。我们将在同一IP上运行扫描并测试扫描结果，然后我们将能够识别公共和非公共子域以及VHosts。

### Vhosts Fuzzing

​	为了扫描VHosts，不需要手动将整个单词列表添加到/etc/hosts，我们将模糊HTTP报头，特别是Host:报头。为此，我们可以使用-H标志来指定一个头，并在其中使用FUZZ关键字，如下所示:

```shell-session
vnswer77@htb[/htb]$ ffuf -w /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://academy.htb:PORT/ -H 'Host: FUZZ.academy.htb'


        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.1.0-git
________________________________________________

 :: Method           : GET
 :: URL              : http://academy.htb:PORT/
 :: Wordlist         : FUZZ: /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt
 :: Header           : Host: FUZZ
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
________________________________________________

mail2                   [Status: 200, Size: 900, Words: 423, Lines: 56]
dns2                    [Status: 200, Size: 900, Words: 423, Lines: 56]
ns3                     [Status: 200, Size: 900, Words: 423, Lines: 56]
dns1                    [Status: 200, Size: 900, Words: 423, Lines: 56]
lists                   [Status: 200, Size: 900, Words: 423, Lines: 56]
webmail                 [Status: 200, Size: 900, Words: 423, Lines: 56]
static                  [Status: 200, Size: 900, Words: 423, Lines: 56]
web                     [Status: 200, Size: 900, Words: 423, Lines: 56]
www1                    [Status: 200, Size: 900, Words: 423, Lines: 56]
<...SNIP...>
```

​	我们看到单词列表中的所有单词都返回200 OK!这是意料之中的，因为我们只是在访问http://academy.htb:PORT/时更改标头。

​	所以，我们知道我们总是会得到200。但是，如果VHost确实存在，并且我们在报头中发送了一个正确的VHost，

​	我们应该得到一个不同的响应大小，在这种情况下，我们将从VHost获取页面，这可能会显示一个不同的页面。

## Filtering Results

到目前为止，我们还没有对ffuf使用任何过滤，默认情况下，结果由它们的HTTP代码自动过滤，它过滤掉代码404 not FOUND，并保留其余部分。然而，正如我们在前面的ffuf运行中看到的，使用代码200可以得到许多响应。因此，在这种情况下，我们将不得不基于另一个因素过滤结果，这将在本节中学习。

### Filtering

Ffuf提供了匹配或过滤特定HTTP代码、响应大小或字数的选项。我们可以看到ffuf -h:

```shell-session
vnswer77@htb[/htb]$ ffuf -h
...SNIP...
MATCHER OPTIONS:
  -mc              Match HTTP status codes, or "all" for everything. (default: 200,204,301,302,307,401,403)
  -ml              Match amount of lines in response
  -mr              Match regexp
  -ms              Match HTTP response size
  -mw              Match amount of words in response

FILTER OPTIONS:
  -fc              Filter HTTP status codes from response. Comma separated list of codes and ranges
  -fl              Filter by amount of lines in response. Comma separated list of line counts and ranges
  -fr              Filter regexp
  -fs              Filter HTTP response size. Comma separated list of sizes and ranges
  -fw              Filter by amount of words in response. Comma separated list of word counts and ranges
<...SNIP...>
```

​	在这种情况下，我们不能使用匹配，因为我们不知道来自其他vhost的响应大小是多少。我们知道错误结果的响应大小，从上面的测试可以看出，它是900，我们可以使用-fs 900过滤掉它。

​	现在，让我们重复前面的命令，添加上面的标志，看看我们得到什么:

```shell-session
vnswer77@htb[/htb]$ ffuf -w /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://academy.htb:PORT/ -H 'Host: FUZZ.academy.htb' -fs 900


       /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.1.0-git
________________________________________________

 :: Method           : GET
 :: URL              : http://academy.htb:PORT/
 :: Wordlist         : FUZZ: /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt
 :: Header           : Host: FUZZ.academy.htb
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
 :: Filter           : Response size: 900
________________________________________________

<...SNIP...>
admin                   [Status: 200, Size: 0, Words: 1, Lines: 1]
:: Progress: [4997/4997] :: Job [1/1] :: 1249 req/sec :: Duration: [0:00:04] :: Errors: 0 ::
```

我们可以通过访问页面来验证，看看我们是否可以连接到它:

![](htb学院使用FFUF 攻击 WEB 应用程序\10.png)

注意1:别忘了加上“admin.academy.Htb "到"/etc/hosts"。

注2:如果您的练习已重新启动，请确保在访问网站时仍然使用正确的端口。

我们看到我们可以进入页面，但我们得到一个空页面，不像我们在academy.htb得到的。

，因此确认这确实是一个不同的VHost。我们甚至可以访问https://admin.academy.htb:PORT/blog/index.php，我们将看到我们将得到404 PAGE NOT FOUND，确认我们现在确实在不同的VHost上。

试着在admin.academy.htb上进行递归扫描。看看你能识别哪些页面。

### 练习

试着运行一个VHost模糊扫描 academy.htb，看看你得到的其他vhost。你还买了哪些vhost 

## Parameter Fuzzing - GET

如果我们对admin.academy.htb进行ffuf 扫描，我们应该找到http://admin.academy.htb:PORT/admin/admin.php。如果我们尝试访问此页面，我们会看到以下内容:

![](htb学院使用FFUF 攻击 WEB 应用程序\11.png)

​	这表明必须有一些东西来标识用户，以验证他们是否有权读取标志。我们没有登录，也没有任何可以在后端验证的cookie。因此，也许有一个键，我们可以传递给页面来读取标志。这样的键通常使用GET或POST HTTP请求作为参数传递。本节将讨论如何模糊这些参数，直到我们确定一个可以被页面接受的参数。

提示:模糊参数可能会暴露可公开访问的未发布参数。这样的参数往往较少测试和不太安全，所以测试这些参数对于我们在其他模块中讨论的web漏洞是很重要的

### GET Request Fuzzing

​	类似于我们如何模糊网站的各个部分，我们将使用ffuf来枚举参数。让我们首先从GET请求的模糊开始，它通常是在URL之后传递的，带有一个?符号，例如:http://admin.academy.htb:PORT/admin/admin.php?param1=key。因此，我们所要做的就是将上面示例中的param1替换为FUZZ并重新运行扫描。然而，在我们开始之前，我们必须选择一个合适的单词表。

同样，SecLists在/opt/useful/SecLists/Discovery/Web-Content/burp-parameter-names.txt中有。有了这个，我们就可以进行扫描了。

同样，我们将得到许多结果，因此我们将过滤掉我们得到的默认响应大小。

```shell-session
vnswer77@htb[/htb]$ ffuf -w /opt/useful/SecLists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php?FUZZ=key -fs xxx


        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.1.0-git
________________________________________________

 :: Method           : GET
 :: URL              : http://admin.academy.htb:PORT/admin/admin.php?FUZZ=key
 :: Wordlist         : FUZZ: /opt/useful/SecLists/Discovery/Web-Content/burp-parameter-names.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
 :: Filter           : Response size: xxx
________________________________________________

<...SNIP...>                    [Status: xxx, Size: xxx, Words: xxx, Lines: xxx]
```

我们确实收到了回复。让我们尝试访问页面并添加这个GET参数，看看我们现在是否可以读取标志:

![](htb学院使用FFUF 攻击 WEB 应用程序\12.png)

正如我们所看到的，我们得到的唯一的hit已经被弃用，似乎不再使用。

## Parameter Fuzzing - POST

POST请求和GET请求之间的主要区别是POST请求不随URL传递，不能简单地附加在?的象征。POST请求在HTTP请求中的数据字段中传递。

查看Web请求模块以了解更多关于HTTP请求的信息。为了用ffuf模糊数据字段，我们可以使用-d标志，正如我们之前在ffuf -h的输出中看到的那样。我们还必须添加-X POST来发送POST请求。



提示:在PHP中，"POST" data "content-type"只能接受"application/x-www-form-urlencoded"。因此，我们可以在"ffuf"中使用"-H 'Content-Type: application/x-www-form-urlencoded'"来设置它。

所以，让我们重复前面所做的，但是将FUZZ关键字放在-d标志之后:

```shell-session
vnswer77@htb[/htb]$ ffuf -w /opt/useful/SecLists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'FUZZ=key' -H 'Content-Type: application/x-www-form-urlencoded' -fs xxx


        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.1.0-git
________________________________________________

 :: Method           : POST
 :: URL              : http://admin.academy.htb:PORT/admin/admin.php
 :: Wordlist         : FUZZ: /opt/useful/SecLists/Discovery/Web-Content/burp-parameter-names.txt
 :: Header           : Content-Type: application/x-www-form-urlencoded
 :: Data             : FUZZ=key
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
 :: Filter           : Response size: xxx
________________________________________________

id                      [Status: xxx, Size: xxx, Words: xxx, Lines: xxx]
<...SNIP...>
```

​	正如我们这次看到的，我们得到了一些结果，与模糊GET和另一个参数(id)时得到的结果相同。让我们看看如果发送带有id参数的POST请求会得到什么。我们可以用curl来实现，如下所示:

```shell-session
vnswer77@htb[/htb]$ curl http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'id=key' -H 'Content-Type: application/x-www-form-urlencoded'

<div class='center'><p>Invalid id!</p></div>
<...SNIP...>
```

正如我们所看到的，现在消息显示无效id!

## Value Fuzzing

在模糊处理一个工作参数之后，我们现在必须模糊处理将返回我们需要的标志内容的正确值。本节将讨论参数值的模糊处理，一旦我们开发了单词列表，它应该与参数的模糊处理相当相似。

### Custom Wordlist

当涉及到模糊参数值时，我们可能并不总是能找到适合我们的预先制作的单词列表，因为每个参数都需要特定类型的值。对于一些参数，比如用户名，我们可以为潜在的用户名找到一个预先制作的单词列表，或者我们可以根据可能正在使用网站的用户创建自己的单词列表。

对于这种情况，我们可以在seclists目录下查找各种单词列表，并尝试找到一个可能包含与目标参数匹配的值的单词列表。

在其他情况下，比如自定义参数，我们可能不得不开发自己的单词列表。在本例中，我们可以猜测id参数可以接受某种类型的数字输入。这些id可以是自定义格式，也可以是顺序的，比如从1-1000或1-1000000等等。我们将从包含从1到1000的所有数字的单词列表开始。有许多方法可以创建这个单词列表，从手动在文件中输入id，或者使用Bash或Python编写脚本。

最简单的方法是在Bash中使用以下命令，将1-1000中的所有数字写入一个文件:

```shell-session
vnswer77@htb[/htb]$ for i in $(seq 1 1000); do echo $i >> ids.txt; done
```

一旦我们运行命令，我们应该准备好我们的单词列表:

```shell-session
vnswer77@htb[/htb]$ cat ids.txt

1
2
3
4
5
6
<...SNIP...>
```

现在我们可以转移到模糊的值。

### Value Fuzzing

我们的命令应该非常类似于我们用来fuzz参数的POST命令，但是我们的fuzz关键字应该放在参数值所在的位置，我们将使用我们刚刚创建的ids.txt单词列表，如下所示:

```shell-session
vnswer77@htb[/htb]$ ffuf -w ids.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'id=FUZZ' -H 'Content-Type: application/x-www-form-urlencoded' -fs xxx


        /'___\  /'___\           /'___\
       /\ \__/ /\ \__/  __  __  /\ \__/
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/
         \ \_\   \ \_\  \ \____/  \ \_\
          \/_/    \/_/   \/___/    \/_/

       v1.0.2
________________________________________________

 :: Method           : POST
 :: URL              : http://admin.academy.htb:30794/admin/admin.php
 :: Header           : Content-Type: application/x-www-form-urlencoded
 :: Data             : id=FUZZ
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
 :: Filter           : Response size: xxx
________________________________________________

<...SNIP...>                      [Status: xxx, Size: xxx, Words: xxx, Lines: xxx]
```

我们看到我们马上就被击中。我们最终可以使用curl来发送另一个POST请求,就像我们在上一节中所做的那样,使用我们刚刚发现的id值,并收集该标志。

### 练习

尝试创建“ids.txt”单词列表，用模糊扫描识别接受的值，然后在带有“curl”的“POST”请求中使用它来收集标志。旗帜的内容是什么?
