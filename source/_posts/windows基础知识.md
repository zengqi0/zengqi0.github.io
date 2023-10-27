---
title: windows基础知识
date: 2023-04-25 15:08:28
cover: /img/windows.png
categories:
 - 渗透测试
 - HTB
---



# Windows简介

​	作为渗透测试人员，了解各种技术非常重要。透彻了解 Windows 和 Linux 操作系统有利于广泛的评估类型。我们在评估期间遇到的大多数系统，无论是本地还是云端，都将基于这两个操作系统。重要的是要了解如何攻击和防御这些操作系统，以及如何将它们分别用作执行进一步渗透测试活动的平台。

## Windows 操作系统

​	微软于 1985 年 11 月 20 日首次推出 Windows 操作系统。Windows 的第一个版本是 MS-DOS 的图形操作系统外壳。更高版本的 Windows 桌面引入了 Windows 文件管理器、程序管理器和打印管理器程序。

​	Windows 95 是 Windows 和 DOS 的第一个完全集成，并首次提供内置的 Internet 支持。此版本还推出了 Internet Explorer 网络浏览器。自初始版本以来，已经发布了十几个版本的 Windows，例如 Windows XP、Vista 和 8，直到当前版本：Windows 10。随着时间的推移，Microsoft 为每个 Windows 桌面版本提供了各种版本，以满足从休闲消费者到企业客户的每个人。

​	Windows Server 于 1993 年随 Windows NT 3.1 Advanced Server 一起发布。多年来，Windows NT 经历了多次更新，添加了 Internet 信息服务 (IIS)、各种网络协议、管理向导以促进管理任务等技术。随着 Windows 2000 的发布，Microsoft 推出了 Active Directory，最初旨在帮助系统管理员设置文件共享、数据加密、VPN 等。Windows Server 2000 还包括 Microsoft 管理控制台 (MMC) 和支持的动态磁盘卷。

​	Windows Server 2003 紧随其后，具有服务器角色、内置防火墙、卷影复制服务等。Windows Server 2008 包括故障转移群集、Hyper-V 虚拟化软件、Server Core、事件查看器以及对 Active Directory 的主要增强。多年来，Microsoft 发布了更多服务器版本，包括 Server 2012、Server 2016 和最近的 Server 2019。这个最新版本增加了对 Kubernetes、Linux 容器和更多高级安全功能的支持。

​	随着新版本 Windows 的推出，旧版本被弃用并且不再接收 Microsoft 更新（除非在某些情况下购买了长期支持合同）。Windows Server 2008 和 2012 的安全更新已于 2020 年 1 月 14 日终止。目前，仅支持 Server 2012 R2 及更高版本。然而，由于发现了严重的 SMBv1 漏洞（永恒之蓝），微软在过去几年中为早期版本的 Windows 发布了带外补丁。

​	许多版本的 Windows 现在被视为“旧版”，不再受支持。组织经常发现自己运行各种较旧的操作系统以支持关键应用程序或出于运营或预算方面的考虑。评估员需要了解版本之间的差异以及每个版本固有的各种错误配置和漏洞。

## Windows 版本

以下是主要 Windows 操作系统和相关版本号的列表：

| 操作系统名称                         | 版本号 |
| ------------------------------------ | ------ |
| Windows NT 4                         | 4.0    |
| Windows 2000                         | 5.0    |
| Windows XP                           | 5.1    |
| Windows Server 2003, 2003 R2         | 5.2    |
| Windows Vista, Server 2008           | 6.0    |
| Windows 7, Server 2008 R2            | 6.1    |
| Windows 8, Server 2012               | 6.2    |
| Windows 8.1, Server 2012 R2          | 6.3    |
| Windows 10, Server 2016, Server 2019 | 10.0   |

​	我们可以使用[Get-WmiObject ](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/get-wmiobject?view=powershell-5.1)[cmdlet](https://docs.microsoft.com/en-us/powershell/scripting/developer/cmdlet/cmdlet-overview?view=powershell-7)来查找有关操作系统的信息。此 cmdlet 可用于获取 WMI 类的实例或有关可用 WMI 类的信息。有多种方法可以找到我们系统的版本和内部版本号。我们可以使用该类轻松获取此信息`win32_OperatingSystem`，这表明我们在 Windows 10 主机上，内部版本号为 19041。

![](windows基础知识\1.png)

​	其他一些可以使用的有用类`Get-WmiObject`是`Win32_Process`获取进程列表、`Win32_Service`获取服务列表`Win32_Bios`以获取[基本输入/输出系统](https://en.wikipedia.org/wiki/BIOS)( `BIOS`) 信息。BIOS 是安装在计算机主板上的固件，用于控制计算机的基本功能，例如电源管理、输入/输出接口和系统配置。我们可以使用该`ComputerName`参数来获取有关远程计算机的信息。`Get-WmiObject`可用于启动和停止本地和远程计算机上的服务，等等。[可以在此处](https://ss64.com/ps/get-wmiobject.html)和[此处](https://adamtheautomator.com/get-wmiobject/)找到有关 cmdlet 的更多信息。

## 访问窗口

#### 本地访问概念

如果您现在正在阅读这些文字，则您可以在本地访问某种计算机。无论是智能手机、平板电脑、笔记本电脑、Raspberry Pi 还是台式机。本地访问是访问任何计算机（包括运行 Windows 的计算机）的最常见方式。`Input`很可能通过键盘、触控板和/或鼠标发生。`Output`来自显示屏。拥有员工日常工作的办公空间的组织围绕其员工在组织拥有的计算机上的专用工作空间中工作的想法制定安全策略和安全控制。越来越多的组织越来越多地支持非技术和技术员工的远程工作。对于从事 IT、软件开发和信息安全工作的技术专业人员来说，这并不是一个新的现实。在任何一天，技术专业人员都可以在本地和远程访问多台机器。有了这个，让我们讨论远程访问的概念。

#### 远程访问概念

远程访问是通过网络访问计算机。在远程访问另一台计算机之前，需要对计算机进行本地访问。有无数种远程访问方法。在本模块中，我们将主要使用远程访问方法连接到 Windows 操作系统并与之交互。网络和互联网技术的进步催生了完全依赖计算机系统远程访问和远程管理的整个行业。

考虑[MSP](https://www.techtarget.com/searchitchannel/definition/managed-service-provider)和[MSSP](https://www.gartner.com/en/information-technology/glossary/mssp-managed-security-service-provider)，这两个行业主要依赖于远程管理其客户的计算机系统。此功能使他们能够集中管理、标准化所使用的技术、自动执行大量任务、启用远程工作安排，并在出现问题或出现潜在安全威胁时允许快速响应。远程访问不仅限于 MSP 和 MSSP。拥有 IT、软件开发和/或安全团队的组织每天都使用远程访问方法来构建应用程序、管理服务器和管理员工工作站。一些最常见的远程访问技术包括但不限于：

- 虚拟专用网络 (VPN)
- 安全外壳 (SSH)
- 文件传输协议 (FTP)
- 虚拟网络计算 (VNC)
- Windows 远程管理（或 PowerShell 远程处理）(WinRM)
- 远程桌面协议 (RDP)

我们将主要关注在本模块中使用 RDP。

#### 远程桌面协议 (RDP)

​	RDP 使用客户端/服务器架构，其中客户端应用程序用于通过启用 RDP 访问的网络指定计算机的目标 IP 地址或主机名。启用 RDP 远程访问的目标计算机被视为服务器。重要的是要注意 RDP 默认情况下在逻辑端口上侦听`3389`。请记住，IP 地址用作网络上计算机的逻辑标识符，而逻辑端口是分配给应用程序的标识符。简单来说，我们可以将网络子网视为城镇中的街道（公司网络），该子网中分配给主机的 IP 地址（如该街道上的房屋）以及逻辑端口作为可用于进入房子。

一旦请求（封装在数据包中）通过其 IP 地址到达目标计算机，该请求将根据该请求中指定的端口（作为标头包含在数据包中）定向到计算机上托管的应用程序。[IP 寻址和协议封装在网络简介](https://academy.hackthebox.com/module/details/34)模块中有更详细的介绍。从网络的角度来看，在本模块中，我们只需要了解每台计算机都分配了一个 IP 地址以通过网络进行通信，目标计算机上托管的应用程序侦听特定的逻辑端口。

我们可以使用 RDP 从运行 Linux 或 Windows 的攻击主机连接到 Windows 目标。`Remote Desktop Connection`如果我们从 Windows 主机连接到 Windows 目标，我们可以使用名为( [mstsc.exe](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/mstsc) )的内置 RDP 客户端应用程序。查看下面的剪辑以了解基本用法：

https://academy.hackthebox.com/storage/modules/49/UsingRemoteDesktopConnection.gif

为此，必须已在目标 Windows 系统上[允许远程访问。](https://docs.microsoft.com/en-us/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access)默认情况下，Windows 操作系统不允许远程访问。HTB 学院团队已将我们的许多 Windows 目标配置为在通过 VPN 连接到学院实验室后允许 RDP 访问。

远程桌面连接还允许我们保存连接配置文件。这是 IT 管理员的普遍习惯，因为它可以更方便地连接到远程系统。

https://academy.hackthebox.com/storage/modules/49/SavingRDPConnections.gif

`.rdp`作为渗透测试者，我们可以从在参与时查找这些保存的远程桌面文件 () 中获益。

存在许多其他远程桌面客户端应用程序，其中一些已列在这篇名为“[远程桌面客户端”](https://docs.microsoft.com/en-us/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)的 Microsoft 文章中。我们不会涵盖本模块中的每个远程桌面客户端应用程序。

#### 使用 xfreerdp

[从基于 Linux 的攻击主机，我们可以使用名为xfreerdp](https://linux.die.net/man/1/xfreerdp)的工具远程访问 Windows 目标。您会注意到我们跨多个模块使用 xfreerdp，因为它易于使用、功能集、命令行实用程序和效率。查看下面的剪辑，了解 Pwnbox 的基本用法：

https://academy.hackthebox.com/storage/modules/49/ConnectingwithXfreerdp.gif

​	请记住，我们也可以在命令行中复制并粘贴 xfreerdp 命令，因此我们不需要手动输入选项。xfreerdp 有几个选项可供我们使用，例如驱动器重定向，以便能够将文件传输到目标主机/从目标主机传输文件，这些值得练习，我们将在 HTB Academy 的其他模块中介绍。

​	存在其他 RDP 客户端，例如[Remmina](https://remmina.org/)和[rdesktop](http://www.rdesktop.org/)，我们鼓励您与其他客户端进行试验，看看哪种最适合您。现在我们已经介绍了这些概念，让我们通过生成下面的目标并使用提供的凭据使用 RDP 连接到它来应用它们。

## 连接到 Windows 目标

使用以下命令通过远程桌面 (RDP) 连接：

 使用 xfreerdp

```shell-session
vnswer77@htb[/htb]$ xfreerdp /v:<targetIp> /u:htb-student /p:Password
```

## 操作系统结构

在 Windows 操作系统中，根目录为 <drive_letter>:\（通常为 C 盘）。根目录（也称为启动分区）是安装操作系统的地方。其他物理和虚拟驱动器分配有其他字母，例如 Data (E:)。boot分区的目录结构如下：

| Directory                  | Function                                                     |
| -------------------------- | ------------------------------------------------------------ |
| Perflogs                   | 可以保存Windows性能日志，但默认为空。                        |
| Program Files              | 在32位系统上，所有16位和32位程序都安装在这里。在64位系统上，这里只安装64位程序。 |
| Program Files (x86)        | 32位和16位程序安装在64位版本的Windows上。                    |
| ProgramData                | 这是一个隐藏文件夹，其中包含某些已安装程序运行所必需的数据。无论什么用户在运行程序，程序都可以访问这些数据。 |
| Users                      | 该文件夹包含登录系统的每个用户的用户配置文件，并包含“Public”和“Default”两个文件夹。 |
| Default                    | 这是所有创建的用户的默认用户配置文件模板。每当向系统添加新用户时，他们的配置文件都基于默认配置文件。 |
| Public                     | 此文件夹用于计算机用户共享文件，默认情况下所有用户都可以访问。默认情况下，此文件夹通过网络共享，但需要一个有效的网络帐户才能访问。 |
| AppData                    | 每个用户的应用程序数据和设置存储在一个隐藏的用户子文件夹中(即cliff.moore\AppData)。每个文件夹包含三个子文件夹。漫游文件夹包含与机器无关的数据，这些数据应该遵循用户的配置文件，例如自定义字典。本地文件夹是特定于计算机本身的，永远不会在网络上同步。LocalLow类似于Local文件夹，但它的数据完整性级别较低。因此，它可以被使用，例如，一个web浏览器设置为保护或安全模式。 |
| Windows                    | Windows操作系统所需的大部分文件都包含在这里。                |
| System, System32, SysWOW64 | 包含Windows和Windows API的核心功能所需的所有dll。每当程序要求加载DLL而不指定绝对路径时，操作系统都会搜索这些文件夹。 |
| WinSxS                     | Windows组件商店包含所有Windows组件、更新和服务包的副本。     |

## 使用命令行探索目录

[我们可以使用dir](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/dir)命令探索文件系统。

 

```cmd-session
C:\htb> dir c:\ /a
 Volume in drive C has no label.
 Volume Serial Number is F416-77BE

 Directory of c:\

08/16/2020  10:33 AM    <DIR>          $Recycle.Bin
06/25/2020  06:25 PM    <DIR>          $WinREAgent
07/02/2020  12:55 PM             1,024 AMTAG.BIN
06/25/2020  03:38 PM    <JUNCTION>     Documents and Settings [C:\Users]
08/13/2020  06:03 PM             8,192 DumpStack.log
08/17/2020  12:11 PM             8,192 DumpStack.log.tmp
08/27/2020  10:42 AM    37,752,373,248 hiberfil.sys
08/17/2020  12:11 PM    13,421,772,800 pagefile.sys
12/07/2019  05:14 AM    <DIR>          PerfLogs
08/24/2020  10:38 AM    <DIR>          Program Files
07/09/2020  06:08 PM    <DIR>          Program Files (x86)
08/24/2020  10:41 AM    <DIR>          ProgramData
06/25/2020  03:38 PM    <DIR>          Recovery
06/25/2020  03:57 PM             2,918 RHDSetup.log
08/17/2020  12:11 PM        16,777,216 swapfile.sys
08/26/2020  02:51 PM    <DIR>          System Volume Information
08/16/2020  10:33 AM    <DIR>          Users
08/17/2020  11:38 PM    <DIR>          Windows
               7 File(s) 51,190,943,590 bytes
              13 Dir(s)  261,310,697,472 bytes free
```

树实用程序对于以图形方式显示路径或磁盘的目录结构很有用

```cmd-session
C:\htb> tree "c:\Program Files (x86)\VMware"
Folder PATH listing
Volume serial number is F416-77BE
C:\PROGRAM FILES (X86)\VMWARE
├───VMware VIX
│   ├───doc
│   │   ├───errors
│   │   ├───features
│   │   ├───lang
│   │   │   └───c
│   │   │       └───functions
│   │   └───types
│   ├───samples
│   └───Workstation-15.0.0
│       ├───32bit
│       └───64bit
└───VMware Workstation
    ├───env
    ├───hostd
    │   ├───coreLocale
    │   │   └───en
    │   ├───docroot
    │   │   ├───client
    │   │   └───sdk
    │   ├───extensions
    │   │   └───hostdiag
    │   │       └───locale
    │   │           └───en
    │   └───vimLocale
    │       └───en
    ├───ico
    ├───messages
    │   ├───ja
    │   └───zh_CN
    ├───OVFTool
    │   ├───env
    │   │   └───en
    │   └───schemas
    │       ├───DMTF
    │       └───vmware
    ├───Resources
    ├───tools-upgraders
    └───x64
```

该`tree`命令可以为我们提供大量信息。以下命令可用于遍历 C 盘中的所有文件，一次一屏。可以修改此命令以针对任何目录运行。

```cmd-session
tree c:\ /f | more
```

# 文件系统

Windows 文件系统有 5 种类型：FAT12、FAT16、FAT32、NTFS 和 exFAT。FAT12 和 FAT16 不再用于现代 Windows 操作系统。在本次培训中，我们将涉及 FAT32 和 exFAT 文件系统，但我们的主要重点是 NTFS 文件系统。

FAT32（文件分配表）广泛用于许多类型的存储设备，例如 USB 记忆棒和 SD 卡，但也可用于格式化硬盘驱动器。名称中的“32”是指FAT32使用32位数据来标识存储设备上的数据簇。

**`Pros of FAT32:`**

- 设备兼容性——可用于电脑、数码相机、游戏机、智能手机、平板电脑等。
- 操作系统交叉兼容性 - 它适用于从 Windows 95 开始的所有 Windows 操作系统，并且还受 MacOS 和 Linux 支持。

**`Cons of FAT32:`**

- 只能用于小于 4GB 的文件。
- 没有内置数据保护或文件压缩功能。
- 必须使用第三方工具进行文件加密。

NTFS（新技术文件系统）是自 Windows NT 3.1 以来的默认 Windows 文件系统。除了弥补 FAT32 的不足外，NTFS 还因为改进了数据结构而更好地支持元数据和更好的性能。

**`Pros of NTFS:`**

- NTFS 是可靠的，可以在系统出现故障或断电时恢复文件系统的一致性。
- 通过允许我们对文件和文件夹设置精细权限来提供安全性。
- 支持非常大的分区。
- 内置日记功能，这意味着记录文件修改（添加、修改、删除）。

**`Cons of NTFS:`**

- 大多数移动设备本身不支持 NTFS。
- 电视和数码相机等较旧的媒体设备不支持 NTFS 存储设备。

## 权限

NTFS 文件系统有许多基本和高级权限。一些关键的权限类型是：

| 权限类型       | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| 完全控制       | 允许读取、写入、更改、删除文件/文件夹。                      |
| 调整           | 允许读取、写入和删除文件/文件夹。                            |
| 列出文件夹内容 | 允许查看和列出文件夹和子文件夹以及执行文件。文件夹仅继承此权限。 |
| 读取和执行     | 允许查看和列出文件和子文件夹以及执行文件。文件和文件夹继承此权限。 |
| 写             | 允许将文件添加到文件夹和子文件夹以及写入文件。               |
| 读             | 允许查看和列出文件夹和子文件夹以及查看文件的内容。           |
| 遍历文件夹     | 这允许或拒绝通过文件夹移动到其他文件或文件夹的能力。例如，在此示例中，用户可能无权列出目录内容或查看文档或 Web 应用程序目录中的文件 c:\users\bsmith\documents\webapps\backups\backup_02042020.zip 但应用了遍历文件夹权限，他们可以访问备份档案。 |

文件和文件夹继承其父文件夹的 NTFS 权限以方便管理，因此管理员无需为每个文件和文件夹显式设置权限，因为这会非常耗时。如果确实需要明确设置权限，管理员可以禁用必要文件和文件夹的权限继承，然后直接对每个文件和文件夹设置权限。

## 完整性控制访问控制列表 (icacls)

可以使用安全选项卡下的文件资源管理器 GUI 管理 Windows 中文件和文件夹的 NTFS 权限。除了 GUI 之外，我们还可以使用 icacls 实用程序从命令行在 Windows 中实现对 NTFS 文件权限的精细级别。

`icacls`我们可以通过在工作目录内运行或`icacls C:\Windows`针对当前不在的目录运行来列出特定目录的 NTFS 权限。

 

```cmd-session
C:\htb> icacls c:\windows
c:\windows NT SERVICE\TrustedInstaller:(F)
           NT SERVICE\TrustedInstaller:(CI)(IO)(F)
           NT AUTHORITY\SYSTEM:(M)
           NT AUTHORITY\SYSTEM:(OI)(CI)(IO)(F)
           BUILTIN\Administrators:(M)
           BUILTIN\Administrators:(OI)(CI)(IO)(F)
           BUILTIN\Users:(RX)
           BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
           CREATOR OWNER:(OI)(CI)(IO)(F)
           APPLICATION PACKAGE AUTHORITY\ALL APPLICATION PACKAGES:(RX)
           APPLICATION PACKAGE AUTHORITY\ALL APPLICATION PACKAGES:(OI)(CI)(IO)(GR,GE)
           APPLICATION PACKAGE AUTHORITY\ALL RESTRICTED APPLICATION PACKAGES:(RX)
           APPLICATION PACKAGE AUTHORITY\ALL RESTRICTED APPLICATION PACKAGES:(OI)(CI)(IO)(GR,GE)

Successfully processed 1 files; Failed processing 0 files
```

资源访问级别列在输出中每个用户之后。可能的继承设置是：

- `(CI)`: 容器继承
- `(OI)`: 对象继承
- `(IO)`: 仅继承
- `(NP)`: 不传播继承
- `(I)`: 从父容器继承的权限

在上面的例子中，该`NT AUTHORITY\SYSTEM`帐户具有对象继承、容器继承、仅继承和完全访问权限。这意味着该帐户可以完全控制该目录和子目录中的所有文件系统对象。

基本访问权限如下：

- `F` : 完全访问
- `D` : 删除权限
- `N` : 无法访问
- `M` : 修改权限
- `RX` : 读取和执行访问
- `R` : 只读权限
- `W`: 只写权限

我们可以通过命令行使用添加和删除权限`icacls`。`icacls`在这里，我们在本地管理员帐户的上下文中执行，显示用户没有任何写入权限的`C:\users`目录。`joe`

 

```cmd-session
C:\htb> icacls c:\Users
c:\Users NT AUTHORITY\SYSTEM:(OI)(CI)(F)
         BUILTIN\Administrators:(OI)(CI)(F)
         BUILTIN\Users:(RX)
         BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
         Everyone:(RX)
         Everyone:(OI)(CI)(IO)(GR,GE)

Successfully processed 1 files; Failed processing 0 files
```

​	使用该命令，`icacls c:\users /grant joe:f`我们可以授予 joe 用户对目录的完全控制权，但考虑到`(oi)`并`(ci)`没有包含在命令中，joe 用户将仅对该`c:\users`文件夹拥有权限，而对其中包含的用户子目录和文件没有权限。

 

```cmd-session
C:\htb> icacls c:\users /grant joe:f
processed file: c:\users
Successfully processed 1 files; Failed processing 0 files
```

 

```cmd-session
C:\htb> >icacls c:\users
c:\users WS01\joe:(F)
         NT AUTHORITY\SYSTEM:(OI)(CI)(F)
         BUILTIN\Administrators:(OI)(CI)(F)
         BUILTIN\Users:(RX)
         BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
         Everyone:(RX)
         Everyone:(OI)(CI)(IO)(GR,GE)

Successfully processed 1 files; Failed processing 0 files
```

可以使用命令撤销这些权限`icacls c:\users /remove joe`。

`icacls`非常强大，可以在域设置中使用，以授予某些用户或组对文件或文件夹的特定权限，明确拒绝访问，启用或禁用继承权限，以及更改目录/文件所有权。

# NTFS 与共享权限

------

微软拥有超过[70% 的股份](https://gs.statcounter.com/os-market-share/desktop/worldwide/#monthly-201804-202104)Windows 桌面操作系统的全球市场份额。这解释了为什么大多数恶意软件作者选择为 Windows 编写恶意软件，以及为什么许多人认为 Windows 不如其他操作系统安全。从商业角度来看，恶意软件作者将资源用于为 Windows 编写恶意软件是有意义的。这是一个高价值的目标。任何操作系统都不受恶意软件影响的想法是一种技术谬误。如果可以为操作系统编写软件，那么也可以为操作系统编写病毒。请记住，根据定义，病毒是恶意编写的软件，可以为任何操作系统编写。许多为 Windows 编写的恶意软件变体可以通过应用宽松权限的网络共享在网络上传播。还值得注意的是，时至今日，臭名昭著的`EternalBlue`漏洞仍然困扰着未打补丁的 Windows 系统`SMBv1`，并经常为勒索软件关闭组织铺平道路。

( `Server Message Block protocol`)`SMB`在 Windows 中用于连接文件和打印机等共享资源。它用于大型、中型和小型企业环境。请参阅下图以形象化此概念：

![](windows基础知识\3.png)

注意：每当您看到概念的可视化/图表时，请花点时间彻底理解它。一张图片可以胜过一千个字，但在阅读时很容易跳过。

NTFS 权限和共享权限通常被理解为相同。请注意，它们并不相同，但通常适用于相同的共享资源。让我们看一下可以设置的各个权限，以保护/授予对象对托管在运行 NTFS 文件系统的 Windows 操作系统上的网络共享的访问权限。

#### 共享权限

| 允许           | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| `Full Control` | 允许用户执行更改和读取权限以及更改 NTFS 文件和子文件夹的权限所赋予的所有操作 |
| `Change`       | 允许用户阅读、编辑、删除和添加文件和子文件夹                 |
| `Read`         | 允许用户查看文件和子文件夹内容                               |

#### NTFS 基本权限

| 允许                   | 描述                                                         |
| ---------------------- | ------------------------------------------------------------ |
| `Full Control`         | 允许用户添加、编辑、移动、删除文件和文件夹，以及更改适用于所有允许文件夹的 NTFS 权限 |
| `Modify`               | 允许或拒绝用户查看和修改文件和文件夹的权限。这包括添加或删除文件 |
| `Read & Execute`       | 允许或拒绝用户读取文件内容和执行程序的权限                   |
| `List folder contents` | 允许或拒绝用户查看文件和子文件夹列表的权限                   |
| `Read`                 | 允许或拒绝用户读取文件内容的权限                             |
| `Write`                | 允许或拒绝用户将更改写入文件并将新文件添加到文件夹的权限     |
| `Special Permissions`  | 多种高级权限选项                                             |

#### NTFS 特殊权限

| 允许                             | 描述                                                         |
| -------------------------------- | ------------------------------------------------------------ |
| `Full control`                   | 允许或拒绝用户添加、编辑、移动、删除文件和文件夹以及更改适用于所有允许文件夹的 NTFS 权限 |
| `Traverse folder / execute file` | 即使拒绝用户访问父文件夹级别的内容，也允许或拒绝用户访问目录结构中的子文件夹。也可能允许或拒绝用户执行程序的权限 |
| `List folder/read data`          | 允许或拒绝用户查看父文件夹中包含的文件和文件夹的权限。也可以允许用户打开和查看文件 |
| `Read attributes`                | 允许或拒绝用户查看文件或文件夹的基本属性。基本属性示例：系统、存档、只读和隐藏 |
| `Read extended attributes`       | 允许或拒绝用户查看文件或文件夹的扩展属性。属性因程序而异     |
| `Create files/write data`        | 允许或拒绝用户在文件夹中创建文件和更改文件的权限             |
| `Create folders/append data`     | 允许或拒绝用户在文件夹内创建子文件夹的权限。可以向文件添加数据，但无法覆盖已有内容 |
| `Write attributes`               | 允许或拒绝用户更改文件属性。此权限不授予创建文件或文件夹的权限 |
| `Write extended attributes`      | 允许或拒绝用户更改文件或文件夹的扩展属性。属性因程序而异     |
| `Delete subfolders and files`    | 允许或拒绝用户删除子文件夹和文件的权限。父文件夹不会被删除   |
| `Delete`                         | 允许或拒绝用户删除父文件夹、子文件夹和文件的权限。           |
| `Read permissions`               | 允许或拒绝用户读取文件夹的权限                               |
| `Change permissions`             | 允许或拒绝用户更改文件或文件夹的权限                         |
| `Take ownership`                 | 允许或拒绝用户取得文件或文件夹的所有权。文件的所有者具有更改任何权限的完全权限 |

请记住，NTFS 权限适用于托管文件夹和文件的系统。在 NTFS 中创建的文件夹默认继承父文件夹的权限。可以禁用继承以在父文件夹和子文件夹上设置自定义权限，正如我们稍后将在本模块中所做的那样。当通过 SMB 访问文件夹时，共享权限适用，通常是通过网络从不同的系统访问。这意味着在本地或通过 RDP 登录到机器的人可以通过简单地导航到文件系统上的位置来访问共享文件夹和文件，并且只需要考虑 NTFS 权限。NTFS 级别的权限使管理员可以更精细地控制用户在文件夹或文件中可以执行的操作。

------

## 创建网络共享

为了深入了解 SMB 及其与 NTFS 的关系，我们将在`Windows 10 target box`.

注意：让 Pwnbox 在单独的显示器上全屏打开是一种理想的学习体验，因此我们可能至少有一个显示器专门用于显示书面内容，另一个显示器用于我们正在与之交互的框。或者，如果我们只能访问一个显示器，我们可以使用该显示器与盒子进行交互，并使用智能手机或平板电脑来引用书面内容。

在这种情况下，我们将通过首先在 Windows 10 桌面上创建一个新文件夹来创建一个共享文件夹。请记住，在大多数大型企业环境中，共享是在存储区域网络 (SAN)、网络附加存储设备 (NAS) 或通过服务器操作系统（如 Windows Server）访问的驱动器上的单独分区上创建的。如果我们在桌面操作系统上遇到共享，它要么是小型企业，要么是渗透测试人员或恶意攻击者用来收集和泄露数据的滩头阵地系统。

我们将使用 Windows 中的 GUI 完成此过程。

#### 创建文件夹

我们将使用该`Advanced Sharing `选项来配置我们的共享。

#### 将文件夹设为共享

![](windows基础知识\4.png)

​	请注意共享名称如何自动默认为文件夹的名称。此外，我们可以看到可以限制可以同时连接到该共享的用户数量。在实际环境中，管理员最好根据经常需要访问共享资源的用户数量来设置此数量。

与 NTFS 权限类似，共享资源有一个`access control list`( )。`ACL`我们可以将其视为 SMB 权限列表。请记住，对于共享资源，SMB 和 NTFS 权限列表都适用于在 Windows 中共享的每个资源。ACL 包含`access control entries`( `ACEs`)。`users`通常，这些 ACE 由& （也称为安全主体）组成`groups`，因为它们是管理和跟踪对共享资源的访问的合适机制。

注意默认的访问控制条目和权限设置。

#### 共享权限 ACL（共享选项卡）

![](windows基础知识\5.png)

​	现在，我们将应用这些设置来测试此 ACL 的效果和按原样应用的权限。我们将通过打开终端并使用 来测试 Pwnbox 的连接性`smbclient`。

​	注意：服务器在技术上是一种软件功能，用于为客户端的请求提供服务。在这种情况下，Pwnbox 是我们的客户端，而 Windows 10 目标框是我们的服务器。

#### 使用 smbclient 连接到共享

```shell-session
vnswer77@htb[/htb]$ smbclient -L IPaddressOfTarget -U htb-student
Enter WORKGROUP\htb-student's password: 

	Sharename       Type      Comment
	---------       ----      -------
	ADMIN$          Disk      Remote Admin
	C$              Disk      Default share
	Company Data    Disk      
	IPC$            IPC       Remote IPC
```

​	如果我们的所有条目都正确并且我们的权限列表中的 Everyone 组至少具有读取权限，那么有什么可能会阻止我们访问此共享？

## Windows Defender 防火墙注意事项

​	Windows Defender 防火墙可能会阻止对 SMB 共享的访问。由于我们是从基于 Linux 的系统进行连接，因此防火墙已阻止任何未加入同一系统的设备进行访问`workgroup`。同样重要的是要注意，当 Windows 系统是工作组的一部分时，所有`netlogon`请求都会根据该特定 Windows 系统的`SAM`数据库进行身份验证。当 Windows 系统加入 Windows 域环境时，所有 netlogon 请求都经过身份验证`Active Directory`. 工作组和 Windows 域在身份验证方面的主要区别在于工作组使用本地 SAM 数据库，而在 Windows 域中使用基于网络的集中式数据库 (Active Directory)。我们在尝试登录 Windows 系统并进行身份验证时必须知道此信息。考虑托管 htb-student 帐户的位置以正确连接到目标。

在防火墙阻止连接方面，这可以通过完全停用 Windows 中的每个防火墙配置文件或通过在`Windows Defender Firewall advanced security settings`. 与大多数防火墙一样，Windows Defender 防火墙允许或拒绝流量（在这种情况下为访问和连接请求）流动`inbound`和/或`outbound`

不同的入站和出站规则与 Defender 中不同的防火墙配置文件相关联。

Windows Defender 防火墙配置文件：

- `Public`
- `Private`
- `Domain`

​	最佳做法是启用预定义规则或添加自定义例外，而不是完全停用防火墙。不幸的是，为了方便或缺乏理解，防火墙被完全停用是很常见的。当通过使用组策略加入 Windows 域环境时，可以集中管理桌面系统上的防火墙规则。组策略概念和配置超出了本模块的范围。

​	启用适当的`inbound`防火墙规则后，我们将成功连接到共享。请记住，我们只能连接到共享，因为我们使用的用户帐户 ( `htb-student`) 在`Everyone group`. 回想一下，我们将 Everyone 组的特定共享权限设置为读取，这实际上意味着我们将只能读取该共享上的文件。一旦与共享建立连接，我们就可以创建一个`mount point`从我们的 Pwnbox 到 Windows 10 目标框的文件系统。这是我们还必须考虑 NTFS 权限与共享权限一起应用的地方。回想一下，NTFS 是 Windows 中的默认文件系统。让我们跳回我们的 xfreerdp 会话与我们的 Windows 10 目标框并查看公司数据文件夹上的 NTFS 权限。

#### NTFS 权限 ACL（安全选项卡）

![](windows基础知识\6.png)

​	可以对用户和组应用 NTFS 权限进行更精细的控制。任何时候我们看到权限旁边有一个灰色复选标记，它是从父目录继承的。默认情况下，所有 NTFS 权限都是从父目录继承的。在 Windows 世界中，这`C:\ drive`是管理所有目录的父目录，除非系统管理员要在新创建的文件夹的高级安全设置中禁用继承。

​	在许多情况下，组织的系统管理员将负责决定用户或用户组获得网络资源的哪些权限。这就是为什么许多鱼叉式网络钓鱼攻击都是针对系统管理员和其他 IT 领导者的。他们对他们监督的环境中允许的内容有很大的影响，在许多情况下甚至比组织的非技术 C 级领导者还要大。例如，在医院工作的医生或管理人员没有网络管理权限，但系统管理员有。

​	现在让我们在共享级别给 Everyone 组`Full control`，并通过尝试从我们的 Pwnbox 桌面创建一个共享的挂载点来测试更改的影响

#### 安装到共享

```shell-session
vnswer77@htb[/htb]$ sudo mount -t cifs -o username=htb-student,password=Academy_WinFun! //ipaddoftarget/"Company Data" /home/user/Desktop/
```

如果此命令不起作用，请检查语法。如果语法正确但命令仍然有效，`cifs-utils`则可能需要安装。这可以通过以下命令完成：

#### 安装 CIFS 实用程序

 安装 CIFS 实用程序

```shell-session
vnswer77@htb[/htb]$ sudo apt-get install cifs-utils
```

一旦我们在 Pwnbox 的桌面上成功创建了挂载点，我们应该看看 Windows 内置的一些工具，它们可以让我们跟踪和监控我们所做的事情。

该`net share`命令允许我们查看系统上的所有共享文件夹。请注意我们创建的共享以及 C:\ 驱动器。

```
Do you remember us sharing the C:\ drive?
```

我们没有手动共享 C:。Windows 系统上包含最关键文件的最重要驱动器在安装时通过 SMB 共享。这意味着任何具有适当访问权限的人都可以远程访问网络上每个 Windows 系统的整个 C:\。

我们还可以看到我们创建的共享。

#### 使用净份额显示份额

 使用净份额显示份额

```cmd-session
C:\Users\htb-student> net share

Share name   Resource                        Remark

-------------------------------------------------------------------------------
C$           C:\                             Default share
IPC$                                         Remote IPC
ADMIN$       C:\WINDOWS                      Remote Admin
Company Data C:\Users\htb-student\Desktop\Company Data

The command completed successfully.
```

​	`Computer Management`是另一个我们可以用来识别和监控 Windows 系统上的共享资源的工具。

#### 从计算机管理监控共享

![](windows基础知识\7.png)

​	我们可以在`Shares`、`Sessions`和中四处寻找`Open Files`，了解这为我们提供了哪些信息。如果出现我们协助个人或组织应对与 SMB 相关的违规行为的情况，这些是检查并开始了解违规行为可能如何发生以及可能遗留的内容的好地方。

#### 在事件查看器中查看共享访问日志

​	`Event Viewer`是调查在 Windows 上完成的操作的另一个好地方。几乎每个操作系统都有日志记录机制和用于查看捕获的日志的实用程序。知道日志就像计算机的日志条目，计算机在其中记下执行的所有操作以及与该操作相关的大量详细信息。我们可以查看为访问 Windows 10 目标框以及创建、编辑和访问共享文件夹时执行的每个操作创建的日志。

![](windows基础知识\8.png)

## Windows 服务和进程

### Windows 服务

服务是 Windows 操作系统的主要组成部分。它们允许创建和管理长期运行的流程。Windows 服务可以在系统启动时自动启动，无需用户干预。即使用户在系统上注销他们的帐户后，这些服务也可以继续在后台运行。

也可以创建应用程序作为服务安装，例如安装在服务器上的网络监控应用程序。Windows 上的服务负责 Windows 操作系统中的许多功能，例如网络功能、执行系统诊断、管理用户凭据、控制 Windows 更新等。

Windows 服务通过服务控制管理器 (SCM) 系统进行管理，可通过`services.msc`MMC 插件访问。

此插件提供了一个 GUI 界面，用于与服务交互和管理服务，并显示有关每个已安装服务的信息。此信息包括服务名称、描述、状态、启动类型和运行该服务的用户。

`sc.exe`也可以使用[PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/overview?view=powershell-7) cmdlet通过命令行查询和管理服务，例如`Get-Service`.

 

```powershell-session
PS C:\htb> Get-Service | ? {$_.Status -eq "Running"} | select -First 2 |fl


Name                : AdobeARMservice
DisplayName         : Adobe Acrobat Update Service
Status              : Running
DependentServices   : {}
ServicesDependedOn  : {}
CanPauseAndContinue : False
CanShutdown         : False
CanStop             : True
ServiceType         : Win32OwnProcess

Name                : Appinfo
DisplayName         : Application Information
Status              : Running
DependentServices   : {}
ServicesDependedOn  : {RpcSs, ProfSvc}
CanPauseAndContinue : False
CanShutdown         : False
CanStop             : True
ServiceType         : Win32OwnProcess, Win32ShareProcess
```

​	服务状态可以显示为正在运行、已停止或已暂停，并且可以将它们设置为手动启动、自动启动或在系统启动时延迟启动。如果某些操作已触发服务启动或停止，则服务也可以显示为正在启动或正在停止状态。Windows 有三类服务：本地服务、网络服务和系统服务。服务通常只能由具有管理权限的用户创建、修改和删除。围绕服务权限的错误配置是 Windows 系统上常见的权限升级向量。

在 Windows 中，我们有一些[关键的系统服务](https://docs.microsoft.com/en-us/windows/win32/rstmgr/critical-system-services)，如果不重启系统，就无法停止和重启。如果我们更新这些服务之一正在使用的任何文件或资源，我们必须重新启动系统。

| 服务                      | 描述                                                         |
| ------------------------- | ------------------------------------------------------------ |
| smss.exe                  | 会话管理子系统。负责处理系统上的会话。                       |
| csrss.exe                 | 客户端服务器运行时进程。Windows子系统的用户模式部分。        |
| wininit.exe               | 启动Wininit文件.ini文件，该文件列出了在安装程序后重新启动计算机时要对Windows进行的所有更改。 |
| logonui.exe               | 用于方便用户登录PC                                           |
| lsass.exe                 | 本地安全认证服务器验证用户登录PC或服务器的合法性。它生成负责对Winlogon服务的用户进行身份验证的进程。 |
| services.exe              | 管理服务的启动和停止操作。                                   |
| winlogon.exe              | 负责处理安全注意序列，在登录时加载用户配置文件，并在屏幕保护程序运行时锁定计算机。 |
| System                    | 运行Windows内核的后台系统进程                                |
| svchost.exe with RPCSS    | 管理从动态链接库(扩展名为.dll的文件)运行的系统服务，如“自动更新”、“Windows防火墙”和“即插即用”。使用远程过程调用(RPC)服务(RPC) |
| svchost.exe with Dcom/PnP | 管理从动态链接库(扩展名为.dll的文件)运行的系统服务，如“自动更新”、“Windows防火墙”和“即插即用”。使用DCOM (Distributed Component Object Model)和PnP (Plug and Play)服务。 |

此[链接](https://en.wikipedia.org/wiki/List_of_Microsoft_Windows_components#Services)包含 Windows 组件列表，包括关键服务。

## 进程

进程在 Windows 系统的后台运行。它们或者作为 Windows 操作系统的一部分自动运行，或者由其他已安装的应用程序启动。

与已安装应用程序关联的进程通常可以终止，而不会对操作系统造成严重影响。某些进程很关键，如果终止，将阻止操作系统的某些组件正常运行。一些示例包括 Windows 登录应用程序、系统、系统空闲进程、Windows 启动应用程序、客户端服务器运行时、Windows 会话管理器、服务主机和本地安全机构子系统服务 (LSASS) 进程。

## 本地安全机构子系统服务 (LSASS)

`lsass.exe`是负责在 Windows 系统上实施安全策略的进程。当用户尝试登录系统时，此过程会验证他们的登录尝试并根据用户的权限级别创建访问令牌。LSASS 还负责用户帐户密码的更改。与此过程相关的所有事件（登录/注销尝试等）都记录在 Windows 安全日志中。LSASS 是一个价值极高的目标，因为存在多种工具可以提取此过程存储在内存中的明文和哈希凭证。

## 系统内部工具

SysInternals[工具套件](https://docs.microsoft.com/en-us/sysinternals)是一组可移植的 Windows 应用程序，可用于管理 Windows 系统（大部分无需安装）。这些工具可以从 Microsoft 网站下载，也可以通过在`\\live.sysinternals.com\tools`Windows 资源管理器窗口中键入内容直接从可访问 Internet 的文件共享中加载。

例如，我们可以直接从这个共享中运行 procdump.exe，而不需要直接下载到磁盘。

 

```cmd-session
C:\htb> \\live.sysinternals.com\tools\procdump.exe -accepteula

ProcDump v9.0 - Sysinternals process dump utility
Copyright (C) 2009-2017 Mark Russinovich and Andrew Richards
Sysinternals - www.sysinternals.com

Monitors a process and writes a dump file when the process exceeds the
specified criteria or has an exception.

Capture Usage:
   procdump.exe [-mm] [-ma] [-mp] [-mc Mask] [-md Callback_DLL] [-mk]
                [-n Count]
                [-s Seconds]
                [-c|-cl CPU_Usage [-u]]
                [-m|-ml Commit_Usage]
                [-p|-pl Counter_Threshold]
                [-h]
                [-e [1 [-g] [-b]]]
                [-l]
                [-t]
                [-f  Include_Filter, ...]
                [-fx Exclude_Filter, ...]
                [-o]
                [-r [1..5] [-a]]
                [-wer]
                [-64]
                {
                 {{[-w] Process_Name | Service_Name | PID} [Dump_File | Dump_Folder]}
                |
                 {-x Dump_Folder Image_File [Argument, ...]}
                }
				
<SNIP>
```

​	该套件包括`Process Explorer`、增强版`Task Manager`和 等工具`Process Monitor`，可用于监控文件系统、注册表以及与系统上运行的任何进程相关的网络活动。一些额外的工具是用于监控互联网活动的 TCPView 和可用于通过 SMB 协议远程管理/连接系统的 PSExec。

这些工具可用于渗透测试人员，例如，发现有趣的进程和可能的特权升级路径以及横向移动。

## 任务管理器

​	Windows 任务管理器是用于管理 Windows 系统的强大工具。它提供有关正在运行的进程、系统性能、正在运行的服务、启动程序、登录用户/登录用户进程和服务的信息。可以通过右键单击任务栏并选择`Task Manager`，按 ctrl + shift + Esc，按 ctrl + alt + del 并选择`Task Manager`，打开开始菜单并键入`Task Manager`，或`taskmgr`从 CMD 或 PowerShell 控制台键入来打开任务管理器。

![](windows基础知识\9.png)

| 标签       | 描述                                                         |
| ---------- | ------------------------------------------------------------ |
| 进程选项卡 | 显示正在运行的应用程序和后台进程的列表，以及每个应用程序和后台进程的 CPU、内存、磁盘、网络和电源使用情况。 |
| 性能选项卡 | 显示图表和数据，例如 CPU 利用率、系统正常运行时间、内存使用情况、磁盘和网络以及 GPU 使用情况。我们还可以打开`Resource Monitor`，这使我们可以更深入地了解当前的 CPU、内存、磁盘和网络资源使用情况。 |

![](windows基础知识\10.png)

| 标签               | 描述                                                         |
| ------------------ | ------------------------------------------------------------ |
| 应用程序历史选项卡 | 显示一段时间内每个应用程序的当前用户帐户的资源使用情况。     |
| 启动选项卡         | 显示哪些应用程序配置为在引导时启动以及对启动过程的影响。     |
| 用户选项卡         | 显示登录用户以及与其会话关联的进程/资源使用情况。            |
| 详细信息选项卡     | 显示每个正在运行的应用程序的名称、进程 ID (PID)、状态、关联的用户名、CPU 和内存使用情况。 |
| 服务选项卡         | 显示每个已安装服务的名称、PID、描述和状态。也可以从此选项卡访问服务加载项。 |

## 进程浏览器

[Process Explorer](https://docs.microsoft.com/en-us/sysinternals/downloads/process-explorer)是 Sysinternals 工具套件的一部分。该工具可以显示程序运行时加载了哪些句柄和 DLL 进程。Process Explorer 显示了当前正在运行的进程的列表，从那里，我们可以看到在一个视图中选择了哪些句柄，或者在另一个视图中已加载的 DLL 和内存交换文件。我们还可以在该工具内进行搜索，以显示哪些进程与特定句柄或 DLL 相关联。该工具还可用于分析父子进程关系，以查看应用程序生成了哪些子进程，并帮助解决任何问题，例如进程终止时可能遗留的孤立进程。

# 服务权限

------

回想一下，服务允许管理长时间运行的进程，并且是 Windows 操作系统的关键部分。系统管理员经常将它们视为潜在的威胁载体，它们可用于加载恶意 DLL、在无需管理员帐户访问权限的情况下执行应用程序、提升权限甚至保持持久性。Windows 服务中的这些威胁媒介通常是通过第 3 方软件设置的服务权限配置错误以及管理员在安装过程中容易犯的错误而产生的。

了解服务权限重要性的第一步就是简单地了解它们的存在并注意它们。在服务器操作系统上，DHCP 和 Active Directory 域服务等关键网络服务通常使用分配给执行安装的管理员的帐户进行安装。安装过程的一部分包括分配特定服务以使用指定用户的凭据和特权运行，默认情况下，指定用户在当前登录的用户上下文中设置。

例如，如果我们在 DHCP 安装期间以 Bob 的身份登录到服务器，那么除非另有说明，否则该服务将被配置为以 Bob 的身份运行。这会带来什么坏事？那么，如果 Bob 离开组织或被解雇怎么办？典型的业务实践是禁用 Bob 的帐户作为他退出过程的一部分。在这种情况下，使用 Bob 的帐户运行的 DHCP 和其他服务会怎样？这些服务将无法启动。DHCP 或动态主机配置协议负责将 IP 地址出租给网络上的计算机。如果此服务在 Windows DHCP 服务器上停止，则请求 IP 地址的客户端将不会收到 IP 地址。这意味着服务配置错误可能导致停机和生产力损失。强烈建议创建个人用户帐户以运行关键网络服务。

我们还应该注意服务权限和它们执行的目录的权限，因为可以用恶意 DLL 或可执行文件替换可执行文件的路径。让我们检查一下在 Windows 10 上运行的服务的权限，以便更好地理解这一点。

## 使用 services.msc 检查服务

正如流程和服务部分所讨论的，我们可以使用它`services.msc`来查看和管理所有服务的几乎所有细节。让我们仔细看看与`Windows Update`( `wuauserv`) 关联的服务。

![](windows基础知识\11.png)

记下可用于查看和配置的不同属性。在使用命令行工具检查和管理服务时，了解服务名称特别有用。可执行文件的路径是服务启动时要执行的程序和命令的完整路径。如果目标目录的 NTFS 权限配置为弱权限，攻击者可以将原始可执行文件替换为恶意创建的可执行文件。我们将在本模块的 NTFS 与共享权限部分详细讨论 NTFS 权限。

![](windows基础知识\12.png)

默认情况下，大多数服务以 LocalSystem 权限运行，这是单个 Windows 操作系统上允许的最高访问级别。并非所有应用程序都需要本地系统帐户级权限，因此在考虑在 Windows 环境中安装新应用程序时，逐案进行研究是有益的。确定可以以尽可能少的权限运行以符合最小权限原则的应用程序是一种很好的做法。

[这是最小特权原则的一个细分](https://www.cloudflare.com/learning/access-management/principle-of-least-privilege/)

Windows 中值得注意的内置服务帐户：

- 本地服务
- 网络服务
- 本地系统

注意：我们也可以创建新帐户并将其用于运行服务的唯一目的。

![](windows基础知识\13.png)

如果服务失败，恢复选项卡允许配置步骤。请注意如何将此服务设置为在第一次失败后运行程序。这是攻击者可以用来通过利用合法服务来运行恶意程序的另一种载体。

------

## 使用 sc 检查服务

sc 也可用于配置和管理服务。让我们尝试一些命令。

```cmd-session
C:\Users\htb-student>sc qc wuauserv
[SC] QueryServiceConfig SUCCESS

SERVICE_NAME: wuauserv
        TYPE               : 20  WIN32_SHARE_PROCESS
        START_TYPE         : 3   DEMAND_START
        ERROR_CONTROL      : 1   NORMAL
        BINARY_PATH_NAME   : C:\WINDOWS\system32\svchost.exe -k netsvcs -p
        LOAD_ORDER_GROUP   :
        TAG                : 0
        DISPLAY_NAME       : Windows Update
        DEPENDENCIES       : rpcss
        SERVICE_START_NAME : LocalSystem
```

该`sc qc`命令用于查询服务。这是知道服务名称可以派上用场的地方。如果我们想通过网络查询设备上的服务，我们可以在 之后立即指定主机名或 IP 地址`sc`。

 

```cmd-session
C:\Users\htb-student>sc //hostname or ip of box query ServiceName
```

我们还可以使用 sc 来启动和停止服务。

 

```cmd-session
C:\Users\htb-student> sc stop wuauserv

[SC] OpenService FAILED 5:

Access is denied.
```

请注意，如果我们不在管理上下文中运行它，我们是如何被拒绝执行此操作的。如果我们使用 运行命令提示符`elevated privileges`，我们将被允许完成此操作。

 

```cmd-session
C:\WINDOWS\system32> sc config wuauserv binPath=C:\Winbows\Perfectlylegitprogram.exe

[SC] ChangeServiceConfig SUCCESS

C:\WINDOWS\system32> sc qc wuauserv

[SC] QueryServiceConfig SUCCESS

SERVICE_NAME: wuauserv
        TYPE               : 20  WIN32_SHARE_PROCESS
        START_TYPE         : 3   DEMAND_START
        ERROR_CONTROL      : 1   NORMAL
        BINARY_PATH_NAME   : C:\Winbows\Perfectlylegitprogram.exe
        LOAD_ORDER_GROUP   :
        TAG                : 0
        DISPLAY_NAME       : Windows Update
        DEPENDENCIES       : rpcss
        SERVICE_START_NAME : LocalSystem
```

如果我们正在调查我们怀疑系统有恶意软件的情况，sc 将使我们能够快速搜索和分析常见的目标服务和新创建的服务。与使用 GUI 工具（如`services.msc`.

我们可以使用的另一种有用的方法来检查服务权限`sc`是通过`sdshow`命令。

 

```cmd-session
C:\WINDOWS\system32> sc sdshow wuauserv

D:(A;;CCLCSWRPLORC;;;AU)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;BA)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;SY)S:(AU;FA;CCDCLCSWRPWPDTLOSDRCWDWO;;;WD)
```

乍一看，输出看起来很疯狂。似乎我们在命令中做错了什么，但这种疯狂是有意义的。Windows 中的每个命名对象都是[安全对象](https://docs.microsoft.com/en-us/windows/win32/secauthz/securable-objects)，甚至一些未命名的对象也是安全的。如果它在 Windows 操作系统中是安全的，它将有一个[安全描述符](https://docs.microsoft.com/en-us/windows/win32/secauthz/security-descriptors)。安全描述符标识对象的所有者和包含`Discretionary Access Control List`( `DACL`) 和`System Access Control List`( `SACL`) 的主要组。

通常，DACL 用于控制对对象的访问，而 SACL 用于说明和记录访问尝试。本节将研究 DACL，但相同的概念也适用于 SACL。

```
D:(A;;CCLCSWRPLORC;;;AU)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;BA)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;SY)
```

这种将字符组合在一起并由左括号和右括号分隔的格式称为 ( `Security Descriptor Definition Language`) `SDDL`。

我们可能会想从左到右阅读，因为这是英语的典型书写方式，但在与计算机交互时可能会大不相同。从第一个字母和一组括号开始，按此顺序阅读`Windows Update`( ) 服务的整个安全描述符：`wuauserv`

```
D: (A;;CCLCSWRPLORC;;;AU)
```

1. D: - 前面的字符是 DACL 权限
2. AU: - 定义安全主体 Authenticated Users
3. A;; - 允许访问
4. CC - SERVICE_QUERY_CONFIG 是全名，它是向服务控制管理器（SCM）查询服务配置
5. LC - SERVICE_QUERY_STATUS 是全名，它是向服务控制管理器（SCM）查询服务的当前状态
6. SW - SERVICE_ENUMERATE_DEPENDENTS 是全名，它会枚举一个依赖服务的列表
7. RP - SERVICE_START 是全名，它会启动服务
8. LO - SERVICE_INTERROGATE 是全名，它将查询服务的当前状态
9. RC - READ_CONTROL 是全名，它会查询服务的安全描述符

当我们阅读安全描述符时，很容易迷失在看似随机的字符顺序中，但回想一下，我们实际上是在查看访问控制列表中的访问控制条目。分号之间的每组 2 个字符表示允许特定用户或组执行的操作。

```
;;CCLCSWRPLORC;;;
```

在最后一组分号之后，字符指定允许执行这些操作的安全主体（用户和/或组）。

```
;;;AU
```

左括号之后和第一组分号之前的字符定义是允许还是拒绝操作。

```
A;;
```

`Windows Update`与( ) 服务关联的整个安全描述符`wuauserv`具有三组访问控制条目，因为存在三个不同的安全主体。每个安全主体都应用了特定的权限。

------

## 使用 PowerShell 检查服务权限

使用`Get-Acl`PowerShell cmdlet，我们可以通过定位注册表中特定服务的路径来检查服务权限。

 

```powershell-session
PS C:\Users\htb-student> Get-ACL -Path HKLM:\System\CurrentControlSet\Services\wuauserv | Format-List

Path   : Microsoft.PowerShell.Core\Registry::HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\wuauserv
Owner  : NT AUTHORITY\SYSTEM
Group  : NT AUTHORITY\SYSTEM
Access : BUILTIN\Users Allow  ReadKey
         BUILTIN\Users Allow  -2147483648
         BUILTIN\Administrators Allow  FullControl
         BUILTIN\Administrators Allow  268435456
         NT AUTHORITY\SYSTEM Allow  FullControl
         NT AUTHORITY\SYSTEM Allow  268435456
         CREATOR OWNER Allow  268435456
         APPLICATION PACKAGE AUTHORITY\ALL APPLICATION PACKAGES Allow  ReadKey
         APPLICATION PACKAGE AUTHORITY\ALL APPLICATION PACKAGES Allow  -2147483648
         S-1-15-3-1024-1065365936-1281604716-3511738428-1654721687-432734479-3232135806-4053264122-3456934681 Allow
         ReadKey
         S-1-15-3-1024-1065365936-1281604716-3511738428-1654721687-432734479-3232135806-4053264122-3456934681 Allow
         -2147483648
Audit  :
Sddl   : O:SYG:SYD:AI(A;ID;KR;;;BU)(A;CIIOID;GR;;;BU)(A;ID;KA;;;BA)(A;CIIOID;GA;;;BA)(A;ID;KA;;;SY)(A;CIIOID;GA;;;SY)(A
         ;CIIOID;GA;;;CO)(A;ID;KR;;;AC)(A;CIIOID;GR;;;AC)(A;ID;KR;;;S-1-15-3-1024-1065365936-1281604716-3511738428-1654
         721687-432734479-3232135806-4053264122-3456934681)(A;CIIOID;GR;;;S-1-15-3-1024-1065365936-1281604716-351173842
         8-1654721687-432734479-3232135806-4053264122-3456934681)
```

请注意此命令如何以易于阅读的格式和 SDDL 返回特定帐户权限。此外，代表每个安全主体（用户和/或组）的 SID 存在于 SDDL 中。`sc`这是我们从命令提示符运行时没有得到的东西。

了解如何从命令行与服务及其相关权限进行交互，可以更轻松地编写这些任务的脚本。虽然知道如何从 GUI 执行这些任务是件好事，但随着我们开始进入更大的网络环境和域，它并不能很好地扩展。

## Windows 会话

#### 交互的

交互式或本地登录会话由用户通过输入其凭据向本地或域系统进行身份验证来启动。`runas`可以通过直接登录系统、通过命令行使用命令请求辅助登录会话或通过远程桌面连接来启动交互式登录。

#### 非交互式

Windows 中的非交互式帐户不同于标准用户帐户，因为它们不需要登录凭据。有 3 种类型的非交互式帐户：本地系统帐户、本地服务帐户和网络服务帐户。Windows 操作系统通常使用非交互帐户来自动启动服务和应用程序，而无需用户交互。这些帐户没有与之关联的密码，通常用于在系统启动时启动服务或运行计划任务。

三种类型的帐户之间存在差异：

| 帐户         | 描述                                                         |
| ------------ | ------------------------------------------------------------ |
| 本地系统帐户 | 又称账户`NT AUTHORITY\SYSTEM`，这是Windows系统中最强大的账户。它用于各种与操作系统相关的任务，例如启动 Windows 服务。此帐户比本地管理员组中的帐户更强大。 |
| 本地服务帐户 | 称为`NT AUTHORITY\LocalService`帐户，这是 SYSTEM 帐户的低权限版本，具有与本地用户帐户类似的权限。它被授予有限的功能，可以启动一些服务。 |
| 网络服务帐户 | 这称为`NT AUTHORITY\NetworkService`帐户，类似于标准域用户帐户。它具有与本地计算机上的本地服务帐户类似的权限。它可以为某些网络服务建立经过身份验证的会话。 |

# 与 Windows 操作系统交互

------

#### 图形用户界面

图形用户界面 (GUI) 的概念是在 1970 年代后期由 Xerox Palo Alto 研究实验室引入的。它被添加到 Apple 和 Microsoft 操作系统中，以解决可能难以导航命令行的日常用户的可用性问题。大多数临时 Windows 计算机用户不需要通过命令行与操作系统交互。顾名思义，GUI 为用户提供了一个交互式点击界面，用于与操作系统和已安装的应用程序和服务进行交互。

GUI 的引入打开了广泛的吸引力并在许多人口统计中打开了对计算机的访问，因为用户将能够与他们的计算机交互而无需记住命令或了解任何编程语言。系统管理员通常使用基于 GUI 的系统来管理 Active Directory、配置 IIS 或与数据库交互。

## 远程桌面协议 (RDP)

[RDP](https://support.microsoft.com/en-us/help/186607/understanding-the-remote-desktop-protocol-rdp)是专有的 Microsoft 协议，允许用户通过网络连接到远程系统并获得图形用户界面。用户使用 RDP 客户端软件连接到运行 RDP 服务器软件的目标系统。RDP 使用端口 3389 打开专用网络通道来回发送数据。通过 RDP 连接时，用户可以访问 GUI，就好像他们实际上坐在计算机前并在本地登录一样。RDP 通常被系统管理员用来快速管理远程系统。它还可以允许用户在连接到虚拟专用网络 (VPN) 后在旅行或在家工作时访问他们的工作计算机。

## Windows 命令行

命令行界面使用户能够更好地控制他们的系统，并可用于执行各种日常、管理和故障排除任务。它可以用来引入自动化以快速执行某些任务（例如一次将许多用户添加到域中）。在 Windows 操作系统中，从命令行与系统交互的主要两种方式是通过命令提示符 (CMD) 和 PowerShell。

Microsoft 的Windows[命令参考](https://download.microsoft.com/download/5/8/9/58911986-D4AD-4695-BF63-F734CD4DF8F2/ws-commands.pdf)是一个全面的 AZ 命令参考，其中包括大多数 Windows 命令的概述、用法示例和命令语法，建议熟悉它。

## 命令指令

命令提示符 (cmd.exe) 用于输入和执行命令。用户可以输入一次性命令，例如`ipconfig`查看 IP 地址信息或执行更高级的任务，例如设置计划任务或创建脚本和批处理文件。命令提示符可以从开始菜单打开，方法是`cmd`在运行对话框中键入，或者直接从`C:\Windows\system32\cmd.exe`.

启动后，`cmd.exe`我们可以键入`help`以查看可用命令的列表。

 图形用户界面

```cmd-session
C:\htb> help
For more information on a specific command, type HELP command-name
ASSOC          Displays or modifies file extension associations.
ATTRIB         Displays or changes file attributes.
BREAK          Sets or clears extended CTRL+C checking.
BCDEDIT        Sets properties in boot database to control boot loading.
CACLS          Displays or modifies access control lists (ACLs) of files.
CALL           Calls one batch program from another.
CD             Displays the name of or changes the current directory.
CHCP           Displays or sets the active code page number.
CHDIR          Displays the name of or changes the current directory.
CHKDSK         Checks a disk and displays a status report.
CHKNTFS        Displays or modifies the checking of disk at boot time.
CLS            Clears the screen.
CMD            Starts a new instance of the Windows command interpreter.
COLOR          Sets the default console foreground and background colors.
COMP           Compares the contents of two files or sets of files.
COMPACT        Displays or alters the compression of files on NTFS partitions.
CONVERT        Converts FAT volumes to NTFS.  You cannot convert the
               current drive.
COPY           Copies one or more files to another location.

<SNIP>
```

有关特定命令的更多信息，我们可以键入`help <command name>`.

 图形用户界面

```cmd-session
C:\htb> help schtasks

SCHTASKS /parameter [arguments]

Description:
    Enables an administrator to create, delete, query, change, run and
    end scheduled tasks on a local or remote system.

Parameter List:
    /Create         Creates a new scheduled task.

    /Delete         Deletes the scheduled task(s).

    /Query          Displays all scheduled tasks.

    /Change         Changes the properties of scheduled task.

    /Run            Runs the scheduled task on demand.

    /End            Stops the currently running scheduled task.

    /ShowSid        Shows the security identifier corresponding to a scheduled task name.

    /?              Displays this help message.

Examples:
    SCHTASKS
    SCHTASKS /?
    SCHTASKS /Run /?
    SCHTASKS /End /?
    SCHTASKS /Create /?
    SCHTASKS /Delete /?
    SCHTASKS /Query  /?
    SCHTASKS /Change /?
    SCHTASKS /ShowSid /?
```

请注意，某些命令有自己的帮助菜单，可以通过键入来访问`<command> /?`。例如，有关`ipconfig`命令的信息可以在下面看到。

 图形用户界面



## 电源外壳

Windows PowerShell 是 Microsoft 设计的命令外壳，更适合系统管理员使用。PowerShell 与 Windows 命令行一样，具有交互式命令提示符以及强大的脚本环境。PowerShell 建立在 .NET Framework 之上，后者用于在 Windows 上构建和运行应用程序。这使它成为直接与操作系统交互的非常强大的工具。

与命令提示符一样，PowerShell 让我们可以直接访问文件系统，并且我们可以在 cmd shell 中运行大部分相同的命令。

## 小程序

PowerShell 使用[cmdlet](https://docs.microsoft.com/en-us/powershell/scripting/developer/cmdlet/cmdlet-overview?view=powershell-7)，它们是 shell 中内置的小型单功能工具。有 100 多个核心 cmdlet，并且已经编写了许多额外的 cmdlet，或者我们可以编写自己的 cmdlet 来执行更复杂的任务。PowerShell 还支持用于系统管理任务、自动化等的简单和复杂脚本。

Cmdlet 的形式为`Verb-Noun`. 例如，该命令`Get-ChildItem`可用于列出我们当前的目录。Cmdlet 还带有参数或标志。我们可以键入`Get-ChildItem -`并按 tab 键来遍历参数。诸如此类的命令`Get-ChildItem -Recurse`将向我们显示当前工作目录和所有子目录的内容。另一个例子是`Get-ChildItem -Path C:\Users\Administrator\Documents`获取另一个目录的内容。最后，我们可以组合这样的参数以递归地列出另一个目录中所有子目录的内容`Get-ChildItem -Path C:\Users\Administrator\Downloads -Recurse`：

## 别名

PowerShell 中的许多 cmdlet 也有别名。例如，用于更改目录的 cmdlet 的别名`Set-Location`是`cd`或`sl`。同时，别名`Get-ChildItem`是`ls`和`gci`。我们可以通过键入查看所有可用的别名`Get-Alias`。

 图形用户界面

```powershell-session
PS C:\htb> get-alias

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Alias           % -> ForEach-Object
Alias           ? -> Where-Object
Alias           ac -> Add-Content
Alias           asnp -> Add-PSSnapin
Alias           cat -> Get-Content
Alias           cd -> Set-Location
Alias           CFS -> ConvertFrom-String                          3.1.0.0    Microsoft.PowerShell.Utility
Alias           chdir -> Set-Location
Alias           clc -> Clear-Content
Alias           clear -> Clear-Host
Alias           clhy -> Clear-History
Alias           cli -> Clear-Item
Alias           clp -> Clear-ItemProperty
```

我们还可以设置我们自己的别名，`New-Alias`并使用 获取任何 cmdlet 的别名`Get-Alias -Name`。

 图形用户界面

```powershell-session
PS C:\htb> New-Alias -Name "Show-Files" Get-ChildItem
PS C:\> Get-Alias -Name "Show-Files"

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Alias           Show-Files
```

PowerShell 有一个针对 cmdlet、函数、脚本和概念的帮助系统。这不是默认安装的，但我们可以运行`Get-Help <cmdlet-name> -Online`命令打开 cmdlet 的在线帮助或在我们的 Web 浏览器中运行。我们可以键入`Update-Help`本地下载安装帮助文件。

 图形用户界面

```powershell-session
PS C:\htb> help

TOPIC
    Windows PowerShell Help System

SHORT DESCRIPTION
    Displays help about Windows PowerShell cmdlets and concepts.

LONG DESCRIPTION
    Windows PowerShell Help describes Windows PowerShell cmdlets,
    functions, scripts, and modules, and explains concepts, including
    the elements of the Windows PowerShell language.

    Windows PowerShell does not include help files, but you can read the
    help topics online, or use the Update-Help cmdlet to download help files
    to your computer and then use the Get-Help cmdlet to display the help
    topics at the command line.

    You can also use the Update-Help cmdlet to download updated help files
    as they are released so that your local help content is never obsolete.

    Without help files, Get-Help displays auto-generated help for cmdlets,
    functions, and scripts.


  ONLINE HELP
    You can find help for Windows PowerShell online in the TechNet Library
    beginning at http://go.microsoft.com/fwlink/?LinkID=108518.

    To open online help for any cmdlet or function, type:

        Get-Help <cmdlet-name> -Online
		
<SNIP>
```

除非安装了帮助文件，否则键入诸如 之类的命令`Get-Help Get-AppPackage`将仅返回部分帮助。

 图形用户界面

```powershell-session
PS C:\htb>  Get-Help Get-AppPackage

NAME
    Get-AppxPackage

SYNTAX
    Get-AppxPackage [[-Name] <string>] [[-Publisher] <string>] [-AllUsers] [-PackageTypeFilter {None | Main |
    Framework | Resource | Bundle | Xap | Optional | All}] [-User <string>] [-Volume <AppxVolume>]
    [<CommonParameters>]


ALIASES
    Get-AppPackage


REMARKS
    Get-Help cannot find the Help files for this cmdlet on this computer. It is displaying only partial help.
        -- To download and install Help files for the module that includes this cmdlet, use Update-Help.
```

## 运行脚本

PowerShell ISE（集成脚本环境）允许用户即时编写 PowerShell 脚本。它还具有 PowerShell 命令的自动完成/查找功能。PowerShell ISE 允许我们在同一控制台中编写和运行脚本，从而实现快速调试。

我们可以通过多种方式运行 PowerShell 脚本。如果我们知道这些功能，我们可以在本地运行脚本，也可以在使用下载底座加载到内存后运行脚本，如下例所示。

 图形用户界面

```powershell-session
PS C:\htb> .\PowerView.ps1;Get-LocalGroup |fl

Description     : Users of Docker Desktop
Name            : docker-users
SID             : S-1-5-21-674899381-4069889467-2080702030-1004
PrincipalSource : Local
ObjectClass     : Group

Description     : VMware User Group
Name            : __vmware__
SID             : S-1-5-21-674899381-4069889467-2080702030-1003
PrincipalSource : Local
ObjectClass     : Group

Description     : Members of this group can remotely query authorization attributes and permissions for resources on
                  this computer.
Name            : Access Control Assistance Operators
SID             : S-1-5-32-579
PrincipalSource : Local
ObjectClass     : Group

Description     : Administrators have complete and unrestricted access to the computer/domain
Name            : Administrators
SID             : S-1-5-32-544
PrincipalSource : Local

<SNIP>
```

在 PowerShell 中使用脚本的一种常见方法是导入它，以便所有功能都可以在我们当前的 PowerShell 控制台会话中使用：`Import-Module .\PowerView.ps1`。然后我们可以启动命令并循环浏览选项或键入`Get-Module`以列出所有已加载的模块及其相关命令。

 图形用户界面

```powershell-session
PS C:\htb> Get-Module | select Name,ExportedCommands | fl


Name             : Appx
ExportedCommands : {[Add-AppxPackage, Add-AppxPackage], [Add-AppxVolume, Add-AppxVolume], [Dismount-AppxVolume,
                   Dismount-AppxVolume], [Get-AppxDefaultVolume, Get-AppxDefaultVolume]...}

Name             : Microsoft.PowerShell.LocalAccounts
ExportedCommands : {[Add-LocalGroupMember, Add-LocalGroupMember], [Disable-LocalUser, Disable-LocalUser],
                   [Enable-LocalUser, Enable-LocalUser], [Get-LocalGroup, Get-LocalGroup]...}

Name             : Microsoft.PowerShell.Management
ExportedCommands : {[Add-Computer, Add-Computer], [Add-Content, Add-Content], [Checkpoint-Computer,
                   Checkpoint-Computer], [Clear-Content, Clear-Content]...}

Name             : Microsoft.PowerShell.Utility
ExportedCommands : {[Add-Member, Add-Member], [Add-Type, Add-Type], [Clear-Variable, Clear-Variable], [Compare-Object,
                   Compare-Object]...}

Name             : PSReadline
ExportedCommands : {[Get-PSReadLineKeyHandler, Get-PSReadLineKeyHandler], [Get-PSReadLineOption,
                   Get-PSReadLineOption], [Remove-PSReadLineKeyHandler, Remove-PSReadLineKeyHandler],
                   [Set-PSReadLineKeyHandler, Set-PSReadLineKeyHandler]...}
```

## 执行政策

有时我们会发现无法在系统上运行脚本。这是由于称为 的安全功能`execution policy`，它试图阻止恶意脚本的执行。可能的策略是：

| **政策**       | **描述**                                                     |
| -------------- | ------------------------------------------------------------ |
| `AllSigned`    | 所有脚本都可以运行，但受信任的发布者必须签署脚本和配置文件。这包括远程和本地脚本。在运行由我们尚未列为受信任或不受信任的发布者签名的脚本之前，我们会收到提示。 |
| `Bypass`       | 没有脚本或配置文件被阻止，用户也没有收到警告或提示。         |
| `Default`      | `Restricted`这为 Windows 台式机和`RemoteSigned`Windows 服务器设置了默认执行策略。 |
| `RemoteSigned` | 脚本可以运行，但需要对从 Internet 下载的脚本进行数字签名。本地编写的脚本不需要数字签名。 |
| `Restricted`   | 这允许单独的命令但不允许运行脚本。所有脚本文件类型，包括配置文件 ( `.ps1xml`)、模块脚本文件 ( `.psm1`) 和 PowerShell 配置文件 ( `.ps1`) 都被阻止。 |
| `Undefined`    | 没有为当前作用域设置执行策略。`Restricted`如果 ALL 作用域的执行策略设置为未定义，则将使用默认的执行策略。 |
| `Unrestricted` | 这是非 Windows 计算机的默认执行策略，无法更改。此策略允许运行未签名的脚本，但会在运行不是来自本地 Intranet 区域的脚本之前警告用户。 |

以下是所有范围的当前执行策略的示例。

 图形用户界面

```powershell-session
PS C:\htb> Get-ExecutionPolicy -List

        Scope ExecutionPolicy
        ----- ---------------
MachinePolicy       Undefined
   UserPolicy       Undefined
      Process       Undefined
  CurrentUser       Undefined
 LocalMachine    RemoteSigned
```

执行策略并不意味着是限制用户操作的安全控制。用户可以通过直接在 PowerShell 窗口中键入脚本内容、下载并调用脚本或将脚本指定为编码命令来轻松绕过该策略。也可以通过调整执行策略（如果用户具有适当的权限）或为当前进程范围设置执行策略（这几乎可以由任何用户完成，因为它不需要配置更改并且只会被为用户会话的持续时间设置）。

下面是更改当前进程（会话）的执行策略的示例。

 图形用户界面

```powershell-session
PS C:\htb> Set-ExecutionPolicy Bypass -Scope Process

Execution Policy Change
The execution policy helps protect you from scripts that you do not trust. Changing the execution policy might expose
you to the security risks described in the about_Execution_Policies help topic at
https:/go.microsoft.com/fwlink/?LinkID=135170. Do you want to change the execution policy?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"): Y
```

我们现在可以看到执行策略已更改。

 图形用户界面

```powershell-session
PS C:\htb>  Get-ExecutionPolicy -List

        Scope ExecutionPolicy
        ----- ---------------
MachinePolicy       Undefined
   UserPolicy       Undefined
      Process          Bypass
  CurrentUser       Undefined
 LocalMachine    RemoteSigned
```

## Windows 管理规范 (WMI)

WMI 是 PowerShell 的一个子系统，为系统管理员提供了强大的系统监控工具。WMI 的目标是整合企业网络中的设备和应用程序管理。WMI 是 Windows 操作系统的核心部分，从 Windows 2000 开始预装。它由以下组件组成：

| **组件名称**   | **描述**                                                     |
| -------------- | ------------------------------------------------------------ |
| WMI 服务       | Windows Management Instrumentation 进程，它在启动时自动运行，并充当 WMI 提供程序、WMI 存储库和管理应用程序之间的中介。 |
| 管理对象       | 可以由 WMI 管理的任何逻辑或物理组件。                        |
| WMI 供应商     | 监视与特定对象相关的事件/数据的对象。                        |
| 班级           | WMI 提供程序使用它们将数据传递给 WMI 服务。                  |
| 方法           | 这些附加到类并允许执行操作。例如，方法可用于启动/停止远程机器上的进程。 |
| WMI 存储库     | 存储与 WMI 相关的所有静态数据的数据库。                      |
| CMI 对象管理器 | 从 WMI 提供程序请求数据并将其返回给请求它的应用程序的系统。  |
| 接口           | 使应用程序能够访问 WMI 基础结构。                            |
| WMI消费者      | 通过 CMI 对象管理器向对象发送查询。                          |

WMI 的一些用途是：

- 本地/远程系统的状态信息
- 在远程机器/应用程序上配置安全设置
- 设置和更改用户和组权限
- 设置/修改系统属性
- 代码执行
- 调度进程
- 设置日志记录

这些任务都可以使用 PowerShell 和 WMI 命令行界面 (WMIC) 的组合来执行。WMI 可以通过 Windows 命令提示符运行，方法是键入`WMIC`以打开交互式 shell 或直接运行命令（例如`wmic computersystem get name`获取主机名）。我们可以通过键入查看 WMIC 命令和别名的列表`WMIC /?`。

 

```cmd-session
C:\htb> wmic /?

WMIC is deprecated.

[global switches] <command>

The following global switches are available:
/NAMESPACE           Path for the namespace the alias operate against.
/ROLE                Path for the role containing the alias definitions.
/NODE                Servers the alias will operate against.
/IMPLEVEL            Client impersonation level.
/AUTHLEVEL           Client authentication level.
/LOCALE              Language id the client should use.
/PRIVILEGES          Enable or disable all privileges.
/TRACE               Outputs debugging information to stderr.
/RECORD              Logs all input commands and output.
/INTERACTIVE         Sets or resets the interactive mode.
/FAILFAST            Sets or resets the FailFast mode.
/USER                User to be used during the session.
/PASSWORD            Password to be used for session login.
/OUTPUT              Specifies the mode for output redirection.
/APPEND              Specifies the mode for output redirection.
/AGGREGATE           Sets or resets aggregate mode.
/AUTHORITY           Specifies the <authority type> for the connection.
/?[:<BRIEF|FULL>]    Usage information.

For more information on a specific global switch, type: switch-name /?

Press any key to continue, or press the ESCAPE key to stop
```

以下命令示例列出了有关操作系统的信息。

```cmd-session
C:\htb> wmic os list brief

BuildNumber  Organization  RegisteredUser  SerialNumber             SystemDirectory      Version
19041                      Owner           00123-00123-00123-AAOEM  C:\Windows\system32  10.0.19041
```

WMIC 使用别名和关联的动词、副词和开关。上面的命令示例用于`LIST`显示数据，副词`BRIEF`仅提供核心属性集。[此处](https://docs.microsoft.com/en-us/windows/win32/wmisdk/wmic)提供了动词、开关和副词的详细列表。`Get-WmiObject` [WMI 可以通过使用模块](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/get-wmiobject?view=powershell-5.1)与 PowerShell 一起使用。此模块用于获取 WMI 类的实例或有关可用类的信息。该模块可用于本地或远程机器。

在这里我们可以获得有关操作系统的信息。

```powershell-session
PS C:\htb> Get-WmiObject -Class Win32_OperatingSystem | select SystemDirectory,BuildNumber,SerialNumber,Version | ft

SystemDirectory     BuildNumber SerialNumber            Version
---------------     ----------- ------------            -------
C:\Windows\system32 19041       00123-00123-00123-AAOEM 10.0.19041
```

我们还可以使用`Invoke-WmiMethod` [模块](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/invoke-wmimethod?view=powershell-5.1)，它用于调用 WMI 对象的方法。一个简单的例子是重命名文件。我们可以看到命令正确完成，因为`ReturnValue`设置为 0。

 

```powershell-session
PS C:\htb> Invoke-WmiMethod -Path "CIM_DataFile.Name='C:\users\public\spns.csv'" -Name Rename -ArgumentList "C:\Users\Public\kerberoasted_users.csv"


__GENUS          : 2
__CLASS          : __PARAMETERS
__SUPERCLASS     :
__DYNASTY        : __PARAMETERS
__RELPATH        :
__PROPERTY_COUNT : 1
__DERIVATION     : {}
__SERVER         :
__NAMESPACE      :
__PATH           :
ReturnValue      : 0
PSComputerName   :
```

本节简要概述`WMI`、`WMIC`以及组合`WMIC`和`PowerShell`。`WMI`对蓝队和红队操作员都有广泛的用途。本课程的后面部分将展示一些可以在攻击性方面用于枚举和横向移动的方法`WMI`。

## 微软管理控制台 (MMC)

MMC 可用于对管理单元或管理工具进行分组，以管理 Windows 主机内的硬件、软件和网络组件。它从 Windows Server 2000 开始就存在，并且可以在所有 Windows 版本上运行。我们还可以使用 MMC 创建自定义工具并将它们分发给用户。MMC 使用管理单元的概念，允许管理员创建一个自定义的控制台，仅使用管理多个服务所需的管理工具。可以添加这些管理单元来管理本地和远程系统。

我们只需`mmc`在“开始”菜单中键入即可打开 MMC。当我们第一次打开MMC时，里面是一片空白。

![](windows基础知识\14.png)

从这里，我们可以浏览到 File --> `Add or Remove Snap-ins`，并开始自定义我们的管理控制台。

![](windows基础知识\15.png)

当我们开始添加管理单元时，系统会询问我们是要添加管理单元来仅管理本地计算机，还是用于管理网络上的另一台计算机。

![](windows基础知识\16.png)

添加完管理单元后，它们将出现在 MMC 的左侧。从这里，我们可以将管理单元集保存为 .msc 文件，这样下次打开 MMC 时它们就会全部加载。默认情况下，它们保存在“开始”菜单下的“Windows 管理工具”目录中。下次我们打开 MMC 时，我们可以选择加载我们创建的任何视图。

![](windows基础知识\17.png)

# 适用于 Linux 的 Windows 子系统 (WSL)

------

[WSL](https://docs.microsoft.com/en-us/windows/wsl/)是一种允许 Linux 二进制文件在 Windows 10 和 Windows Server 2019 上原生运行的功能。它最初是为需要直接运行 Bash、Ruby 和原生 Linux 命令行工具（如 、 、 等）的开发`sed`人员`awk`设计`grep`的在他们的 Windows 工作站上。WSL 的第二个版本于 2019 年 5 月发布，引入了一个真正的 Linux 内核，利用了 Hyper-V 功能的一个子集。

`Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux`可以通过以管理员身份运行 PowerShell 命令来安装 WSL 。启用此功能后，我们可以从 Microsoft Store 下载 Linux 发行版并安装，也可以手动下载我们选择的 Linux 发行版并从命令行解压并安装。

WSL 安装了一个名为 的应用程序`Bash.exe`，只需`bash`在 Windows 控制台中键入以生成 Bash shell 即可运行该应用程序。我们从这个 shell 中获得了 Linux 主机的完整外观和感觉，包括标准的 Linux 目录结构。

 

```powershell-session
PS C:\htb> ls /

bin dev home lib lLib64 media opt root sbin srv tmp var
boot etc init 1lib32 Libx32 mnt proc run Snap sys usr
```

我们可以通过目录访问主机操作系统上的 C$ 卷和其他卷`mnt`，从而实现从 WSL 主机和 Windows 主机操作系统的无缝过渡。一旦进入这个 bash shell，我们就可以像与任何基于 Linux 的操作系统交互一样与 WSL 交互：运行命令、安装更新/包等。

 

```powershell-session
PS C:\htb> uname -a

Linux WS01 4.4.0-18362-Microsoft #476-Microsoft Frit Nov 01 16:53:00
PST 2019 x86_64 x86 _64 x86_64 GNU/Linux
```



# 桌面体验与服务器核心

------

[Windows Server Core](https://docs.microsoft.com/en-us/windows-server/administration/server-core/what-is-server-core)最初是随 Windows Server 2008 一起发布的，它是一个只包含关键服务器功能的简约服务器环境。因此，与桌面体验 (GUI) 对应的服务器核心相比，它具有更低的管理要求、更小的攻击面，并且使用更少的磁盘空间和内存。在 Server Core 中，所有配置和维护任务都通过命令行、PowerShell 或使用 MMC 或远程服务器管理工具 (RSAT) 进行远程管理来执行。

虽然 Server Core 旨在通过缺少 GUI 来减少占用空间，但仍然支持一些图形程序，例如注册表编辑器、记事本、系统信息、Windows Installer、任务管理器和 PowerShell。它还支持一些 Sysinternals 套件工具，例如 Active Directory Explorer、Process Explorer、Process Monitor 和 TCPView。

从 Windows Server 2019 开始，必须在安装时选择服务器核心或桌面体验，并且两者都不能回滚（即，将服务器核心转换为桌面体验）。安装后，可以通过 完成 Server Core 的初始设置`Sconfig`，这是一个基于文本的界面（实际上是由 WScript 执行的 VBScript）。`Sconfig`用于执行各种常见命令，例如配置网络、检查/安装 Windows 更新、帐户管理、配置远程管理、激活 Windows 等。

![](windows基础知识\18.png)

某些服务器应用程序无法在 Server Core 上运行，包括 Microsoft Server Virtual Machine Manager 2019 (SCVMM)、System Center Data Protection Manager 2019、SharePoint Server 2019、Project Server 2019。

总而言之，Server Core 重量更轻，占用资源更少，但学习曲线更陡峭，更难管理。它也有一些限制，例如使用某些 GUI 程序执行管理任务。

在任何环境中，应该根据服务器的业务需求和预期用途以及负责维护它的管理员的技能水平来决定是使用桌面体验还是服务器核心来安装服务器。下表显示了服务器核心与桌面体验上可用的一些应用程序。这是一个常见应用程序的列表，而不是一个详尽的列表。

| **应用**                    | **服务器核心** | **桌面体验** |
| --------------------------- | -------------- | ------------ |
| 命令提示符                  | 可用的         | 可用的       |
| Windows PowerShell/微软.NET | 可用的         | 可用的       |
| 注册                        | 可用的         | 可用的       |
| 磁盘管理.msc                | 无法使用       | 可用的       |
| 服务器管理器                | 无法使用       | 可用的       |
| Mmc.exe                     | 无法使用       | 可用的       |
| 事件vwr                     | 无法使用       | 可用的       |
| 服务.msc                    | 无法使用       | 可用的       |
| 控制面板                    | 无法使用       | 可用的       |
| Windows资源管理器           | 无法使用       | 可用的       |
| 任务管理器                  | 可用的         | 可用的       |
| Internet Explorer 或边缘    | 无法使用       | 可用的       |
| 远程桌面服务                | 可用的         | 可用的       |

# Windows 安全

------

安全性是 Windows 操作系统中的一个重要主题。Windows 系统有许多活动部件，这些部件呈现出巨大的攻击面。由于许多内置应用程序、功能和设置层，Windows 系统很容易配置错误，因此即使它们已完全修补，也容易受到攻击。

它具有许多可能被滥用的内置功能，并且存在各种严重漏洞，导致广泛使用且非常有效的远程和本地漏洞利用。

多年来，Microsoft 改进了 Windows 安全性。随着我们世界的互联性不断扩大，攻击者变得越来越老练，Microsoft 不断添加新功能，系统管理员可以使用这些功能来强化系统并主动阻止和检测入侵和滥用的企图。

Windows 遵循特定的安全原则。这些是系统中可以针对特定操作进行授权或验证的单元。这些单元包括用户、网络上的计算机、线程或进程。这些原则旨在使攻击者或恶意软件更难获得未经授权的访问并以意外方式利用系统。

------

## 安全标识符 (SID)

系统上的每个安全主体都有一个唯一的安全标识符 (SID)。系统自动生成SID。这意味着，即使例如我们在系统上有两个相同的用户，Windows 也可以根据他们的 SID 区分这两个用户及其权限。SID 是不同长度的字符串值，存储在安全数据库中。这些 SID 被添加到用户的访问令牌中，以标识用户有权执行的所有操作。

SID 由标识符权限和相对 ID (RID) 组成。在 Active Directory (AD) 域环境中，SID 还包括域 SID。

 

```powershell-session
PS C:\htb> whoami /user

USER INFORMATION
----------------

User Name           SID
=================== =============================================
ws01\bob S-1-5-21-674899381-4069889467-2080702030-1002
```

SID 被分解为这种模式。

 

```powershell-session
(SID)-(revision level)-(identifier-authority)-(subauthority1)-(subauthority2)-(etc)
```

让我们逐个分解 SID。

| **数字**                        | **意义**   | **描述**                                                     |
| ------------------------------- | ---------- | ------------------------------------------------------------ |
| 小号                            | 标识符     | 将字符串标识为 SID。                                         |
| 1个                             | 修订级别   | 迄今为止，这从未改变，而且一直如此`1`。                      |
| 5个                             | 标识符权限 | 标识创建 SID 的机构（计算机或网络）的 48 位字符串。          |
| 21                              | 分权1      | 这是一个可变数字，用于标识 SID 所描述的用户与创建它的机构的关系或组。它告诉我们该机构以什么顺序创建了用户的帐户。 |
| 674899381-4069889467-2080702030 | 分权2      | 告诉我们哪个计算机（或域）创建了该号码                       |
| 1002                            | 分权3      | 区分一个帐户和另一个帐户的 RID。告诉我们这个用户是普通用户、来宾、管理员还是其他组的一部分 |

------

## 安全帐户管理器 (SAM) 和访问控制条目 (ACE)

SAM 授予网络执行特定进程的权限。

访问权限本身由访问控制列表 (ACL) 中的访问控制条目 (ACE) 管理。例如，ACL 包含定义哪些用户、组或进程有权访问文件或执行进程的 ACE。

访问安全对象的权限由安全描述符给出，分为两种类型的 ACL：`Discretionary Access Control List (DACL)`或`System Access Control List (SACL)`。用户启动或启动的每个线程和进程都经过授权过程。此过程的一个组成部分是访问令牌，由本地安全机构 (LSA) 验证。除了 SID 之外，这些访问令牌还包含其他与安全相关的信息。了解这些功能是学习如何在特权升级阶段使用和解决这些安全机制的重要部分。

------

## 用户帐户控制 (UAC)

[用户帐户控制 (UAC)](https://docs.microsoft.com/en-us/windows/security/identity-protection/user-account-control/how-user-account-control-works)是 Windows 中的一项安全功能，可防止恶意软件运行或操纵可能损坏计算机或其内容的进程。UAC 中有管理员批准模式，旨在防止在管理员不知情的情况下安装不需要的软件或防止进行系统范围的更改。如果您安装了特定的软件，您肯定已经看到了同意提示，并且您的系统已经要求您确认是否要安装它。由于安装需要管理员权限，因此会弹出一个窗口，询问您是否要确认安装。对于没有安装权限的标准用户，执行将被拒绝，或者您将被要求输入管理员密码。此同意提示会中断恶意软件或攻击者尝试执行的脚本或二进制文件的执行，直到用户输入密码或确认执行。要了解 UAC 的工作原理，我们需要知道它的结构和工作原理，以及什么会触发同意提示。下图，改编自源码[在这里](https://docs.microsoft.com/en-us/windows/security/identity-protection/user-account-control/how-user-account-control-works)，说明了 UAC 是如何工作的。

![](windows基础知识\19.png)

## 注册表

注册表是 Windows[中](https://en.wikipedia.org/wiki/Windows_Registry)对操作系统至关重要的分层数据库。它存储 Windows 操作系统和选择使用它的应用程序的低级设置。它分为计算机特定数据和用户特定数据。`regedit`我们可以通过从命令行或 Windows 搜索栏中键入来打开注册表编辑器。

树结构由主文件夹（根键）组成，子文件夹（子键）及其条目/文件（值）位于其中。可以在子项中输入 11 种不同类型的值。

| **价值**                | **类型**                                                     |
| ----------------------- | ------------------------------------------------------------ |
| REG_BINARY              | 任何形式的二进制数据。                                       |
| REG_DWORD               | 一个 32 位数字。                                             |
| REG_DWORD_LITTLE_ENDIAN | little-endian 格式的 32 位数字。Windows 设计为在小端计算机体系结构上运行。因此，此值在 Windows 头文件中定义为 REG_DWORD。 |
| REG_DWORD_BIG_ENDIAN    | 大端格式的 32 位数字。一些 UNIX 系统支持大端架构。           |
| REG_EXPAND_SZ           | 一个以 null 结尾的字符串，其中包含对环境变量的未扩展引用（例如，“%PATH%”）。它将是 Unicode 或 ANSI 字符串，具体取决于您使用的是 Unicode 还是 ANSI 函数。要扩展环境变量引用，请使用[**ExpandEnvironmentStrings**](https://docs.microsoft.com/en-us/windows/win32/api/processenv/nf-processenv-expandenvironmentstringsa)函数。 |
| REG_LINK                | [**一个以 null 结尾的 Unicode 字符串，包含通过使用 REG_OPTION_CREATE_LINK 调用RegCreateKeyEx**](https://docs.microsoft.com/en-us/windows/desktop/api/Winreg/nf-winreg-regcreatekeyexa)函数创建的符号链接的目标路径。 |
| REG_MULTI_SZ            | 以空字符串 (\0) 结尾的以 null 结尾的字符串序列。下面是一个示例：*String1* \0 *String2* \0 *String3* \0 *LastString* \0\0 第一个 \0 终止第一个字符串，倒数第二个 \0 终止最后一个字符串，最后一个 \0 终止序列。请注意，最终终止符必须计入字符串的长度。 |
| REG_NONE                | 没有定义的值类型。                                           |
| REG_QWORD               | 一个 64 位数字。                                             |
| REG_QWORD_LITTLE_ENDIAN | little-endian 格式的 64 位数字。Windows 设计为在小端计算机体系结构上运行。因此，此值在 Windows 头文件中定义为 REG_QWORD。 |
| REG_深圳                | 以 null 结尾的字符串。这将是 Unicode 或 ANSI 字符串，具体取决于您使用的是 Unicode 还是 ANSI 函数。 |

来源：[https ://docs.microsoft.com/en-us/windows/win32/sysinfo/registry-value-types](https://docs.microsoft.com/en-us/windows/win32/sysinfo/registry-value-types)

下的每个文件夹`Computer`都是一个键。根键都以`HKEY`. 诸如的键`HKEY-LOCAL-MACHINE`缩写为`HKLM`。HKLM 包含与本地系统相关的所有设置。此根键包含六个子键，如`SAM`、`SECURITY`、`SYSTEM`、`SOFTWARE`、`HARDWARE`和`BCD`，它们在启动时加载到内存中（`HARDWARE`动态加载除外）。

整个系统注册表存储在操作系统的多个文件中。您可以在 下找到这些`C:\Windows\System32\Config\`。

 

```powershell-session
PS C:\htb> ls

    Directory: C:\Windows\system32\config

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----         12/7/2019   4:14 AM                Journal
d-----         12/7/2019   4:14 AM                RegBack
d-----         12/7/2019   4:14 AM                systemprofile
d-----         8/12/2020   1:43 AM                TxR
-a----         8/13/2020   6:02 PM        1048576 BBI
-a----         6/25/2020   4:36 PM          28672 BCD-Template
-a----         8/30/2020  12:17 PM       33816576 COMPONENTS
-a----         8/13/2020   6:02 PM         524288 DEFAULT
-a----         8/26/2020   7:51 PM        4603904 DRIVERS
-a----         6/25/2020   3:37 PM          32768 ELAM
-a----         8/13/2020   6:02 PM          65536 SAM
-a----         8/13/2020   6:02 PM          65536 SECURITY
-a----         8/13/2020   6:02 PM       87818240 SOFTWARE
-a----         8/13/2020   6:02 PM       17039360 SYSTEM
```

用户特定的注册表配置单元 (HKCU) 存储在用户文件夹中（即`C:\Windows\Users\<USERNAME>\Ntuser.dat`）。

 

```powershell-session
PS C:\htb> gci -Hidden

    Directory: C:\Users\bob

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d--h--         6/25/2020   5:12 PM                AppData
d--hsl         6/25/2020   5:12 PM                Application Data
d--hsl         6/25/2020   5:12 PM                Cookies
d--hsl         6/25/2020   5:12 PM                Local Settings
d--h--         6/25/2020   5:12 PM                MicrosoftEdgeBackups
d--hsl         6/25/2020   5:12 PM                My Documents
d--hsl         6/25/2020   5:12 PM                NetHood
d--hsl         6/25/2020   5:12 PM                PrintHood
d--hsl         6/25/2020   5:12 PM                Recent
d--hsl         6/25/2020   5:12 PM                SendTo
d--hsl         6/25/2020   5:12 PM                Start Menu
d--hsl         6/25/2020   5:12 PM                Templates
-a-h--         8/13/2020   6:01 PM        2883584 NTUSER.DAT
-a-hs-         6/25/2020   5:12 PM         524288 ntuser.dat.LOG1
-a-hs-         6/25/2020   5:12 PM        1011712 ntuser.dat.LOG2
-a-hs-         8/17/2020   5:46 PM        1048576 NTUSER.DAT{53b39e87-18c4-11ea-a811-000d3aa4692b}.TxR.0.regtrans-ms
-a-hs-         8/17/2020  12:13 PM        1048576 NTUSER.DAT{53b39e87-18c4-11ea-a811-000d3aa4692b}.TxR.1.regtrans-ms
-a-hs-         8/17/2020  12:13 PM        1048576 NTUSER.DAT{53b39e87-18c4-11ea-a811-000d3aa4692b}.TxR.2.regtrans-ms
-a-hs-         8/17/2020   5:46 PM          65536 NTUSER.DAT{53b39e87-18c4-11ea-a811-000d3aa4692b}.TxR.blf
-a-hs-         6/25/2020   5:15 PM          65536 NTUSER.DAT{53b39e88-18c4-11ea-a811-000d3aa4692b}.TM.blf
-a-hs-         6/25/2020   5:12 PM         524288 NTUSER.DAT{53b39e88-18c4-11ea-a811-000d3aa4692b}.TMContainer000000000
                                                  00000000001.regtrans-ms
-a-hs-         6/25/2020   5:12 PM         524288 NTUSER.DAT{53b39e88-18c4-11ea-a811-000d3aa4692b}.TMContainer000000000
                                                  00000000002.regtrans-ms
---hs-         6/25/2020   5:12 PM             20 ntuser.ini
```

------

## Run 和 RunOnce 注册表项

还有所谓的注册表配置单元，其中包含一组逻辑键、子键和值，以支持在操作系统启动或用户登录时加载到内存中的软件和文件。这些配置单元可用于维护对注册表的访问系统。这些称为[Run 和 RunOnce 注册表项](https://docs.microsoft.com/en-us/windows/win32/setupapi/run-and-runonce-registry-keys)。

Windows 注册表包括以下四个键：

 

```powershell-session
HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run
HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\RunOnce
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\RunOnce
```

`HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run`这是登录系统时的密钥示例。

 

```powershell-session
PS C:\htb> reg query HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run

HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run
    SecurityHealth    REG_EXPAND_SZ    %windir%\system32\SecurityHealthSystray.exe
    RTHDVCPL    REG_SZ    "C:\Program Files\Realtek\Audio\HDA\RtkNGUI64.exe" -s
    Greenshot    REG_SZ    C:\Program Files\Greenshot\Greenshot.exe
```

`HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run`下面是显示当前用户登录系统时运行的应用程序的示例。

 

```powershell-session
PS C:\htb> reg query HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run

HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run
    OneDrive    REG_SZ    "C:\Users\bob\AppData\Local\Microsoft\OneDrive\OneDrive.exe" /background
    OPENVPN-GUI    REG_SZ    C:\Program Files\OpenVPN\bin\openvpn-gui.exe
    Docker Desktop    REG_SZ    C:\Program Files\Docker\Docker\Docker Desktop.exe
```

------

## 应用程序白名单

应用程序白名单是允许在系统上存在和运行的已批准软件应用程序或可执行文件的列表。目标是保护环境免受不符合组织特定业务需求的有害恶意软件和未经批准的软件的侵害。实施强制白名单可能是一项挑战，尤其是在大型网络中。一个组织应该首先在审计模式下实施白名单，以确保所有必要的应用程序都被列入白名单，并且不会被遗漏的错误阻止，这可能导致比它修复的问题更多的问题。

相反，黑名单指定要阻止的有害或不允许的软件/应用程序列表，并允许运行/安装所有其他软件/应用程序。白名单基于“零信任”原则，其中除特别允许的软件/应用程序外，所有软件/应用程序都被视为“不良”。维护白名单通常很少被人听到，因为系统管理员只需要指定允许的内容，而不用不断地用新的恶意应用程序更新“黑名单”。

[NIST](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-167.pdf)等组织推荐使用白名单，尤其是在高安全性环境中。

------

## 应用锁

[AppLocker](https://docs.microsoft.com/en-us/windows/security/threat-protection/windows-defender-application-control/applocker/applocker-overview)是 Microsoft 的应用程序白名单解决方案，最早在 Windows 7 中引入。AppLocker 使系统管理员可以控制用户可以运行哪些应用程序和文件。它提供了对可执行文件、脚本、Windows 安装程序文件、DLL、打包应用程序和打包应用程序安装程序的精细控制。

它允许根据文件属性创建规则，例如发布者名称（可以从数字签名中派生）、产品名称、文件名称和版本。还可以根据文件路径和哈希值设置规则。可以根据业务需要将规则应用于安全组或单个用户。AppLocker 可以先在审核模式下部署以测试影响，然后再强制执行所有规则。

------

## 本地组策略

组策略允许管理员设置、配置和调整各种设置。在域环境中，组策略从域控制器下推到组策略对象 (GPO) 链接到的所有加入域的计算机上。这些设置也可以使用本地组策略在单个计算机上定义。

可以在域环境和非域环境中在本地配置组策略。本地组策略可用于调整某些无法通过控制面板访问的图形和网络设置。它还可用于锁定具有严格安全设置的个人计算机策略，例如仅允许安装/运行某些程序或执行严格的用户帐户密码要求。

我们可以通过打开“开始”菜单并键入来打开本地组策略编辑器`gpedit.msc`。编辑器在 Local Computer Policy 下分为两类 -`Computer Configuration`和`User Configuration`.

![](windows基础知识\20.png)

例如，我们可以通过启用设置来打开本地计算机策略以启用 Credential Guard `Turn On Virtualization Based Security`。Credential Guard 是 Windows 10 中的一项功能，可通过隔离操作系统的 LSA 进程来防止凭据盗窃攻击。

![](windows基础知识\21.png)

我们还可以启用微调帐户审核并从本地组策略编辑器配置 AppLocker。值得探索本地组策略并了解可用于锁定 Windows 系统的各种方法。

### 练习

查找 bob.smith 用户的 SID。

```
**Get-WmiObject -Class Win32_UserAccount -filter “name = ‘bob.smith’”**.
```



## Windows Defender 杀毒软件

Windows Defender Antivirus (Defender)，以前称为 Windows Defender，是内置的防病毒软件，随 Windows 操作系统免费提供。它最初是作为 Windows XP 和 Server 2003 的可下载反间谍软件工具发布的。Defender 开始作为 Windows Vista/Server 2008 操作系统的一部分进行预打包。该程序在 Windows 10 Creators Update 中更名为 Windows Defender Antivirus。

Defender 具有多项功能，例如实时保护，可实时保护设备免受已知威胁，以及云提供的保护，可与自动样本提交结合使用，以上传可疑文件进行分析。当文件被提交到云保护服务时，它们会被“锁定”以防止任何潜在的恶意行为，直到分析完成。另一个功能是防篡改，它可以防止通过注册表、PowerShell cmdlet 或组策略更改安全设置。

Windows Defender 由安全中心管理，可以从中启用和管理各种额外的安全功能和设置。

可以调整实时保护设置以添加文件、文件夹和内存区域以控制文件夹访问以防止未经授权的更改。我们还可以将文件或文件夹添加到排除列表中，这样它们就不会被扫描。一个例子是将用于渗透测试的工具文件夹排除在扫描之外，因为它们将被标记为恶意并被隔离或从系统中删除。受控文件夹访问是 Defender 的内置勒索软件保护。

我们可以使用 PowerShell cmdlet`Get-MpComputerStatus`检查启用了哪些保护设置。

 

```powershell-session
PS C:\htb> Get-MpComputerStatus | findstr "True"
AMServiceEnabled                : True
AntispywareEnabled              : True
AntivirusEnabled                : True
BehaviorMonitorEnabled          : True
IoavProtectionEnabled           : True
IsTamperProtected               : True
NISEnabled                      : True
OnAccessProtectionEnabled       : True
RealTimeProtectionEnabled       : True
```

虽然没有任何防病毒解决方案是完美的，但与其他解决方案（甚至是付费解决方案）相比，Windows Defender 在每月检测率测试中表现出色。此外，由于它是作为操作系统的一部分预装的，因此不会给系统带来“膨胀”，例如添加浏览器扩展和跟踪器的其他程序。众所周知，其他产品会由于挂接到操作系统的方式而减慢系统速度。

Windows Defender 并非没有缺陷，应该成为围绕配置和补丁管理核心原则构建的纵深防御策略的一部分，而不是将其视为保护我们系统的灵丹妙药。定义不断更新，新版本的 Windows Defender 内置于主要操作版本中，例如 Windows 10 版本 1909，这是撰写本文时的最新版本。

Windows Defender 将从常见的开源框架（如 Metasploit）或未更改版本的工具（如 Mimikatz）中获取有效负载。

![](windows基础知识\22.png)

尽管它变得越来越困难，但仍然可以完全绕过安装了最新定义的最新版本强制执行的 Windows Defender 保护。

# 技能评估 - Windows 基础知识

------

Inlanefreight 最近发生了一起事件，一名心怀不满的营销员工访问了内部托管的 HR 共享并删除了几个机密文件和文件夹。值得庆幸的是，IT 团队拥有良好的备份并恢复了所有已删除的数据。现在有人担心，这名心怀不满的员工能够首先获得人力资源份额。执行安全评估后，您发现 IT 团队可能不完全了解权限在 Windows 中的工作原理。您正在进行培训和演示，向部门展示在 Windows 环境中共享文件的良好安全实践，以及从命令行查看服务以检查是否存在任何潜在的篡改。

注意：请务必按照出现的顺序完成每个步骤。从第 1 步开始，逐步完成第 8 步，包括每一步的所有相关规范。请注意，每个步骤都旨在让您有机会应用本模块中教授的技能和概念。慢慢来，玩得开心，如果遇到困难，请随时与我们联系。

在此演示中，您是：

## 1. 创建一个名为 Company Data 的共享文件夹

## 2. 在 Company Data 文件夹中创建一个名为 HR 的子文件夹

## 3. 创建一个名为 Jim 的用户

- `Uncheck: User must change password at logon`

## 4.创建一个名为HR的安全组

## 5. 将 Jim 添加到 HR 安全组

## 6.将HR安全组添加到共享公司数据文件夹和NTFS权限列表

- `Remove the default group that is present`
- `Share Permissions: Allow Change & Read`
- `Disable Inheritance before issuing specific NTFS permissions`
- `NTFS permissions: Modify, Read & Execute, List folder contents, Read, Write`

## 7.将HR安全组添加到HR子文件夹的NTFS权限列表中

- `Remove the default group that is present`
- `Disable Inheritance before issuing specific NTFS permissions`
- `NTFS permissions: Modify, Read & Execute, List folder contents, Read, and Write`

## 8. 使用 PowerShell 列出有关服务的详细信息

### 练习

